---
title: '\[NCP\] 9. TroubleShooting'
excerpt: '대표적인 트러블 슈팅 사례'

categories:
    - 'Naver Cloud Platform'
tags:
    - [NCP, Cloud]

permalink: /NaverCloud/9

toc: true
toc_sticky: true

date: 2024-04-08
last_modified_at: 2024-04-08
---

{% include_relative agenda.md %}

## TroubleShooting

네이버 클라우드 플랫폼을 사용하면서 직면할 문제를 어떻게 해결할지에 대한 방법

### 적절한 용량 산정

-   컴퓨팅 파워

    -   IO Wait 이 발생하는 시점부터 서브시 품질은 떨어지기 시작
    -   SWAP을 사용하는 경우 메모리 증설
    -   관련 명령어: sar, ps, top

-   네트워크

    -   동접 기준으로 파일(페이지)를 기한내에 전송할 수 있을지를 가지고 산정
    -   서버의 경우 전송 속도 500Mbps 기준으로 서버 대수 산정
    -   CDN의 경우 가용량 기준으로 산정

-   용량 산정 주요 지표
    -   파일(페이지) 크기
    -   동접

### 성능 측정

-   웹 서비스

    -   WMS
    -   Ngrinder
    -   Pinpoint
    -   AB

-   MySQL 성능 측정

    -   Percona TPCC
    -   sysbench
    -   Apache Jmeter

<br>
<br>

-   AB의 설치

    -   YUM install httpd-tools

-   AB 옵션
    -   -n 요청 수
    -   -c 동시에 요청하는 요청 수
    -   -g 측정한 모든 값을 gunplot 혹은 TSV파일로 기록
    -   -t 성능을 검사하는 최대 시간(초 단위)
    -   -v 출력에 대한 상세한 정보 지정
        -   4: 헤더에 대한 정보
        -   3: 응답 코드
        -   2: 정보
    -   -X 프록시 서버를 사용하여 요청

### 문제 진단을 위해 활용 가능한 상품

-   웹 서비스

    -   WMS
    -   Pinpoint

-   DB

    -   Cloud for MySQL

        -   DB Dashboard
        -   OS Dashboard
        -   DB Logs
        -   Query TimeLine

    -   Cloud for MS-SQL
        -   DB Dashboard
        -   Performance
        -   DB Logs

### 문제 해결을 위한 명령어들

-   Linux

    -   tcpdump
    -   Nmap
    -   Traceroute
    -   Sar
    -   Ps
    -   Lsof

-   Windows

    -   Microsoft Message Analyzer
    -   이벤트 뷰어
    -   Performance Monitor
    -   PSTools

-   sar

    -   리소스 사용량에 대한 로그 조회
    -   Linux에서 시스템에 대한 광범위한 모니터링
    -   CPU 사용률, 메모리, iowait, 로드를 비롯한 다양한 정보를 확인 가능
    -   Demo
        -   Sar -A
        -   Sar -b (I/O)
        -   Sar -n (network)
        -   Sar -W (컨텍스위칭)

-   Ping

    -   ICMP를 이용한 프로그램
    -   해당 호스트가 살아있는지 죽어 있는지
    -   방화벽에 의해 막혀 있는지 확인하기 위해 사용하는 기초적인 명령어
    -   Windows와 Linux에서의 옵션은 서로 상이

-   nmap

    -   포트스캔용 툴
    -   오픈되어 있는 포트에 대해 스캔
    -   호스트 뿐만 아니라 대역으로도 탐지
    -   응용 프로그램 버전 및 OS 탐지
    -   Linux와 Windows 버전 사용 가능

    -   단일 호스트 검색
        -   Nmap 106.10.50.45, 10.41.83.101
        -   Nmap -v 106.10.50.45, 10.41.83.101
    -   대역으로 검색
        -   Nmap 106.10.50.45-50, Nmap 106.10.60.\*, Nmap 106.10.50.0/24, Nmap 10.41.83.101
    -   방화벽 확인
        -   Nmap -sA 106.10.50.45, 10.41.83.101
    -   방화벽으로 보호되는 호스트에 대한 스캔
        -   Nmap -PN 106.10.50.45, 10.41.83.101
    -   모든 패킷 표시
        -   Nmap -packet-trace 106.10.50.45, 10.41.83.101

