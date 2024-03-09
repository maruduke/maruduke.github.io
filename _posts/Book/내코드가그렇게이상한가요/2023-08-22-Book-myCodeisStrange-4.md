---
title: \[내 코드가 그렇게 이상한가요?\] 4. 조건 분기
excerpt: '복잡한 분기 처리 무너뜨리기'

categories:
    - Book
tags:
    - [Book, JAVA]

permalink: /Book/5

toc: true
toc_sticky: true

date: 2023-08-22
last_modified_at: 2024-03-08
---

## 조건 분기

if 조건문은 다양한 장소에서 사용 가능한 기능이지만
조건문을 중접할 경우 가독성이 낮아지는 문제가 발생한다.
가독성 문제를 해결하기 위한 방안으로는 다음과 같다.

### 1. 조기 리턴으로 중첩 제거하기

미리 종료 조건을 리턴을 함으로써 가독성 향상

```java
if (member.hitPoint <= 0) return;
if (!member.canAct()) return;

~~~
```

---

### 2. else구문도 조기 리턴으로 해결

```java
// 기존 방식
if(hitPointRate == 0) return HealthCondition.dead;
else if(hitPointRate < 0.3) return HealthCondition.danger;
else if(hitPointRate < 0.5) return HealthCondition.caution;
else return HealthCondition.fine;

// 개선 방식
if(hitPointRate == 0) return HealthCondition.dead;
if(hitPointRate < 0.3) return HealthCondition.danger;
if(hitPointRate < 0.5) return HealthCondition.caution;

return HealthCondition.fine;

```

---

### 3. 인터페이스로 switch 조건문 중복 해소

```java
//switch 조건문을 사용하여 코드 작성

 enum MagicType {
	fire,
	lighting,
	hellfire
}


switch(magicType) {
	case fire:
		magicPoint = 2;
		break;
	case lightning:
		magicPoint = 5 + (int) (member.level * 0.2);
		break;
}

return magicPoint;

```

해당 코드에 요구사항 변경 시 case문을 같이 수정해야 하므로, 수정 누락(case 구문 추가 누락) 문제 등이 발생한다.

-   주요 문제
    1. 조건 추가 누락 등 여러 요소 발생
    2. 폭발적으로 늘어나는 switch 조건문 중복 발생으로 가독성 하락 문제

해결 방안: `인터페이스로 해결`

코드의 전체적인 구조를 다음과 같이 수정한다.

<img src="/assets/images/book/myCodeisStrange/4-1.jpeg" text-align = 'center' width= "70%">

<!-- ![View](/assets/images/book/myCodeisStrange/4-1.jpeg) -->

1. 상위 인터페이스 정의

```java
interface Magic {
	String name();
	int costMagicPoint();
	int attackPower();
	int costTechnicalPoint();
}
```

2. 인터페이스를 상속 받은 클래스 생성

```java
class Fire implements Magic {
	private final Member member;

	Fire(final Member member) {
		this.member = member;
	}

	public String name() {
		return "파이어";
	}

	public int costMagicPower() {
		return 20 + (int) (member.level * 0.5);
	}

	public int costTechnicalPoint() {
		return 0;
	}
}

```

3. switch 조건문이 아니라, Map 자료구조로 변경하고 Map에 객체 입력

```java
final Map<Type, Magic> magics = new HashMap<>();
// 생략

final Fire fire = new Fire(member);
final Lightning lightning = new Lightning(member);

magics.put(MagicType.fire, fire);
magics.put(MagicType.lightning, lightning);

// 마법 공격력 변경하기
// 메서드의 매개변수로 magictype.hellfire을 전달하면 호출됨
void magicAttack(final MagicType magicType) {
	final Magic usingMagic = magics.get(magicType);
	usingMagic.attackPower();
}
```

4. 기존 switch문을 인터페이스로 마법 처리 변경하기

```java
final Map<Type, Magic> magics = new HashMap<>();

// ...생략

// 마법 공격 실행하기
void magicAttack(final MagicType magicType) {
	final Magic usingMagic = magics.get(magicType);

	showMagidName(usingMagic);
	consumeMagicPoint(usingMagic);
	consumeTechnicalPoint(usingMagic);
	magicDamage(usingMagic);
}


// 마법 이름을 화면에 출력하기

void showMagidName(final Magic magic) {
	final String name = magic.name();
}

void consumeMagicPoint(final Magic magic) {
	final int costMagicPoint = magic.costMagicPoint();
}

void consumeTechnicalPoint(final Magic magic) {
	final int costTechnicalPoint = magic.costTechnicalPoint();
}


void magicDamage(final Magic magic) {
	final int attackPower = magic.attackPower();
}
```

