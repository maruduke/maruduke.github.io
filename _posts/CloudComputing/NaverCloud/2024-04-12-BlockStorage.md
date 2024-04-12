---
title: '\[NCP 실습\] Block Storage 추가'
excerpt: 'Block Storage 생성 및 파티션, 파일시스템 설정'

categories:
    - 'Naver Cloud Platform'
tags:
    - [NCP, Cloud]

permalink: /NaverCloud/ex/4

toc: true
toc_sticky: true

date: 2024-04-12
last_modified_at: 2024-04-12
---

## Block Storage 실습 목차

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

<p align = "center">
    <img src="/assets/images/NCP/ex4-1.png" width="90%">
</p>
Server -> Storage 선택 -> 스토리지 생성 선택

### SSD 생성

<p align = "center">
    <img src="/assets/images/NCP/ex4-2.png" width="90%">
</p>

-   하이퍼바이저: Xen 선택 -> 현재 서버가 Xen 기반
-   스토리지 이름
-   스토리지 종류: SSD, HDD
-   적용 서버 선택: 스토리지를 적용할 서버 선택
-   크기: 저장 용량 크기

값을 다 넣었으면 추가 버튼 클릭하여 생성

> 비슷한 방법으로 하드디스크 Storage 10GB 2개를 생성해보시오

<p align = "center">
    <img src="/assets/images/NCP/ex4-3.png" width="90%">
</p>

### 생성 Storage 확인

<p align = "center">
    <img src="/assets/images/NCP/ex4-4.png" width="90%">
</p>

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

우선 클라우드 서버에 ssh 접속 후 시스템에 연결된 디스크와 정보 확인.

```bash
## 현재 시스템에 연결된 디스크와 파티션 정보 출력
$ fdisk -l
```

<p align = "center">
    <img src="/assets/images/NCP/ex4-5.png" width="90%">
</p>

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

<p align = "center">
    <img src="/assets/images/NCP/ex4-6.png" width="90%">
</p>

-   두번째 파티션 생성

두번째 파티션의 경우 Partition number와 sector 설정부분에서 차이를 모인다.

<p align = "center">
    <img src="/assets/images/NCP/ex4-7.png" width="90%">
</p>

-   파티션 조회

command p 를 통해서 파티션을 조회하면 xvdb1, xvdb2로 파티션이 성공적으로 생성됨을 확인할 수 있음

<p align = "center">
    <img src="/assets/images/NCP/ex4-8.png" width="90%">
</p>

-   저장

마지막으로 command w을 입력하여 fdisk를 종료하면서 설정한 파티션을 저장한다.

<p align = "center">
    <img src="/assets/images/NCP/ex4-9.png" width="90%">
</p>

---

## 파일 시스템 생성과 마운트

마운트: 저장 장치에 접근할 수 있는 경로를 디렉토리 구조에 편입시키는 작업을 의미한다.

### 추가 SSD에 파일 시스템 생성

파일 시스템은 컴퓨터에서 파일이나 자료를 쉽게 발견 및 접근할 수 있도록 보관 또는 조직하는 체계이다. 각 파티션 별로 설치하는 과정은 다음과 같다.

```bash
$ lsblk # 파티션 확인
```

<p align = "center">
    <img src="/assets/images/NCP/ex4-10.png" width="90%">
</p>

```bash
$ mkfs -t ext4 /dev/xvdb1 # xvdb1에 ext4 파일 시스템 생성
$ mkfs.ext4 /dev/xvdb2 # xvdb2에 ext4 파일 시스템 생성
```

<p align = "center">
    <img src="/assets/images/NCP/ex4-11.png" width="90%">
</p>

### 파일시스템 생성 확인

```bash
$ lsblk -f
```

<p align = "center">
    <img src="/assets/images/NCP/ex4-12.png" width="90%">
</p>

## 파일 시스템을 디렉토리에 마운트

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

<p align = "center">
    <img src="/assets/images/NCP/ex4-13.png" width="90%">
</p>

마운트 포인트를 확인하면 정상적으로 마운트 되었음을 확인할 수 있음
