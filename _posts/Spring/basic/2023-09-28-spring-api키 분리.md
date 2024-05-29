---
title: '\[Spring\] Api키 분리 보관하기'
excerpt: '키 분리를 통한 보안성 강화'

categories:
    - 'Spring_Basic'
tags:
    - [Spring, basic, Git]

permalink: /Spring_Basic/1

toc: true
toc_sticky: true

date: 2024-04-05
last_modified_at: 2024-04-05
---

Spring boot로 개발하는 도중, 수시로 갱신되는 api키의 경우 변경될 경우 위치를 찾아 일일이 수정하기 귀찮다는 문제,  
AWS와 같이 요금을 지불하는 API키를 git에 공개적으로 올려놓을 경우 문제가 발생할 수 있기 때문에 분리하여 숨겨둘 필요성을 느꼈다.

<br>

---

<br>

## 해결법

우선 키를 저장할 파일을 **`application-API-KEY.properties`**(application.yml에 같은 디렉터리 위치에) 생성하여 API 비밀키를 다음과 같이 저장한다.  
파일 이름 명명 규칙은 `application-${사용자 지정 이름}.yml` 형식이다.

```properties
## applicaiton-API-KEY.properties
lol_gg_key = API키 값

```

Spring이 생성한 파일을 인식하여 관리할 수 있도록 **`application.yml`** 파일에 다음과 같이 ${사용자 지정 이름}을 설정한다.

```yml
## application.yml
spring:
    profiles:
        include: API-KEY
```

이제 필요로 하는 컴포넌트에 해당 값을 주입시켜 확인하면 된다.  
API키를 주입시키기 위해서는 다음과 같이 @Value 데코레이터를 사용한다.

```java

    // @Value("${API_key 이름}")
    @Value("${lol_gg_key}")
    private String apiKey;

```

마지막으로 git에 해당 파일이 push되지 않도록 .gitignore파일에 추가하면 된다.

```properties
### api-key ###
src/main/resources/application-API-KEY.properties
```
