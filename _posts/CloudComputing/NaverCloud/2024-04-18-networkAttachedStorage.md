---
title: '\[NCP 실습\] NAS(Network Attached Storage)'
excerpt: ''

categories:
    - 'Naver Cloud Platform'
tags:
    - [NCP, Cloud]

permalink: /NaverCloud/ex/9

toc: true
toc_sticky: true

date: 2024-04-16
last_modified_at: 2024-04-16
---

## NAS

네트워크를 통해서 연결되는 저장장치(여러 사용자가 접근 가능)

물리적인 형태의 NAS(내부에 디스크 탑재)

### 구조

이미지

파일 송수신 및 저장에 특화된 컴퓨터(서버)

### NAS 연결

내부 네트워크 / 인터넷을 통한 NAS 공유 가능

### NAS 제공 프로토콜

NFS(Network File System): 리눅스 클라이언트를 위한 시스템
CIFS(Common Internet File System): 윈도우 클라이언트를 위한 시스템

이름은 파일 시스템이지만 실제로는 네트워크 프로토콜 형태이다.
각각의 프로토콜을 제공하는 소프트웨어가 NAS에 탑재(nfs-kernel-server, samba 등)

### 보통 NAS 사용 방법

웹 어플리케이션을 통한 파일 업다운
OS의 네트워크 드라이브 연결 => Attached이라는 의미와 연결

### NCP의 NAS

-   NCP 내의 여러 개 서버와 공유 가능
    -   리전에 대한 의존성 - 같은 리전에서 생성된 서버에만 연결 가능
-   볼륨당 500GB~ 10TB 생성 가능
    -   온라인 상태에서 볼륨 축소/확장 가능
    -   용량이 커질수록 고성능 제공
-   스냅샷 기능 제공
    -   NAS 볼륨 공간 내에서 일정 부분 할당

## NAS 실습

1. NCP에서의 NAS 볼륨 생성 및 연결
    1. NFS 볼륨 생성
2. 리눅스 서버에서 NAS 볼륨 연결
3. NAS 볼륨 크기 조정
4. Snapshot 생성

### NAS 생성

service -> storage -> NAS 선택

설정 값 입력

NAS의 접근 제어 설정 ACL 설정 -> NFS 볼륨에 대한 접근 제어 설정(Read/Write 가능)

### NAS 볼륨 연결

리눅스 서버는 NAS를 NFS로 마운트
생성된 NAS는 NFS로 설정

서버에 ssh 연결 후 볼륨 마운트 시도

우분투에서 NFS 볼륨 연결을 위한 패키지 설정

```bash
$ apt-get install -y nfs-common
```

설치가 완료되었으면 마운트 실행

```bash

# 마운트 포인트 생성
$ mkdir -p /mnt/nfs

# mount.nfs [NFS 볼륨 마운트 정보] /mnt/nfs -> 생성된 NFS 볼륨을 마운트
$ mount.nfs 169.254.84.53:/n3051960_nfsdisk /mnt/nfs
```

### NAS 볼륨 크기 변경

NAS 볼륨 선택 -> 볼륨 설정 -> 용량 변경

NAS의 경우 볼륨 크기 축소도 가능하다. 600G -> 500G

서버에 연결된 상태에서도 라이브로 볼륨 크기 변경 가능

## 스냅샷 설정

NAS 볼륨 선택 -> 볼륨 설정 -> 스냅샷 설정

스냅샷의 경우 NAS 용량 일부를 스냅샷을 저장하는데 사용한다. 얼마만큼의 비율을 저장할지 설정

### 스냅 샷 생성 준비 단계

간단한 text 파일 2개 작성

이미지 파일

### NAS 스냅샷 생성하기

Services -> NAS -> Snapshot 선택

스냅 샷 생성 후 목록 확인

앞선 준비 단계가 적용된 스냅샷 생성

### 스냅 샷을 이용한 복구

복구를 위해 변화를 주는 준비 단계 작업

-> 준비 단계에서 생성한 텍스트 파일 모두 삭제(NAS 볼륨의 내용을 변경)

앞서 생성한 스냅샷에서 복구 클릭

삭제된 파일이 스냅샷을 통해 복구됨을 확인

### 스냅샷 생성2

/mnt/nfs에 파일을 추가로 생성하고 변경

NAS 볼륨을 선택하고 스냅샷 즉시 생성 버튼 클릭

텍스트 파일 모두 삭제

새로 추가된 스냅샷에서 복구 클릭

스냅샷을 통한 복구 확인

복구 확인

### 확인 과제

가장 최근의 스냅샷이 아닌 이전의 것으로 복구한다면 어떻게 될까? NAS 볼륨, 스냅샷 목록 상의 변화

하나가 됨 -> 아마도 복구를 하면서 snapshot 내용도 복구됨과 동시에 기존의 가졌던 snapshot을 없었으므로 사라지는 현상이 발생
