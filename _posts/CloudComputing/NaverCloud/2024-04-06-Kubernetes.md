---
title: '\[NCP\] 2. Kubernetes'
excerpt: '가상화 기술과 네이버 클라우드 플랫폼 Compute 서비스'

categories:
    - 'Naver Cloud Platform'
tags:
    - [NCP, Cloud]

permalink: /NaverCloud/7

toc: true
toc_sticky: true

date: 2024-04-06
last_modified_at: 2024-04-06
---

## Kubernetes Service

-   완전 관리형 Kubernetes Cluster 제공
    -   Container의 배포, 운영, 확장을 자동화하기 위한 플랫폼
    -   컨트롤 패널의 설치, 운영에 대한 고민 없이, 사용자는 Container가 구동되는 Worker Node에 대한 관리만 하면 됨
    -   Container Registry, Load Balancer 등 네이버 클라우드 플랫폼 다른 서비스와 통합하여 사용 가능

### 주요 기능

-   Automatic Binpacking: Worker node의 가용성을 유지하면서 보유한 리소스를 충분히 활용할 수 있도록 `스스로 스케쥴링하며 컨테이너를 배치`
-   Storage Orchestration: 로컬 저장소를 선택하거나 NFS, iSCSI 등과 같은 공유 네트워크 스토리지를 컨테이너에 할당/마운트 하여 사용 가능함
-   Secret & Configuration Management: 어플리케이션 연동 및 `접근 제어를 위한 보안 키, 설정 내역을 컨테이너 이미지의 변경 없이 업데이트` 할 수 있고 외부로 노출하지 않고 사용 가능
-   Horizontal Scailng: CPU 사용률과 같은 metric을 기반으로 pod의 Deployments, replicaset을 스케줄링하여 수평적 확장 가능
-   Service Discovery & Load Balancing: 컨테이너에 IP 주소를 자동으로 할당하고 클러스터 내 트래픽을 로드 밸런싱 할 수 있는 컨테이너 세트에 단일 DNS 이름 할당
-   Self Healing: `실패한 컨테이너를 자동으로 다시 시작`, 사용자가 정의한 헬스체크에 `응답이 없는 컨테이너 종료`. 워커 노드 장애시 `사용 가능한 다른 워커 노드에 컨테이너 다시 기동`
-   Batch Execution: 배치 및 CI 작업 부하를 관리할 수 있으므로 원하는 경우 실패한 컨테이너 대체 가능
-   Automatic Rollback & Rollouts: 응용 프로그램이나 구성에 대한 변경 사항을 점진적으로 업데이트하고 문제 발생 시 자동으로 롤백 할 수 있음

## 핵심 개념

### Pod

-   pod는 쿠버네티스 애플리케이션의 기본 실행 단위
-   쿠버네티스 객체 모델 중 만들고 배포할 수 있는 가장 작고 간단한 단위

1. 단일 컨테이너만 동작하는 pod
    - 쿠버네티스는 `컨테이너가 아닌 pod를 직접 관리`
2. 함께 동작하는 작업이 필요한 다중 컨테이너가 동작하는 pod
    - pod는 강하게 결합되어 있고 리소스 공유가 필요한 다중으로 함께 배치된 컨테이너로 구성

### Deployment

-   애플리케이션을 다운타임없이 업데이트 가능하도록 지원해주는 쿠버네티스 리소스

### Demonset

-   K8S 클러스터 내 `모든 노드에서 Pod의 복제본을 실행하도록 함`
-   클러스터에 노드가 추가되면 Pod도 추가되고, 노드가 삭제되면 해당 노드에서 기동되던 Pod도 삭제됨

### Service

-   `클러스터 내 Pod들에게 접근하기 위한 방법`으로 사용
-   여러 Pod를 묶어 Healthy한 Pod로 Traffic `라우팅하는 로드 밸런싱 기능` 제공
-   묶여진 pod들이 서비스를 제공함
-   클러스터의 Service CIDR 중에서 지정된 IP로 생성 가능
-   서비스 이름은 클러스터 내 고유한 DNS로 동작

Service Type에는 어떤 것들이 있는가?
이미지

### Ingress

-   외부에서 오는 Traffic들을 클러스터 내부의 Service로 라우팅하기 위한 `규칙 집합`
-   Ingress에 대한 Service 타입 지정은 `외부 Traffic수신을 위해, NodePort 혹은 LoadBalancer 지정 필요`

-   NCR은 로드 밸런서및 공인 아이피 사용을 최소화하기 위해, 하나의 VIP에 모든 도메인을 할당
-   Ingress를 이용하여 HTTP Request의 Host와 URL Path 정보를 통해 타켓 Pod로 라우팅

### 서비스 소개

이미지

Kubernetes Control Plane에 대한 설치, 운영, 유지관리 필요 없이 쉽게 Cluster 구성 가능
클러스터 생성 시 별도의 설치 필요 없이 자동으로 설치되며, Dashboard 접속을 통해 모니터링 수행