5. 값 객체 도입하기

```java
class Fire implements Magic {
	private final Member member;

	Fire(final Member member) {
		this.member = member;
	}

	public String name() {
		return "파이어";
	}

	public int costMagicPower() {
		final int value = 0 + (int) (member.level * 0.5)
		return new AttackPower(value);
	}

	public int costTechnicalPoint() {
		return new TechnicalPoint(0);
	}
}
```

---

### 4. 조건 분기 중복과 중첩

-   다중 중첩된 복잡한 분기를 제거하기 위해 사용

메서드로 판별 값 반환

```java
/*
 * @return 골드 회원이라면 true
 * @param history 구매 이력
 */

 boolean isGoldCustomer(PurchaseHistory history) {

    if(1000000 <= history.totalAmount) {
        if(10<= history.purchaseFrequencyPerMonth) {
            if (history.returnRate <= 0.001) {
                return true;
            }
        }
    }
    return false;
 }

/*
 * @return 실버 회원이라면 true
 * @param history 구매 이력
 */

 boolean isSilverCustomer(PurchaseHistory history) {

  if(10<= history.purchaseFrequencyPerMonth) {
      if (history.returnRate <= 0.001) {
           return true;
      }
  }

    return false;
 }
```

-   전략 패턴으로 조건 집약하기

-   여러 등급이 추가될 경우 같은 판정 로직이 여러 곳에 작성될 것. 같은 판정 로직을 재사용하려면 정책 패턴이 있습니다.

-   조건을 부품으로 만들고, 부품으로 만든 조건을 조합해서 사용하는 패턴

1. 인터페이스로 조건을 만듬.
2. 만든 조건을 map 자료구조에 저장
3. 저장된 조건을 검수 및 확인

```java
interface ExcellentCustomerRule {
	/*
		@param history 구매 이력
		@return 조건을 만족하는 경우 true
	*/
	boolean ok(final PurchaseHistory history);
}

// 골드 등급 조건
class GoldCustomerPurchaseAmountRule implements ExcellentCustomerRule {
	public boolean ok(final PurchaseHistory history) {
		return 1000000000 <= history.totalAmount;
	}
}

// 구매 조건
class PurchaseFrequencyRule implements ExcellentCustomerRule {
	public boolean ok(final PurchaseHistory history) {
		return 10 <= history.purchaseFrequencyPerMonth;
	}
}

// 환불 조건
class ReturnRateRule implements ExcellentCustomerRule {
	public boolean ok(final PurchaseHistory history) {
		return history.returnRate <= 0.001;
	}
}

// 전략 패턴
class ExcellentCustomerPolicy {
	private final Set<ExcellentCustomerRule> rules;

	ExcellentCustomerPolicy() {
		rules = new HashSet();
	}

/*
    규칙을 모두 map에 넣는다.
	규칙 추가
	@param rule 규칙
*/
	void add(final ExcellentCustomerRule rule) {
		rules.add(rule);
	}

/*
	@param history 구매 이력
	@return 규칙을 모두 만족하는 경우 true
*/
	boolean complyWithAll(final PurchaseHistory history) {
		for (ExcellentCustomerRule each : rules) {
			if(!each.ok(history)) return false;
		}
		return true;
	}
}

// 해당하는 규칙을 추가 후 확인 과정 거침
```

---

### 5. 자료형 확인에 조건 분기 사용하지 않기

-   문제 상황

```java
// 자료형 확인 방식

interface HotelRates {
	Money fee();
}

class RegularRates implements HotelRates{
	public Money fee() {
		return new Money(700000);
	}
}

class PremiumRates implements HotelRates{
	public Money fee() {
		return new Money(1200000);
	}
}

// 전략 패턴으로 숙박 요금 전환 가능
// 성수기 때 일반 숙식과 프리미엄 요금을 상향하는 로직 급하게 구현

Money busySeasonFee;

if(hotelRates instanceof RegularRates) {
	busySeasonFee = hotelRates.fee().add(new Money(30000));
}

else if(hotelRates instanceof PremiumRates) {
	busySeasonFee = hotelRates.fee().add(new Money(50000));
}

```

