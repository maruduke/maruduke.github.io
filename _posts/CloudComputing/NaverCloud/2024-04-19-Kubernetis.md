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

## Private Subnet 생성

internet gateway = private 설정

## Load balancer를 위한 private subnet 생성

---

## 서버 설정

### 쿠버네티스 서비스 설치

서버에 네이버 클라우드에 쿠버네티스 서비스를 컨트롤하는 kubectl을 설치 필요
서버에 ssh 접속하여 명령어를 통해 설치

1. 업데이트 및 쿠버네티스 apt 레포지토리를 사용하기 위한 패키지 설치

```bash
$ sudo apt-get update
$ sudo apt-get install -y apt-transport-https ca-certificates curl
```

2. 쿠버네티스 패키지 레포지토리를 위한 공인 키 다운로드

우분투 버전이 22.04일 경우 /etc/apt/keyrings가 존재하지 않으므로 파일 생성 필요

```bash

# ubuntu 22.04 버전일 경우
$ mkdir /etc/apt/keyrings

$ curl -fsSL https://pkgs.k8s.io/core:/stable:/v1.30/deb/Release.key | sudo gpg --dearmor -o /etc/apt/keyrings/kubernetes-apt-keyring.gpg
$ sudo chmod 644 /etc/apt/keyrings/kubernetes-apt-keyring.gpg
```

3. 쿠버네티스 apt 레포지토리 추가.

```bash
$ echo 'deb [signed-by=/etc/apt/keyrings/kubernetes-apt-keyring.gpg] https://pkgs.k8s.io/core:/stable:/v1.30/deb/ /' | sudo tee /etc/apt/sources.list.d/kubernetes.list

$ sudo chmod 644 /etc/apt/sources.list.d/kubernetes.list
```

4. apt 패키지를 업데이트하고 kubectl 설치

```bash
$ sudo apt-get update

$ sudo apt-get install -y kubectl
```

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

Services - Storage - Object Storage

버킷 생성

## Container Registry 생성

## Dockerfile 생성

설정한 서버에 Docker file 생성

도커파일 이미지 빌드

```bash
$ docker build -t image_apache .

```
