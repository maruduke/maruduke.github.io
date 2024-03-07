---
title: \[내 코드가 그렇게 이상한가요?\] 2. 불변 활용하기
excerpt: '불변 제대로 활용하기'

categories:
    - Book
tags:
    - [Book, JAVA]

permalink: /Book/2

toc: true
toc_sticky: true

date: 2023-08-20
last_modified_at: 2023-08-20
---

## 문제점

가변 변수는 사용하기 편리하고 유용하지만, 여러 에러가 발생 가능하기 쉬운 기능이다.
가변 변수를 활용하면서 발생하는 문제는 다음과 같다.

---

<mark> 1. 가변 인스턴스 재활용하기 </mark>

다음 코드는 무기와 공격력을 나타내는 클래스로 구성되어있다.  
1개의 공격력 인스턴스를 생성하여 2개의 무기에게 해당 클래스를 주입했다.

```java
// 공격력을 나타내는 클래스
class AttackPower {
	static final int MIN = 0;
	int value; // 가변 변수

	AttackPower(int value) {
		if (value < MIN){
			throw new IllegalArgumentException();
		}
		this.value = value;
	}

}


// 무기를 나타내는 클래스
class Weapon() {
	final AttackPower attackPower;

	Weapon(AttackPower attackPower) {
		this.attackPower = attackPower;
	}
}




AttackPower attackPower = new AttackPower(20);

// 하나의 AttackPower 인스턴스 재사용하기
Weapon weaponA = new Weapon(attackPower);
Weapon weaponB = new Weapon(attackPower);


weaponA.attackPower.value = 25;

/*
재사용하고 있는 공격력을 변경할 경우 결과
weaponA.value = 25
weaponB.value = 25
*/
```

A 무기의 공격력 인스턴스를 수정하자 같은 인스턴스를 주입받은 B 무기 또한 영향을 받았다.

---

해당 문제를 해결 하기 위해 다음과 같이 개별적으로 공격력 인스턴스를 생성한다.

```java
// 공격력을 나타내는 클래스
class AttackPower {

    // ... 생략

// 공격력 인스턴스를 개별적으로 생성
AttackPower attackPowerA = new AttackPower(20);
AttackPower attackPowerB = new AttackPower(20);

Weapon weaponA = new Weapon(attackPowerA);
Weapon weaponB = new Weapon(attackPowerB);

weaponA.attackPower.value += 5;

/*
weaponA.value = 25
weaponB.value = 20

*/
```

---

<mark>2. 함수로 가변 인스턴스 조작하기 </mark>

함수로 가변 인스턴스를 조작하는 경우는 특별한 문제가 없어 보인다.  
그러나 스레드 개념을 활용하기 시작하면 명령이 겹쳐 오류가 발생한다.

```java
// 공격력을 나타내는 클래스
class AttackPower {
	static final int MIN = 0;
	int value; // 가변 변수

	AttackPower(int value) {
		if (value < MIN){
			throw new IllegalArgumentException();
		}
		this.value = value;
	}

	// 공격력을 변화시키는 reinforce, disable 메소드 추가

	/*
	* 공격력 강화하기
	* @param increment 공격력 증가량
	*/
	void reinforce(int increment) {
		value += increment;
	}

	// 무력화하기
	void disable() {
		value = MIN;
	}
}



// 처음에는 정상적으로 동작하였으나 어느 날부터 갑자기 공격력이 0이되는 일이 종종 발생
// 원인 조사결과 다른 스레드에서 사용되어 공격력을 0이 되는 함수 호출
```

## 대안

앞의 문제를 최소화하기 위한 방안은 다음과 같다.

---

### 불변 변수로 만들어 재할당 막기

가변 변수로 만들경우 `예상치 못한 곳에서 변형(멀티 스레드 환경)`이 일어나 오류가 발생할 수 있다.  
따라서 불변 변수로 만들어 변형을 못하도록 한다.

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

### 매개 변수도 불변으로 만들기

전달되는 매개 변수와 지역 변수 또한 로직 중간에 변경되어 오류가 발생하는 경우가 많으므로 불변 변수로 지정한다.

```java
class Money {

    Money add(final int other) {
        int added = amount + other;
        return new Money(added, currency);
    }
}
```

---

### 함수의 영향 범위 한정하기

-   `주요 작용`

    -   함수가 매개변수를 전달 받고, 값을 리턴하는 것