-   instanceof는 자료형을 판정하는 연산자
    인터페이스 구현 클래스의 자료형을 확인해서 분기하고 있음. (인터페이스를 사용하였는데 조건 분기 발생)

-   리스코프 치환규칙(기반 자료형을 하위 자료형으로 변경해도 코드는 문제없이 동작) 위반
-   인터페이스의 의미를 이해하지 못하고 사용시 이와 같은 로직 발생

#### 개선 방식

```java
// 성수기 요금도 인터페이스로 변경 성수기 요금을 리턴하는 busySeasonFee메서드 추가

interface HotelRates {
	Money fee();
	Money busySeasonFee(); // 성수기 요금
}

class RegularRates implements HotelRates{
	public Money fee() {
		return new Money(700000);
	}

	public Money busySeasonFee() {
		return fee().add(new Money(30000));
	}
}

class PremiumRates implements HotelRates{
	public Money fee() {
		return new Money(1200000);
	}

	public Money busySeasonFee() {
		return fee().add(new Money(50000));
	}
}

Money busySeasonFee = hotelRates.busySeasonFee();


// 조건 분기를 써야 하는 상황에는 일단 인터페이스 설계를 떠올리자!
```

---

### 6. 플래그 매개변수

메서드의 기능을 전환하는 boolean 자료형의 매개 변수를 플래그 매개변수라고 부른다. 플래그를 사용한 코드는 다음과 같다.

```java
void damage(boolean damamgeFlag, int damageAmount) {

    if(damageFlag == true) {
        //물리 데미지(히트포인트 기반 데미지)
        member.hitPoint -= damageAmount;
        if(0 < member.hitPoint) return;

        member.hitPoint = 0;
        member.addState(StateType.dead);
    }

    else {
        // 마법 데미지(매직포인트 기반 데미지)
        member.magicPoint -= damageAmount;
        if(0 < member.magicPoint) return;

        member.magicPoint = 0;
    }
}

```

앞의 코드는 damageFlag로 물리 대미지인지 마법 대미지인지 구분하고 있음

-   문제점

    -   해당 메서드는 어떤 일을 하는지 예측하기 어렵다.
    -   예측을 위해 메서드 내부 로직 확인 필요

-   해결방법: 메서드 분리하기
    -   메서드는 하나의 기능만 하도록 설계
    -   따라서 플래그 매개변수를 받는 메서드는 기능별로 분리하는 것이 좋음

해결 방법을 적용한 코드는 다음과 같다. 기능별로 메서드를 나누고, 메서드에 맞는 이름을 붙이면 가독성 상승한다.

```java
void hitPointDamage(final int damageAmount) {

    member.hitPoint -= damageAmount;
    if( 0< member.hitPoint) return;

    member.hitPoint = 0;
    member.addState(StateType.dead);
}

void magicPointDamage(final int damageAmount) {

    member.magicPoint -= damageAmount;
    if( 0 < member.magicPoint) return;
		member.magicPoint = 0;
}


```

구현한 메서드를 인터페이스를 활용하여 전략 패턴으로 전환한다.

```java


// 데미지를 나타내는 인터페이스
interface Damage{
    void execute(final int damageAmount);
}


//히트 포인트 대미지

class HitpointDamamge implements Damage{
    // 생략

    public void execute(final int damageAmount) {
        member.hitPoint -= damageAmount;
        if ( 0 < member.hitPoint ) return;

        member.hitPoint = 0;
        member.addState(StateType.dead);
    }
}

class MagicPointDamage implements Damage{

    public void execute(final int damageAmount) {
        member.magicPoint -= damageAmount;
        if ( 0 < member.magicPoint ) return;

        member.magicPoint = 0;
    }
}


// enum와 Map으로 변경합니다.

enum DamageType {
    hitPoint,
    magicPoint
}

private final Map<DamageType, Damage> damages;


void applyDamage(final DamageType damageType, final int damageAmount) {
    final Damage damage = damages.get(damageType);
    damage.execute(damageAmount);
}
```
