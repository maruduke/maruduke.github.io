---
title: '5. Spring Cloud Gateway'
excerpt: 'Zuul 소개 및 설정'

categories:
    - MSA
tags:
    - [Spring, MSA]

permalink: /MSA/5

toc: true
toc_sticky: true

date: 2024-01-30
last_modified_at: 2024-01-30
---

## 1. 게이트웨이

### 게이트 웨이 주요 기능

1. 인증과 인가
2. API 라우팅
3. QoS
4. 로깅 및 모니터링
5. 입력 유효성 검사

### 동작 원리

<img src="/assets/images/msa/5-1.png">

서비스 이용자(Client)는 서비스와 직접적으로 소통하지 않고 Gateway를 통하여 통신하게 된다.

1. Client가 Gateway에 http요청을 전송한다.
2. Gateway는 Client가 보낸 요청을 분석하여 대응되는 서비스를 찾는다.
3. 대응되는 서버의 물리적인 주소로 Client의 요청에 대한 응답을 전송한다.
4. Gateway에서 받은 응답을 다시 Client에게 전송한다.

-   Gateway는 Client와의 모든 요청, 응답이 지나가는 통로이기 때문에 인증과 모니터링에 있어 큰 장점을 지닌다.

## 2. Zuul과 Spring Cloud Gateway

Spring에서 Gateway 기능을 제공하는 서비스는 Zuul과 Spring Cloud Gateway가 존재한다.

현업에서 아직까지 Zuul라이브러리를 많이 사용한다고 하지만 2018년 이후 개발, 패치가 중단되었기 때문에 공식적으로 Gateway가 추천된다.

## 3. Gateway 기본 설정

1. 의존성 설치

해당 방식은 reactive(비동기 구현)을 기반으로 한 것이므로 webflux를 설치한다.

```gradle
    implementation 'org.springframework.boot:spring-boot-starter-webflux'
    implementation 'org.springframework.cloud:spring-cloud-starter-config'
    implementation 'org.springframework.cloud:spring-cloud-starter-gateway'
    implementation 'org.springframework.cloud:spring-cloud-starter-netflix-eureka-client'
    implementation 'org.springframework.boot:spring-boot-starter-actuator'
```

2. 설정 파일 작성

```yml
spring:
    application:
        name: Gateway

    # Config-server에서 설정 파일 수신
    config:
        import: 'optional:configserver:'
    # Eureka Server에 등록된 서비스 목록 수신 설정
    # 해당 목록을 기반으로 서비스를 라우팅함
    cloud:
        gateway:
            discovery:
                locator:
                    lower-case-service-id: true
                    enabled: true

eureka:
    client:
        register-with-eureka: true
        fetch-registry: true
        service-url:
            default-zone: http://localhost:8761/eureka/
            # Eureka server 등록

management:
    endpoints:
        web:
            exposure:
                include: '*'
```

3. 테스트

Eureka 서버에 연결된 서비스를 Gateway를 통해 요청하기 위해서는 http://{gateway uri}/{request service.name}/{api}으로 웹 브라우저에 서비스 요청

```
GET http://localhost:8080/{application.name}/{api}
```

## 4. 인증/인가

설명 추가

<!-- <img src="/assets/images/Kafka/1-4.png"> -->
