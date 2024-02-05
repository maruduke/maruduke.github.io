---
title: '3. spring cloud config server'
excerpt: '마이크로 서비스 Config server 구현'

categories:
    - MSA
tags:
    - [Spring, MSA]

permalink: /MSA/3

toc: true
toc_sticky: true

date: 2024-01-23
last_modified_at: 2024-01-23
---

## 1. Spring cloud config server 구조

<img src="/assets/images/msa/3-1.png">

Config-server의 경우 Git에서 정의된 다른 서비스의 config 데이터를 받은 후 해당 데이터를 필요한 micro service에게 제공하는 역할을 한다.

### 2. Config Server 구축

1. 의존성 설정

```gradle
    implementation 'org.springframework.cloud:spring-cloud-config-server'
    implementation 'org.springframework.boot:spring-boot-starter-actuator:3.1.2'
```

2. 서버 설정

```java
// ConfigServerApplication.class

@EnableConfigServer // config-server 서비스 허용 어노테이션
@SpringBootApplication
public class ConfigServerApplication {

    public static void main(String[] args) {
        SpringApplication.run(ConfigServerApplication.class, args);
    }

}
```

3. application.yml 설정

```yml
# server port: 8888 지정
server:
    port: 8888

spring:
    application:
        name: config-server
    # git Repository의 yml 설정 파일 저장 위치 정의
    cloud:
        config:
            server:
                git:
                    uri: https://github.com/{git 사용자 명}/{저장소 이름}
                    username: { username }
                    password: { password }
                    # git 저장소가 private일 경우 uesrname, password 입력

# 액츄에이터 허용 범위 설정
management:
    endpoints:
        web:
            exposure:
                include: '*' # 테스트를 위해 모두 허용. 그외 경우 health, refresh 활성화
```

4. Github config 파일 생성

-   깃허브에 각 서비스별 Config.yml 파일 생성한다.

5. 테스트

```
GET http://localhost:8888/{yml 파일 명}/default
```

파일 명을 test-dev.yml, test-prod.yml일 경우

```
GET http://localhost:8888/test/dev
GET http://localhost:8888/test/prod
```

<img src="/assets/images/msa/3-2.png">

### 3. Config 클라이언트 서버 구축

1. 의존성 설치

```gradle
implementation 'org.springframework.cloud:spring-cloud-config-client'
implementation 'org.springframework.boot:spring-boot-starter-actuator'
```

2. application.yml 구성(config server 정보 받아옴)

```yml
spring:
    # application name + yml 파일 탐색
    application:
        name: test
    profiles:
        active: dev
    # spring config-server 위치 정의 optional:{server uri}
    config:
        import: optional:configserver:http://localhost:8888

management:
    endpoints:
        web:
            exposure:
                include: '*'
```

3. 테스트

```yml
# test-dev.yml
Code:
    info: local
    message: test
```

```java

@RestController
public class configController {

    @Value("${code.message}")
    private String message;
    @Value("${code.info}")
    private String info;

    @GetMapping("/")
    public String test() {
        System.out.println(info);
        return message;
    }

}
```

5. 환경 설정 변경 전파 적용 방법

위에 기술한 것처럼 /actuator/refresh 종단점을 호출하여 환경설정값을 갱신해도 되지만 인스턴스가 수가 많아지면 환경설정 정보가 변경될 때마다 인스턴수의 수만큼 종단점을 호출해줘야 한다. 이에 대한 해결 방법으로는 두 가지가 있다.

클라우드 버스 활용  
메시지 브로커를 통해 이벤트 구독

 <!-- <img src="/assets/images/Kafka/1-4.png"> -->
