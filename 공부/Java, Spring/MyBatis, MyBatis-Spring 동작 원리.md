# MyBatis, MyBatis-Spring 동작 원리

## 0. MyBatis란?

- Java객체와 SQL문을 자동으로 Mapping해주는 ORM(Object Relation Mapping) 프레임워크
  
  - 접근성이 좋고, 코드가 간결해진다. (jdbc의 모든 기능을 제공하기 때문에, jdbc 코드를 많은 부분 대체할 수 있다.)
  
  - 오픈소스이고 무료이다.
  
  - 하지만 테이블이 변경되고 DTO가 변경될 때마다 매핑에 대한 부분을 다시 수정해줘야하는 번거로움이 있다.

## 1. MyBatis3의 구조

### 1-1. MyBatis3의 DB Access 구조

- 컴포넌트
  
  - MyBatis configuration file (xml)
    
    - DB 연결 정보, Mapping 파일 경로 등 환경정보 설정
  
  - org.apache.ibatis.session.SqlSessionFactoryBuilder
    
    - SqlSessionFactory를 생성하는 역할
  
  - org.apache.ibatis.session.SqlSessionFactory
    
    - MyBatis의 전역 정보를 가지고 실행을 제어 (Application당 하나만 생성 권장)
    - SqlSession 생성하는 역할
  
  - org.apache.ibatis.session.SqlSession
    
    - SQL 실행 및 트랜잭션 관리를 실행하는 핵심 클래스
    
    - Thread-Safe하지 않기 때문에 여러 thread에서 공유할 경우 오류 발생
  
  - mapper xml 파일
    
    - SQL문, OR Mapping 설정
  
  - mapper java(interface) 파일
    
    - mapper xml(SQL)을 호출하는 인터페이스
    
    - MyBatis3가 인터페이스에 대한 구현체를 자동 생성해주기 때문에, 개발자는 인터페이스만 작성하면 된다. 

- DB에 Access하는 순서
  
  1. Application이 SqlSessionFactoryBuilder에게 SqlSessionFactory를 빌드하도록 요청한다.
  
  2. SqlSessionFactoryBuilder는 SqlSessionFactory를 생성하기 위해, MyBatis config file을 통해 환경정보를 얻는다.
  
  3. SqlSessionFactoryBuilder가 SqlSessionFactory를 생성한다.
     (여기까지의 과정은 1회 시행, 이후 과정은 요청이 들어올 때마다 수행)
  
  4. 클라이언트가 Application에게 요청을 한다.
  
  5. Application은 SqlSessionFactory에서 SqlSession을 가져온다.
  
  6. SqlSessionFactory가 SqlSession을 생성하고 Application에게 반환한다.
  
  7. Application이 SqlSession에서 Mapper 인터페이스의 구현체를 얻는다.
  
  8. Application이 Mapper 인터페이스의 메서드를 호출한다.
  
  9. Mapper 인터페이스의 구현체가 SqlSession 메서드를 호출하고 SQL 실행을 요청한다.
  
  10. SqlSession은 Mapper xml 파일에서 실행할 SQL을 가져와 SQL을 실행한다.

### 1-2. MyBatis-Spring의 DB Access 구조

- 컴포넌트
  
  - org.mybatis.spring.SqlSessionFactoryBean - SqlSessionFactory를 생성하고 Spring DI 컨테이너에 개체를 저장하는 컴포넌트 (표준 MyBatis3에서는 SqlSessionFactory가 MyBatis config file을 필요로 하지만, SqlSessionFactoryBean을 사용하면 config file 없이도 SqlSessionFactory를 빌드할 수 있다.)
  
  - org.mybatis.spring.mapper.MapperFactoryBean - Singleton Mapper 개체를 만들고 Spring DI 컨테이너에 개체를 저장하는 컴포넌트 (표준 MyBatis3에 의해 생성된 매퍼 객체는 Thread safe하지 않아서 각 thread에 대한 인스턴스를 할당해야 했지만, MyBatis-Spring에 의해 생성된 매퍼 객체는 Thread safe하다. 따라서 Singleton 컴포넌트에 DI를 적용할 수 있다.)
  
  - org.mybatis.spring.SqlSessionTemplate - SqlSession 인터페이스를 구현하는 Singleton 버전의 SqlSession 컴포넌트 (마찬가지로 표준 MyBatis3에 의해 생성된 SqlSession 개체는 Thread safe하지 않지만 MyBatis-Spring에서는 safe하다.)

- DB에 Access하는 순서
  
  1. SqlSessionFactoryBean은 SqlSessionFactoryBuilder에게 SqlSessionFactory를 빌드하도록 요청한다.
  
  2. SqlSessionFactoryBuilder는 SqlSessionFactory를 빌드하기 위해 MyBatis config file을 읽는다.
  
  3. SqlSessionFactoryBuilder는 MyBatis config file에 따라 SqlSessionFactory를 생성한다. 생성된 SqlSessionFactory는 Spring DI 컨테이너에 저장된다.
  
  4. MapperFactory Bean은 Thread safe한 SqlSession(SqlSessionTemplate)와 매퍼 개체(Mapper 인터페이스의 프록시 객체)를 생성한다. 역시 Spring DI 컨테이너에 저장된다.
     (여기까지의 과정은 1회 시행, 이후 과정은 요청이 들어올 때마다 수행)
  
  5. 클라이언트가 Application에게 요청을 한다.
  
  6. Application은 DI 컨테이너에서 주입한 매퍼 개체의 메서드를 호출한다.
  
  7. 매퍼 객체는 호출된 메서드에 해당하는 SqlSession 메서드를 호출한다.
  
  8. SqlSession(SqlSessionTemplate)은 안전한 SqlSession 메서드를 호출한다.
  
  9. SqlSession은 트랜잭션에 할당된 MyBatis3 표준 SqlSession을 사용한다. 트랜잭션에 할당된 SqlSession이 존재하지 않을 경우 SqlSessionFactory 메서드를 호출하여 표준 MyBatis3의 SqlSessin을 가져온다.
  
  10. SqlSessionFactory는 MyBatis3 표준 SqlSession을 반환한다. 반환된 MyBatis3 표준 SqlSession이 트랜잭션에 할당되기 때문에 동일한 트랜잭션 내에 있는 경우 새 SqlSession을 생성하지 않고 동일한 SqlSession을 사용한다. on 메서드를 호출하고 SQL 실행을 요청한다.
  
  11. MyBatis3 표준 SqlSession은 매핑 파일에서 실행할 SQL을 가져와서 실행한다.


