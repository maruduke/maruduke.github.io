---
title: '\[NCP 실습\] Logical Volumn'
excerpt: 'Logical Volumn 생성 및 분배'

categories:
    - 'Naver Cloud Platform'
tags:
    - [NCP, Cloud]

permalink: /NaverCloud/ex/5

toc: true
toc_sticky: true

date: 2024-04-12
last_modified_at: 2024-04-12
---

## Logical Volumn

<p align = "center">
    <img src="/assets/images/NCP/ex5-1.png" width="90%">
</p>

-   LVM(Logical Volumn Manager)을 이용하여 물리적인 디스크의 공간을 논리적으로 합치거나 나눔

## 목표

-   10GB의 2개의 볼륨을 합쳐 20GB의 볼륨 그룹으로 만든다.
-   볼륨 그룹을 15GB씩 2개로 나눈 Logical Volumn을 만든다.

## 추가 HDD에 Logical Volumn 생성

스토리지 정보, 또는 서버 정보의 스토리지 항목에서 경로 정보 확인

### HDD에 파티션 생성

<p align = "center">
    <img src="/assets/images/NCP/ex5-2.png" width="90%">
</p>

-> 여기까지는 이전의 파티션 생성과 같으나 추가적으로 설정이 필요

```bash
$ t # 파티션 타입 변경
$ 8e # Linux LVM
$ w # 파티션 테이블 저장, 다른 디스크에도 똑같이 작업
```

<p align = "center">
    <img src="/assets/images/NCP/ex5-3.png" width="90%">
</p>

## PV(Physical Volumn)

-   물리적 디스크 또는 파티션을 LVM에서 사용할 수 있는 형태로 전환
-   PV 내부적으로 PE(Physical Extent)로 구성

### pv 생성

```bash
$ pvcreate /dev/xvdc1 # 각 파티션에 대해 pv 생성
$ pvcreate /dev/xvdd1

$ pvscan # 각 파티션 별로 생성된 pv 확인

$ pvdisplay # pv에 대한 세부 정보 출력
```

<p align = "center">
    <img src="/assets/images/NCP/ex5-4.png" width="90%">
</p>

## VG(Volumn Group)

PV를 묶어 만든 논리적인 그룹

### VG 생성

```bash
# lvmVG라는 이름의 VG생성
$ vgcreate lvmVG /dev/xvdc1 /dev/xvdd1

$ vgdisplay
```

<p align = "center">
    <img src="/assets/images/NCP/ex5-5.png" width="90%">
</p>

## LV(Logical Volumn)

-   VG내에서 논리적으로 분할된 블록 장치

### LV 생성

```bash
# lvmVG의 15GB 공간을 lvmV1에 할당
$ lvcreate --size 15GB -n lvmLV1 lvmVG
# 수행 후 남은 공간 전부를 lvmLv2에 할당하여 생성
$ lvcreate --extents 100%FREE -n lvmLV2 lvmVG

$ lvdisplay
```

<p align = "center">
    <img src="/assets/images/NCP/ex5-6.png" width="90%">
</p>

### LV에 파일 시스템 생성

```bash
$ mkfs.ext4 /dev/lvmVG/lvmLV1
$ mkfs.ext4 /dev/lvmVG/lvmLV2
```

### LV의 파일 시스템 마운트

```bash
$ mkdir -p /mnt/lvm1
$ mkdir -p /mnt/lvm2

$ mount /dev/lvmVG/lvmLV1 /mnt/lvm1
$ mount /dev/lvmVG/lvmLV2 /mnt/lvm2

$ df -h
$ lsblk
```

<p align = "center">
    <img src="/assets/images/NCP/ex5-7.png" width="90%">
</p>

<p align = "center">
    <img src="/assets/images/NCP/ex5-8.png" width="90%">
</p>
