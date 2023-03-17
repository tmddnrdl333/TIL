## Spring Boot 3.0 이상 버전에서 QueryDsl 사용하는 방법

https://velog.io/@juhyeon1114/Spring-QueryDsl-gradle-%EC%84%A4%EC%A0%95-Spring-boot-3.0-%EC%9D%B4%EC%83%81

블로그에 자세히 설명해준 덕에  살았다...

SSAFY 1년 하면서 Spring Boot 2.몇 버전만 사용했었고 Java도 11만 사용했었는데,

Spring Boot 3에 Java 17을 쓰니까 문제가 조금 발생을 하긴 하는구나 싶다.

`QuerydslConfig`를 만들어서

`@PersistenceContext`와 `EntityManager`를 딱 입력했는데,

둘다 `javax`가 아닌 `jakarta`로 import 되는 것이 아니겠는가??

그리고 웃긴게...

`JPAQueryFactory(entityManager)` 이렇게 생성자를 써야 하는데, 정작 `JPAQueryFatory`는 `javax`의 `entityManager`만 args로 하는 생성자만 있다. 그래서 오류가 난다...

그니까 JPAQueryFactory까지 jakarta의 생성자를 지원하는 게 있던가... 아니면 전부 javax로 바꾸던가... 해야 해결되지 않겠는가?

그래서 찾아보니까...

[java - Why has javax.persistence-api been replaced by jakarta.persistence-api in spring data jpa starter? - Stack Overflow](https://stackoverflow.com/questions/60021815/why-has-javax-persistence-api-been-replaced-by-jakarta-persistence-api-in-spring)

`build.gradle`에 `spring boot starter data jpa`가 3.0 버전부터 `javax.persistence-api`를 제공하는 것이 아닌 `jakarta.persistence-api`를 제공하는 것으로 바뀌었기 때문에 그런 거라고 한다..?!

그래서 다시 Spring Boot 3.0 이상에서 QueryDsl을 사용하는 방법에 대해 구글링했더니 맨 위에 링크의 블로그를 찾을 수 있었다.

`build.gradle`에 `annotationProcessor` 두 개만 붙여주면, 오류가 없어진다!

```groovy
annotationProcessor "jakarta.annotation:jakarta.annotation-api"
annotationProcessor "jakarta.persistence:jakarta.persistence-api"
```

이렇게...
