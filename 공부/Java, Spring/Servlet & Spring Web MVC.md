# Servlet & Spring Web MVC

## Servlet

### Servlet의 등장

기존 어플리케이션의 두 가지 문제점을 해결하기 위해 Servlet이 등장하게 됐다.

1. 각 요청에서 Process를 매번 생성하던 걸, Thread로 변경해서 리소스 소모가 큰 문제를 해결.

2. 동일 요청이라도 매번 구현체를 생성하던 문제는, 싱글톤 패턴을 활용해서 하나의 구현체를 재사용하도록 변경하여 해결.
- 서버가 부담해야 할 리소스들을 줄이면서 보다 효율적으로 동적 데이터를 클라이언트에게 제공할 수 있게 됨.

다시 정리하자면...(그뭔십?

- 서블릿(Servlet)이란, 동적 웹페이지를 만들 때 사용되는 자바 기반의 웹 애플리케이션 프로그래밍 기술이다.

### 실질적 이점?

일일히 요청 값을 파싱하고 응답 메시지를 만들어주는 일련의 과정... 이 번거로움을 덜기 위해, 많은 API를 제공함. 사실상 만들어둔 API를 그대로 활용하면 많은 기능을 쓸 수 있다는 점이 개발자에게는 가장 큰 실질적 이득...

### Servlet Container

Servlet의 생성, 호출, 제거 등 모든 작업을 관리하면서 생명주기를 담당함.

클라이언트 요청 시 매핑되는 서블릿이 있으면 부르고, 없으면 생성함. 다 쓰면 소멸하지 않고 일단 관리함. 이런 흐름으로 관리...

하지만?

1. 요청마다 1:1 매칭이기 때문에 공통 로직이 반복 수행될 수 있음.

2. Servlet 의존적인 코드를 낳음.

어떻게 해결할 수 있을까?

클라이언트의 요청을 받는 서버 최 앞단에 Front Controller를 둔다. (Front Controller Pattern)

요청마다 각 서블릿을 사용하는 것이 아닌, 하나의 서블릿을 통해 요청을 수행할 수 있게 되어서, 중복 로직이 제거되고, 관리도 수월해진다!

Front Controller의 역할을 정리해보자.

1. 클라이언트 - 서버 연결

2. 각 요청에 맞는 컨트롤러를 매핑하여 정보를 보관

3. 요청이 들어오면 매핑 정보를 찾아 해당 컨트롤러를 호출

4. 전달할 결과를 생성

5. 결과를 사용자에게 반환

역할이 너무 많은데... 그럼 분리해보자.

1. 요청 핸들러(컨트롤러) 검색

2. 객체를 만들어 요청 정보를 전달(Parameter, Model)

3. ViewName반환

4. ViewName과 일치하는 뷰 검색

5. Model에 대한 데이터를 서블릿에 담아 응답

## Spring Web MVC

위의 내용은 그래서 뭐냐... 그걸 Framework로 구현한게? Spring Web MVC이다\~

- Front Controller는 Dispatcher Servlet이 되었고,

- 컨트롤러나 핸들러를 호출하기 전에 중간에 어뎁터 역할을 해주는 Handler Adapter라는 기능을 통해 다양한 종류의 핸들러를 호출해주는 기능을 추가했다.

그래서 뭐가 좋아졌냐?

DispatcherServlet 내부에는 Servlet WebApplicationContext와 Root WebApplicationContext로 나뉘는데,

Servlet WebApplicationContext에는 기존과 같이 웹 요청을 담당하는 객체들이 관리된다. (Controllers, ViewResolver, HandlerMapping)

Root WebApplicationContext에는 서비스 계층이나 Repository 등, 환경에 독립적인 빈들이 들어가서 관리된다. (Services, Repositories) (필요시 DispatcherServlet이 알아서 주입한다.)

## 정리

이렇게 Spring Web MVC에서 제공하는 DispatcherServlet 웹 요청 처리 관련 구현체들을 사용하면, 개발자들은 실제로 작성해야 하는 핸들러, 즉 비즈니스 로직에만 집중할 수 있는 환경이 주어진 셈이다!


