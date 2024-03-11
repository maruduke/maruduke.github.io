---
title: \[내 코드가 그렇게 이상한가요?\] 7. 설계
excerpt: '설계의 건전성을 해치는 악마들'

categories:
    - Book
tags:
    - [Book, JAVA]

permalink: /Book/7

toc: true
toc_sticky: true

date: 2023-08-25
last_modified_at: 2024-03-11
---

## Dead Code

> 절대로 실행되지 않는 조건 내에 있는 코드

-   코드 가독성 감소
-   언젠가 버그가 될 가능성이 큼

-   발견 즉시 바로 제거
-   IDE의 정적 분석 도구에는 데드 코드를 식별하는 기능이 있으니 활용

---

## YaGNI 원칙

> You Aren't Gonna Need It  
> 지금 필요 없는 기능을 만들지 마라!

-   미래를 예측하고 미리 만든 코드는 데드 코드가 됨

---

## Magic Number

> 로직 내부에 직접 작성되어, 의미를 알기 힘든 숫자

-   설명이 없는 숫자는 개발자를 혼란스럽게 함
-   따라서 상수로 변경하고 커밋

---

## 전역 변수

> -   여러 로직에서 전역 변수를 참조하고 값을 변경하면, 어디에서 어떤 시점에서 값을 변경했는지 파악하기 힘듬
> -   전역 변수를 참조하고 있는 로직을 변경해야 할 때, 해당 변수를 참조하는 다른 로직에서 버그가 발생하는지 검토 필요
> -   동기화가 필요한 경우 DeadLock 에 빠질 수 있음
> -   동기화를 하고 싶은 인스턴스 변수가 하나뿐이라고 해도, 해당 인스턴스의 다른 인스턴스 변수까지 모두 잠그므로, 성능상 문제가 큼

-   영향 범위가 최소화 되도록 설계
    -   관계 없는 로직은 접근하지 못하도록 설계

---

## NULL 문제

> -   null 이 들어갈 수 있다고 전제하고 로직을 만들면, 모든 곳에서 null 체크 필요
> -   null 체크 코드로 인한 가독성 하락
> -   실수로 null 체크 안할 시 에러 발생

-   null을 다루지 않도록 설계

    -   null을 리턴하지 않는 설계
    -   null을 전달하지 않는 설계

-   null safety
    -   null에 의한 오류가 아예 발생하지 않게 만드는 구조
    -   `null 안전 자료형`: null을 아예 저장할 수 없게 만드는 자료형

```java
val name: String = null // compile error
```

---

## 예외를 catch하고도 무시하는 코드

```java
try {
    reservation.add(product);
} catch(Exception e) {

}
```

-   원인 분석을 어렵게 만듬
    -   오류가 발생해도 탐지할 방법이 없음
    -   어느 시점에 어떤 문제가 발생했는지 찾기 어려움

---

## 설계 질서를 파괴하는 Meta programming

`Meta Programming` : 프로그램 실행 중에 해당 프로그램 구조 자체를 제어하는 프로그래밍  
`Reflaction Programming` : 프로그램에서 임의의 클래스에 접근할 수 있는 기능

### Reflaction으로 인한 Class 구조와 값 변경 문제

```java
class Level {
	private static final int MIN = 1;
    private static final int MAX = 99;
	final int value;

    private Level(final int value) {...}
    static level initialize() {...}
    Level increase() {...}
    ...
}
```

```java
Level level = Level.initialize();
Field field = Level.class.getDeclaredField('value');
field.setInt(999);
```

-   잘못된 값이 들어 갈 수 있음
-   Reflaction 을 남용하면, 오류가 어디서 발생할 지 모름

---

## 기술 중심 Packaging

> Package를 구분할 때도 폴더를 적절히 나누지 않으면 악마를 부를 수 있음

-   구조에 따라 폴더와 패키지를 나누는 것
-   비즈니스 클래스를 기술 중심 패키징에 따라 폴더를 구분하면 관련성을 알기 매우 힘듬

---

## Sample Code 복사해서 붙여넣기

> -   framework 마다 공식 사이트가 있음
> -   각 사이트의 샘플 코드를 그대로 복사하고 붙여 넣으면, 좋지 않은 구조가 되기 쉬움
> -   따라서 샘플 코드는 참고만!

---

## Silver Bullet

> -   새로운 기술은 개발 현장의 모든 문제를 해결해 줄 것처럼 보임
>     하지만 현실에서 발생하는 문제는 특정 기술 하나로 해결할 수 있을 정도로 단순하지 않음

-   어떤 문제가 있을 때, 어떤 방법이 해당 문제에 효과적인지, 비용이 더 들지는 않는지 평가하고 판단하는 자세
-   문제와 목적을 머릿속에 새겨두고 적절한 기술을 선택할 수 있도록 노력해야 함

-   Best가 아닌 Better을 목표로!

---
