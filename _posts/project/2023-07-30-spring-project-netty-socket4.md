---
title: '\[Project\] netty-socket.io 실시간 채팅 방 구현(4)'
excerpt: 'Flutter를 활용하여 페이지 구성 - 프론트엔드'

categories:
    - Project
tags:
    - [Flutter, Dart]

permalink: /project/4

toc: true
toc_sticky: true

date: 2023-12-30
last_modified_at: 2024-03-13
---

## Github Repository

[netty-socket.io + Flutter로 개발한 채팅창](https://github.com/maruduke/flutter-chatting)

---

앞서 설명한 글로 통하여 백엔드는 전체적으로 구현해 보았다. 이번에는 최근 배운 Flutter를 활용하여 Frontend를 구현하여 방 생성 창, 생성된 방 목록 확인 및 입장, 채팅 창을 구현하여 클라이언트에게 보여주는 것을 목표로 한다.

## 구성

채팅 창 페이지는 3 페이지로 구현했다.

1. 채팅 방 생성 페이지
2. 채팅 방 목록 페이지
3. 채팅 송수신 목록 페이지

---

## 1. 채팅 방 생성 페이지

### 방 생성 요청 보내기

`lib/Service/room_service`

```dart
import "dart:convert";

import "package:frontend/models/room_data.dart";
import "package:http/http.dart" as http;

class RoomService {
  static const String baseurl = "http://localhost:8080";
  static const String room = "room";

  // 비동기적 데이터 실행
  static void createRoom(String roomName) async {
    final url = Uri.parse("$baseurl/$room");
    // POST 요청으로 방 생성 실행
    final response = await http.post(url, body: roomName);

    if (response.statusCode == 200) {
      print("create room");
    }
    throw Error();
  }
}
```

### 화면 구성

<p align="center">
    <img src="/assets/images/project/netty-socket_io_flutter/3-2.png" width="90%">
</p>

화면 구성은 간단하게 채팅 방 이름을 작성할 수 있는 textarea와 서버에 요청을 전송하는 버튼 2가지로 구성하였다.

`lib/screnns/create_room_screen.dart`

```dart
import 'package:flutter/material.dart';
import 'package:frontend/services/room_service.dart';

class CreateRoom extends StatelessWidget {
  const CreateRoom({super.key});

  void onPressed(String text) {
    // 방 생성
    RoomService.createRoom(text);
  }

  @override
  Widget build(BuildContext context) {
    final roomName = TextEditingController();

    return Scaffold(
      appBar: AppBar(
        title: const Center(
          child: Text("방 생성"),
        ),
      ),
      body: Column(
        children: [
          const SizedBox(height: 30),
          TextField(
            controller: roomName,
            decoration: const InputDecoration(
              border: OutlineInputBorder(),
              labelText: 'Room name',
            ),
          ),
          const SizedBox(
            height: 30,
          ),
          TextButton(
            onPressed: () {
              onPressed(roomName.text);
              Navigator.pop(context);
            },
            child: const Text("create Room"),
          ),
        ],
      ),
    );
  }
}
```

---

## 2. 채팅 방 목록 페이지

서버에서 가져 온 모든 채팅 방 목록을 출력하고 클라이언트가 방을 선택하면 채팅 방에 입장하도록 설계하였다.

### 채팅 방 데이터 수신 클래스

채팅방에 대한 필요한 데이터를 저장하는 클래스를 선언한다.

`lib/models/room_data`

```dart
class Room {
  late final int id;
  late final String name;

  Room.fromJson(Map<String, dynamic> json) {
    id = json['id'];
    name = json['name'];
  }
}
```

### 서버에서 모든 채팅 방 정보 가져오기

RoomService 클래스 내에 모든 채팅 방 리스트를 받아오는 getRoomList() 함수를 사용하여 RestApi로 비동기적으로 데이터를 받아온다.

`lib/services/room_service.dart`

```dart
import "dart:convert";

import "package:frontend/models/room_data.dart";
import "package:http/http.dart" as http;

class RoomService {

  // server 주소
  static const String baseurl = "http://localhost:8080";
  static const String room = "room";

  // Future: 비동기적으로 데이터를 받아오는 자료형
  static Future<List<Room>> getRoomList() async {
    final List<Room> roomInstances = [];

    // Get 요청 전송
    final url = Uri.parse("$baseurl/$room");
    final response = await http.get(url);

    if (response.statusCode == 200) {
      final List<dynamic> rooms = jsonDecode(response.body);

      // 데이터 파싱
      for (var room in rooms) {
        roomInstances.add(Room.fromJson(room));
      }

      return roomInstances;
    }
    throw Error();
  }

}
```

### 화면 구성

<p align = "center">
    <img src="/assets/images/project/netty-socket_io_flutter/3-1.png" width="90%">
</p>

페이지 상단에는 채팅 방 생성 페이지로 갈 수 있는 버튼이 있다.
그리고 아래에는 서버에서 받은 채팅 방 목록을 리스트 형태로 출력한다.
해당 리스트에 접속하길 원하는 방에 입장하면 해당 채팅방에 입장할 수 있도록 이벤트를 삽입한다.

`lib/screen/main_screen.dart`

```dart
import 'package:flutter/material.dart';
import 'package:frontend/models/room_data.dart';
import 'package:frontend/screens/chat_room_screen.dart';
import 'package:frontend/screens/create_room.dart';
import 'package:frontend/services/room_service.dart';
import 'package:frontend/widgets/chat_room_widget.dart';

// 대화 room 보여주기
class MainScreen extends StatelessWidget {
  MainScreen({super.key});

  final Future<List<Room>> rooms = RoomService.getRoomList();

  @override
  Widget build(BuildContext context) {
    return Scaffold(
      appBar: AppBar(
        title: const Text('chat room'),
        actions: [
          IconButton(
              onPressed: () {
                Navigator.push(
                  context,
                  MaterialPageRoute(
                    builder: (context) => const CreateRoom(),
                  ),
                );
              },
              icon: const Icon(Icons.create))
        ],
      ),
      body: FutureBuilder(
        future: rooms,
        builder: (context, snapshot) {
          if (snapshot.hasData) {
            return makeList(snapshot);
          }
          return const Center(
            child: CircularProgressIndicator(),
          );
        },
      ),
    );
  }

  ListView makeList(AsyncSnapshot<List<Room>> snapshot) {
    return ListView.separated(
      itemCount: snapshot.data!.length,
      itemBuilder: (context, index) {
        Room room = snapshot.data![index];
        return TextButton(
          onPressed: () {
            Navigator.push(
              context,
              MaterialPageRoute(
                builder: (context) => ChatRoomScreen(roomName: room.name),
              ),
            );
          },
          child: chatRoom(room: room),
        );
      },
      separatorBuilder: (context, index) => const SizedBox(height: 10),
    );
  }
}
```

---

## 3. 채팅 목록 페이지

마지막으로 서로 메시지를 주고 받을 수 있는 채팅 목록 페이지이다. 서버와 클라이언트가 서로 소켓 연결을 통해 실시간으로 데이터를 주고 받을 수 있다.  
또한 클라이언트는 연결을 통해서 같은 방에 있는 모든 클라이언트에게 메시지를 전송하거나 받을 수 있다.

### 서버와의 소켓 연결과 채팅 송수신

`/lib/service/socket_service.dart`

```dart
import 'package:flutter/material.dart';
import 'package:frontend/models/message.dart';
import 'package:socket_io_client/socket_io_client.dart' as IO;

class SocketService {
  String host = 'localhost';
  String port = '8082';
  String roomName;
  late IO.Socket socket;
  late Function cb;

  SocketService(this.roomName, this.cb) {
    connectRoom(roomName);
  }

  connectRoom(String room) {
    socket = IO.io(
      'http://$host:$port?room=$room',
      IO.OptionBuilder()
          .setTransports(['websocket'])
          .disableAutoConnect()
          .build(),
    );

    // server, client 연결
    socket.connect();

    socket.onConnect((_) => debugPrint("socket connect"));
    socket.on("get_message", (data) {
      Message message = Message.fromJson(data as Map<String, dynamic>);
      cb(message);
    });
  }

  // 메시지 전송 이벤트
  sendMessage(String message) {
    Message sendMessage = Message(socket.id!, message, roomName);
    if (socket.id != null) {
      socket.emit("send_message", sendMessage.toJson());
      cb(sendMessage);
    } else {
      debugPrint("socket is null");
    }
  }

  String getSocketId() {
    return socket.id!;
  }
}
```

### 화면 구성

가장 하단에 메시지를 입력하는 textarea와 위에서 아래로 왼편에 상대방이 보낸 메시지를 오른편에는 자신이 보낸 메시지를 넣는 식으로 작성하였다.

<p align = "center">
    <img src="/assets/images/project/netty-socket_io_flutter/3-3.png" width="90%">
</p>

`/lib/screen/chat_room_screen`

```dart
import 'package:flutter/material.dart';
import 'package:frontend/models/message.dart';
import 'package:frontend/services/socket_service.dart';
import 'package:frontend/widgets/message_box.dart';


class ChatRoomScreen extends StatefulWidget {
  final String roomName;

  const ChatRoomScreen({super.key, required this.roomName});

  @override
  State<ChatRoomScreen> createState() => _ChatRoomState();
}

class _ChatRoomState extends State<ChatRoomScreen> {
  final _messageController = TextEditingController();
  late final String roomName;
  late final SocketService socketService;
  List<Message> messages = [];

  void getMessage(Message message) {
    setState(() {
      messages.add(message);
      debugPrint('$messages.length');
    });
  }

  @override
  void initState() {
    roomName = widget.roomName;
    socketService = SocketService(roomName, getMessage);
    super.initState();
  }

  @override
  Widget build(BuildContext context) {
    return Scaffold(
      backgroundColor: Colors.cyan.shade50,
      appBar: AppBar(
        title: Text(roomName),
      ),
      body: Column(
        children: [
          Flexible(
            child: ListView.separated(
              separatorBuilder: (BuildContext context, int index) =>
                  const SizedBox(height: 10),
              reverse: false,
              itemCount: messages.length,
              itemBuilder: (context, index) {
                if (messages.isNotEmpty) {
                  if (messages[index].id == socketService.getSocketId()) {
                    // 자신의 메시지 오른쪽 배치
                    return MessageBox(
                      boxColor: Colors.yellow,
                      isMe: true,
                      message: messages[index].message,
                    );
                  } else {
                    // 타인의 메시지 왼쪽 정렬
                    return MessageBox(
                      boxColor: Colors.white,
                      isMe: false,
                      message: messages[index].message,
                    );
                  }
                }
                return const CircularProgressIndicator();
              },
            ),
          ),
          const Divider(
            height: 1.0,
          ),
          ColoredBox(
            color: Colors.white,
            child: Row(
              children: [
                Flexible(
                  child: TextField(
                    controller: _messageController,
                  ),
                ),
                IconButton(
                    onPressed: () {
                      if (_messageController.text != "") {
                        debugPrint("onpressed send");
                        socketService.sendMessage(_messageController.text);

                        debugPrint("test");
                        _messageController.text = "";
                      }
                    },
                    icon: const Icon(Icons.send))
              ],
            ),
          )
        ],
      ),
    );
  }
}
```
