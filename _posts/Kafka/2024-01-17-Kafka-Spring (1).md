---
title: '1. Docker를 사용한 Kafka 설치'
excerpt: 'Kafka Docker-compose를 통한 설치'

categories:
    - Kafka
tags:
    - [Spring, Kafka]

permalink: /Kafka/1

toc: true
toc_sticky: true

date: 2024-01-17
last_modified_at: 2024-01-17
---

## 1. Docker-Compose.yml 작성

Docker를 사용하여 Kafka, zookeeper를 설치하기 위해
docker-compose파일을 작성한다.

docker-compose.yml

```yml
# docker-compose 버전
version: '3.1'
services:
    # 서비스 명
    zookeeper:
        # 사용할 이미지
        image: wurstmeister/zookeeper
        container_name: zookeeper
        ports:
            - '2181:2181'
    # 서비스 명
    kafka:
        # 사용할 이미지
        image: wurstmeister/kafka
        container_name: kafka
        ports:
            - '9092:9092'
        environment:
            KAFKA_ADVERTISED_HOST_NAME: localhost
            # zookeeper(서비스이름):2181(컨테이너내부포트) 로 대상을 지정
            KAFKA_ZOOKEEPER_CONNECT: zookeeper:2181
# 실행: docker-compose -f docker-compose.yml up -d
```

## 2. 이미지 설치 및 실행

아래의 명령어를 터미널에 실행하여 Docker 이미지를 설치 및 컨테이너를 실행한다.

```
$ docker-compose -f docker-compose.yml up -d
```

<img src="/assets/images/Kafka/1-1.png">

터미널에 docker ps를 입력하여 컨테이너 실행 여부 확인  
<img src="/assets/images/Kafka/1-2.png">

## 3. 카프카 컨테이너 설정

1. 카프카 디렉토리 이동

```
$ docker exec -it kafka /bin/sh
$ cd /opt/kafka_2.13-2.8.1/bin
```

2. topic 설정

```
$ kafka-topics.sh --create --zookeeper zookeeper:2181 --replication-factor 1 --partitions 1 --topic quickstart
```

3. producer 설정

```
$ kafka-console-producer.sh --topic quickstart --bootstrap-server localhost:9092

```

해당 명령어 이후 입력된 정보는 Topic에 전송됨  
<img src="/assets/images/Kafka/1-3.png">

4. 전송된 데이터 확인

새로운 터미널을 열고 위의 1번 과정을 다시 진행

```
$ kafka-console-consumer.sh --topic quickstart --from-beginning --bootstrap-server localhost:9092
```

다음 명령어 입력시 전송된 메시지 확인 가능하다.  
<img src="/assets/images/Kafka/1-4.png">
