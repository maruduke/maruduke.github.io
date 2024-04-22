---
title: '\[NCP 실습\] Docker Registry'
excerpt: ''

categories:
    - 'Naver Cloud Platform'
tags:
    - [NCP, Cloud]

permalink: /NaverCloud/ex/12

toc: true
toc_sticky: true

date: 2024-04-19
last_modified_at: 2024-04-19
---

## DB 생성

Services - Database - Cloud DB for Redis

### Config Group 생성

-   Service - Database - Cloud DB for Redis - Config Group

version 설정 잘해야 한다.

### subnet 생성

redis는 private subnet을 생성해야 가능

### redis 생성

추가적으로 backup 설정을 추가

### ACG 접근 제어 풀기

Services - Server - ACG

자동 생성된 ACG 설정

Inbound 규칙만 설정

### 서버 접속 정보 확인

### 연결 확인
