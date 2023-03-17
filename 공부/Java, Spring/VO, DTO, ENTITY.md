# VO, DTO, ENTITY

## VO (value object)

- 값 객체
- 값 비교를 위해 사용됨. equals 와 hashCode를 override함.
- 오버라이드 결과로 VO는 값이 동일하면 같은 객체로 봄.
- Immutable한 불변 객체로 사용됨. 따라서 Setter는 존재하지 않고 Getter만 존재함. 그래서 lombok으로 @Data를 달아선 안됨.

## DTO (data transfer object)

- 데이터 전송 객체
- JSON형태 데이터 통신을 위해 Serialize해서 쓰는 것이 바로 DTO, Controller에서 return하는 것도 DTO.

## ENTITY

- 실제 DB테이블과 매칭되는 클래스.
- Entity를 Parameter로 받거나 리턴해서는 안된다! (영속성을 깨는 행위!)
- Entity와 DTO를 분리하는 이유는 Layer별 역할을 분리하고 Entity의 영속성을 지키기 위해.
- 이론적으로는 둘을 분리하는 것이 맞지만, 실무적으로 간단한 CRUD를 만들 때 Entity를 그대로 리턴하기도 한다. DB테이블과 View에서 사용하는 객체가 완전 동일하다면 동일한 두 객체를 만드는 것이 로직적으로 낭비일 수도 있기 때문이다.
