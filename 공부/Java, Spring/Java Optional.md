# Java Optional

## 1. Optional이란?

### NullPointerException

개발을 할 때 가장 많이 발생하는 예외 중 하나. 이를 피하려면 null 체크를  해야 하는데, null 체크를 일일히 할 경우 코드가 복잡해지고 번거로워진다. 그래서 null 대신 초기값 사용을 권장하기도  한다.

### Optional

Java8부터 `Optional<T>`를 사용해 NPE를 방지하도록 도와준다. `Optional<T>`는 null이 올 수 있는 값을 감싸는 Wrapper 클래스로, 참조하더라도 NPE가 발생하지 않도록 도와준다. Optional 클래스는 아래와 같이 value에 값을 저장하기 때문에, 값이 null이더라도 바로 NPE가 발생하지 않으며, 클래스이기 때문에 각종 메서드를 제공한다.

```java
public final class Optional<T> {
    
// If non-null, the value; if null, indicates no value is present
private final T value;

...

}
```

## 2. Optional 활용

### Optional.empty() - 값이 null인 경우

생성할 때 이렇게 생성할 수도 있다.

```java
Optional<String> optional = Optional.empty();


System.out.println(optional); // Optional.empty
System.out.println(optional.isPresent()); // false
```

### Optional.of() - 값이 null이 아닌 경우

절대 null이 아닌 값을 of로  생성할 수 있다. 만약 Null로 저장하려고 하면 NPE가 발생한다!

```java
Optional<String> oprional = Optional.of("MyName");
```

### Optional.ofNullable() - 값이 null일 수도, 아닐수도 있는 경우

```java
Optional<String> optional = Optional.ofNullable(getName());
String name = optional.orElse("annonymous"); // 값이 없다면 annonymous 출력
```

### 예시 1

```java
// Java8 이전
List<String> names = getNames();
List<String> tempNames = list != null 
    ? list 
    : new ArrayList<>();

// Java8 이후
List<String> nameList = Optional.ofNullable(getNames())
    .orElseGet(() -> new ArrayList<>());
```

### 예시 2

```java
// before
public String findPostCode() {
    UserVO userVO = getUser();
    if (userVO != null) {
        Address address = user.getAddress();
        if (address != null) {
            String postCode = address.getPostCode();
            if (postCode != null) {
                return postCode;
            }
        }
    }
    return "우편번호 없음";
}

// after
public String findPostCode() {
    // 위의 코드를 Optional로 펼쳐놓으면 아래와 같다.
    Optional<UserVO> userVO = Optional.ofNullable(getUser());
    Optional<Address> address = userVO.map(UserVO::getAddress);
    Optional<String> postCode = address.map(Address::getPostCode);
    String result = postCode.orElse("우편번호 없음");

    // 그리고 위의 코드를 다음과 같이 축약해서 쓸 수 있다.
    String result = user.map(UserVO::getAddress)
        .map(Address::getPostCode)
        .orElse("우편번호 없음");
}
```

### 정리

Wrapper 클래스이다. Wrapping 하고 다시 풀고, null일 경우 대체 함수를 호출하는 등의 오버헤드를 감수한다. 때문에 잘 사용해야 한다.

즉, Optional 메서드의 결과가 null이 될 수 있으며, null에 의해 오류가 발생할 가능성이 높을 때 반환 값으로만 사용해야 한다.



## 3. Optional의 orElse와 orElseGet의 차이

### orElse와 orElseGet의 차이

- orElse는 파라미터로 값을 받고,

- orElseGet은 파라미터로 (인터페이스)함수를 받는다.

아래를 보면, 첫 함수는 값이 비어있을 때 orElse를 호출하도록 되어있고,

두번째 함수는 orElseGet을 호출한다.

```java

public void findUserEmailOrElse() {
    String userEmail = "Empty";
    String result = Optional.ofNullable(userEmail)
    	.orElse(getUserEmail());
        
    System.out.println(result);
}

public void findUserEmailOrElseGet() {
    String userEmail = "Empty";
    String result = Optional.ofNullable(userEmail)
    	.orElseGet(this::getUserEmail);
        
    System.out.println(result);
}

private String getUserEmail() {
    System.out.println("getUserEmail() Called");
    return "mangkyu@tistory.com";
}
```

결과는 아래와 같다!

```java
// 1. orElse
getUserEmail() Called
Empty

// 2. orElseGet
Empty
```

orElse는 값을 받는다고 했다. 때문에 괄호 안에 있는 getUserEmail이 실행돼서 값을 먼저 만들고 파라미터로 값을 전달한다. 때문에 print가 실행된 것이다.

반면에 orElseGet은 인터페이스, 함수가 전달됐기 때문에, null일 경우에 함수가 실행될 뿐이지, null이 아닌 위와 같은 경우에는 실행이 안되는 것이다.



이와같은 차이가 있기 때문에? 문제가 없다 하더라도, orElse보다 orElseGet이 비용이 적으니 orElse의 사용은 최대한 피하는게 좋겠다.



[[Java] Optional이란? Optional 개념 및 사용법 - (1/2) - MangKyu's Diary](https://mangkyu.tistory.com/70)


