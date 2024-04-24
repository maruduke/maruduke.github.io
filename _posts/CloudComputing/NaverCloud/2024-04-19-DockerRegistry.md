---
title: '\[NCP 실습\] Docker Registry'
excerpt: ''

categories:
    - 'Naver Cloud Platform'
tags:
    - [NCP, Cloud]

permalink: /NaverCloud/ex/11

toc: true
toc_sticky: true

date: 2024-04-19
last_modified_at: 2024-04-19
---

## Subnet 설정

### Private Subnet 생성

<p align = "center">
    <img src="/assets/images/NCP/ex11-1.png" width="90%">
</p>

internet gateway 전용 여부를 private 설정하여 다른 사람이 Registry에 접근하지 못하도록 설정

---

## 테스트 서버 환경 설정

도커 이미지를 생성하고 docker registry에 해당 이미지를 업로드하는 것을 테스트하기 위해 docker를 설치하는 방법에 대한 설명이다.

### Docker 설치

1. 필요한 패키지 설치

```bash
$ sudo apt-get install apt-transport-https ca-certificates curl gnupg-agent software-properties-common
```

2. 도커의 공식 GPG키 추가

```bash
$ curl -fsSL https://download.docker.com/linux/ubuntu/gpg | sudo apt-key add -
```

3. 도커의 공식 apt 저장소를 추가 및 저장소 업데이트

```bash
$ sudo add-apt-repository "deb [arch=amd64] https://download.docker.com/linux/ubuntu $(lsb_release -cs) stable"

$ sudo apt-get update

```

4. 도커 설치

```bash
$ sudo apt-get install docker-ce docker-ce-cli containerd.io
```

---

## Object Storage 생성

업로드한 도커 이미지를 저장할 수 있는 공간인 Object Storage의 Bucket을 생성한다.

Services - Storage - Object Storage

<p align = "center">
    <img src="/assets/images/NCP/ex11-2.png" width="90%">
</p>

<p align = "center">
    <img src="/assets/images/NCP/ex11-3.png" width="90%">
</p>

<p align = "center">
    <img src="/assets/images/NCP/ex11-4.png" width="90%">
</p>

<p align = "center">
    <img src="/assets/images/NCP/ex11-5.png" width="90%">
</p>

## Container Registry 생성

Services - Container - Container Registry

<p align = "center">
    <img src="/assets/images/NCP/ex11-6.png" width="90%">
</p>

레지스트리 생성 클릭

<p align = "center">
    <img src="/assets/images/NCP/ex11-7.png" width="90%">
</p>
레지스트리 이름과 생성한 버킷을 선택하여 레지스트리 생성

## 테스트용 Docker 이미지 생성

### 설정한 서버에 Dockerfile 생성

```bash
$ mkdir docker
$ cd docker
$ vi Dockerfile
```

<p align = "center">
    <img src="/assets/images/NCP/ex11-8.png" width="90%">
</p>

### Dockerfile 작성

이미지를 만들기 위해서 Dockerfile 작성

<p align = "center">
    <img src="/assets/images/NCP/ex11-9.png" width="90%">
</p>

### Dockerfile 이미지 빌드

```bash
# docker build -t [이미지 이름] [경로]
$ docker build -t image_apache .
```

<p align = "center">
    <img src="/assets/images/NCP/ex11-10.png" width="90%">
</p>

빌드된 이미지 확인

<p align = "center">
    <img src="/assets/images/NCP/ex11-11.png" width="90%">
</p>

이미지 실행시켜 제대로 동작하나 확인하기

```bash
$ docker run -tid -p 4000:80 --name=test image_apache
```

<p align = "center">
    <img src="/assets/images/NCP/ex11-12.png" width="90%">
</p>

정상적으로 도커 이미지가 실행에 성공했다면 브라우저에 host:port를 입력하였을 경우 다음과 같은 화면이 출력된다.

<p align = "center">
    <img src="/assets/images/NCP/ex11-13.png" width="90%">
</p>

## 생성한 도커 이미지를 레지스트리에 업로드

도커 레지스트리에 접근하기 위해 Private Endpoint 값을 복사하여 login한다.

<p align = "center">
    <img src="/assets/images/NCP/ex11-14.png" width="90%">
</p>

```bash
$ docker login [Private EndPoint]
```

<p align = "center">
    <img src="/assets/images/NCP/ex11-15.png" width="90%">
</p>

도커 이미지를 레지스트리에 업로드할 수 있도록 태그를 붙여준다.

```bash
$ docker image tag \[이미지 이름\] \[\[Private EndPoint\]/\[이미지_이름:태그\]\]
```

<p align = "center">
    <img src="/assets/images/NCP/ex11-16.png" width="90%">
</p>

도커 이미지 업로드

```bash
$ docker push [생성된 태그 이미지 이름]
```

버킷이나 Container Repository를 확인하면 다음과 같이 저장되어 있음을 확인

<p align = "center">
    <img src="/assets/images/NCP/ex11-17.png" width="90%">
</p>
