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

{% include_relative agenda.md %}

## Compute Service

-   cpu

    -   vCPU(코어 수) 단위로 할당
    -   CPU의 Core가 vCPU에 맵핑되는 구조
    -   Over Commit(가상화된 core를 쉐어하여 사용 가능) 허용
    -   스펙 변경 가능

<br>

-   Memory

    -   GB 단위로 할당
    -   Over Commit 허용 안함
    -   스펙 변경 가능

<br>

-   HDD

    -   OS 영역이 기본 할당<mark>(리눅스 50GB or 윈도우 100GB 기본)</mark>
    -   OS 영역은 용량 확장이나 축소 불가
    -   SSD 타입과 HDD 타입으로 구분

<br>

-   GPU

    -   GPU 카드 단위로 할당(GPU Core 단위로 할당하지 않음)
    -   `Pass Through 방식으로 제공(Grid X)`
    -   최대 2장까지 제공

<br>

-   Network
    -   Physical 10Gbps
    -   Logical 1Gbps
    -   실제로는 500Mbps ~ 1Gbps 제공(Rx + TX)

### 서버 타입

<center>

| 타입               | 운영체제                | 하드웨어                |
| ------------------ | ----------------------- | ----------------------- |
| Micro              | CentOS, Ubuntu          | 1vCPU, 1G RAM, 50G HDD  |
| Standard(4배수)    | CentOS, Ubuntu, Windows | 2~16vCPU, 4G ~ 32G RAM  |
| High Memory(8배수) | CentOS, Ubuntu, Windows | 8~32vCPU, 64 ~ 256G RAM |
| High CPU(2배수)    | CentOS, Ubuntu, Windows | 2~32vCPU, 4 ~ 64G RAM   |

</center>

<br>

-   마이크로 서버
    -   회원 가입 후 결제 정보를 등록한 월부터 1년간 무료로 사용 가능
    -   테스트 용도
    -   추가 디스크 사용 불가

<br>

-   Standard 타입
    -   일반적으로 사용하는 서버 타입

<br>

-   High Memory 타입
    -   대용량 메모리가 필요한 경우

<br>

-   VDS Server(Virtual Dedicated Server)
    -   기존 클라우드 일반적인 서버가 물리서버 기반에 하이퍼 바이저 위에 다수의 VM이 생성되는 것과 달리 `VDS는 단 하나의 VM만 생성`
    -   Over Commit이 발생하지 않음

<br>

-   Bare Metal Server
    -   `하이퍼바이저 없이 하드웨어의 자원을 그대로 사용`
        -   기본적인 관리는 IPMI를 통해 관리
    -   오라클과 같이 라이센스상 클라우드에 대한 제약이 있는 경우 사용
    -   특이서버
        -   Dual Intel Xeon Silver 4114 -> 20Core cpu MSSQL이 사용할 수 있는 최대 Core수 만큼 사용
        -   Dual Intel Xeon Sliver 4112 -> 8Core cpu Oracle 전용

<br>

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

<br>

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
    -   서버 이름
    -   서버 이미지 이름
    -   서버 사양
    -   상태
    -   비공인 IP
    -   공인 IP
    -   Zone
    -   모니터링 설정 상태

<br>

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
    -   반드시 `정지된 상태에서 변경`
    -   다른 서버 타입으로 변경하고자 할 경우 `내 서버 이미지를 생성하여 해당 이미지로 다른 타입의 서버 생성`
    -   ex) High CPU(2core 4GB) -> High CPU(8core 16GB) 스펙 변경 시 이미지 생성 없이 바로 가능
    -   ex) High CPU -> High Memory 타입으로 변경 시 내 서버 이미지 생성이 필요

<br>

-   내 서버 이미지 생성

    -   서버의 현재 상태 이미지로 생성
    -   이미지 생성은 서버가 정지, 운영 중일 때 가능
    -   타 리전에 서버 생성시에는 먼저 대상 리전으로 복제

<br>

-   유사 서버 생성

    -   선택한 서버와 동일한 스펙의 서버 생성시 사용
    -   서버 이미지, 스토리지, 서버 타입, 요금제, Zone, 인증키, ACG 정보를 동일하게 적용

<br>

-   스토리지 생성

    -   OS영역 기본 50GB에 추가적으로 디스크 생성해서 사용
    -   최소 10GB에서 최대 2TB까지 디스크 생성 가능
    -   최대 15개 디스크 추가 가능
    -   생성된 스토리지는 용량 증설 가능(증설만 가능, 감축은 불가)

<br>

