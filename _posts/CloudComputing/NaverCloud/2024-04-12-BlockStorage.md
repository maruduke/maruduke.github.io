---
title: '\[NCP 실습\] Block Storage 추가'
excerpt: 'Server 생성'

categories:
    - 'Naver Cloud Platform'
tags:
    - [NCP, Cloud]

permalink: /NaverCloud/ex/4

toc: true
toc_sticky: true

date: 2024-04-10
last_modified_at: 2024-04-10
---

## Block Storage

1. 서버에 추가 스토리지 생성
    - SSD 1개(20GB), HDD 2개(10GB)
2. SSD 스토리지에 파티션 및 파일 시스템 생성
    - SSD를 10GB씩 2개 파티션 분할
    - 각 파티션에 ext4파일 시스템 생성
    - 각 파일 시스템을 디렉토리에 마운트
3. HDD 스토리지에 Logical Volumn 구성(LVM 이용)
    - Linux LVM 파티션 생성
    - PV, VG, LV 생성
    - 파일 시스템 생성 및 마운트
4. 스토리지 용량 증설
    - SSD 용량 30GB로 증설
    - 파티션 및 파일 시스템에 용량 적용
5. 스냅샷 생성
    - 테스트 스토리지 생성
    - 스냅샷 생성
    - 기존 스토리지와 스냅샷 스토리지 비교

## Block Storage 생성

Server -> Storage 선택 -> 스토리지 생성 선택

### SSD 생성

-   하이퍼바이저: Xen 선택 -> 현재 서버가 Xen 기반
-   스토리지 이름
-   스토리지 종류: SSD, HDD
-   적용 서버 선택: 스토리지를 넣을 서버 선택
-   크기: 저장 용량 크기 정의

값을 다 넣었으면 추가 버튼 클릭

> 비슷한 방법으로 하드디스크 Storage 10GB 2개를 생성해보아라

### 생성 Storage 확인

-   SSD는 용량 크기에 따른 IOPS 보장

    -   0~100GB: 4000 IOPS
    -   100GB 초과 용량: 40IOPS/GB(최대 20000 IOPS)
    -   ex) 120GB의 IOPS = 4000+800 = 4800IOPS

-   각 디스크의 연결 정보 표시: [서버명]:[블록 장치 파일 경로]

---

## 디스크와 파티션 정보 확인

-   서버 항목에서 storage를 넣은 서버를 확인한다. 서버를 클릭하여 나타나는 정보창에 스토리지 정보를 확인하면 SSD 등의 장치 파일 경로를 확인할 수 있다.
    SSD 경로: /dev/xvdb
    HDD 경로: /dev/xvdc, /dev/xvdd

추가한 스토리지의 경우에는 추가적으로 각각의 디스크에 파티션 생성, 파일 시스템 생성, 파일 시스템을 기존의 디렉토리 구조에 마운트를 실행해주어야 사용 할 수 있다.

우선 클라우드 서버에 ssh 접속 과정 수행.

```bash
## 현재 시스템에 연결된 디스크와 파티션 정보 출력
$ fdisk -l
```

이미지를 확인하면 /dev/xvdb에 대한 디스크 정보를 확인해보면 아직 파티션은 구성되지 않음을 확인할 수 있음

## 디스크의 파티션 생성

### 추가 SSD에 파티션 생성

-   첫번째 파티션 생성

```bash
$ fdisk /dev/xvdb # /dev/xvdb에 대한 파티션 테이블 생성
$ n # 신규 파티션 생성
$ p # 프라이머리 파티션 생성
$ 1 # 파티션 번호 지정
$ 2048 # 첫번째 파티션의 시작 섹터 번호 입력
$ +10G # 파티션에 할당할 용량 크기 입력
```

-   두번째 파티션 생성

이미지 대체

-   파티션 조회

이미지 대체

-   저장

command w을 입력하여 fdisk를 종료하면서
설정한 파티션을 저장한다.

---

## 파일 시스템 생성과 마운트

### 추가 SSD에 파일 시스템 생성

```bash
$ lsblk # 파티션 확인
```

```bash
$ mkfs -t ext4 /dev/xvdb1 # xvdb1에 ext4 파일 시스템 생성
$ mkfs.ext4 /dev/xvdb2 # xvdb2에 ext4 파일 시스템 생성
```

### 파일시스템 생성 확인

```bash
$ lsblk -f
```

### 파일 시스템을 디렉토리에 마운트

```bash

# 마운트 포인트 디렉토리 생성
$ mkdir -p /mnt/part1
$ mkdir -p /mnt/part2

# 파일 시스템 마운트 명령
$ mount -t ext4 /dev/xvdb1 /mnt/part1
$ mount -t ext4 /dev/xvdb2 /mnt/part2

# 사용 가능한 블록 장치 리스트 출력
$ lsblk


```

이미지
마운트 포인트를 통한 파일 시스템 사용 가능
