---
title: 'React-Router'
excerpt: ''

categories:
    - 리액트
tags:
    - [리액트, FrontEnd, JS]

permalink: /React/1

toc: true
toc_sticky: true

date: 2024-02-03
last_modified_at: 2024-02-03
---

## 리액트 프로젝트 생성 및 설정

### 리액트 프로젝트 생성

```console
$ npx create-react-app {프로젝트 네임}
```

### React-Router 모듈 설치

React-Router로 기존의 웹페이지 처럼 브라우저 주소창에 따라서 다양한 컴포넌트를 보여주도록 구성한다.

-> 그냥 a태그를 사용하면 페이지 전체가 새로 로딩되므로 라우팅을 통해 부드러운 화면전환을 목적으로 한다.
리액트는 SPA 방식을 사용한다. 자세한건 검색으로 알아보자

앞의 방식으로 프로젝트 생성 시 자동으로 삽입되어 있으나 따로 설치하는 경우는 다음과 같은 명령어를 입력한다.

```
$ npm i react-router-dom
```

## React Router 설정

src/router/root.js 파일 생성

```js
import { createBrowserRouter } from 'react-router-dom';

const root = createBrowserRouter([]);

export default root;
```

-   createBrowserRouter: 어떤 경로에는 어떤 컴포넌트를 보여줄 것인지 결정

root.js 실행을 위해 App.js 파일에 넣어줌

```js
import './App.css';
import { root } from './router/root';

import { RouterProvider } from 'react-router-dom';

function App() {
    return <RouterProvider router={root} />;
}

export default App;
```

프로젝트를 실행하면 root.js에 있는 설정을 활용해서 주소창의 경로를 기준으로 컴포넌트들을 보여주게 됨.

## Java

## Python
