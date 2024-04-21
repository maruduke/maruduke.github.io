---
title: '\[NCP\] 2. Container'
excerpt: '가상화 기술과 네이버 클라우드 플랫폼 Compute 서비스'

categories:
    - 'Naver Cloud Platform'
tags:
    - [NCP, Cloud]

permalink: /NaverCloud/6

toc: true
toc_sticky: true

date: 2024-04-06
last_modified_at: 2024-04-06
---

## Container Registry

-   컨테이너 이미지를 쉽게 저장하고 관리할 수 있는 컨테이너 레지스트리 관리 서비스
    -   Public/Private Endpoint 제공을 통해 나만의 컨테이너 레지스트리를 구성
    -   도커 레지스트리 HTTP API V2 지원
-   저장소를 Object Storage를 이용

    -   컨테이너 관리가 용이
    -   컨테이너 취약점 분석 가능

-   활용
    -   도커 파일을 이용해 이미지 생성
    -   NCP Container Registry에서 도커 이미지를 업로드

### 일반 VM과 컨테이너 차이

-   Virtualization은 단일 시스템에서 여러 OS가 동시에 실행
-   Container는 `동일한 OS` 커널을 공유, 시스템 나머지 부분을 `프로세스를 격리`
-   `하나의 OS 커널 위에` 각각의 개별 프로세스와 그에 따른 `환경을 격리화`시키는 방식. OS 가상화 보다 오버헤드가 적고, 성능 손실이 적음

## Docker Registry

-   사용자에 생성된 이미지는 NCP Container Registry라는 이미지 저장 공간(Docker Registry)에 업로드할 수 있고, 다른 사용자의 이미지를 공유하여 사용 가능하다.

-   Docker Hub, Private Registry(NCP Container Registry, ACR, ECR, ...)

## Naver Cloud Container Registry

-   Public, Private Endpoint 모두 제공하므로 컨테이너 업데이트 시 여러 오퍼레이션 사용 가능
-   컨테이너 이미지 관리: Object Storage 기반의 서비스이므로 컨테이너 조회 및 관리가 용이
-   협업 및 접근 제어: Sub Account를 사용하여 사용자와 역할별로 서로 다른 권한을 지정할 수 있어, 저장소를 다른 사용자와 공유 가능
-   `컨테이너 취약점 분석`: Container Registry에 등록된 컨테이너 이미지의 이상징후 및 CVE기반의 보안 취약점을 분석, 각 고유 CVE 보안 취약점을 심각도에 따라 구분, 스캔 결과를 기반으로 최약점을 제거함으로써 컨테이너 보안 강화
