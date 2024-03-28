---
title: '\[Project\] TheMore(가계부) 팀프로젝트 회고록(작성 중)'
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

| 인원 | 프로젝트 기간          | 개발 영역                                                |
| ---- | ---------------------- | -------------------------------------------------------- |
| 6명  | 2024.3.15 ~ 3.24 (9일) | 로그인 기능, 채팅 기능, 서버와 클라이언트 연결 전반 설계 |

### 프로젝트 Github 저장소

[The More(더 모아) 가계부](https://github.com/iiiiii-dle/TheMore)

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

---

<br>

## 프로젝트 회고

이번 프로젝트를 진행함으로써 가장 의미있는 경험이라하면 협업을 진행하면서 발생할 수 있는 문제를 해결하는 방법에 대한 방법론과 수행함으로써 얻은 경험이다.

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

ERD표를 통하여 개발 도중에 필요한 데이터가 갑작스럽게 추가되는 케이스가 발생하지 않아 개발 중 어려움은 없었지만
일부 기능이 시간 관계 상 미구현으로 전환되면서 `사용되지 않는 값`이 유지되는 경우가 발생하였다.  
불필요한 영역이지만 로직을 삭제하는 작업이 필요하여 오류 발생 가능성 등 시간 문제 상으로 유지하였다.

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

캠프과정에서 학습한 영역만 사용하다 보니 RestApi를 활용하지 않고 WebSocket만을 이용하여 통신을 전달하였다. 해당 문제로 인해 페이지를 이동할 때마다 웹소켓이 끊어지므로 페이지별로 웹소켓을 다시 연결할 필요가 생겼다.
또한 소켓에서 전송하는 JSON 데이터 내부에 일일이 CMD 키 값을 넣어 서버에서 실행시킬 기능을 정의할 필요가 있었다.

가계부나 로그인 기능은 웹소켓을 이용하여 실시간 통신을 요구하지 않으므로 서버측에 불필요한 리소스 사용이나 웹소켓을 사용하기 위해서 설계적으로 불필요하다 싶은 것을 구현하는데 시간을 사용한 점에 대해서는 아쉬움이 남는다. 그래도 프로젝트에 부적합한 기술을 사용했다는 점에서 아쉬움이 남을 뿐 실제 웹 소켓 통신을 활용하여 JSON 데이터를 주고 받는 과정 대한 이해와 원하는 기능을 수행하기 위한 분류 등을 경험하여 소켓 통신의 이해도가 상승했다는 점에서는 긍정적으로 생각한다.
<br>

### Session

로그인 후 인증을 위해서 브라우저의 Session Storage에 DB의 User정보를 저장하여 소켓 통신을 할 경우 서버에 전송하여 사용자를 인증을 하는데 사용하였다.
원래라면 서버 내부에서 세션 값을 생성하고 세션 값을 키값으로 User정보를 밸류 값으로 하는 저장소를 구현하여 설계해야 하지만 시간 문제 상으로 UserId 값을 그대로 클라이언트 쪽으로 보내는 방식을 사용했다. ~~해킹에 아주 취약한 방식~~ 더불어 User값이 삭제되지 않아 메모리를 점유하고 있는 문제가 존재했다.

다음 프로젝트에서는 Session값과 UserId값을 매칭하여 인메모리 캐시인 Redis에 저장하는 것을 목표로 하고자 한다. Redis를 활용하면 일정 시간이 지나면 정보를 삭제하는 기능으로 세션 만료 시간이 지나면 로그아웃이 되며, 인메모리이지만 스냅 샷을 통해 디스크에 데이터가 저장되어 서버 다운되더라도 인증 정보 유지 등 인증 구현에 있어서 다방면으로 활용할 수 있는 기술이라고 생각된다.

<br>

### Front와 BackEnd 교차

소켓 통신으로 JSON 데이터를 주고 받았을 경우 어떤 형식의 데이터를 주고 받을 지 키값과 밸류를 미리 정의하지 않아서 프론트와 백엔드 사이 데이터를 전달하는 영역에서 약간의 시행착오를 겪는 시간이 존재하였다. 특히 소켓 통신만을 이용하다 보니 각 데이터 패킷의 구분을 일일이 if문으로 정의하다 보니 모호해진 점도 있다.
프론트 엔드와 백엔드 사이의 어떤 데이터를 전송할지 데이터 흐름도를 작성할 필요성을 느꼈다.

<br>

### 보안

프로젝트를 개발하면서 시간에 쫓겨 보안과 관련된 사항을 배제한 상태로 구현하였다.
앞서 설명한 Session값을 그대로 UserId값을 가져오는 방법이나 수입, 지출 내역과 같은 사용자 정보에 대해서도 DB의 Id값으로 클라이언트에게 전송하여 값을 수정, 삭제하므로 보안에 있어서 클라이언트에게 너무 많은 정보를 공개했다는 생각이 든다.

해결 방법으로 생각한 것은 결국 복합키를 사용하여 서버에서 데이터를 탐색하는 방법이다. 각 컬럼의 정보를 조합하여 Unique키를 만들어 공개하기 어려운 Id값을 숨기는 방법에 대해 고려해보아야겠다. 다만 탐색 시간이 증가하는 문제에 대해서는 다시 한번 고려해볼 필요가 있다고 생각한다.

---

<script>
mermaid.initialize({startOnLoad:true});
window.mermaid.init(undefined, document.querySelectorAll('.mermaid'));
</script>
