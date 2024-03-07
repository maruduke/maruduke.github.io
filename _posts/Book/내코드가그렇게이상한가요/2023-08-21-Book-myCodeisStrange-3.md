---
title: \[내 코드가 그렇게 이상한가요?\] 3. 응집도
excerpt: '응집도'

categories:
    - Book
tags:
    - [Book, JAVA]

permalink: /Book/3

toc: true
toc_sticky: true

date: 2023-08-21
last_modified_at: 2024-03-07
---

## 응집도

-   코드 모듈의 독립성을 나타내는 개념으로, 모듈 내부 구성요소 간 연관 정도
-   하나의 모듈이 하나의 기능을 수행하는 것을 의미
-   응집도가 높을수록 중복 기능이 생성되는 것을 방지할 수 있다.

다음으로 응집도가 낮아지는 설계의 원인에 대해 분석해보겠다.

### 응집도 ⬊: static 메서드 오용

// 데이터와 로직이 서로 다른 클래스에 작성하는 문제 발생

/\*

1.  static 메서드는 인스턴스 변수를 사용할 수 없음
    -> 데이터와 데이터를 조작하는 로직 괴리 발생
    \*/

//4. 왜 static 메서드를 사용할까?
/_
객체지향 언어를 사용할 때 절차 지향 언어의 접근 방법을 사용하려 하기 때문
클래스의 인스턴스를 만들지 않아도 되므로, 간단하게 사용할 수 있으나 응집도가 낮아짐
_/

```java
class OrderManager() {

    static int add(int moneyAmount1, int moneyAmount2) {
        return moneyAmount1 + moneyAmount2;
    }
}

moneyData1.amount = OrderManager.add(moneyData1.amount, moneydata2.amount);
```

```java
// 2. 인스턴스 변수를 사용하는 구조로 변경하기

// 3. 인스턴스 메서드 인척하는 static 메서드 주의하기
class OrderManager() {

		private int discountRate // 할인율
    int add(int moneyAmount1, int moneyAmount2) {
        return moneyAmount1 + moneyAmount2;
    }
}
// 인스턴스 메서드이나 인스턴스 변수 discountRate를 전혀 사용하지 않음
// 매개변수로 받은 값만 활용하므로 static메서드와 차이가 없음
// 메서드 앞에 static 키워드를 추가하여 잘 작동된다면 인스턴스 static 메소드 구분 가능함

```

> -   어떠한 상황에서 static메서드를 사용해야 좋을까?

> 응집도에 영향을 받지 않는 `로그 출력 전용 메서드, 포맷 변환 전용 메서드, 팩토리 메서드 등`을 static메서드로 설계

---

## 2. 초기화 로직 분산

```java
class GiftPoint {
    private static final int MIN_POINT = 0;
    final int value;

    GiftPoint(final int point) {
        if(point < MIN_POINT) {
            throw new IllegalArgumentException("포인트를 0 이상 입력");
        }

        value = point;
    }

    /*
     * 포인트 추가하기
     *
     * @param other 추가포인트
     * @return 추가 후 남은 포인트
     */

     GiftPoint add(final GiftPoint other) {
        return new GiftPoint(value + other.value);
     }

    /*
     * @return 남은 포인트가 소비 포인트 이상이라면 true
     */
    boolean isEnough(final ConsumptionPoint point) {
        return point.value <= value;
    }

    /*
     * 포인트 소비하기
     *
     * @param point 소비포인트
     * @return 소비후 남은 포인트
     */
    GiftPoint consume(final ConsumptionPoint point) {
        if(!isEnough(point)) {
            throw new IllegalArgumentException("포인트 부족");
        }

        return new GiftPoint(value - point.value);
    }

}

// giftpoint 클래스에는 포이트를 추가하는 메서드와 소비하는 메서드가 정의되어 있으므로,
// 응집도가 높아 보이나 사실 그렇지 않음

GiftPoint standardMembershipPoint = new GiftPoint(3000);

GiftPoint premiumMembershipPoint = new GiftPoint(10000);

//생성자를 public으로 만들면, 의도하지 않은 용도로 사용될 수 있음.
//결과적으로 로직이 분산되기 때문에 유지 보수하기 힘들어짐
// 회원 가입 포인트를 변경하기 원할 때 소스 코드 전체 확인 필요

```

1. private 생성자 + 팩토리 메서드를 사용해 목적에 따라 초기화하기

```java
// 초기화 로직 분산을 막으려면 생성자를 private로 만들고, 대신 목적에 따라 팩토리 메서드
// 로 만들기

class GiftPoint {
    private static final int MIN_POINT = 0;
    private static final int STANDARD_MEMBERSHIP_POINT = 3000;
    private static final int PREMIUM_MEMBERSHIP_POINT = 3000;
    final int value;

    // 외부에서는 인스턴스 생성할 수 없음
    private GiftPoint(final int point) {
        if(point < MIN_POINT) {
            throw new IllegalArgumentException("포인트를 0 이상 입력");
        }

        value = point;
    }

    /*
     * @return 표준 가입 기프트 포인트
     */
    static GiftPoint forStandardMembership() {
        return new GiftPoint(STANDARD_MEMBERSHIP_POINT);
    }

    /*
     * @return 프리미엄 가입 기프트 포인트
     */
    static GiftPoint forPremiumMembership() {
        return new GiftPoint(PREMIUM_MEMBERSHIP_POINT);
    }

}
```

