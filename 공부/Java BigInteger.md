# Java BigInteger

2022-12-12 작성

알고리즘 문제를 풀다보면 Java가 불리한 경우가 굉장히 많다...

대표적으로 문자열 파싱에 유리한 Python에 비해 Java는 상대적으로 코드가 복잡해지고, 배열관리도 그런 걸로 알고 있다.

또 이번에 공부하며 알게된 건데... Python은 int 자료형의 크기 범위가 무제한이다.

(이게 가능한 이유는... C나 Java와 다르게 Python은 메모리의 숫자 정보가 담긴 객체를 가리키는 포인터로서 변수를 생성하기 때문이다... 라고만 이해를 했고, 자세한 건 다음 링크를 참고하자. https://velog.io/@toezilla/1D1Q-001.-Python의-int-자료형은-어떻게-범위가-무제한일까)

아무튼 각설하고, 문제를 풀다보면 숫자 범위를 굉장히 신경써야 하는데... 2의 63승이 넘어가면 long 타입으로도 커버가 안된다. 그래서 BigInteger를 써야 한다.

## 선언

```java
BigInteger value1 = new BigInteger("100");

BigInteger value2 = BigInteger.valueof(100);
```

## 사칙연산

> 참고로 value로 시작하는 변수는 BigInteger로 선언된 객체라고 가정한다.

```java
value1 = value2.add(BigInteger.valueof(1));
```

```java
value1 = value2.subtract(BigInteger.TEN);
```

```java
value1 = value2.multiply(BigInteger.TWO);
```

```java
value1 = value2.divide(BigInteger.TWO);
```

## 그 밖의 메서드

### 값 비교

```java
value1.compareTo(BigInteger.TWO);
```

> 같으면 0, 앞이 크면 1, 뒤가 크면 -1을 반환한다.

### 나머지

```java
value1.remainder(BigInteger.TWO);
```

### 최대, 최소

```java
value1.max(BigInteger.TWO);

value1.min(BigInteger.TWO);
```
















