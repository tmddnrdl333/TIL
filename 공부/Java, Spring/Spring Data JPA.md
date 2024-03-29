# Spring Data JPA

## 영속성 컨텍스트

- 엔티티를 영구 저장하는 환경

- Application과 DB 사이에서 객체를 보관하는 가상의 데이터베이스와 같은 역할.

- 엔티티 매니저를 통해 엔티티를 저장하거나 조회하면, 엔티티 매니저는 영속성 컨텍스트에 엔티티를 보관하고 관리한다.

- `em.persist(member)` 엔티티 매니저를 사용해 회원 엔티티를 영속성 컨텍스트에 저장한다는 의미.

## 특징

- 엔티티 매니저를 생성할 때 영속성 컨텍스트가 하나 만들어진다.

- 엔티티 매니저를 통해 영속성 컨텍스트에 접근하고 관리할 수 있다.

## 엔티티의 생명주기

- 비영속(new/transient): 영속성 컨텍스트와 전혀 관계가 없는 상태

- 영속(managed): 영속성 컨텍스트에 저장된 상태

- 준영속(detached): 영속성 컨텍스트에 저장되었다가 분리된 상태

- 삭제(removed): 삭제된 상태

- 비영속
  
  - 엔티티 객체를 생성했지만 아직 영속성 컨텍스트에 저장하지 않은 상태.
  
  - `Member member = new Member();`

- 영속
  
  - 엔티티 매니저를 통해 엔티티를 영속성 컨텍스트에 저장한 상태를 말하며 영속성 컨텍스트에 의해 관리된다는 뜻.
  
  - `em.persist(member);`

- 준영속
  
  - 영속성 컨텍스트가 관리하던 영속 상태의 엔티티를 더이상 관리하지 않으면, 준영속 상태가 된다. `em.detach()`로 만든다.
  
  - `em.detach(member);` 엔티티를 영속성 컨텍스트에서 분리해 준영속 상태로 만든다.
  
  - `em.clear();` 영속성 컨텍스트를 비워도 관리되던 엔티티는 준영속 상태가 된다.
  
  - `em.close();` 영속성 컨텍스트를 종료해도 그렇게 된다.
  
  - 특징
    
    - 1차 캐시, 쓰기 지연, 변경 감지, 지연 로딩을 포함한 영속성 컨텍스트가 제공하는 어떠한 기능도 동작하지 않는다.
    
    - 식별자 값을 가지고 있다.

- 삭제
  
  - 엔티티를 영속성 컨텍스트와 데이터베이스에서 삭제한다.
  
  - `em.remove(member);` 

## 영속성 컨텍스트의 특징

- 영속성 컨텍스트의 식별자 값
  
  - 영속성 컨텍스트는 엔티티를 식별자 값으로 구분한다. 따라서 영속 상태는 식별자 값이 반드시 있어야 한다.

- 영속성 컨텍스트와 데이터베이스 저장
  
  - JPA는 보통 트랜잭션을 커밋하는 순간 영속성 컨텍스트에 새로 저장된 엔티티를 데이터베이스에 반영하는데, 이를 `flush`라 한다.

## 영속성 컨텍스트가 엔티티를 관리할 때의 장점

- 1. 1차 캐시
  
  2. 동일성 보장
  
  3. 트랜잭션을 지원하는 쓰기 지연
  
  4. 변경 감지
  
  5. 지연 로딩

- 1차 캐시
  
  - 영속성 컨텍스트 내부에는 캐시가 있는데, 이를 1차 캐시라고 한다. 영속 상태의 엔티티를 이곳에 저장한다. 1차 캐시의 키는 식별자 값(DB의 기본키)이고 값은 엔티티 인스턴스이다. 조회하는 방법은 다음과 같다.
  
  - `Member member = em.find(Member.class, "member1");`
  
  - 조회의 흐름:
    
    1. 1차 캐시에서 엔티티를 찾는다.
    
    2. 있으면 메모리에 있는 1차 캐시에서 엔티티를 조회한다.
    
    3. 없으면 데이터베이스에서 조회한다.
    
    4. 조회한 데이터로 엔티티를 생성해 1차 캐시에 저장한다.(엔티티를 영속상태로 만든다)
    
    5. 조회한 엔티티를 반환한다.

- 영속 엔티티의 동일성 보장
  
  - 영속성 컨텍스트는 엔티티의 동일성을 보장한다.
  
  - `Member a = em.find(Member.class, "member1");`
  
  - `Member b = em.find(Member.class, "member1");`
  
  - `System.out.print(a==b)` true!

- 트랜잭션을 지원하는 쓰기 지연(transactional write-behind)
  
  - `em.find(member)`를 사용해 member를 저장해도 바로 INSERT SQL이 DB에 보내지는 것이 아니다. 엔티티 매니저는 트랜잭션을 커밋하기 직전까지 내부 쿼리 저장소에 INSERT SQL을 모아둔다. 그리고 트랜잭션을 커밋할 때 모아둔 쿼리를 DB에 보낸다. 이것을 트랜잭션을 지원하는 쓰기 지연이라 한다.

- 변경 감지
  
  - JPA로 엔티티를 수정할 때는 단순히 엔티티를 조회해서 데이터를 변경하면 된다.
  
  - 변경 감지의 흐름
    
    1. 트랜잭션을 커밋하면 엔티티 매니저 내부에서 먼저 플러시가 호출된다.
    
    2. 엔티티와 스냅샷을 비교하여 변경된 엔티티를 찾는다.
    
    3. 변경된 엔티티가 있으면 수정 쿼리를 생성해서 쓰기 지연 SQL 저장소에 저장한다.
    
    4. 쓰기 지연 저장소의 SQL을 flush한다.
    
    5. 데이터베이스 트랜잭션을 커밋한다.

## 플러시

- 플러시는 영속성 컨텍스트의 변경 내용을 데이터베이스에 반영한다. 영속성 컨텍스트의 엔티티를 지우는게 아니라 변경 내용을 데이터베이스에 동기화하는 것이다.

- 플러시의 흐름
  
  1. 변경 감지가 동작해서 스냅샷과 비교해서 수정된 엔티티를 찾는다.
  
  2. 수정된 엔티티에 대해서 수정 쿼리를 만들고 SQL 저장소에 등록한다.
  
  3. 쓰기 지연 SQL 저장소의 쿼리를 데이터베이스에 전송한다.

- 플러시하는 방법
  
  1. `em.flush()`
  
  2. 트랜잭션 커밋시 자동  호출
  
  3. JPQL 쿼리 실행시 자동 호출


