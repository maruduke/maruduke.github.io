---
title: 'netty-socket.io 채팅 방 구현(1)'
excerpt: 'netty-socket.io 기초'

categories:
    - spring-project
tags:
    - [spring, socket.io]

permalink: /Spring/project/1

toc: true
toc_sticky: true

date: 2023-12-26
last_modified_at: 2023-12-26
---

## netty-socket.io

스프링 프레임워크에서는 WebSocket 통신 기술은 보통 SockJS라이브러리를 기본적으로 사용한다. 반면 NodeJS와 같은 프레임워크에서는 Socket.io를 기반으로 개발한다.

Socket.io가 지원하는 기술이 다양하고 편리하여 Spring에서도 Socket.io를 지원하지 않을까 싶어 관련 기술을 찾던 도중 Socket.io를 지원하는 netty-socket.io를 발견하여 사용하게 되었다.

-   공부하던 중 왜 해당 기술을 사용하지 않는가에 대해 궁금했는데 아마 SockJS의 STOMP프로토콜에서는 Kafka나 RabbitMQ와 같은 대용량 처리 기술과 연계되었지만 netty-socket.io는 대용량 기술 연계 기술이 없는 문제로 인하여 사용하지 않을까 생각 중이다. 추가적으로 socket Room과 같은 편의 기능도 kafka 등으로 구현 가능하다는 점도 있는 것 같다.

## 1. 의존성 설정

build.gradle에 의존성을 추가하여 netty-socket.io 라이브러리 설치

```gradle
implementation 'com.corundumstudio.socketio:netty-socketio:2.0.6'
```

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
        // socket port 번호는 http 통신 포트와 다르게 설정해야 오류가 발생하지 않음

        return new SocketIOServer(config);

    }
}
```

## 3. Socket server 실행

앞에서 설정한 socket 서버를 실행을 담당하는 파일이다.

/Config/ServerCommandLine.java

```java

// ServerCommandLine.java
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

## 4. Socket 연결,연결 끊김 시 이벤트 설정

/Config/SocketModule.java
Socket 연결, 끊김 이벤트를 추가하여 서버의 연결 상태를 확인하는 데 활용한다.

```java
@Component
public class SocketModule {
    private final SocketIOServer server;
    private final SocketServcie socketServcie;

    public SocketModule(SocketIOServer server, SocketServcie socketServcie) {
        this.server = server;
        this.socketServcie = socketServcie;

        // socket client 연결 성공 시 실행 메소드 추가
        server.addConnectListener(onConnected());

        // socket client 연결 끊김 발생 시 실행 메소드 추가
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

## 5. Socket event 추가

다음은 클라이언트가 전송한 특정 메시지를 연결된 서버에 받았을 경우, 수신받은 메시지의 데이터 등을 처리하는 방법이다.
/Config/SocketModule.java에 해당 코드를 추가한다.

```java
@Component
public class SocketModule {
    private final SocketIOServer server;
    private final SocketServcie socketServcie;

    public SocketModule(SocketIOServer server, SocketServcie socketServcie) {

        // ...생략

        // 특정 메시지(send_message)를 수신 시 실행되는 메서드를 설정
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
