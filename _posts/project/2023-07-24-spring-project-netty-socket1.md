---
title: '\[Project\] netty-socket.io 실시간 채팅 방 구현(1)'
excerpt: 'netty-socket.io 기초 - 백엔드'

categories:
    - Project
tags:
    - [spring, socket.io]

permalink: /project/1

toc: true
toc_sticky: true

date: 2023-12-26
last_modified_at: 2023-12-26
---

## Github Repository

[netty-socket.io + Flutter로 개발한 채팅창](https://github.com/maruduke/flutter-chatting)

---

## netty-socket.io

스프링 프레임워크에서는 WebSocket 통신 기술은 보통 SockJS라이브러리를 기본적으로 사용한다. 반면 NodeJS와 같은 프레임워크에서는 Socket.io를 기반으로 개발한다.

Socket.io가 지원하는 기술이 다양하고 편리하여 Spring에서도 지원하는 기능이 있지 않을까 싶어 관련 기술을 찾던 도중 Socket.io를 지원하는 netty-socket.io를 발견하여 사용하게 되었다.

> -   공부하던 중 왜 해당 기술을 사용하지 않는 이유가 무엇일까 생각해보았다.
>
>     -   SocketIO 기술은 브라우저 호환성 문제가 아직 남아 있는 반면 SockJS의 경우 호환성 우려가 없다는 점
>     -   SockJS의 STOMP프로토콜에서는 Kafka나 RabbitMQ와 같은 메시지 브로커와 간단하게 연계되었지만 netty-socket.io는 연결을 일일이 구현해줘야 한다는 점
>     -   netty-socket.io 기술의 메뉴얼, 자료 부재 등(정말 관련 자료를 찾기 힘들었다.)

위와 같은 이유로 사용되지 않고 있지 않을까 생각된다.

### netty-socket.io 채팅창 깃허브 링크

[netty-socket.io 채팅창 깃허브 링크](https://github.com/maruduke/flutter-chatting/tree/main)

## 프로젝트 구현

해당 프로젝트는 netty-socket.io와 flutter를 활용하여 간단하게 채팅 방 기능을 구현하는 것을 목표로 한다.

### 1. 의존성 설정

build.gradle에 의존성을 추가하여 netty-socket.io 라이브러리 설치

```gradle
implementation 'com.corundumstudio.socketio:netty-socketio:2.0.6'
```

---

### 2. Socket 서버 설정

Socket 서버 설정과 관련된 내용을 작성한다.
/Config/socketConfig.java

```java
//socketConfig.java

@Configuration
public class socketConfig {

    // 소켓 서버 url 설정
    private String host = "localhost";


    private int port = 8082;


    @Bean
    public SocketIOServer socketIOServer() {
        com.corundumstudio.socketio.Configuration config = new com.corundumstudio.socketio.Configuration();
        config.setHostname(host);
        config.setPort(port);


        return new SocketIOServer(config);

    }
}
```

### Trouble Shooting

```java
private int port = 8082;
```

> socket port 번호는 http 통신 포트와 다르게 설정해야 오류가 발생하지 않음
>
> -   localhost:8080으로 서버 포트를 설정하였다면 소켓 포트는 다른 포트(8082)로 지정. ~~간단한 실수인데 에러 메시지가 없어서 많이 헤멨다.~~

---

### 3. Socket server 실행

앞에서 설정한 socket 서버를 실행을 담당하는 파일이다.
스프링 부트 구동 시점에 서버가 실행되도록 선언한다.

`/Config/ServerCommandLine.java`

```java

@Component
@RequiredArgsConstructor
public class ServerCommandLine implements CommandLineRunner {

    private final SocketIOServer socketIOServer;

    @Override
    public void run(String... args) throws Exception {
        socketIOServer.start();
    }
}
```

---

### 4. Socket 연결,연결 끊김 시 이벤트 설정

Socket서버에서 발생하는 연결, 끊김 이벤트를 추가하여 클라이언트와 서버의 연결 상태를 확인 및 이벤트를 처리하는 데 활용하는 함수를 선언한다.

`/Config/SocketModule.java`

```java
@Component
public class SocketModule {
    private final SocketIOServer server;
    private final SocketServcie socketServcie;

    public SocketModule(SocketIOServer server, SocketServcie socketServcie) {
        this.server = server;
        this.socketServcie = socketServcie;

        // client 연결 성공 시 이벤트 추가
        server.addConnectListener(onConnected());

        // client 연결 끊김 발생 시 이벤트 추가
        server.addDisconnectListener(onDisconnected());

    }


    // 클라이언트 연결 시 서버 측에서 로그 출력
    private ConnectListener onConnected() {
        return (client) -> {
            System.out.println("Client Connected to socket" + client.getSessionId().toString());
        };

    }

    // 클라이언트 연결이 끊겼을 경우 로그 출력
    private DisconnectListener onDisconnected() {
        return (client) -> {
            System.out.println("Client Disconnected from socket" + client.getSessionId().toString());
        };
    }
}
```

---

### 5. Socket event 추가

다음은 클라이언트가 전송한 특정 메시지를 연결된 서버에 받았을 경우, 수신받은 메시지의 데이터 등을 처리하는 방법이다.

`/Config/SocketModule.java`

```java
@Component
public class SocketModule {
    private final SocketIOServer server;
    private final SocketServcie socketServcie;

    public SocketModule(SocketIOServer server, SocketServcie socketServcie) {

        // ...생략

        /*
        * 특정 메시지를 수신 시 실행되는 메서드를 설정
        * 1. 이벤트 이름("send_message")
        * 2. 전송된 데이터를 파싱할 클래스
        * 3. 받은 데이터를 처리하는 함수
        */
        server.addEventListener("send_message", String.class, onChatReceived());
    }

    // ...생략

        // 클라이언트가 메시지를 보냈을 경우 해당 메시지를 서버에서 출력
        private DataListener<String> onChatReceived() {
        return (senderClient, data, ackSender) -> {
            System.out.println(data.toString());

        };
    }
```

---

## Socket Room 구현

해당 파트에서는 참여자가 채팅 룸에 입장하여 같은 룸에 있는 참여자끼리 메시지를 소통할 수 있는 형태를 구현하고자 한다.

---

### 1. Message 클래스 선언

```java
@Getter
public class Message {

    private String id;
    private String message;
    private String room;

    Message(String message, String room) {
        this.message = message;
        this.room = room;
    }
}

```

## 2. Room 참가

기존의 SocketModule의 Socket 연결 이벤트 수정하여, 클라이언트가 소켓에 접속할 때 사용한 url 파싱한다.

파싱한 데이터는 room의 이름이며 해당 이름을 사용하여 같은 이름의 채팅 룸에 입장하도록 한다.

그리고 서버에서는 Socket.io의 기능 중 하나인 Room 기능을 활용하여 채팅 룸에 입장하도록 코드를 작성한다.

`/Config/socketModule.java`

```java

    private ConnectListener onConnected() {
        return (client) -> {
            // url 파싱 ex) http://localhost:8082?room=RoomName
            String room = client.getHandshakeData().getSingleUrlParam("room");
            // 클라이언트가 room을 입장
            client.joinRoom(room);
        };
    }
```

## 3. Room 내의 참가자들에게 데이터 발송 서비스 생성

다음은 메시지 발송 서비스이다.
클라이언트가 속한 룸의 모든 참가자들의 정보를 받아와 자신을 제외한 모든 룸 참가자에게 메시지를 발송하는 함수로 Service 클레스에 선언한다.

`Service/SocketService.java`

```java
@Service
@RequiredArgsConstructor
public class SocketServcie {
    private final SocketIOServer socketIOServer;

    public void sendMessage(String room, String eventName, SocketIOClient senderClient, String message) {
        for(SocketIOClient client : senderClient.getNamespace().getRoomOperations(room).getClients()) {
            // 자신을 제외한 모든 Room 참가자에게 메일 발송 이벤트 실행
            if(!client.getSessionId().equals(senderClient.getSessionId())) {
                client.sendEvent(eventName, new Message(message, room));
            }
        }
    }
}
```

## 4. Socket이벤트를 통하여 데이터 발송 서비스 이벤트 설정

앞에서 만든 메시지 발송 함수를 클라이언트의 메시지 이벤트가 발생할 경우 실행하도록 socketModule에 함수를 정의한다.

`/Config/socketModule.java`

```java
    public SocketModule(SocketIOServer server, SocketServcie socketServcie) {
        this.server = server;
        this.socketServcie = socketServcie;

        // ...

        // Message.class를 선언하여 클라이언트에서 받을 데이터 형태를 정의한다.
        server.addEventListener("send_message", Message.class, onChatReceived());
    }
// ... 생략

    private DataListener<Message> onChatReceived() {
        return (senderClient, data, ackSender) -> {
            socketServcie.sendMessage(data.getRoom(), "get_message", senderClient, data.getMessage());

        };
    }
```
