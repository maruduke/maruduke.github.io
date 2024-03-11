---
title: \[내 코드가 그렇게 이상한가요?\] 5. 컬렉션
excerpt: '중접을 제거하는 구조화 테그니션'

categories:
    - Book
tags:
    - [Book, JAVA]

permalink: /Book/5

toc: true
toc_sticky: true

date: 2023-08-23
last_modified_at: 2024-03-11
---

### 이미 존재하는 기능을 다시 구현하지 않기

```java
boolean hasPrisonKey = false;

for (Item each : items) {
    if(each.name.equals("감옥 열쇠")) {
        hasPrisonKey = true;
        break;
    }
}
```

-   같은 기능을 다음과 같이 구현할 수 있다.

```java
boolean hasPrisonKey = items.stream().anyMatch({
	item -> item.name.equals("감옥열쇠")
});
```

-   anymatch 메서드는 컬렉션 전용 메서드로 조건을 만족하는 요소가 하나라도 존재하면 true를 리턴합니다.
-   표준 컬렉션 라이브러리에는 anyMatch이외에도 편리한 메서드가 존재한다.
    for 반복문을 사용해 컬렉션을 직접 조작하고 있다면 같은 기능을 하는 메서드를 찾아보시오

### 반복 처리 내부의 조건 분기 처리

```java
for (Member member : members) {
	if (0 < member.hitPoint) {
    	if (member.containsState(stateType.poison)) {
        	member.hitPoint -= 10;
            if (member.hitPoint <= 0) {
            	member.hitPoint = 0;
            }
        }
    }
}
```

-   조기 continue, break문을 활용하여 중첩을 제거하기

```java
for (Member member : members) {
	if (member.hitPoint == 0) continue;
    if (!member.containsState(StateType.poison)) continue;

    member.hitPoint -= 10;

    if (0 < member.hitPoint) continue;

    member.hitPoint = 0;
}
```

### 응집도가 낮은 컬렉션 처리

```java
// 필드 맵과 관련된 제어 담당하기 클래스
class FieldManager {

    // 멤버를 추가합니다.
    void addMember(List<Memeber> members, Member newMember) {

        if( members.stream().anyMatch(member -> member.id == newMember.id)) {
            throw new RuntimeException("이미 존재하는 멤버입니다.");
        }

        if( members.size() == MAX_MEMBER_COUNT) {
            throw new RuntimeException("이 이상 멤버를 추가할 수 없습니다.");
        }

        members.add(newMember);
    }

    // 파티 멤버가 1명이라도 존재하는 true를 리턴
    boolean partyIsAlive(List<Member> members) {
        return members.stream().anyMatch(member -> member.isAlive());
    }
}
```

-   FieldManager는 게임에서 필드 맵을 관리하는 클래스입니다.
-   파티에 멤버를 추가하는
    addMember메서드와 파티에 살아 있는 멤버가 있는지 리턴하는 partyIsAlive가 정의되어 있다.

-   그런데 필드 맵 말고도 멤버를 추가하는 시점이 존재한다.

```java
// 게임 중에 발생하는 특별 이벤트를 제어하는 클래스

class SpecialEventManager {
	// 멤버를 추가합니다.
	void addMember(List<Member> members, Member member) {
		members.add(member);
	}
}

```

-   멤버 추가하는 메서드가 중복된다.
-   마찬가지로 fieldManager의 다른 로직도 중복 구현 될 가능성이 존재한다.
-   컬렉션과 관련된 작업을 처리하는 코드가 여기저기 구현되어 응집도가 낮아지는 현상 발생한다.

#### 해결 방법: 컬렉션 처리를 캡슐화하기

-   응집도가 낮아지는 문제는 일급 컬렉션 패턴을 사용해 해결할 수 있다.
-   일급 컬렉션이란 컬렉션과 관련된 로직은 캡슐화하는 디자인 패턴
    -   컬렉션 자료형의 인스턴스 변수에 잘못된 값이 할당되지 않게 막고, 정상적으로 조작하는 메서드 구현

```java
import java.util.ArrayList;

class Party {

    private final List<Member> members;

    Party() {
        members = new ArrayList<Member>();
    }

    Party add(final Member newMember) {
        List<Member> adding = new ArrayList<>(members);
        adding.add(newMember);
        return new Party(adding);
    }
}
```

### 외부로 전달할 때 컬렉션의 변경 막기

-   인스턴스 변수를 그대로 외부에 전달하면 외부에서 마음대로 조작될 가능성이 존재하므로 collection요소를 변경하지 못하도록 불변으로 만들어줌

```java
class Party {
/** 멤버리스트(외부에서 변경 못하는 불변 리스트*/
	List<Member> members() {
		return members.unmodifiableList();
	}
}
```
