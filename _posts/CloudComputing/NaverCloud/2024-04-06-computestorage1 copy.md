---
title: '\[NCP\] 2. Compute/Storage'
excerpt: '가상화 기술과 네이버 클라우드 플랫폼 Compute 서비스'

categories:
    - 'Naver Cloud Platform'
tags:
    - [NCP, Cloud]

permalink: /NaverCloud/2

toc: true
toc_sticky: true

date: 2024-04-06
last_modified_at: 2024-04-06
---

## 가상화 기술

-   전가상화
    -   하드웨어 전체를 가상화하는 방식
    -   하드웨어를 관리하는 하이퍼바이저가 VM들에게 하드웨어 리소스를 할당하는 방식
    -   하이퍼바이저가 모든 리소스를 관리하는 만큼 성능적으로 오버헤드 발생
    -   VM은 일반적인 OS를 그대로 사용
-   반가상화
    -   전가상화의 성능 저하를 해결하고자 모든 명령에 대해 DOM0를 통해 요청하는 방식이 아닌 하이퍼 콜을 통해 직접 요청
    -   하이퍼콜을 사용하기 위해서는 VM의 OS가 이를 지원해야함

### Hyper Visor

-   하드웨어와 VM사이에 VM과 하드웨어를 관리하기 위한 가상화 관리 소프트웨어

Type1, Type2로 나뉘어짐 (사진 추가 필요)

## Compute Service

-   cpu

    -   vCPU(코어 수) 단위로 할당
    -   CPU의 Core가 vCPU에 맵핑되는 구조
    -   Over Commit(가상화된 core를 쉐어하여 사용 가능) 허용
    -   스펙 변경 가능

-   Memory

    -   GB 단위로 할당
    -   Over Commit 허용 안함
    -   스펙 변경 가능

-   HDD

    -   OS 영역이 기본 할당(리눅스 50GB or 윈도우 100GB 기본)
    -   OS 영역은 용량 확장이나 축소 불가
    -   SSD 타입과 HDD 타입으로 구분

-   GPU
    -   GPU 카드 단위로 할당(GPU Core 단위로 할당하지 않음)
    -   Pass Through 방식으로 제공
    -   최대 2장까지 제공
-   Network
    -   Physical 10Gbps
    -   Logical 1Gbps
    -   실제로는 500Mbps ~ 1Gbps 제공

### 서버 타입

| 타입               | 운영체제                | 하드웨어                |
| ------------------ | ----------------------- | ----------------------- |
| Micro              | CentOS, Ubuntu          | 1vCPU, 1G RAM, 50G HDD  |
| Standard(4배수)    | CentOS, Ubuntu, Windows | 2~16vCPU, 4G ~ 32G RAM  |
| High Memory(8배수) | CentOS, Ubuntu, Windows | 8~32vCPU, 64 ~ 256G RAM |
| High CPU(2배수)    | CentOS, Ubuntu, Windows | 2~32vCPU, 4 ~ 64G RAM   |

-   마이크로 서버
    -   회원 가입 후 결제 정보를 등록한 월부터 1년간 무료로 사용 가능
    -   테스트 용도
    -   추가 디스크 사용 불가
-   Standard 타입
    -   일반적으로 사용하는 서버 타입
-   High Memory 타입
    -   대용량 메모리가 필요한 경우
-   VDS Server(Virtual Dedicated Server)
    -   기존 클라우드 일반적인 서버가 물리서버 기반에 하이퍼 바이저 위에 다수의 VM이 생성되는 것과 달리 VDS는 단 하나의 VM만 생성
    -   Over Commit이 발생하지 않음
-   Bare Metal Server
    -   하이퍼바이저 없이 하드웨어의 자원을 그대로 사용
        -   기본적인 관리는 IPMI를 통해 관리
    -   오라클과 같이 라이센스상 클라우드에 대한 제약이 있는 경우 사용
    -   특이서버
        -   Dual Intel Xeon Silver 4114 -> 20Core cpu MSSQL이 사용할 수 있는 최대 Core수 만큼 사용
        -   Dual Intel Xeon Sliver 4112 -> 8Core cpu Oracle 전용
-   GPU Server
    -   딥 러닝을 위한 GPU 서버 팜.
    -   Nvidia p40/v100 장착
    -   Nvidia GRID 기술이 아닌 Pass Through 를 적용하여 제공
    -   VM당 최대 2장의 GPU 제공