-   스토리지 용량 변경

    -   스토리지에 대해 용량 증설 가능
    -   용량 증설은 서버 정지 혹은 연결 해제 후 가능
    -   용량 증설 후 데이터 삭제 없이 해당 공간을 사용하기 위해서는 볼륨 관리 툴을 이용해야 함
        -   `Linux: growpart`
        -   `Window: diskmanagement`

<br>

-   물리 배치 그룹 관리

    -   서버들을 최대한 다른 클러스터에 분산시키기 위한 방법
    -   분산 결과에 대해 보장하진 않는 Best-Effort 방식

<br>

-   반납 보호 설정

    -   사용자의 부주의로 서버가 반납되지 않도록 설정하는 기능

<br>

-   상세 모니터링

    -   서버들의 모니터링에 대해 보다 상세하게 모니터링 하는 기능
    -   상세 모니터링 설정 시 해당 서버는 모니터링 서비스에서 정보 확인

<br>

-   네트워크 모니터링 설정

    -   서버에서 발생하는 트래픽에 대한 상세 내용을 확인할 수 있도록 설정하는 기능
    -   네트워크 트래픽 모니터링 서비스에서 정보 확인 가능

<br>

-   서버 이름 변경

    -   Main Console에서 보여지는 서버 이름 변경
    -   실제 서버명이 변경되지는 않음(설정 파일을 수정해야 실제 서버명 수정)

<br>

-   인증키 변경

    -   서버 생성 시 생성한 인증키를 다른 인증키로 변경
    -   초기 패스워드를 변경하지 않은 상태에서 인증키 분실 시 초기 패스워드를 확인하기 위해 인증키 변경이 필요
    -   인증키 변경시에는 ID인 이메일로 인증 정보 전송

## Server Image Operation

-   내 서버 이미지 만들기

    -   서버에 모든 스토리지를 이미지로 생성
        -   추가 스토리지까지 이미지로 생성
        -   추가 스토리지의 용량이 클 경우 스토리지 해제 후 OS 영역만 이미지로 생성
        -   리전간 복제시에는 OS 영역만 이미지로 생성하고 데이터는 SFTP 같은 방식을 이용해 전송
    -   VPC 플랫폼은 서버가 `운영중 상태도 생성가능` Classic 플랫폼은 `정지 상태로 변경 필요`

<br>

-   이미지를 이용하여 서버를 생성이 필요한 경우
    -   리전간 서버 복제
    -   서버 스펙 변경시
    -   서버와 마스터 이미지 생성 및 이를 이용한 서버 생성
    -   ACG 변경(Classic only)

### Server Image Builder Operation

-   템플릿을 이용한 서버 이미지 생성
    -   내부적으로 packer 사용
    -   VM서버에 원하는 구성정보(Provisioners)를 스크립트로 별도 관리
    -   멀티 클라우드 환경에서 템플릿을 이용해 다양한 클라우드 플랫폼의 서버 이미지 생성
-   내 서버 이미지와 다른점
    -   다양한 어플리케이션이 설치되고, 데이터가 축적된 서버를 이미지화하는 반면
    -   서버 이미지 빌더로 만든 이미지는 init script로 만든 서버를 곧바로 이미지로 만든 것과 같은 효과
-   네이버 클라우드 플랫폼의 서버 이미지 빌더의 특징
    -   Packer의 provisioners부분을 웹을 통해 설정 가능하도록 제공
    -   Packer를 통한 서버 이미지 빌드 가능

1. Script 생성
    1. 부팅 디스크 크기, 이미지 타입, OS 이미지 타입 선택
    2. Provisioners 작성 및 추가 스토리지 설정
    3. Provisioners 작성은 Packer의 형식을 따름
2. 이미지 생성
    1. 이미지 빌더 스크립트로 만들 서버 이미지 생성

## Storage Operation

-   서버 생성 시 OS 영역은 50GB, 혹은 100GB로 서버가 생성
    -   서버 이미지 생성 시 생성 시간 최소화 및 다양한 Operation시 효율을 높이기 위한 방안
    -   `Linux의 경우 50GB, Windows의 경우 100GB로 생성`
-   기본 컨셉
    -   기본적인 어플리케이션은 OS영역에 설치, 데이터는 추가 스토리지에 저장하여 시스템 `Crash 발생 시 데이터 유실 최소화 빠른 복구가 가능하도록 함`.
-   스토리지 Spec
    -   `스토리지당 10GB에서 최대 2TB`까지 설정 가능
    -   서버 1대당 `최대 16개의 스토리지`(OS 스토리지 1개+ 15개 추가 스토리지)
-   스토리지 detach 가능
    -   스토리지를 서버에서 해제(detach)하여 다른 서버에 연결(attach) 기능

## Snapshot Operation

