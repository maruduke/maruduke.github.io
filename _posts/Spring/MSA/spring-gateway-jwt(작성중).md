## Spring Cloud GateWay와 OAuth2, JWT 사용

1. 일체화
2. 분리

## GateWay와 OAuth2 일체화

## GateWay와 OAuth2 분리

Gateway는 JWT만 검증하고 서비스에 넘겨줌. OAuth2는 따로 서비스를 만들어 Google, github 등 다른 계정을 연결하고 JWT값을 반환.
보통 JWT 정보는 Redis의 저장됨. 간단하게 인메모리 h2를 사용해도 될지도?

장점: 단일 책임 원칙, 유지 보수 편리  
단점: 서비스 오버 헤드 발생?

1. 의존성 설정 추가

기존의 gateway의 build.gradle에서 의존성 추가

```gradle
implementation 'org.springframework.boot:spring-boot-starter-oauth2-client'
```

2. application.yml 설정 추가

```yml
spring:
    security:
        oauth2:
            client:
                registration:
                    google:
                        clientId: 'clientId'
                        clientSecret: 'password'
```

3. ROLE 생성

사용자의 권한을 관리할 Enum 클래스 Role을 생성한다.

Spring Security의 권한은 항상 \_ROLE이 앞에 있어야한다.