-   `부수 효과`
    -   함수가 매개변수를 전달 받고 값을 리턴하는 것 이외에 외부 상태를 변경하는 것

부수 효과가 있는 함수는 영향 범위를 예측하기 어려움으로 함수가 영향을 주거나 받는 범위를 한정하는 것이 좋음

함수는 다음 항목을 만족하도록 설계

-   데이터는 매개변수로 받음
-   상태를 변경하지 않음
-   값은 함수의 리턴 값으로 돌려줌

따라서 매개 변수로 상태를 받고, 상태는 변경하지 않고, 값을 리턴하기만 하는 함수가 가장 이상적이다.

---

### 불변으로 만들어서 예기치 못한 상황 막기

위의 원칙들을 적용하여 불변 변수로 만든 코드는 다음과 같다.
메서드는 불변 변수를 받고 불변 인스턴스를 반환한다.

```java
// 공격력을 나타내는 클래스
class AttackPower {
	static final int MIN = 0;
	final int value; // 가변 필요 변수

	AttackPower(int value) {
		if (value < MIN){
			throw new IllegalArgumentException();
		}
		this.value = value;
	}

	// 공격력을 변화시키는 reinforce, disable 메소드 추가

	/*
	* 공격력 강화하기
	* @param increment 공격력 증가량
	* @return 증가된 공격력
	*/
	AttackPower reinforce(final AttackPower increment) {
		return new AttackPower(this.value + increment.value);
	}

	/*
	* 무력화하기
	* @return 무력화된 공격력
	*/
	AttackPower disable() {
		return new AttackPower(MIN);
	}
}


// 무기를 나타내는 클래스 개선 버전

class Weapon() {
	final AttackPower attackPower;

	Weapon(final AttackPower attackPower) {
		this.attackPower = attackPower;
	}

	Weapon reinforce(final AttackPower increment) {
		final AttackPower reinforced = attackPower.reinforce(increment);
		return new Weapon(reinforced);
	}
}


// 정리

final AttackPower attackPowerA = new AttackPower(20);
final AttackPower attackPowerB = new AttackPower(20);

final Weapon weaponA = new Weapon(attackPowerA);
final Weapon weaponB = new Weapon(attackPowerB);

final AttackPower increment = new AttackPower(5);
final Weapon reinforceWeaponA = weaponA.reinforce(increment);
```

---

### 상태를 변경하는 메서드 설계하기

-   상태를 변화시키는 메서드를 mutater이라고 함.
-   조건에 맞는 올바른 상태로 변경하는 mutater로 수정

```java

class HitPoint {

    private static final int MIN = 0;
    int amount;

    HitPoint(final int amount) {
        if (amount < MIN) {
            throw new IllegalArgumentException();
        }
        this.amount = amount;
    }


    /**
    * 대미지 받는 처리
    * @param damageAmount 데미지 크기
     */

    void damage(final int damageAmount) {
        final int nextAmount = amount - damageAmount;
		// 조건 체크
        amount = Math.max(MIN, nextAmount);
    }

    /* @return 히트포인트가 0이라면 true */
    boolean isZero() {
        return amount == MIN;
    }
}

class Member {

    final HitPoint hitPoint;
    final States states;
    // 생략

    /**
     * 데미지 받는 처리
     * @param damageAmount 데미지 크기
     */
    void damage(final int damageAmount) {
        hitPoint.damage(damageAmount);
		// 조건 체크
        if(hitPoint.isZero()) {
            states.add(StateType.dead);
        }
    }

}
```

---

### 기본적으로 불변으로 성능 중시 시 가변으로

-   기본적으로 불변으로

    -   변수의 의미가 변하지 않아 혼동이 적음
    -   동작이 안정적이기 때문에 결과 예측이 쉬움
    -   코드의 영향 범위가 한정적이므로 유지 보수가 쉬움

-   가변이 필요한 경우
    -   성능이 중요한 경우(크기가 큰 인스턴스 생성은 시간이 오래 걸림)

---

### 코드 외부와 데이터 교환은 국소화

-   특별한 이유 없이 외부 상태에 의존하는 코드를 작성할 경우, 동작 예측이 힘들어지므로 문제가 발생할 가능성이 높음
-   파일을 읽고 쓰는 I/O 조작은 코드 외부의 상태에 의존
