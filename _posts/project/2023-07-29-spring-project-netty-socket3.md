---
title: '\[Project\] netty-socket.io 실시간 채팅 방 구현(3) (수정 중)'
excerpt: '채팅방 구현 api 작성 - 백엔드 '

categories:
    - Project
tags:
    - [Flutter, Dart]

permalink: /project/3

toc: true
toc_sticky: true

date: 2023-12-30
last_modified_at: 2024-03-13
---

## Github Repository

[netty-socket.io + Flutter로 개발한 채팅창](https://github.com/maruduke/flutter-chatting)

---

## 구성

앞에서는 소켓을 연결하여 대화 내역을 전달하는 기능을 구현했다.
하지만, 채팅방 접속과 같은 서비스를 구현하기 위해서는 소켓 서비스 외에 추가적인 기능이 필요하다.
필요한 기능은 다음과 같다.

1. 채팅 방 생성 및 저장
2. 생성된 채팅 방 목록 가져오기

### 채팅 방 생성 및 DB저장

간단한 데이터 베이스 테스트 확인을 위해서 H2를 인메모리 설정으로 사용하였다.
ORM으로는 JPA 사용을 위해 설정을 추가하였다.

1. H2 Config

`application.yml`

```yml
# h2 DB in-memory 설정
spring:
    datasource:
        hikari:
            driver-class-name: org.h2.Driver
            jdbc-url: jdbc:h2:mem:test1
            username: sa
            password: 1234

    jpa:
        hibernate:
            ddl-auto: create
        properties:
            hibernate:
                format_sql: true

logging:
    level:
        org.hibernate.SQL: debug
        org.hibernate.type: trace
```

2. 방 생성 서비스 설정

생성된 방을 저장하기 위해서 `JPA`를 활용하여 H2 DB에 저장하도록 Repository 클래스를 생성한다.

`Repository / RoomRepository`

```java
public interface RoomRepository extends JpaRepository<Room, Long> {

}
```

데이터 저장 서비스 구현

`Service / RoomService`

```java
@RequiredArgsConstructor
@Service
public class RoomService {
    final RoomRepository roomRepository;

    public void saveRoom(Room room) {
        roomRepository.save(room);
    }

}
```

클라이언트에서 방 생성 요구 사항을 처리할 수 있도록 RestApi형태의 Controller 구현

`/Controller/RoomController`

```java
@RestController
@RequiredArgsConstructor
public class RoomController {

    private final RoomService roomService;

    @PostMapping("/room")
    public void createRoom(@RequestBody() String roomName) {

        Room room = new Room(roomName);
        roomService.saveRoom(room);
    }

}
```

### 생성된 채팅 방 목록 가져오기

클라이언트가 채팅방에 입장할 수 있도록 모든 채팅 방 목록을 서버에서 가져올 필요가 있다.  
DB에 저장된 모든 목록을 조회하고 반환한다.

Repository에 모든 방 조회 기능 추가
`Repository / RoomRepository`

```java
public interface RoomRepository extends JpaRepository<Room, Long> {
    // 생성된 모든 방 조회 메서드
    List<Room> findAll();
}
```

`Repository / RoomService`

```java
@Service
public class RoomService {
    final RoomRepository roomRepository;

    // ... 생략

    public List<RoomResponse> getRoomList() {
        List<Room> rooms = roomRepository.findAll();
        List<RoomResponse> collect = rooms.stream()
                .map(m -> new RoomResponse(m.getId(),m.getName()))
                .collect(Collectors.toList());
        if(collect != null)
            return collect;
        else
            return null;
    }

}
```

Controller 추가
`Repository / RoomController`

```java
@RestController
@RequiredArgsConstructor
public class RoomController {

    private final RoomService roomService;

    // ... 생략

    @GetMapping("/room")
    @CrossOrigin(origins = "http://localhost:58162/")
    public List<RoomResponse> getRoomList() {
        return roomService.getRoomList();
    }

}
```