-   TCPView

    -   Windows 시스템의 TCP/UDP에 대한 상세 정보 확인
        -   Process, PID, Protocol, Address/Port, State, Packet Size
    -   작업중인 시스템의 오픈 된 네트워크 연결 프로세스 확인
    -   비슷한 netstat 리눅스 있음

-   traceroute

    -   ICMP와 TTL을 이용하여 경로를 확인하는 프로그램
    -   Windows에서는 tracert
    -   최근의 네트워크 장비들은 ICMP에 대해 응답하지 않음
    -   글로벌 환경에서 어떤 회선을 타고가는지 확인 할때 사용

-   ps

    -   현재 프로세스를 확인하는 명령어
    -   Top과 유사

-   lsof

    -   현재 프로세스가 사용(오픈)하고 있는 파일 리스트 출력
    -   특정 프로세스가 CPU 점유율이 높을 경우 어떤 문제인지 확인하기 위해 사용

-   tcpdump

    -   트래픽 모니터링
    -   네이버 클라우드 플랫폼의 서버들의 경우 LB로 연결된 구조의 경우 사용이 제한적
    -   일반적인 사용법
        -   호스트 관련
            -   Tcpdump host
            -   Tcpdump src
            -   Tcpdump dst
            -   Tcpdump net
        -   프로토콜 관련
            -   Tcpdump port
            -   Tcpdump src port
            -   Tcpdump dst port

-   Microsoft Message Analyzer

    -   Netmon의 업그레이드 버전

-   이벤트 뷰어

    -   Windows의 syslog
    -   주로 살펴봐야 하는 항목
        -   Window 로그 > 시스템
            -   시스템 Fault가 발생할 경우 이에 대한 로그 기록
        -   Windows 로그 > 보안
            -   시스템 로그인 내역
        -   Windows 로그 > 응용 프로그램
            -   응용 프로그램의 다양한 로그 기록
        -   응용 프로그램 및 서비스 로그
            -   응용 프로그램, 서비스에 대한 이벤트 로그 기록

-   성능 모니터

    -   Windows의 sar
    -   Windows 시스템의 광범위한 리소스 모니터링
        -   Processor
        -   Process
        -   Memory
        -   System
        -   Network Interface
        -   Physical Disk / Logical Disk

-   리소스 및 성능 모니터

    -   Windows 시스템을 진단하고 보고서 작성

-   Debug Diag

    -   Process의 Crash, Memory Leak 패턴을 확인하는 디버그 도구
    -   Crash Dump에 대한 분석 도구

-   PAL(Performance Analysis of Logs Tool)

    -   서버 타입 별 성능 카운터 오브젝트 제공
    -   Threshold에 따른 성능 분석 리포트 제공

-   xPerf
    -   Process의 Crash, Memory Leak 패턴을 확인하는 디버그 도구

### 문제 해결을 위한 Log

-   Linux
    -   일반적인 시스템 로그
        -   /var/log
    -   HTTPD 로그
        -   /var/log/httpd
-   Windows
    -   Eventvwr을 통한 로그 모니터링

### Logrotate

-   시스템 연속성 보장을 위해 디스크 사용률 관리는 필수
    -   리눅스의 경우 /파티션이 Full이 되는 순간 서비스 정지
    -   디스크의 사용률을 높이는 요인
        -   로그
        -   DB데이터
-   로그에 대하여 정해진 처리 방식으로 삭제거나 파일을 바꿔주는 역할
    -   로그 파일을 바꿔줌으로써 시스템 성능을 높이는 역할 수행

### 커널 파라미터 변경

-   Sysctl 명령어를 이용한 변경

    -   Linux의 경우 커널 파라미터를 변경함으로써 성능을 변경할 수 있다.

-   Sysctl.conf

    -   리눅스 커널 파라미터 중 주요한 항목에 대한 설정

-   Limits.conf

    -   사용자별 limit 설정
    -   프로세스, 오픈 파일 등이 sysctl.conf 파일과 함께 변경되어야 효과

-   파일 확인
    -   오픈할 수 있는 파일 개수 확인 cat /proc/sys/fs/file-nr
    -   변경
        -   /etc/sysctl.conf
        -   fs.file-max=819200
-   TCP 타임 아웃

    -   확인 sysctl -a | grep keep
    -   net.ipv4.tcp_keepalive_time = 3600

-   Limits.conf
    -   사용자별 limit 설정
    -   프로세스, 오픈 파일 등이 sysctl.conf 파일과 함께 변경되어야 효과
