---
title: '\[NCP 실습\] Object Storage'
excerpt: 'Object Storage의 개념과 실습'

categories:
    - 'Naver Cloud Platform'
tags:
    - [NCP, Cloud]

permalink: /NaverCloud/ex/9

toc: true
toc_sticky: true

date: 2024-04-17
last_modified_at: 2024-04-17
---

## Object Storage 기본 개념

| 특성             | Object Storage                         | Block Storage                        |
| ---------------- | -------------------------------------- | ------------------------------------ |
| 데이터 처리 방식 | 데이터를 객체 단위로 저장              | 데이터를 작은 블록으로 분할하여 저장 |
| 접근방식         | RESTful API 및 웹 인터페이스 사용      | Low Level I/O, 파일 시스템 연결      |
| 스케일링         | 대규모 확장 가능, 클라우드 환경에 적합 | 제한적인 확장, 디스크 추가 필요      |
| 데이터 관리      | 메타데이터와 함께 객체로 저장          | 계층적 구조, 파일 시스템 사용        |
| 대표 서비스      | AWS S3                                 | AWS EBS                              |

### 용도

-   대규모 데이터 저장(이미지, 비디오)
-   웹 콘텐츠 및 미디어 제공
-   백엔드 데이터 저장

### 특징

-   최대 10TB 크기의 파일까지 저장 가능(API 사용시)
-   <mark>S3 Compatibility(호환) API 지원</mark>
-   NCP의 다양한 서비스와 연계
    -   Image Optimizer, Cloud Log Analytics, CDN+ 등
-   최대 1000개의 버킷(Bucket) 생성 가능
    -   Bucket: 파일 및 데이터를 저장할 수 있는 공간(최상위 폴더)

## 실습 목차

1. 기본 사용법 실습(웹 콘솔)
    1. Object Storage 이용 신청
    2. 웹 콘솔에서 버킷 생성
    3. 웹 콘솔에서 파일 업로드/ 다운로드
    4. 인증키 생성
2. S3 Browser 실습
    1. S3 브라우저 설치 및 로그인
    2. 버킷 생성 및 파일 업/ 다운로드
3. API 실습(Python 예제)
    1. Object Storage Api 호출
    2. S3 API 사용
4. 버킷 접근 제어
5. 파일 권한 관리

## 웹 콘솔 실습

### Object Storage 이용 신청

-   Services - Storage - Object Storage 클릭

<p align = "center">
    <img src="/assets/images/NCP/ex9-1.png" width="90%">
</p>

Object Storage 이용신청 클릭

<p align = "center">
    <img src="/assets/images/NCP/ex9-2.png" width="90%">
</p>

### 각종 파일을 저장할 저장소인 bucket 생성

<p align = "center">
    <img src="/assets/images/NCP/ex9-3.png" width="90%">
</p>

버킷의 이름은 리전 내에서 고유한 이름을 입력해야함

<p align = "center">
    <img src="/assets/images/NCP/ex9-4.png" width="90%">
</p>

설정 관리는 Default 값 적용

<p align = "center">
    <img src="/assets/images/NCP/ex9-5.png" width="90%">
</p>

권한 관리에서 버킷에 대한 이용 권한은 공개 안함을 선택

<p align = "center">
    <img src="/assets/images/NCP/ex9-6.png" width="90%">
</p>

최종 결과 확인 후 이상 없을 시 버킷 생성

<p align = "center">
    <img src="/assets/images/NCP/ex9-7.png" width="90%">
</p>

### 버킷 폴더 생성

생성된 버킷 오른쪽에 동그라미 버튼 열을 클릭하여 새 폴더 선택

<p align = "center">
    <img src="/assets/images/NCP/ex9-8.png" width="90%">
</p>

새로 생성할 폴더 이름을 입력

<p align = "center">
    <img src="/assets/images/NCP/ex9-9.png" width="90%">
</p>

### 파일 업로드

파일을 선택하고 파일 올리기 클릭

<p align = "center">
    <img src="/assets/images/NCP/ex9-10.png" width="90%">
</p>

파일을 드래그 앤 드롭 또는 파일 탐색기를 통하여 파일 업로드

<p align = "center">
    <img src="/assets/images/NCP/ex9-11.png" width="90%">
</p>

올린 파일을 확인하고 전송 시작 버튼을 클릭하여 업로드

<p align = "center">
    <img src="/assets/images/NCP/ex9-12.png" width="90%">
</p>

확인 결과 파일이 정상적으로 업로드 되었다.

<p align = "center">
    <img src="/assets/images/NCP/ex9-13.png" width="90%">
</p>

파일을 삭제하고 싶다면 다음과 같은 과정으로 삭제가 가능하다.
파일 폴더 선택 -> 편집 -> 삭제하기

## API 인증키 생성

Object Storage를 외부에서 접근하기 위해서는 인증키를 발급받아 인증을 받을 필요가 있다. 인증키를 발급 받는 방법은 다음과 같다.

네이버 클라우드 플랫폼 메인 포탈에 들어가서 인증키 생성

메인포탈 - 마이페이지 - 인증키 관리

<p align = "center">
    <img src="/assets/images/NCP/ex9-14.png" width="90%">
</p>

인증키 생성 클릭

