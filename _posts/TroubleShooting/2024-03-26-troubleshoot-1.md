---
title: 'java.sql.SQLIntegrityConstraintViolationException: Duplicate entry  ~ for key ~'
excerpt: 'UNIQUE 설정 오류'

categories:
    - troubleShoot
tags:
    - [JAVA, SQL, TroubleShoot]

permalink: /Java/troubleShoot/1

toc: true
toc_sticky: true

date: 2024-03-26
last_modified_at: 2024-03-26
---

## 문제 원인

자바를 이용하여 MySQL에 데이터를 삽입을 실행하는 과정에서 발생한 에러이다.

`에러 메시지`

<p align = "center">
    <img src="/assets/images/troubleShoot/1-1.png" width="90%">
</p>

> ### 문제 원인
>
> UNIQUE 속성을 가진 컬럼에 같은 값을 삽입함으로써 발생한 문제

salt 컬럼에 UNIQUE 속성을 주었음에도 같은 값을 입력해주면서 발생한 문제

<p align = "center">
    <img src="/assets/images/troubleShoot/1-2.png" width="90%">
</p>

## 해결 방법

해당 문제는 UNIQUE 값을 가져야하는 상태에서는 당연히 발생해야 하는 버그이다. 다만 이번의 경우는 팀 프로젝트 진행 중 발생한 실수이므로 UNIQUE 속성을 지워줌으로써 해결하였다.