### Preinstall Image

서버에 어플리케이션을 설치하는데 번거로움 해소
사전의 어플리케이션을 설치한다.

-   DBMS
    -   MySQL
    -   Cubrid
    -   Redis
    -   MariaDB
    -   PostgreSQL
    -   MS-SQL
-   Application
    -   LAMP
    -   WordPress
    -   RabbitMQ
    -   Tomcat
    -   ...

## 서버 Operation

### 서버 생성

-   리전 선택
    -   서버를 생성할 리전 선택
-   부팅 디스크 이미지 선택
    -   디스크, 이미지 타입 선택
-   OS 선택
    -   OS이미지 선택
-   서버 설정
    -   존, 스토리지 종류, 서버 세대, 서버 타입, 요금제 선택

### 서버 상태 값

-   생성시
    -   생성중
    -   부팅중
    -   설정중
    -   운영중
-   정지시
    -   종료중
    -   정지
-   시작시
    -   부팅중
    -   운영중

### 서버 Information

-   서버 리스트 화면에서 제공되는 정보
    -   서버 이름, 서버 이미지 이름, 서버 사양, 상태, 비공인 IP, 공인 IP, Zone, 모니터링 설정 상태
-   서버 선택 시 제공되는 정보
    -   생성일시
    -   구동일시
    -   스토리지
    -   Init Script
    -   Network Interface 적용 가능 여부
    -   포트포워딩 정보
    -   반납 보호
    -   ACG
    -   SSD 스토리지 추가 여부

### 서버 Operation

-   서버 스펙 변경

    -   동일한 서버 타입에 한하여 CPU, Memory 스펙 변경 가능
    -   반드시 정지된 상태에서 변경
    -   다른 서버 타입으로 변경하고자 할 경우 내 서버 이미지를 생성하여 해당 이미지로 다른 타입의 서버 생성

-   내 서버 이미지 생성
    -   서버의 현재 상태 이미지로 생성
    -   이미지 생성시 반드시 정지된 상태에서 변경
    -   타 리전에 서버 생성시에는 먼저 대상 리전으로 복제
-   유사 서버 생성

    -   선택한 서버와 동일한 스펙의 서버 생성시 사용
    -   서버 이미지, 스토리지, 서버 타입, 요금제, Zone, 인증키, ACG 정보를 동일하게 적용

-   스토리지 생성

    -   OS영역 50GB에 추가적으로 디스크 생성시 사용
    -   최소 10GB에서 최대 2TB까지 디스크 생성 가능
    -   최대 16개 디스크 추가 가능
    -   생성된 스토리지는 용량 증설 가능(증설만 가능 감축은 불가)

-   스토리지 용량 변경
    -   스토리지에 대해 용량 증설 가능
    -   용량 증설은 서버 정지 혹은 연결 해제 후 가능
    -   용량 증설 후 데이터 삭제 없이 해당 공간을 사용하기 위해서는 볼륨 관리 툴을 이용해야 함`(Linux: growpart, Window: diskmanagement)`
-   서버 그룹 생성 및 관리

    -   서버 그룹은 서버 리스트 뷰에서 필터링을 위한 용도

-   반납 보호 설정
    -   사용자의 부주의로 서버가 반납되지 않도록 설정하는 기능
-   상세 모니터링
    -   서버들의 모니터링에 대해 보다 상세하게 모니터링 하는 기능
    -   상세 모니터링 설정 시 해당 서버는 모니터링 서비스에서 정보 확인
-   네트워크 모니터링 설정
    -   서버에서 발생하는 트래픽에 대한 상세 내용을 확인할 수 있도록 설정하는 기능
    -   네트워크 트래픽 모니터링 서비스에서 정보 확인 가능
-   서버 이름 변경
    -   MC에서 보여지는 서버 이름 변경
    -   실제 서버명이 변경되지는 않음(설정 파일을 수정해야 실제 서버명 수정)
-   인증키 변경
    -   서버 생성 시 생성한 인증키를 다른 인증키로 변경
    -   초기 패스워드를 변경하지 않은 상태에서 인증키 분실 시 초기 패스워드를 확인하기 위해 인증키 변경이 필요
    -   인증키 변경시에는 ID인 이메일로 인증 정보 전송
