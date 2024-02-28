---
title: 5. 도커 컴포즈로 분산 애플리케이션 실행하기
author: maruduke
date: 2023-10-01 14:10:00 +0800
categories: [Docker]
tags: [docker]
---

## 도커 컴포즈 파일의 구조

분산 애플리케이션에서 Dockerfile은 애플리케이션의 한 부분을 패키징하는 수단에 지나지 않는다. 프론트엔드, 백엔드 API, 데이터베이스를 갖춘 애플리케이션을 패키징하려면 각 컴포넌트에 하나씩 세 개의 Dockerfile 스크립트가 필요하다.  
이 컨테이너를 일일이 실행하면 오류의 원인이 될 수 있기 때문에 도커 컴포즈 파일에 애플리케이션의 구조를 정의하면 된다.  
도커 컴포즈 파일은 애플리케이션의 원하는 상태, 즉 모든 컴포넌트가 실행 중일 때 어떤 상태여야하는지 기술하는 파일이다. 도커 컴포즈 파일을 작성하고 컴포즈 도구를 사용해 애플리케이션을 실행한다.  
다음은 도커 컴포즈 파일의 전체 스크립트다.

``` yml
version: '3.7'

services:

    todo-web: 
        image: diamol/ch06-todo-list
        ports:
            - "8020:80"
        networks:
            - app-net

networks:
    app-net:
        external:
            name: nat

```

- `version`: 도커 컴포즈 파일 형식의 버전
- `services`: 애플리케이션을 구성하는 모든 컴포넌트를 열거하는 부분
- `networks`: 서비스 컨테이너가 연결될 모든 도커 네트워크를 열거하는 부분

diamol/ch06-todo-list이미지로부터 단일 컨테이너로 실행되며, 컨테이너의 80번 포트로 호스트 컴퓨터의 8020번 포트를 통해 공개한다. 그리고 app-net이라는 이름의 도커 네트워크에 연결된다. 최종적인 결과는 `docker container run -p 8020:80 --name todo-web --network nat diamol/ch06-todolist`명령을 실행한 것이다.  
서비스 이름 아래로는 속성이 기술된다. image는 실행할 이미지를 지정하는 필드이고, ports는 공개할 포트에 대한 정보 networks는 컨테이너가 접속할 도커 네트워크를 정의하는 필드이다. 서비스가 구성될 네트워크 이름은 app-net이다. 그러나 networks 항목을 보면 이 네트워크는 nat이라는 이름의 외부 네트워크로 연결된다. external 필드의 의미는 nat 네트워크가 이미 존재하므로 새로 생성하지 말라는 의미이다.  
도커 컴포즈를 사용하려면 명령행에서 docker-compose명령을 실행하면 된다.

``` Console
// 도커 네트워크를 생성하고 도커 컴포즈 명령으로 애플리케이션 실행

docker network create nat
docker-compose up
```

## 도커 컴포즈를 사용해 여러 컨테이너로 구성된 애플리케이션 실행하기


예시 애플리케이션은 자바로 구현된 웹 프론트엔드, Go로 구현된 REST API, Node.js로 구현된 로그 수집 모듈로 구현되었다. 차례로 이들 컨테이너를 실행시켜 애플리케이션을 가동했으며 모든 컨테이너를 동일한 도커 가상 네트워크에 미리 약속된 이름으로 접속시켜 애플리케이션 구성 요소가 서로 통신할 수 있도록 했다.
  
아래 컴포즈 스크립트를 보면 services필드에 이 이미지 갤러리 애플리케이션이 기술돼있다. 여기서는 서비스 구성값에 집중할 수 있도록 네트워크 설정을 생략했으나 이 애플리케이션도 nat 네트워크에 접속된다.

``` yml

accesslog:
    image: diamol/ch04-access-log

iotd:
    image: diamol/ch04-image-gallery
    ports: 
        - "8010:80"
    depends_on:
        - accesslog
        - iotd

```
accesslog서비스는 공개하는 포트나 설정값도 없이 이미지 이름만 적혀있다.  
반면 iotd 서비스는 REST API다. 스크립트에 이미지 이름과 함께 80번 포트를 호스트 컴퓨터의 무작위 포트를 통해 공개하도록 작성했다.  
image-gallery 서비스는 이미지 이름과 함께 8010번 포트를 호스트 컴퓨터의 8010번 포트를 통해 공개한다.  
또 depends_on항목을 추가해 이 서비스는 다른 두 서비스에 의존한다는 사실도 기술했다. 이 의존성을 만족하기 위해 컴포즈는 image-gallery서비스를 실행하기 전 두 서비스를 먼저 실행하려 시도하게 된다.  
  
