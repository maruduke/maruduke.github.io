---
title: '\[NCP 실습\] 디스크 snapshot'
excerpt: 'snapshot 생성'

categories:
    - 'Naver Cloud Platform'
tags:
    - [NCP, Cloud]

permalink: /NaverCloud/ex/7

toc: true
toc_sticky: true

date: 2024-04-14
last_modified_at: 2024-04-14
---

## snapshot

|                  | 스냅샷                                                                         | 백업                                        |
| ---------------- | ------------------------------------------------------------------------------ | ------------------------------------------- |
| 목적             | 데이터의 현재 상태포착<br> 시스템의 빠른 롤백<br> 가상 머신 환경에서 상태 관리 | 데이터의 장기적인 보존 및 복구              |
| 데이터 저장 방식 | 데이터의 현재 상태에 대한 이미지, 원본 데이터와의 차이점만 저장                | 외부 저장소에 복사하여 보관 주기적으로 수정 |
| 복구 시간        | 짧다                                                                           | 길다                                        |
| 용도             | 시스템 상태의 빠른 롤백                                                        | 데이터 보호 및 재해 복구                    |
| 보존기간         | 짧다                                                                           | 길다                                        |

### snapshot 생성 준비과제

1. 스냅샷 생성을 위한 디스크 생성

스토리지 이름: server1-disk4
하이퍼바이저: XEN
적용 서버: server1
크기: 20GB

2. 생성한 디스크에 대해 파티션 생성
   그림 넣기
   xvde1 : 10G
   xvde2 : 10G

3. 각 파티션에 다음과 같은 파일 시스템 생성

xvde1: xfs
xvde2: ext4

4. 각 파티션을 다음과 같은 디렉토리에 마운트

xvde1: /mnt/part3
xvde2: /mnt/part4

5. 각 디렉토리에 다음과 같은 텍스트 파일 생성

/mnt/part3/snaptest: snapshot test for xfs
/mnt/part4/snaptest: snapshot test for ext4

```bash
$ echo "snapshot test for xfs" > /mnt/part3/snapshot
$ cat /mnt/part3/snaptest
snapshot test for xfs

$ echo "snapshot test for ext4" > /mnt/part4/snapshot
$ cat /mnt/part4/snaptest
snapshot test for ext4
```

### storage에 대한 Snapshot 생성

server - Storage - server1-disk4 선택 - 스토리지 설정 - 스냅샷 생성

### snapshot을 이용한 Block Storage 생성

-   server -> storage -> 스토리지 생성

이미지 스냅샷 설정

lsblk -f 이미지
스냅샷으로 생성한 스토리지 정보는 파티션 파일 시스템 종류 및 UUID가 기존 스토리지와 동일하다.

스냅 샷 마운트

1. xfs 파일 시스템 마운트 시 오류 발생
2. ext4 파일 시스템의 마운트는 정상작동
3. xvdf2 (ext4) 파티션만 마운트

### XFS 마운트 오류 원인 확인

```bash
# 리눅스 커널 메시지 출력
$ dmesg | tail
```

원인: xvdf1의 파일 시스템 UUID가 중복됨(xvde1 과 동일) UUID 변경 필요

해결법

```bash
# UUID 생성
$ uuidgen

# xvdf1 파티션의 파일 시스템 복구
$ xfs_repair -L /dev/xvdf1

# xfs 파일 시스템의 파라미터 변경 -u: 파일 시스템의 uuid 변경
$ xfs_admin -U {앞선 uuidgen 명령 출력값} /dev/xvdf1

# 바뀐 uuid 확인
$ blkid

# 파일 시스템 다시 마운트 정상 동작
$ mount /dev/xvdf1 /mnt/part5

$ cat /mnt/part5/snaptest
```

마운트 된 파일 시스템에 내용을 비교하면 동일한 파일이 복사됨을 확인할 수 있음
