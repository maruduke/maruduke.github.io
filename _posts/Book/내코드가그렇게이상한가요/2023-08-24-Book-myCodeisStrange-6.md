---
title: \[내 코드가 그렇게 이상한가요?\] 6. 강한 결합
excerpt: '복잡해서 풀 수 없는 구조'

categories:
    - Book
tags:
    - [Book, JAVA]

permalink: /Book/6

toc: true
toc_sticky: true

date: 2023-08-24
last_modified_at: 2024-03-11
---

## 결합도와 책무

어떤 온라인 쇼핑몰에서 할인 서비스가 추가되었습니다.

-   상품 하나당 3,000원 할인
-   최대 200,000원까지 상품 추가 가능

해당 서비스를 담당 하는 DiscountManager 클래스 구현

```java
class DiscountManager {
	boolean add(Product product, ProductDiscount productDiscount) {
    	int discountPrice = getDiscountPrice(product.price);
        ...
    }

    static int getDiscountPrice(int price) {
    	int discountPrice = price - 3000;
        ...
    }
}
```

-   DiscountManager.add 메서드 역할

    -   올바른 상품 확인
    -   getDiscountPrice로 할인 가격 계산
    -   productDiscount.canDiscount를 확인하여 할인 가능한 경우 할인 가격을 모두 더하고, 할인 불가능한 경우 원래 상품 가격을 모두 더합니다.
    -   가격 총합이 상한가인 200,000원 이내인 경우 상품 리스트 추가

### 새로운 요구사항 추가

DiscountManager를 담당하지 않은 다른 담당자가 SummerDiscountManager 클래스를 구현

```java
class SummerDiscountManager {
	...
    boolean add(Product product) {
    	if (product.canDiscount) {
        	tmp = discountManager.totalPrice + discountManager.getDiscountPrice(product.price);
            ...
        }
    }
    ...
}
```

-   올바른 상품 확인
-   할인 금액이 일반 할인과 마찬가지로 3000원 까지이므로 getDiscountPrice로 할인 가격 계산
-   productDiscount.canDiscount를 확인하여 할인 가능한 경우 할인 가격을 모두 더하고, 할인 불가능한 경우 원래 상품 가격을 모두 더합니다.
-   가격 총합이 상한가인 300,000원 이내인 경우 상품 리스트 추가

#### 다양한 버그 발생

-   일반 할인 가격을 3000원 → 4000원 변경하였을 경우

```java
class DiscountManager {
	...
    static int getDiscountPrice(int price) {
    	int discountPrice = price - 4000;
        ...
    }
}
```

-   DiscountManager 구현 담당자는 할인가를 계산하는 DiscountManager.getDiscountPrice를 변경하였으나 `여름 할인 서비스에서도 할인 가격이 4,000원`이 되어 버림

-   여름 할인 서비스를 담당하는 SummerDiscountManager에서 DiscountManager.getDiscountPrice를 사용하고 있기 때문입니다.

#### 로직의 위치에 일관성이 없음으로 일어나는 일

-   DiscountManager 가 상품 정보 말고도 너무 많은 일을 함
    Product가 직접 해야 하는 유효성 검사 로직이 DiscountManager, SummerDiscountManager 에 구현되어 있음
-   ProductDiscount.canDiscount, Product.canDiscount 의 이름이 유사해서 어떤 것이 어떤 상품인지 구분이 어려움
-   SummerDicsountManager 에서 DiscountManager 의 로직을 활용함

### 단일 책임 원칙

자신의 관심사와 관련해서, 정상적으로 동작하도록 제어하는것 → 클래스가 담당하는 책임은 하나로 제한해야 한다.

```java

// 정가 담당
class RegularPrice {

    private static final int MIN_AMOUNT = 0;
    final int amount;

    // 가격 유효성 검사 체크
    RegularPrice(final int amount) {
        if( amount < MIN_AMOUNT) {
            throw new IllegalArgumentException("가격은 0 이상이어야 함");
        }

        this.amount = amount;
    }
}

// 할인가 담당
class RegularDiscountedPrice {

    private static final int MIN_AMOUNT = 0;
    private static final int DISCOUNT_AMOUNT = 4000;
    private int amount;

    // 가격 할인 후 유효성 체크
    RegularDiscountedPrice(final RegularPrice price) {
        int discountedAmount = price.amount - DISCOUNT_AMOUNT;

        if(discountedAmount < MIN_AMOUNT) {
            discountedAmount = MIN_AMOUNT;
        }

        amount = discountedAmount;
    }
}

// 여름 할인 담당
class SummerDiscountedPrice {

    private static final int MIN_AMOUNT = 0;
    private static final int DISCOUNT_AMOUNT = 3000;
    final int amount;

    // 여름 할인 적용 후 유효성 체크
    SummerDiscountedPrice(final RegularPrice price) {
        int discountedAmount = price.amount - DISCOUNT_AMOUNT;
        if(discountedAmount < MIN_AMOUNT) {
            discountedAmount = MIN_AMOUNT;
        }

        amount = discountedAmount;
    }
}
```
