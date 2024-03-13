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

Socket.io가 지원하는 기술이 다양하고 편리하여 Spring에서도 Socket.io를 지원하지 않을까 싶어 관련 기술을 찾던 도중 Socket.io를 지원하는 netty-socket.io를 발견하여 사용하게 되었다.

> 공부하던 중 왜 해당 기술을 사용하지 않는가에 대해 궁금했는데 생각되는 이유는 다음과 같다.
>
> -   SocketIO 기술은 브라우저 호환성 문제가 아직 남아 있는 반면 SockJS의 경우 호환성 우려가 없다는 점
> -   SockJS의 STOMP프로토콜에서는 Kafka나 RabbitMQ와 같은 대용량 처리 기술과 패키지로 연계되었지만 netty-socket.io는 대용량 기술 연계 기술이 없는 문제로 인하여 사용하지 않을까 생각 중이다.
> -   Netty 서버의 어려움과 netty-socket.io 기술의 메뉴얼, 자료 부재 등(정말 관련 자료를 찾기 힘들었다.)

위와 같은 이유로 사용되지 않고 있지 않을까 생각된다. 그래도 최근에 리액티브 프로그래밍으로 인해 Netty가 사용되는 만큼 충분히 주목 받을 수 있는 기술이라 생각된다.

### netty-socket.io 채팅창 깃허브 링크

[netty-socket.io 채팅창 깃허브 링크](https://github.com/maruduke/flutter-chatting/tree/main)

## 1. 의존성 설정

build.gradle에 의존성을 추가하여 netty-socket.io 라이브러리 설치

```gradle
implementation 'com.corundumstudio.socketio:netty-socketio:2.0.6'
```

---

## 2. Socket 서버 설정

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
> -   localhost:8080으로 서버 포트를 설정하였다면 소켓 포트는 다른 포트(8082)로 지정. ~~간단한 실수인데 오류 메시지도 없어서 많이 헤멨다.~~

---

## 3. Socket server 실행

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

## 4. Socket 연결,연결 끊김 시 이벤트 설정

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

## 5. Socket event 추가

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