도커 컴포즈를 사용해 분리 모드로 애플리케이션을 실행한다.
``` console
$ docker-compose up --detach
```
도커 컴포즈는 의존 관계를 준수해 세 개의 컨테이너를 만든다. 그러므로 image-gallery 컨테이너는 accesslog와 iotd서비스가 실행된 다음 실행된다. --detach플래그를 사용하면 컨테이너가 백그라운드로 실행되므로 명령을 실행한 창에 로그가 출력되진 않는다.

  
도커 컴포즈를 이용하여 iotd서비스의 컨테이너 수를 늘려보자. 그리고 웹 페이지를 리프레시하며 iotd 컨테이너 로그를 살펴보라
``` Console
$ docker-compose up -d --scale iotd=3

$ docker-compose logs --tail=1 iotd
```
웹페이지를 몇 차례 리프레시하면 웹 애플리케이션이 API에 데이터를 요청하고, 이들 요청을 늘어난 컨테이너가 고르게 나눠 처리하는데, API서비스가 요청을 처리할 때마다 남기는 컨테이너 로그로 이를 확인할 수 있다.


## 도커 컨테이너 간의 통신

도커 컨테이너는 별도의 네트워크 공간을 가진 가상환경으로 도커 엔진으로부터 부여된 가상의 IP주소를 가지며 모두 같은 도커 네트워크로 연결돼 이 IP주소를 통해 서로 통신할 수 있다.  
그러나 컨테이너가 교체되면 IP주소도 변경된다. IP주소가 변경돼도 문제가 없도록 도커에서 DNS를 이용해 서비스 디스커버리 기능을 제공한다.

도커에도 DNS 서비스가 내장돼 있다. 컨테이너에서 실행 중인 애플리케이션도 다른 구성 요소에 접근하기 위해 이 DNS서비스를 사용한다. 도커 네트워크 상에 있는 다른 컨테이너의 정보를 사용할 수 있다. 
  
도커 컴포즈로 애플리케이션을 실행하고, 웹 컨테이너에서 DNS 조회 명령을 실행해보자
``` Console
$ docker-compose up -d scale --iodt=3

# 리눅스 컨테이너
$ docker container exec -it image-of-the-day_image-gallery_1 sh

# DNS서비스에서 조회
$ nslookup accesslog

# DNS서비스에서 조회
$ nslookup iotd

$ exit
```
각각의 컨테이너가 가르키는 세 개의 IP 주소가 출력됨

하나의 도메인에 대해 DNS 조회 결과에 여러 개의 IP 주소가 나올 수 있다. 도커 컴포즈는 이 점을 활용해 간단한 로드 밸런싱을 구현 할 수 있다. 여러 개의 IP주소가 담긴 조회 결과를 어떻게 활용할지는 전적으로 애플리케이션이 결정한다. 간단하게 조회 결과의 첫 번째 IP주소만 사용할 수도 있다. 모든 컨테이너에 고르게 부하가 분배되도록 도커의 DNS시스템은 조회 결과의 순서를 매번 변화시킨다. 이 방법으로 컨테이너 간의 트래픽을 고르게 분산시킬 수 있다.


## 도커 컴포즈로 애플리케이션 설정값 지정하기

원격 컨테이너에서 동작하는 PostgreSQL데이터베이스를 사용하도록 해 보자

``` yml
services:

    todo-db: 
    image: diamol/postgres:11.5
    ports: 
        - 5433:5432
    networks:
        - app-net
    
    todo-web:
        image: diamol/ch06-todo-list
        ports: 
            - "8020:80"
        environment:
            - Database:Provider=Postgres
        depends_on:
            - todo-db
        networks:
            - app-net
        secrets:
            - source: postgres-connection
            target: /app/config/secrets.json
    
```

- environment: 컨테이너 안에서 사용될 환경 변수 값이 정의된다. 실행시 컨테이너 안의 환경변수 Database:Provider의 값이 Postgres로 설정된다.
- secrets: 컨테이너 내부의 파일에 기록될 비밀값을 정의한다. 이 애플리케이션이 실행되면 컨테이너에 /app/config/secrets.json 파일이 생성되고, 이 파일에는 postgres-connection이라는 이름의 비밀값의 값이 기록된다.

비밀값은 클러스트 환경에서 쿠버네티스나 도커 스웜 같은 컨테이너 플랫폼을 통해 제공된다. 평소에는 클러스터 데이터베이스에 암호화돼 있기 때문에 데이터베이스 패스워드, 인증서, API 키 등 민감한 정보로 구성된 설정값을 전달하는 데 적합하다.

``` yml
# 로컬 파일에서 비밀값을 읽어오는 컴포즈 파일

secrets:
    postgres-connection:
        file: ./config/secrets.json
```

애플리케이션 설정값을 컴포즈 파일에 정의하면, 같은 도커 이미지라도 다양하게 활용할 수 있고 서로 다른 환경에 대한 설정을 명시적으로 정의할 수 있다. 개발 환경과 테스트 환경의 컴포즈 파일을 별도로 작성해 두면 공개하는 포트를 환경에 따라 달리하거나 애플리케이션의 기능을 선택적으로 활성화할 수 있다.

``` Console

# 애플리케이션 확인
docker-compose up -d

docker-compose ps

```
