---
title: '\[Project\] netty-socket.io 채팅 방 구현(2)'
excerpt: 'socket Room 생성 - 백엔드'

categories:
    - Project
tags:
    - [spring, socket.io]

permalink: /project/2

toc: true
toc_sticky: true

date: 2023-12-29
last_modified_at: 2023-12-29
---

## Github 주소

[netty-socket.io + Flutter로 개발한 채팅창](https://github.com/maruduke/flutter-chatting)

## 소개

해당 파트에서는 참여자가 채팅 룸에 입장하여 같은 룸에 있는 참여자끼리 메시지를 소통할 수 있는 형태를 구현하고자 한다.

## 1. Message 클래스 선언

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

기존의 SocketModule의 Socket 연결 이벤트 수정하여, 클라이언트가 소켓에 접속할 때 사용한 url의 파싱한다.

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
