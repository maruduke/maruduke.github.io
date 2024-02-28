---
title: '4.Spring Cloud Eureka'
excerpt: 'Eureka 설치 및 설정'

categories:
    - MSA
tags:
    - [Spring, MSA]

permalink: /MSA/4

toc: true
toc_sticky: true

date: 2024-01-29
last_modified_at: 2024-01-29
---

## 1. Service Registry & Discovery

마이크로 서비스가 자신의 서비스를 동적으로 등록하여 스스로 라이프 사이클을 관리할 수 있게 하고, 서비스가 등록되면 서비스 탐색 대상에 포함되어 발견되게 함으로써 수동 작업을 피하고 자동화하는 역할을 수행한다.

-   디스커버리는 모든 서비스 관리 감독 및 상태 확인 역할
-   클라이언트와 통신에는 직접적으로 기여하지 않고 필요한 경우 서비스 위치 데이터를 전송함

### 서비스 디스커버리 메커니즘

-   Peer to Peer

    -   서비스 디스커버리 클러스터의 각 노드의 서비스 인스턴스의 상태를 공유

-   부하 분산

    -   요청을 동적으로 분산
    -   정적이며 수동으로 관리되는 로드 밸런서가 서비스 디스커버리로 대체

-   회복성

    -   서비스 디스커버리 클라이언트는 서비스 정보를 로컬에 캐시하여 서비스 디스커버리 서비스가 가용하지 않을 때도 로컬 캐시에 저장된 정보를 기반으로 동작

-   장애 내성
    -   서비스 디스커버리는 서비스 인스턴스의 비정상을 탐지하고 가용 서비스 목록에서 인스턴스 제거

### 서비스 디스커버리 메커니즘 구성

-   서비스 동적 등록

    -   서비스 인스턴스가 시작될 때 자신의 물리적 위치, 경로, 포트를 서비스 레지스트리에 등록

-   서비스 동적
    -   서비스 레지스트리엫서 현재 가용 중인 필요한 서비스를 호출
-   정보 공유
-   상태 모니터링
    -   인스턴스들은 주기적으로 자신의 상태를 레지스트리에 전송, 가용할 수 없는 경우 레지스트리에서 해당 서비스를 제외

<img src="/assets/images/msa/4-1.png">

## 설정

```gradle
// build.gradle
implementation 'org.springframework.cloud:spring-cloud-starter-netflix-eureka-server'
```

```yml
# applicaiton.yml
server:
    port: 8761
spring:
    application:
        name: eurekaserver
    profiles:
        active: default
    # config-server 연결
    config:
        import: optional:configserver:http://localhost:8888/eurekaserver
```

```yml
# github -> eurekaserver.yml
eurenka:
    server: wait-time-in-ms-when-sync-empty: 5 # Eureka Server가 시작되고 Peer node에서 인스턴스를 가져올 수 없을 때 얼마나 기다릴 것인가?
    client:
        register-with-eureka: false # 유레카 서버를 레지스트리에 넣을지 여부
        fetch-registry: false # 레지스트리에 있는 정보를 가져올지 여부

management:
    endpoints:
        web:
            exposure:
                include: '*'
```

```java
@EnableEurekaServer
@SpringBootApplication
public class EurekaApplication {

    public static void main(String[] args) {
        SpringApplication.run(EurekaApplication.class, args);
    }

}
```

## 클라이언트 설정

```gradle
implementation 'org.springframework.cloud:spring-cloud-starter-netflix-eureka-client'
```

<!-- <img src="/assets/images/Kafka/1-4.png"> -->