## 3. 범용 처리 클래스(Common/Util)

```java
/*
	static 메서드를 빈번하게 볼 수 있는 클래스로, 범용 처리를 위한 클래스가 존재
	일반적으로 이런 클래스에는 Common, Util이라는 이름이 붙
*/

class Common() {
	static ~~~
}
```

횡단 관심사

-   꼭 필요한 경우가 아니면 범용 처리 클래스를 만들지 않는 것이 좋음
-   but, 로그 출력과 오류 확인은 모든 동작에서 필요한 기능 이처럼, 넓게 활용되는 기능을 횡단 관심사라고 부른다.
    -   로그 출력
    -   오류 확인
    -   디버깅
    -   예외처리
    -   캐시
    -   동기화
    -   분산 처리

## 4. ⬈ 결과를 리턴하는데 매개변수 사용하지 않기

```java
// 매개 변수 변경하고 있음
void shift(Location location, int shiftX, int shiftY) {
	location.x += shiftX;
	location.y += shiftY;
}

// -> 매개 변수가 변경되었음을 외부에서 알 수 없음
// 매개 변수를 변경하지 않는 구조로 개선

class Location {
	final int x;
	final int y;

	Location(final int x, final int y) {
		this.x = x;
		this.y = y;
	}

	Location shift(final int shiftX, final int shiftY) {

		final int nextX = x + shiftX;
		final int nextY = y + shiftY;

		return new Location(nextX, nextY);

	}
}
```

## 응집도 ⬊: 매개변수가 너무 많은 경우

매개변수가 너무 많은 메서드는 응집도가 낮아짐

→ 기본 자료형에 대한 집착을 버려라

→ 중복 코드가 다양해짐

```java
// 정가라는 구체적인 자료형 설계

class RegularPrice {
	final int amount;

	/*
		@param amount 금액
	*/
	RegularPrice(final int amount) {
		if (amount < 0) {
			throw new IllegalArgumentException();
		}
	}

	this.amount = amount;
}

class DiscountedPrice{
	final int amount;

	/*
		@param regularPrice 정가
		@param discountRate 할인율
	*/

	DiscountedPrice(final RegularPrice regularPrice, final DiscountRate discountRate) {
		// regularPrice와 discountRate를 사용하여 계산
	}
}

// 의미 있는 단위는 모두 클래스로 만들기
// 매개 변수가 너무 많으면 개념적으로 의미 있는 클래스를 만들어야 함

/*매직 포인트*/
class MagicPoint {
	// 현재 잔량
	int currentAmount;
	// 원래 최댓값
	int originalMaxAmount;
	// 장비 착용에 따른 최댓값 증가량
	List<Integer> maxIncrements;
}

// 이렇게 코드를 만들면, 최댓값 계산 로직과 회복 로직이 다른 클래스에도 작성될 수 있음
// 따라서 매직포인트 최댓값 계산과 회복 메서드를 MagicPoint클래스에 정의
// 이때 다른 클래스에서 불필요한 조작 금지를 위해 인스턴스 변수는 private로 만듬

class MagicPoint {
	// 현재 잔량
	private int currentAmount;
	// 원래 최댓값
	private int originalMaxAmount;
	// 장비 착용에 따른 최댓값 증가량
	private List<Integer> maxIncrements;

	/* @return 현재 매직포인트 잔량*/
	int current() {
		return currentAmount;
	}

	/* @return 매직포인트 최댓값*/
	int max() {
		int amount = originalMaxAmount;
		for (int each : maxIncrements) {
			amount += each;
		}
		return amount;
	}

	/*
		매직 포인트 회복하기
		@param recoveryAmount 회복량
	*/

	void recover(final int recoveryAmount) {
		currentAmount = Math.min(currentAmount + recoveryAmount, max());
	}

	/*
		매직포인 소비하기
		@param consumeAmount 소비량
	*/

	void consume(final int consumeAmount) { ... }
}
```

## 6. 묻지 않고 명령하기

다른 객체의 내부 상태를 기반으로 판단하거나 제어하려 하지 말고, 메서드로 명령해서 객체가 알아서 판단하고 제어하도록 설계하라는 의미

```java
class Equipments {
    private boolean canChange;
    private Equipment head;
    private Equipment armor;
    private Equipment arm;

    /*
     * 갑옷 착용하기
     *
     * @param newArmor 장비할 갑옷
     */
    void equipArmor(final Equipment newArmor) {
        if(canChange) {
            armor = newArmor;
        }
    }

    /*
     * 전체 장비 해제하기
     */
    void deactivateAll() {
        head = Equipment.EMPTY;
        armor = Equipment.EMPTY;
        arm = Equipment.EMPTY;
    }

}
```
