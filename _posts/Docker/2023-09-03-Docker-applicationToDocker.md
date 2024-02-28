---
title: 3. 애플리케이션 빌드 실전 예제
author: maruduke
date: 2023-09-03 14:10:00 +0800
categories: [Docker]
tags: [docker]
---

## 멀티 스테이지

```Dockerfile
FROM diamol/base AS build-stage
RUN echo 'Building...' > /build.txt

FROM diamol/base AS test-stage
COPY --from=build-stage /build.txt /build.txt
RUN echo 'Testing...' >> /build.txt

FROM diamol/base
COPY --from=test-stage /build.txt /build.txt
CMD cat /build.txt
```

이 스크립트는 빌드가 여러 단계로 나뉘는 멀티 스테이지 빌드가 적용한 것이다. 각 빌드 단계는 FROM 인스트럭션으로 시작된다. 필요한 경우 빌드 단계에 AS파라미터를 이용해 이름을 붙일 수도 있다. 위의 코드가 멀티 스테이지 빌드의 예로 두 단계는 build-stage, test-stage로 이름이 붙어 있고, 마지막 한 단계는 이름이 없다. 빌드가 여러 단계로 나뉘어 있다고는 하지만, 최종 산출물은 마지막 단계의 내용물을 담은 도커 이미지다.



## 자바 소스 코드

이 애플리케이션은 자바 스프링부트를 사용해 구현한 것으로, 앞으로 도커를 이용해 이 애플리케이션을 빌드하고 실행하는 과정을 체험할 것이다. 이 애플리케이션을 빌드하고 실행하기 위해 자바 빌드 도구를 따로 설치할 필요가 없다. 필요한 도구는 모두 도커 이미지를 통해 가져온다. 

이 애플리케이션은 표준적인 자바 빌드 도구인 메이븐과 OpenJDK를 사용한다. 

``` Dockerfile

FROM diamol/maven AS builder


WORKDIR /usr/src/iotd
COPY pom.xml
RUN mvn -B dependency:go-offline

COPY . .
RUN mvn package

# app
FROM diamol/openjdk

WORKDIR /app
COPY --from=builder /usr/src/iotd/target/iotd-service-0.1.0.jar .

EXPOSE 80
ENTRYPOINT ["java", "-jar", "/app/iotd-service-0.1.0.jar"]

```

- 기반 이미지는 diamol/maven이다. 이 이미지는 메이븐과 OpenJDK를 포함한다.
- builder 단계는 먼저 이미지에 작업 디렉터리를 만든 후 이 디렉터리에 pom.xml 파일을 복사하며 시작된다. 이 파일에는 메이븐에서 수행할 빌드 절차가 정의돼 있다.
- 첫 번째 RUN 인스트럭션에 메이븐이 실행돼 필요한 의존 모듈을 내려 받는다. 이 과정에는 상당한 시간이 걸리기 때문에 별도의 단계로 분리해 레이어 캐시를 활용할 수 있도록 한다. 새로운 의존 모듈이 추가될 경우, XML파일이 변경됐을 것이므로 이 단계가 다시 실행된다.
- 그다음에는 COPY . . 인스트럭션을 통해 나머지 소스 코드가 복사된다. 이 인스트럭션은 '도커 빌드가 실행 중인 디렉터리에 포함된 모든 파일과 서브 디렉터리를 현재 이미지 내 작업 디렉터리로 복사하라'는 의미이다.
- builder 단계의 마지막은 mvn package 명령을 실행하는 것이다. 이 명령은 애플리케이션을 빌드하고 패키징하라는 의미다. 입력은 자바 소스 코드이며, 출력은 JAR 포맷으로 패키징된 자바 애플리케이션이다. 
  
  
- 기반 이미지는 diamol/openjdk이다. 이 이미지는 자바 11런타임을 포함하지만, 메이븐은 포함하지 않는다.
- 이번에도 이미지에 작업 디렉터리를 만든 다음, 여기에 앞서 builder단계에서 만든 JAR파일을 복사한다. 이 JAR파일은 모든 의존 모듈과 컴파일된 애플리케이션을 포함하는 단일 파일이다. 그러므로 builder단계의 파일 시스템에서 이 파일만 가져오면 된다.
- 애플리케이션은 80번 포트를 주시하는 웹 서버 애플리케이션이다. 그러므로 이 포트를 EXPOSE 인스트럭션을 통해 외부로 공개해야 한다.
- ENTRYPOINT는 CMD인스트럭션과 같은 기능을 하는 인스트럭션이다. 해당 이미지로 컨테이너가 실행되면 도커가 이 인스트럭션에 정의된 명령을 실행한다. 이 이미지의 경우 java명령으로 빌드된 JAR 파일을 실행한다.

## Node.js 소스 코드



``` Dockerfile
FROM diamol/node AS builder

WORKDIR /src
COPY src/pacakge.json

RUN npm install

#app
FROM diamol/node

EXPOSE 80
CMD ["node", "server.js"]

WORKDIR /app
COPY --from=builder /src/node_modules/ /app/node_modules/
COPY src/ .

```

diamol/node를 기반 이미지로 사용한다. 이 이미지는 Node.js 런타임과 npm이 설치된 이미지이다. builder 단계에서 애플리케이션의 의존 모듈이 정의된 package.json파일을 복사한 다음, npm install명령을 실행해 의존 모듈을 받는다.



## 멀티 스테이지 Dockerfile 스크립트 이해하기

- 첫 번째 장점: 표준화
어떤 운영체제를 사용하든, 어떤 도구를 설치했는지와 상관없이 모든 빌드 과정이 도커 컨테이너 내부에서 이뤄진다.

- 두 번째 장점: 성능 향상
멀티 스테이지 빌드의 각 단계는 자신만의 캐시를 따로 갖는다. 그리고 도커는 빌드 중에 각 인스트럭션에 해당하는 레이어 캐시를 찾는다. 해당되는 캐시를 찾지 못하면 남은 인스트럭션이 모두 실행되지만, 그 범위가 해당 단계 안으로 국한된다. 이어지는 다음 단계는 다시 캐시를 재사용하면서 시작될 수 있다.

- 세 번째 장점: 이미지를 가능한 작게 유지 가능


