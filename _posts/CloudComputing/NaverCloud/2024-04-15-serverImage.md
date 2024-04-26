---
title: '\[NCP 실습\] Server image'
excerpt: 'Server image 생성과 유사 Server 생성'

categories:
    - 'Naver Cloud Platform'
tags:
    - [NCP, Cloud]

permalink: /NaverCloud/ex/8

toc: true
toc_sticky: true

date: 2024-04-15
last_modified_at: 2024-04-15
---

## 목차

Server + Block Storage를 이미지화

1. 서버 이미지 생성
    1. 서버 이미지 생성
    2. 서버 이미지를 통한 복제 서버 생성
    3. 원본 서버와 복제 서버 비교
2. 유사 서버 생성
    1. 유사 서버 생성
    2. 원본 서버와 유사 서버 비교

## Server Image 생성

-   Service - Server

server1 선택 -> 서버 관리 및 설정 변경 -> 내 서버 이미지 생성

<p align = "center">
    <img src="/assets/images/NCP/ex8-1.png" width="90%">
</p>

생성된 서버 이미지는 원본 서버에서 적용된 스토리지까지 모두 포함

<p align = "center">
    <img src="/assets/images/NCP/ex8-2.png" width="90%">
</p>

### Server Image를 통한 복제 Server 생성

-   Service - Server Image

server1-img 선택 -> 서버 생성

<p align = "center">
    <img src="/assets/images/NCP/ex8-3.png" width="90%">
</p>

최초로 이미지를 활용하여 서버를 생성할 경우 서버 생성 콘솔 선택 창이 나타난다. 이번 실습에서는 기존 콘솔 화면 선택한다.

<p align = "center">
    <img src="/assets/images/NCP/ex8-4.png" width="90%">
</p>

콘솔 화면을 선택하면 서버 생성 콘솔이 나타남. 필요한 변수 값 입력

<p align = "center">
    <img src="/assets/images/NCP/ex8-5.png" width="90%">
</p>

-   VPC
-   Subnet
-   스토리지 종류
-   서버 세대
-   서버 타입
-   요금제 선택
-   서버 개수
-   서버 이름
-   Network Interface

### 원본서버와 복제 서버 비교

원본 서버

<p align = "center">
    <img src="/assets/images/NCP/ex8-6.png" width="90%">
</p>

복제 서버

<p align = "center">
    <img src="/assets/images/NCP/ex8-7.png" width="90%">
</p>

-   비교

    -   원본 서버와 생성 서버의 OS 및 스토리지 개수/ 크기 동일

    -   복제 서버의 공인 IP를 통해 아파치 웹 서버의 디폴트 페이지 확인

    -   원본 서버에서 설치된 소프트웨어가 그대로 적용

    -   복제 서버에 접속 -> 원본 서버의 변경된 비밀번호는 복제 서버에 적용되지 않음: 인증키를 통해 확인한 관리자 비밀번호 사용

    -   원본서버와 복제 서버의 파티션 구조, 파일 시스템, UUID가 같음 소프트웨어적으로 동일

## 유사 Server 생성

서버 선택 -> 서버 관리 및 설정 변경 -> 유사 서버 생성

<p align = "center">
    <img src="/assets/images/NCP/ex8-8.png" width="90%">
</p>

유사 서버를 생성할 경우 원본 서버의 이름을 제외한 거의 모든 영역에서 동일한 파라미터를 가지며 수정할 수 없다.

<p align = "center">
    <img src="/assets/images/NCP/ex8-9.png" width="90%">
</p>

### 원본 서버와 유사 서버 비교

<p align = "center">
    <img src="/assets/images/NCP/ex8-10.png" width="90%">
</p>

동일한 스펙의 서버 생성
서버 기본 스토리지만 적용
공인 IP 생성되지 않음

유사 서버에 공인 IP를 적용 후 웹 브라우저에서 접속
유사 서버에서는 기본 OS 이미지만 적용(아파치 웹서버는 적용되지 않음 -> Init Script 적용 안됨)
