---
title: '\[Project\] 가계부 구현 회고록'
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

### 프로젝트 Github 저장소

[The More(더 모아) 가계부](https://github.com/iiiiii-dle/TheMore)

### 프로젝트 개요

BitCamp 클라우드 개발자 과정 8기 2번째 프로젝트이다.  
자세한 기능 설명은 깃 주소를 참조하길 바란다.

### 프로젝트 기간 , 협업 인원, ... 기타 정보

프로젝트 기간 3-15 ~ 3-24

## 프로젝트 회고록

### 첫 팀 프로젝트로서 협업 방식

-   기능 작성표
-   DB 선 작성
-   github -> 협업 방식에 대한 이모저모, ...
    일어난 사고 -> git rollback문제, 실수로 브랜치 머지 안하고 날린거 등

-   slack
-   notion > 칸반보드

## 이런 저런 생각

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