-   `스토리지 단위`로 스냅샷 생성
-   내 서버 이미지와 다른 점
    -   내 서버 이미지는 서버 단위로 스냅샷을 생성하는데 반해 스냅샷은 스토리지 단위로 스냅샷을 생성
    -   스냅샷은 추가 스토리지로만 변환 가능
-   용도
    -   OS 영역의 문제로 부팅이 안되는 상황에서 OS 영역의 데이터를 복구해야 할 경우
        -   OS 영역의 스토리지는 스냅샷으로 생성
        -   해당 스냅샷을 다른 서버의 추가 스토리지로 변환
        -   해당 서버에서 원하는 데이터를 추출

## Public IP Operation

-   외부에서 서버에 접속하는 방법
    -   공인 IP를 할당 접속
    -   포트 포워딩을 통한 접속(Classic)
    -   VPN을 이용한 접속
-   공인 IP의 리소스 종속성
    -   VPC 환경에서는 공인 IP는 리전에 종속
        -   존 구분 없이 공인 IP 이용이 가능
    -   Classic 환경에서는 공인 IP는 존에 종속
        -   특정 존에 종속되는 리소스는 다른 존에서 접근이 불가능
        -   즉, KR-1 Zone에 있는 공인 IP는 KR-2 Zone에서 이용 불가
-   공인 IP는 서버에 종속적이지 않으며, 공인 IP를 서버에 맵핑하는 구조

## Init Script Operation

-   서버 생성 시 OS 설치 후 post script를 통해 사용자가 원하는 기능 수행
-   Linux Shell, Python, Perl 스크립트로, Windows는 VB 스크립트로 작성 가능
-   서버 생성 시 초기 1회에 한하여 실행
    -   서버 재부팅 등의 작업 시엔 동작하지 않음

## ACG

-   서버 방화벽 역할
-   Default ACG와 Custom ACG로 구분
-   프로토콜은 TCP,UDP, ICMP 중 선택, 접근 소스는 IP(CIDR), ACG Group 설정 가능

| classic vs vpc | Classic             | vpc                 |
| -------------- | ------------------- | ------------------- |
| 최대 생성 개수 | 계정당 100개        | vpc당 500개         |
| 적용 대상      | 서버                | 서버 NIC            |
| 적용 ACG 개수  | 서버당 5개          | NIC당 3개           |
| 속성           | 맵핑 정보 변경 불가 | 맵핑 정보 변경 가능 |
| 의존성         | 의존성 없음         | VPC에 의존          |
| Rule 속성      | Inbound             | in/out bound        |

## Private Subnet & Network Interface Operation

-   네이버 클라우드 플랫폼의 서버 네트워크 특징
    -   서버 생성 시, 초기에 Network Interface는 하나가 생성됨
    -   IP Allas 허용하지 않음
    -   추가 NIC 허용하지 않음
-   다양한 환경
    -   HA 구성이 필요한 경우 VIP가 필요
    -   VPN으로 연결하는 경우 Peer 네트워크는 대역폭으로 선언
-   네이버 클라우드 플랫폼의 방안
    -   새로운 NIC를 생성할 수 있게 하되 해당 NIC들 만의 네트워크를 구성
    -   해당 네트워크는 192.168.X.0/24 대역을 생성할 수 있도록 Private Subnet 기능 제공

### private Subnet

-   192.168.X.0/24 대역만 사용 가능
-   각 존 당 하나의 private subnet을 만들 수 있다.
-   ACG가 적용되지 않음

-   사용 방법

    -   Private Subnet에서 대역 설정
    -   Network Interface에서 서버에 해당 대역을 IP 할당(Network interface에서 설정을 해야 NIC 추가가 가능)

-   VPC 플랫폼에서의 서버 네트워크 구성
    -   서버 생성 시, 최대 3개의 Network Interface 생성이 가능
    -   각 NIC에는 Secondary IP 부여 가능
    -   하나의 Network Interface에는 최대 5개까지의 Secondary IP를 추가할 수 있음
        -   하나의 NIC가 가질 수 있는 IP의 개수는 최대 6개(Primary IP 1개, Secondary IP 5개)
        -   멀티 IP 이용을 위해선 부가적인 network script 작성 및 적용 필요

### Secure Zone

-   금융 정보, 개인 정보와 같이 격리되어야 하고 모니터링 되어야 하는 서버를 위치시키기 위한 존

-   특징
    -   인터넷 통신 불가
    -   네이버 클라우드 플랫폼 내부의 허용된 서버와의 통신만 가능
    -   Secure Zone은 망 방화벽을 통해 Secure Zone 내의 서버를 외부로부터 보호
    -   방화벽은 Inbound뿐만 아니라 Outbound 트래픽까지 제어
    -   로그 저장 가능
