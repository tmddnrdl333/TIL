# Spring JPA FetchType.Lazy로 인한 오류와 FetchType.Eager

오류가 발생해서 구글링으로 해결했다.

오류 발생 지점을 보여주자면...



## 발단

상품의 목록을 조회하려고 상품을 findAll하고 JSON으로 return받은 값을 getList API 콜 했는데, 갑자기 `InvalidDefinitionException`이 발생하면서, `No serializer found for class ...` 이런 오류 문구가 떴다. (물론 500에러)

serializer라는 단어에서 뭔가 JSON으로 불러오는 과정에서 문제가 생긴 걸 알긴 했는데, 이해하기 위해 내가 만든 Entity들을 간략하게 보면...

```java
public class Item {
    ...

    @ManyToOne(fetch = FetchType.LAZY)
    @JoinColumn(name = "category_no", nullable = false)
    private Category category;

    ...
}
```

```java
public class Category {
    @Id
    @Column(nullable = false)
    private int no;
    @Column(nullable = false)
    private String name;
}
```

이렇게 아이템에 카테고리라는 엔티티를 ManyToOne으로 넣었는데...

(`FetchType.LAZY`는 이전 프로젝트에서 팀원들이 이렇게 하는게 성능적으로도 보기로도 좋다고 해서 그냥 따라서 썼음... ~~앞으로는 잘 알고 쓰자.~~)

이 상태에서 Item의 List를 조회했더니? 앞서 말한 오류가 발생했다.



## FetchType.LAZY와 FetchType.EAGER

1. 지연로딩 (FetchType.LAZY)

A Entity(부모)를 조회할 때 지연로딩지정된 B Entity(자식)를 즉시 로딩하지 않고, A.getB()로 실제 B엔티티를 호출할 때 database로부터 해당 B엔티티를 조회해오는 방식

2. 즉시로딩 (FetchType.EAGER)

A Entity(부모)를 조회할 때 즉시로딩지정된 B Entity(자식)를 즉시 로딩하는 방식.



## 해결 방법

해결방법은 하나가 아니라 여러가지이다.

application.properties에 `spring.jackson.serialization.fail-on-empty-beans=false` 를 추가하는 방식도 있다고는 하는데, 이는 표면적인 방법이지 근본적인 해결방법이 아니기 때문에 넘어가자.

아래 두 가지 중 상황에 따라 취사선택할 필요가 있다.

1. C, U, D 등의 상황에서(필요에 따라) JSON response에서 자식객체를 제외하고 보내자. (`@JsonIgnore`)

```java
@ManyToOne(fetch = FetchType.LAZY)
@JoinColumn(name = "category_no", nullable = false)
@JsonIgnore
private Category category;
```

이렇게 작성하면, API로 불러온 List 중에, Category는 생략되어 반환받게 된다. 즉, Read하는 상황에 이 Category가 필요한 변수라면 사용하면 안되는 방법이 되겠다.

하지만 C, R, D와 같은 상황에서 성공여부만 확인하고 싶다면 안 쓸 이유도 없다. 그래서 상황에 따라 선택하면 될 것 같다.

2. fetchType자체를 EAGER로 변경한다.

```java
@ManyToOne(fetch = FetchType.EAGER)
@JoinColumn(name = "category_no", nullable = false)
private Category category;
```

이렇게 작성하면... category_no 뿐만 아니라 category 전체를 불러오니 category_name까지 불러오게 된다.

즉, nested된 JSON 형태로 response를 받게 된다.



## 결론

1번 방법과 2번 방법은 위에 쓴 대로, 상황에 따라 유동적으로 선택해야 한다.

이번 프로젝트와 같은 경우에는, Category는 no와 name 이 두 개만 멤버변수로 가지고 있기 때문에, EAGER타입으로 받기로 했다.

다른 부분은 아직 확인 못했지만, Category와 같이 아주 작은 건 EAGER로 바꿔도 될 것 같고, 좀 더 큰 건 어떻게 사용되는지에 따라 또 선택하게 될 것 같다.
