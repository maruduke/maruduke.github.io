---
title: '\[Project\] TheMore(가계부) 팀프로젝트 회고록'
excerpt: 'Java WebSocket을 활용한 가계부 구현'

categories:
    - Project
tags:
    - [Java, WebSocket, Project]

permalink: /project/6

toc: true
toc_sticky: true

date: 2024-03-22
last_modified_at: 2024-03-22
---

## 가계부 구현 프로젝트

### 프로젝트 개요

BitCamp 클라우드 개발자 과정 8기 2번째 프로젝트 TheMore이다.
해당 프로젝트는 사용자에게 가계부 기능을 제공한다. 또한 채팅을 활용하여 서로의 소비 습관을 피드백 할 수 있도록 한다.
자세한 기능 설명은 하단의 Github 주소를 참조해주길 바란다.

프로젝트 인원: 6인  
프로젝트 기간: 2024.3.15 ~ 3.24 (9일)

#### 사용 스택

<p align="center" style="margin: 0">
<img src="https://img.shields.io/badge/HTML5-E34F26?style=for-the-badge&logo=HTML5&logoColor=white">
<img src="https://img.shields.io/badge/CSS3-1572B6?style=for-the-badge&logo=CSS3&logoColor=white">
<img src="https://img.shields.io/badge/Javascript-F7DF1E?style=for-the-badge&logo=Javascript&logoColor=white">
</p>

<p align ="center" style="margin: 0">
<img src="https://img.shields.io/badge/java-007396?style=for-the-badge&logo=openjdk&logoColor=white">
<img src="https://img.shields.io/badge/MySQL-4479A1?style=for-the-badge&logo=MySQL&logoColor=white">
</p>

<p align="center" style="margin: 0">
<img src="https://img.shields.io/badge/Figma-F24E1E?style=for-the-badge&logo=Figma&logoColor=white">
<img src="https://img.shields.io/badge/Slack-4A154B?style=for-the-badge&logo=Slack&logoColor=white">
<img src="https://img.shields.io/badge/Notion-000000?style=for-the-badge&logo=Notion&logoColor=white">
<img src="https://img.shields.io/badge/Github-181717?style=for-the-badge&logo=Github&logoColor=white">
</p>

### 프로젝트 Github 저장소

[The More(더 모아) 가계부](https://github.com/iiiiii-dle/TheMore)

---

## 프로젝트 회고

해당 프로젝트를 진행함으로써 다양한 협업 방법론에 대해 다양한 경험을 진행하게 됨

### 1. 기능 작성표

프로젝트 초반에 어떤 기능을 구현할 지에 대해 엑셀을 활용하여 정의하는 과정을 수행하였다.

<p align = "center">
    <img src="/assets/images/project/TheMore/6-1.png" width="80%">
</p>

미리 정의함으로써 팀원끼리 역할 나눔 수행, 프로젝트 기간 시간을 최대한 맞추기 위해 빠르게 기능 잘라내기, 아무튼 등

### 2. ERD표 작성

기능 작성표를 기반으로 DB 스키마 설계

<p align = "center">
    <img src="/assets/images/project/TheMore/6-2.png" width="80%">
</p>

일부 기능 미구현으로 추가되거나, 사용하지 않는 값들이 생성되는 문제 발생

### 3. Figma 작성

메인 페이지 화면 설계

<p align = "center">
    <img src="/assets/images/project/TheMore/6-3.png" width="80%">
</p>

### 4. Github 설정

branch 설계

-   github -> 협업 방식에 대한 이모저모, ...
    일어난 사고 -> git rollback문제, 실수로 브랜치 머지 안하고 날린거 등, git 최종 머지 때 발생한 문제(main 브랜치를 다른 브랜치로 만들어 발생한 오류 -> 서로 베이스가 달라서 그런지 충돌이 남)

### 5. Slack

Slack 사용, github 연결, 허들을 사용하여 원격 공유, 스레드 기능 등

<p align = "center">
    <img src="/assets/images/project/TheMore/6-4.png" width="80%">
</p>

### 6. Notion

<p align = "center">
    <img src="/assets/images/project/TheMore/6-5.png" width="80%">
</p>

칸반보드, 회의록 작성, ...

---

## 후일담?

### WebSocket 제한

-   session에 대한 생각
-   Redis에 대한 in-memory에 대한 생각
-   Controller에 대한 이모조모

### Front와 BackEnd 교차

-   정보 전달의 어려움
-   데이터 흐름 표 작성 필요

### 보안

-   session키와 마찬가지로 각 지출 내역이나, 수입 내역을 Id값으로 삭제하는데 보안 문제가 발생하지 않을까? 해결법으로 복합키로 어떻게든 구현?
    -   시간 정보로 구별?
