# Spring Boot에서 JPA 사용하기

[Spring_Boot에서__JPA_사용하기](https://velog.io/@swchoi0329/Spring-Boot%EC%97%90%EC%84%9C-JPA-%EC%82%AC%EC%9A%A9%ED%95%98%EA%B8%B0)

위 블로그에 자세히 설명되어 있다...

학습한 내용만 요약해보자.

## Entity 생성

```java
@Entity
@Getter
@NoArgsConstructor
public class Entity {

    @Builder
    public Entity( [args] ) {
        this. [args] = [args];
    }
}
```

블로그는 이렇게 생성하고 있다.

나도 Builder가 필요하긴 한데... Entity 위에 @Builder를 쓰고 싶다. 근데 그러려면 @AllArgsContructor도 있어야만 한다. 때문에 아래와 같이 고쳐 썼다.

```java
@Entity
@Getter
@Builder
@NoArgsConstructor
@AllArgsConstructor
(후략)
```

여기서...

예전 내가 참여한 프로젝트 중, @Data 어노테이션을 쓴 적이 있는 걸 확인했는데,

> @Data = @Getter + @Setter + @RequiredArgsConstructor + @ToString + @EqualsAndHashCode

블로그에서 강조한 것이, **setter**를 쓰지 말라는 것이다. 자바 빈 규약을 생각하면서 getter setter를 무작정 생성하는 경우 클래스의 인스턴스 값들이 언제 변경되는지 명확하게 알 수 없다는 것이 그 이유이다. 따라서, **Entity 클래스에서는 절대 Setter 메소드를 만들지 않는다.**

## [Entity]Repository 생성

- JpaRepository\<Entity 클래스, PK 타입> 을 상속하는 인터페이스, Repository를 만든다.
  
  - 기본적인 CRUD는 자동으로 생성된다.

```java
public interface UserRepository extends JpaRepository<User, Integer> {
    (생략)
}
```

- **@Repository**를 추가할 필요가 <u>없다</u>!

- Entity 클래스와 기본 Entity Repository는 함께 위치해야 한다. <- 고 하는데... 지금까지 그렇게 안하긴 했다.

## API에 대해

API를 만들기 위해 총 3개의 클래스가 필요하다.

1. Request 데이터를 받을 Dto

2. API 요청을 받을 Controller

3. 트랜잭션, 도메인 기능 간 순서를 보장하는 Service

(Service는 트랜잭션, 도메인 간 순서 보장의 역할만 한다.)

- Web Layer
  
  - 컨트롤러(@Controller)와 JSP/freemarker 등 뷰 템플릿 영역
  
  - 필터(@Filter), 인터셉터, 컨트롤러 어드바이스(@ControllerAdvice) 등 **외부 요청과 응답**에 대한 전반적 영역

- Service Layer
  
  - @Service
  
  - 일반적으로 Controller와 Dao의 중간 영역에서 사용됨
  
  - @Transactional이 사용되어야 하는 영역이기도 하다.

- Repository Layer
  
  - **Database**와 같이 데이터 저장소에 접근하는 영역
  
  - Dao(Data Access Object) 영역

- Dtos
  
  - Dto(Data Transfer Object)는 계층 간 데이터 교환을 위한 객체

- Domain Model
  
  - 도메인이라 불리는 개발 대상을 모든 사람이 동일한 관점에서 이해할 수 있고 공유할 수 있도록 단순화시킨 것을 도메인 모델이라고 한다.
  
  - @Entity가 사용된 영역이 도메인 모델이다.

Web, Service, Repository, Dto, Domain 이 5 레이어에서 비지니스 처리를 담당하는 곳은? 바로 **Domain**이다.

기존에 서비스로 처리하던 방식을 트랜잭션 스크립트라고 한다. 주문 취소 로직을 기존 방식으로 하면 아래와 같다. (Service에서)

```java
@Transactional
public Order cancelOrder(int orderId) {
    // 1) DB로부터 주문정보(Orders), 결제정보(Billing), 배송정보(Delivery) 조회
    OrderDto order = ordersDao.selectOrders(orderId);
    BillingDto billing = billingDao.selectBilling(orderId);
    DeliverDto delivery = deliveryDao.selectDelivery(orderId);
    // 2) 배송 취소를 해야 하는지 확인
    String deliveryStatus = delivery.getStatus();
    // 3) if(배송 중) 배송취소
    if("IN-PROGRESS".equals(deliveryStatus)){
        delivery.setStatus("CANCEL");
        deliveryDao.update(delivery);
    }
    // 4) 각 테이블에 취소 상태 Update
    order.setStatus("CANCEL");
    ordersDao.update(order);
    // 5)
    billing.setStatus("CANCEL");
    billingDao.update(billing);

    return order;
}
```

모든 로직이 서비스 클래스 내부에 처리된다. 그렇게 되면 서비스 계층이 무의미해지고, 객체란 단순히 데이터 덩어리의 역할만 하게 된다.

반명, 도메인 모델에서 처리할 경우 아래와 같아진다.

```java
@Transactional
public Order cancelOrder(int orderId) {
    // 1)
    Order order = orderRepository.findById(orderId);
    Billing billing = billingRepository.findById(orderId);
    Deliver delivery = deliveryRepository.findById(orderId);

    // 2-3)
    delivery.cancel();

    // 3)
    order.cancel();
    billing.cancel();

    return order;
}
```

위와 같이, order, billing, delivery가 각자 본인의 취소 이벤트를 처리하면, 서비스 메소드는 트랜잭션과 도메인 간의 순서만 보장해주면 된다.

 
