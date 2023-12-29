---
title: 'netty-socket.io 채팅 방 구현(2)'
excerpt: 'socket Room 생성'

categories:
    - spring-project
tags:
    - [spring, socket.io]

permalink: /Spring/project/2

toc: true
toc_sticky: true

date: 2023-12-29
last_modified_at: 2023-12-29
---

## Room 참가

기존의 SocketModule의 Socket 연결 이벤트 수정

/Config/socketModule.java

```java
    public SocketModule(SocketIOServer server, SocketServcie socketServcie) {
        this.server = server;
        this.socketServcie = socketServcie;

        server.addConnectListener(onConnected());
        server.addDisconnectListener(onDisconnected());

        // Message.class를 선언하여 클라이언트에서 받을 데이터 형태를 정의한다.
        server.addEventListener("send_message", Message.class, onChatReceived());
    }

    private ConnectListener onConnected() {
        return (client) -> {
            // url 파싱 ex) http://localhost:8082?room=RoomName
            String room = client.getHandshakeData().getSingleUrlParam("room");
            // 클라이언트가 room을 입장
            client.joinRoom(room);
        };
    }
```

## Room 내의 참가자들에게 데이터 발송 서비스 생성

Service/SocketService.java

클라이언트가 속한 룸의 모든 참가자들의 정보를 받아와 자신을 제외한 모든 참가자에게 메시지를 발송하는 함수이다.

```java
@Service
@RequiredArgsConstructor
public class SocketServcie {
    private final SocketIOServer socketIOServer;

    public void sendMessage(String room, String eventName, SocketIOClient senderClient, String message) {
        for(SocketIOClient client : senderClient.getNamespace().getRoomOperations(room).getClients()) {
            // 자신을 제외한 모든 Room 참가자에게 메일 발송 이벤트 실행
            if(!client.getSessionId().equals(senderClient.getSessionId())) {
                client.sendEvent(eventName, new Message(MessageType.SERVER, message));
            }
        }
    }
}
```

## Socket이벤트를 통하여 데이터 발송 서비스 실행

/Config/socketModule.java

앞에서 만든 메시지 발송 함수를 클라이언트의 메시지 이벤트가 발생할 경우 실행하도록 socketNodule에 함수를 정의한다.

```java

// ... 생략

    private DataListener<Message> onChatReceived() {
        return (senderClient, data, ackSender) -> {
            socketServcie.sendMessage(data.getRoom(), "get_message", senderClient, data.getMessage());

        };
    }
```
