# Java var

var는 Java 10에 도입됐다.

변수를 선언할 때 타입을 생략할 수 있으며, 컴파일러가 타입을 추론한다.

`var string = "Hello World";`라고 하면 컴파일러가 알아서 String 타입을 추론하여 변수에 타입을 지정해준다.

컴파일 타임에 추론을 하기 때문에, Runtime에 추가 연산을 하지 않아 성능에 영향을 주지는 않는다.

var는 지역 변수에서만 사용할 수 있다.

## 변수 선언

위와 같이 String 뿐 아니라 다른 클래스들도 가능하다.

`var list = new ArrayList<String>();`

## 반복문

```java
int[] arr = {1,2,3};
for ( var n : arr ) {
    // logic
}
```

## 제약사항

- 지역변수에서만 사용할 수 있다.
  
  - 클래스의 멤버변수와 같은 때에는 사용할 수없다. (compile error!)

- 초기화가 필요하다.
  
  - 타입을 추론할 수 있도록 명시적으로 초기화를 해줘야 한다!
  
  - null로 초기화할 수 없다!

- 배열에 사용할 수 없다.
  
  - `var arr = { 1, 2, 3 };` 과 같은 상황도 타입 추론을 할 수 없기 때문에 안된다



추가적으로 더 자세한 내용은 이하 문서를 확인한다.

[Java 10 에서 var 재대로 사용하기 - DEV Community 👩‍💻👨‍💻](https://dev.to/composite/java-10-var-3o67)