<p align = "center">
    <img src="/assets/images/NCP/ex9-15.png" width="90%">
</p>

## S3 Browser 활용

<p align = "center">
    <img src="/assets/images/NCP/ex9-16.png" width="90%">
</p>

S3 Browser는 Amazon Web Services에서 제공하는 개체 스토리지 서비스인 Amazon S3를 사용자 인터페이스를 제공하여 Windows 탐색기와 유사한 방식으로 S3계정에서 파일을 관리할 수 있는 Amazon S3용 Windows 클라이언트이다.

Naver Cloud Platform의 Object Storage는 S3 Browser와 호환 가능하다.

### Account 생성

<p align = "center">
    <img src="/assets/images/NCP/ex9-17.png" width="90%">
</p>

Display name: ncp 계정 아이디 입력
Account Type: S3 Compatible Storage 선택
REST Endpoint: kr.object.ncloudstorage.com 입력
Access Key ID/ Secret Access Key: API 인증키 내용 입력

<p align = "center">
    <img src="/assets/images/NCP/ex9-18.png" width="90%">
</p>

연결이 성공적으로 이루어졌으면 다음과 bucket 파일을 확인할 수 있다.

### S3 Browser bucket 생성

### 파일 업로드

### 다운로드

다운로드 받고자 하는 파일 선택 -> 다운로드

## API 예제

스토리지 이용에 필요한 AWS S3 호환 API 제공
https://api.ncloud-docs.com/docs/storage-objectstorage

HTTP 요청을 통해 버킷 및 파일 사용 가능

-   인증 헤더 생성을 위한 별도 코드 필요
-   AWS에서 제공하는 API 사용하는게 편리

### Object Storage API 예제

https://api.ncloud-docs.com/docs/common-objectstorageapi-objectstorageapi

self.\_sign(...): 인증 헤더를 생성하는 메소드 호출 -> 메소드 내부에서 인증 헤더를 생성하는 로직 수행
request.get(...): HTTP GET 요청 수행

### 예제 실습

google colab에서 테스트

python 예제 코드 복사 붙여넣기

ObjectStorageSample 클래스의 access_key, secret_key 변수 값을 `API 인증키 생성`에서 확인한 값으로 변경

기존 소스 코드 상의 메소드 호출 부분을 주석 처리
현재 사용중인 버킷의 오브젝트 목록을 출력하도록 메소드 호출 추가

```python
sample.list_objects('버킷이름문자열')
```

코드를 실행

출력 확인 : 정상작동시 Response code: 200 출력
Response content 부분을 API 문서의 내용과 비교해 봐라

### S3 API 호출 예제

https://guide.ncloud-docs.com/docs/storage-storage-8-2

AWS SDK를 이용한 Object Storage 사용 예제 코드 제공

python 예제 중 버킷 목록 조회 코드 복사

python용 AWS SDK 설치 : 코드 셀에 !pip install boto3 입력 후 실행

앞서 복사한 코드(버킷 목록 조회) 붙여넣기
access_key, secret_key 수정
print(bucket.get('Name'))로 수정
Ctrl + Enter로 코드 셀 실행하고 출력 확인(현재 생성된 버킷 목록)

## 버킷 접근 제어

bucket management에서
버킷 정보 누르기

-   이벤트 관리: 버킷에서 이벤트 발생 시 알림(Cloud Functions 연동 시) 객체 생성, 복사 , 삭제 등
-   접근 제어: 같은 리전의 특정 서버만 버킷에 접근할 수 있도록 설정 가능(VPC 환경에서 사용)
-   권한 관리
    -   버킷 내 파일/폴더 목록의 공개 여부 설정(개별 파일 공개는 별도 설정)
    -   NCP의 다른 사용자에게 버킷 사용 권한 부여

### 버킷 접근 제어 설정

접근 제어 설정 On
linux-server1만 버킷에 접근 가능(다른 외부 접근 불가)

Object Storage API 예제 코드 재 실행
403 에러 (접근 거부) 발생
linux-server1 이외에 외부 접근 불가 확인

접근 제어 설정 다시 off

## 버킷 권한 관리

### 객체 목록 공개

-   공개 관리 - 전체 공개 - 공개 -> 버킷의 폴더/파일 목록을 누구나 볼 수 있음
-   `참고` 다른 계정 권한 추가 -> 추가된 계정은 API 인증키를 가지고 API를 통해 버킷 이용 가능

별도의 웹 브라우저 주소창에 버킷 url 입력
https://kr.object.ncloudstorage.com/{버킷명}

버킷의 폴더 및 파일 목록용 xml로 출력 확인

공개 관리 - 전체 공개 - 공개 안함

버킷의 폴더 및 파일 목록에 대한 접근 거부 확인

## 폴더 및 파일 공개

폴더 선택 -> 편집 -> 폴더/파일 공개하기

선택한 폴더/파일 공개 - 파일 - 외부에서 누구나 선택 파일 다운로드 가능 - 폴더 - 외부에서 누구나 선택 폴더 내의 모든 파일 다운로드 가능

공개된 폴더 내의 파일 선택

파일의 링크 주소 복사

공개된 파일을 비공개로 전환 후
비공개 파일 링크 주소를 웹 브라우저로 확인 시 접근 거부됨
