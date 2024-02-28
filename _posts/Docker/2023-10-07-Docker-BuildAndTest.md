---
title: 7. 도커 컴포즈를 이용한 애플리케이션 빌드 및 테스트
author: maruduke
date: 2023-10-07 14:10:00 +0800
categories: [Docker]
tags: [docker]
---

## 도커를 이용한 지속적 통합 절차

도커는 CI절차의 일관성을 유지해 준다. 모든 도커 프로젝트는 똑같은 단계를 거쳐 똑같은 유형의 결과물을 생성하기 때문이다. 도커 프로젝트는 코드 변경이나 정해진 일정에 따라 CI절차가 시작되면 도커 이미지를 빌드한다. 이 이미지는 빌드 및 테스트, 패키징, 배포를 위해 레지스트리에 푸시까지 마친 최신 코드를 반영한 이미지다.

CI 절차 실행(최신 코드 받아옴) -> 빌드 단계(도커 이미지 빌드) -> 테스트 단계(컨테이너 실행) -> 배포 단계(레지스트리에 이미지 푸시)

## 도커를 이용한 빌드 인프라스트럭처 구축

필요한 세가지 컴포넌트가 존재하는데 형상 괸리 기능을 제공하는 Gogs, 이미지 배포를 맡을 오픈 소스 도커 레지스트리, 자동화 서버로는 젠킨스가 있으며, 이들 모두 명령 한 번이면 설치할 수 있다.

Gogs의 Git서버가 포트 3000번에서, 젠킨스가 포트 8080번에서, 레지스트리가 포트 5000번에서 실행되도록 한다.

도커 컴포즈 파일 중 젠킨스와 관련된 부분 발췌

```yml
# docker-compose.yml
services:
  jenkins:
    image: diamol/jenkins
    ports:
      - "8080:8080"
    networks:
      - infrastructure

# docker-compose-linux.yml
jenkins:
  volumes:
    - type: bind
      source: /var/run/docker.sock
      target: /var/run/docker.sock
```
