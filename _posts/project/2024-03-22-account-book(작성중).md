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
mermaid: true

date: 2024-03-22
last_modified_at: 2024-03-22
---

<script src="https://cdn.jsdelivr.net/npm/mermaid/dist/mermaid.min.js"></script>

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

해당 프로젝트를 진행함으로써 가장 의미있는 경험을 꼽자면 다양한 툴들을 사용하여 협업 방법을 활용했다는 점이라고 생각한다.

### 1. 기능 작성표

프로젝트 초반에 어떤 기능을 구현할 지에 대해 엑셀을 활용하여 정의하는 과정을 수행하였다.

<p align = "center">
    <img src="/assets/images/project/TheMore/6-1.png" width="80%">
</p>

해당 과정을 통해 여러 이점을 얻을 수 있었는데 내용은 다음과 같았다.

1. 기능들을 미리 정의함으로써 팀원끼리 역할을 분류하는 과정이 수월했다.
2. 팀원들이 업무가 끊난 후 다음 할 업무를 찾지 못하여 발생하는 공백 시간을 최소화 할 수 있다.
3. 이미 자신의 업무를 끝낸 팀원이 개발한 기능과 연계되는 영역을 구현하는 팀원을 돕도록 하여 우선 순위가 잡혔다.
4. 우선순위를 확실하게 하여 부기능을 구현하느라 핵심 기능을 소홀히 하는 경우가 없어졌다.

그외에도 하술할 ERD 작성이나 Figma 작성에 필요한 기본적인 정보를 정의할 수 있는 등 전체적인 설계의 밑바탕이 되었다.

### 2. ERD 작성

앞에서 언급한 기능 작성표를 기반으로 필요한 데이터를 추출하여 DB 스키마 설계하였다.

<p align = "center">
    <img src="/assets/images/project/TheMore/6-2.png" width="80%">
</p>

미리 작성한 ERD표를 통하여 개발 도중에 필요한 데이터가 갑작스럽게 추가되는 케이스가 발생하지 않아 개발 중 난항을 겪지 않았지만
일부 기능이 `시간 관계 상 미구현으로 전환되면서 사용되지 않는 값`이 유지되는 경우가 발생하였다.  
데이터 공간만 차지하는 불필요한 영역이지만 로직을 삭제하는 작업이 필요하여 오류 발생 등 시간 문제 상으로 유지하였다.

### 3. Figma 작성

메인 페이지 화면 설계

<p align = "center">
    <img src="/assets/images/project/TheMore/6-3.png" width="80%">
</p>

페이지의 디자인을 정의함으로써 대략적인 웹페이지 이동 등 전체적인 구조에 대한 이해가 높아졌으며 각자의 특색이 아닌 색감과 같은 디자인이 통일됨

### 4. Github 설정

branch 설계(작성 중....)

<div class="mermaid">
%%{init: { 'logLevel': 'debug', 'theme': 'dark', 'orientation': 'vertical' } }%%
gitGraph
    commit id: "init"
    branch dev_front
    commit id: "init_front"
    checkout main
    branch dev_back
    commit id: "init_back"
    checkout dev_front
    branch fe/feat/func1
    checkout dev_front
    branch fe/feat/func2
    checkout fe/feat/func1
    commit
    checkout fe/feat/func2
    commit
    checkout dev_front
    merge fe/feat/func1
    merge fe/feat/func2
    checkout dev_back
    branch be/feat/func1
    commit
    checkout dev_back
    merge be/feat/func1
    checkout dev_back
    branch be/fix/fix1
    commit id: "fix error"
    checkout dev_back
    merge be/fix/fix1
    checkout main
    merge dev_front
    merge dev_back

</div>

-   github -> 협업 방식에 대한 이모저모, ...
    일어난 사고 -> git rollback문제, 실수로 브랜치 머지 안하고 날린거 등, git 최종 머지 때 발생한 문제(main 브랜치를 다른 브랜치로 만들어 발생한 오류 -> 서로 베이스가 달라서 그런지 충돌이 난 문제)

### 5. Slack

모든 업무에 관한 내용은 Slack을 사용하여 공유하였으며 허들 기능을 통한 화상 통신과 화면 공유, 스레드 기능을 활용하여 특정 업무에 대한 토론을 확인할 수 있었다.  
그리고 Github를 Slack과 연동하여 Pull Request가 올라온 기록을 실시간으로 확인하여 프로젝트 Merge 타이밍을 유동적으로 조정하였다.

<p align = "center">
    <img src="/assets/images/project/TheMore/6-4.png" width="80%">
</p>

### 6. Notion

<p align = "center">
    <img src="/assets/images/project/TheMore/6-5.png" width="80%">
</p>

칸반보드를 작성하여 상대방이 진행 중인 업무가 무엇인지 대략적으로 파악하고 앞으로 해야 할 일 등을 확인하고 진행하였다. 특히 연계되는 기능이 완성된 경우 빠르게 Pull Request를 진행하여 개발한 기능을 가져와 연계하였다.

---

## 프로젝트 후기

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

<script>
mermaid.initialize({startOnLoad:true});
window.mermaid.init(undefined, document.querySelectorAll('.mermaid'));
</script>
