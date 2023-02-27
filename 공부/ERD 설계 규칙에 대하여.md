# ERD 설계 규칙에 대하여...

## 1. 테이블명은 snake case로

java에선 camel case를 주로 쓰지만 (CamelCase)

DB나 API에서는 snake case를 많이 사용한다고 한다. (snake_case)

## 2. 테이블명은 복수형, 컬럼명은 단수형으로

논리적인 상황을 잘 따져야겠지만, 일반적인 상황에서 위와 같이 지킨다.

예를 들어 drinks 라는 테이블 아래 컬럼으로 coke를 넣는다던지...

## 3. 기타

- 테이블명은 직관적으로!

- 데이터는 정확하게

- 약어도 지양!

- 각 테이블 pk를 타 테이블이 참조할 때, 테이블명\_id 이런식으로 외래키 참조한다.

## 4. varchar & char

가볍게 생각했지만, 이 부분에선 짧지 않은 역사가 있다는 걸 알았다.

우선, 가장 간단하게 보자면 varchar는 가변길이이고, char는 고정길이이다.

varchar는 추가로직이 발생하기 때문에 느리다고 생각할 수 있다.

하지만... [OKKY - char대신 varchar를 쓰는이유??](https://okky.kr/articles/217655) 이 글의 댓글에서도 논쟁이 많았는데, 결국 많은 실무자들의 지지를 얻었던 댓글은 butnim님의 주장이다. 요약하자면 아래와 같다.

> 기존에는 고정길이 데이터 타입만 있었고, 이걸 따라서 쓰던 사람들이 있었지만, 많은 문제들이 발생한다. 글자가 깨지거나, 비교가 안되서 trim 떡칠, 스페이스처럼 보이지만 ascii로 된 오염된 데이터가 발생하고... 때문에 아직도 추세는 char + varchar2를 선호한다고 한다.
> 
> 수백명이 뿜어대는 sql이 수천개쯤 되면, 프로젝트 관리자 관점에서는 리스크와 리소스 관리가 생명이다. 간단하게 varchar2를 표준으로 하면 그런 문제를 싸그리 없앨 수 있다.
> 
> (후략)

그래서 단순하게 정리하자면... 나처럼 개인 프로젝트에서는 사실 상관이 없겠지만, 어차피 varchar를 쓰는 편이 낫다고 보고 실무에서도 그렇게 쓰는 쪽이 많은가보다... 그래서 나도 그냥 varchar를 쓰자\~

## 5. DATE, DATETIME, TIME, TIMESTAMP

[mysql DATE, DATETIME, TIME, TIMESTAMP 차이 : 네이버 블로그](https://m.blog.naver.com/nieah914/221810697040)

- DATE: YYYY-MM-DD

- DATETIME: YYYY-MM-DD HH:MM:SS

- TIME: HH:MM:SS

- TIMESTAMP: 1970-01-01 00\:00:01 ~ 2038-01-19 03\:14:07 UTC

DATETIMTE vs. TIMESTAMP

- DATETIME은 문자형, 8byte, 입력시 생성

- TIMESTAMP는 숫자형, 4byte, 저장시 자동 생성
