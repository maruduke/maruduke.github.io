---
title: \[내 코드가 그렇게 이상한가요?\] 1. 클래스 설계
excerpt: 'Class 오류 최소화 설계'

categories:
    - Book
tags:
    - [Book, JAVA]

permalink: /Book/1

toc: true
toc_sticky: true
---

## 1. 생성자로 확실하게 정상적인 값을 설정하자

아무 설정없는 클래스 생성은 나중에 NULL값 참조 오류가 발생할 여지가 크다.  
따라서 생성자를 통해 확실하게 값을 입력해 두자.

```java
class Money{
    private int money;

    Money(int amount) {

        if(amount < 0) {
            throw new IllegalArgumentException("금액이 0 이상인 값을 지정");
        }
        this.amount = amount;
    }
}
```

---

## 2. 계산 로직은 데이터를 가진 클래스에 구현하자

계산 로직을 타 클래스나 다른 곳에 구현하면 중복 발생의 원인이 될 수 있다.  
데이터를 가진 클래스 내부에 계산 로직을 구현하여 중복을 막자.

```java
class Money {
    private int amount;

    int addMoney(int add) {
        amount += add;
    }
}

```

---

## 3. 불변 변수로 만들어서 예상하지 못한 동작 막기

가변 변수로 만들경우 예상치 못한 곳에서 변형이 일어나 오류가 발생할 수 있다.  
따라서 불변변수로 만들어 오류의 가능성을 줄인다.

```java
class Money{

    final int amount;

    Money(int amount) {

        if(amount < 0) {
            throw new IllegalArgumentException("금액이 0 이상인 값을 지정");
        }
        this.amount = amount;
    }
}
```

---

## 4. 불변 변수 값을 변경하고 싶다면 새로운 인스턴스 만들자, 메서드 매개변수와 지역변수도 불변으로 만들기

불변 변수로 지정한 값을 변경하고 싶다면 새로운 본인의 인스턴스를 생성하여 값을 반환하는 형식으로 만들자.

또한 매개 변수와 지역 변수를 변경하여 오류가 발생하는 경우가 많으므로 불변 변수로 지정하여 원인을 제거하자.

```java
class Money {
    final int amount;
    final String currency;

    Money(amount, currency) {
        this.amount = amount;
        this.currency = currency;
    }

    Money add(final int other) {
        int added = amount + other;
        return new Money(added, currency);
    }
}
```

---

## 5. 엉뚱한 값을 전달하지 않도록 하기

기본 자료형 위주로 사용 시 실수로 다른 값을 전달하기 쉽다.
따라서 독자적인 자료형을 사용하여 전달 오류를 최소화한다.

```java
// 잘못된 값 전달
/* ex)
    final int ticketCount = 3;
    return Money.add(ticketCount); -> 가격이 아닌 티켓 수를 더하는 값 전달 오류
*/

class Money{

    Money add(final Money other) {
        if(!currency.equals(other.currency)) {
            throw new IllegalArgumentException("통화 단위가 다릅니다.");
        }

        final int added = amount + other.amount;
        return new Money(added, currency);

    }
}

```

---

## 6. 완전 생성자: 잘못된 상태로부터 클래스를 보호하기 위한 디자인 패턴

매개변수가 없는 디폴트 생성자로 객체 생성은 인스턴스 변수를 초기화하지 않을 가능성이 존재한다.  
인스턴스 변수를 모두 초기화해야 생성할 수 있는 생성자를 만듬

```java
class Money {
    final int amount;
    final String currency;

    Money(amount, currency) {
        this.amount = amount;
        this.currency = currency;
    }

}
```

---

## 7. 값 객체

값을 클래스(자료형)으로 나타내는 디자인 패턴.
애플리케이션에서 사용하는 금액, 날짜, 주문 수, 전화번호 등 다양한 값을 객체로 만들 수 있음.

해당 방식으로 값 혼동 오류를 최소화 할 수 있다.

```java
class Application {
    Money money;
    Day day;
    OrderNum num;
}

class Money {
    int money;
}

class day {
    String day;
}

class OrderNum {
    int num;
}
```

---
