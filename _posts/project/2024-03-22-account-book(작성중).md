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

| 인원 | 프로젝트 기간          |
| ---- | ---------------------- |
| 6명  | 2024.3.15 ~ 3.24 (9일) |

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

<br>

### 프로젝트 Github 저장소

[The More(더 모아) 가계부](https://github.com/iiiiii-dle/TheMore)

---

<br>

## 프로젝트 회고

이번 프로젝트를 진행함으로써 가장 의미있는 경험이라하면 협업을 진행하면서 발생하는 문제를 해결하는 방법에 대한 해결 경험이다.

<br>

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

<br>

### 2. ERD 작성

앞에서 언급한 기능 작성표를 기반으로 필요한 데이터를 추출하여 DB 스키마 설계하였다.

<p align = "center">
    <img src="/assets/images/project/TheMore/6-2.png" width="80%">
</p>

미리 작성한 ERD표를 통하여 개발 도중에 필요한 데이터가 갑작스럽게 추가되는 케이스가 발생하지 않아 개발 중 난항을 겪지 않았지만
일부 기능이 `시간 관계 상 미구현으로 전환되면서 사용되지 않는 값`이 유지되는 경우가 발생하였다.  
데이터 공간만 차지하는 불필요한 영역이지만 로직을 삭제하는 작업이 필요하여 오류 발생 등 시간 문제 상으로 유지하였다.

<br>

### 3. Figma 작성

메인 페이지 화면 설계

<p align = "center">
    <img src="/assets/images/project/TheMore/6-3.png" width="80%">
</p>

페이지의 디자인을 정의함으로써 대략적인 웹페이지 이동 등 전체적인 구조에 대한 이해가 높아졌으며 각자의 특색이 아닌 색감과 같은 디자인이 통일됨

<br>
<br>

### 4. Github 설정

깃허브 기능은 유용하지만 개인이 사용했을 때도 여러번 날려먹은 경험이 있는지라 어떻게 처리를 해야할지 고민이 많았다.  
<br>
다행히 같은 팀원 중 팀 프로젝트 경험을 가지신 분이 길을 제시해주셔서 다음과 같은 방법을 적용했다.

<div class="mermaid" style="overflow:scroll;">
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
    commit id: "프론트 기능 개발"
    checkout fe/feat/func2
    commit id: "프론트 기능 개발2"
    checkout dev_front
    merge fe/feat/func1
    merge fe/feat/func2
    checkout dev_back
    branch be/feat/func1
    commit id: "백엔드 기능 개발"
    checkout dev_back
    merge be/feat/func1
    checkout dev_back
    branch be/fix/fix1
    commit id: "기능 버그 픽스"
    checkout dev_back
    merge be/fix/fix1
    checkout main
    merge dev_front
    merge dev_back

</div>

#### Branch 생성

우선 main 브랜치에서 영역에 따라 2개의 브랜치를 분리하였다.

-   main branch: 프로젝트 종료 시 백엔드, 프론트엔드 Merge

    -   dev-front : 프론트엔드 개발 코드 작성
    -   dev-back : 백엔드 개발 코드 작성

추가적으로 팀원이 추가적인 기능을 개발하거나 버그를 수정하였을 경우 새로운 브랜치를 특정 명명 규칙을 통해 생성하였다.

-   fe : 프론트 branch
-   be : 백엔드 branch

    -   feat: 신규 기능 개발
    -   fix: 버그 픽스
        -   기능 설명

ex) 프론트 엔드 신규 기능 개발

```git
$ git branch fe/feat/{신규 기능 이름} dev-front

$ git checkout fe/feat/{신규 기능 이름}
```

ex2) 백엔드 버그 픽스

```git
$ git branch be/fix/{신규 기능 이름} dev-back

$ git checkout be/fix/{신규 기능 이름}
```

#### Pull Request

여러 생성된 브랜치들을 다시 dev_front나 dev_back에 통합하기 위해서는 Pull Request를 요청하도록 했다.

<p align = "center">
    <img src="/assets/images/project/TheMore/6-6.png" width="80%">
</p>

상단의 이미지와 같이 여러 Pull Request를 한번에 모아서 Merge를 실행하여 처리 한 후 각자의 Local Branch에 Pull을 받아서 Conflict 발생을 최소화 시켰다. 작업 영역이 겹치지 않는 이상 왠만해서는 Conflict가 발생하지 않았다.

#### 사용 중 발생한 몇가지 문제

미리 정해둔 약속으로 인해 일어날 불상사를 최소화하긴 했지만 그래도 프로젝트를 진행하는 도중에 2~3가지의 문제가 발생하였다.

-   branch를 Pull Request하지 않고 삭제하는 경우
    -   ~~이건 내가 너무 멍청했다.~~
-   branch 영역이 Conflict 발생 없이 일부 덮어 씌어 지는 경우
    -   왜 Conflict가 발생하지 않는지는 모르겠지만 작성한 파일의 일부 데이터가 날아가는 문제가 발생하였다.
    -   Merge 히스토리를 살펴 비교하면서 삭제된 데이터를 복구하였다.
-   Main branch 통합 에러
    -   초기 프로젝트 진행 시 main 브랜치를 다른 곳으로 옮기는 과정에서 dev-front와 dev-back사이의 base가 달라서 문제가 발생한 것 같다.
    -   Conflict를 일일이 해결하였으나 해결 후에도 유실된 코드가 발생하였다
    -   rebase를 진행하기에는 마찬가지로 유실의 우려가 존재하였다.
    -   임시방편으로 모든 파일을 삭제하고 복사/붙여넣기 하는 형식으로 통합 처리하였음

<br>

### 5. Slack

모든 업무에 관한 내용은 Slack을 사용하여 공유하였으며 허들 기능을 통한 화상 통신과 화면 공유, 스레드 기능을 활용하여 특정 업무에 대한 토론을 확인할 수 있었다.  
그리고 Github를 Slack과 연동하여 Pull Request가 올라온 기록을 실시간으로 확인하여 프로젝트 Merge 타이밍을 유동적으로 조정하였다.

<p align = "center">
    <img src="/assets/images/project/TheMore/6-4.png" width="80%">
</p>

<br>

### 6. Notion

칸반보드를 작성하여 팀원이 진행 과정을 한눈에 파악할 수 있었다. 이를 통해 업무가 어떻게 진행되는지 완성도가 어느정도되는지 대략적인 일정을 가늠할 수 있었다.
특히 연계되는 기능이 완성된 경우 빠르게 Merge를 진행하여 개발한 기능을 가져와 연계하였다.

<p align = "center">
    <img src="/assets/images/project/TheMore/6-5.png" width="80%">
</p>

---

<br>

## 프로젝트 후기

<br>

### WebSocket 제한

<br>
<br>

-   session에 대한 생각
-   Redis에 대한 in-memory에 대한 생각
-   Controller에 대한 이모조모

### Front와 BackEnd 교차

<br>
<br>

-   정보 전달의 어려움
-   데이터 흐름 표 작성 필요

### 보안

-   session키와 마찬가지로 각 지출 내역이나, 수입 내역을 Id값으로 삭제하는데 보안 문제가 발생하지 않을까? 해결법으로 복합키로 어떻게든 구현?
    -   시간 정보로 구별?

<script>
mermaid.initialize({startOnLoad:true});
window.mermaid.init(undefined, document.querySelectorAll('.mermaid'));
</script>
