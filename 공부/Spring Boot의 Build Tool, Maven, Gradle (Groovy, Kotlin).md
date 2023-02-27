## Spring Boot의 Build Tool, Maven & Gradle (Groovy/Kotlin)

[[Java] Gradle, Groovy Gradle, Kotlin Gradle — 일단은 내 이야기](https://kdhyo.kr/87)

위 블로그에 잘 나와있지만, 요약해본다.



- Maven 특징
  
  - xml 사용
  
  - Apache 라이센스로 배포되는 오픈소스 소프트웨어
  
  - 자바용
  
  - 직접 연결한 라이브러리들과 라이브러리들이 엮여있는 다른 라이브러리들까지 연동되어 관리



-  Gradle 특징
  
  - 오픈소스 기반 빌드 자동화 시스템
  
  - JVM기반 빌드도구로, Ant와 Maven을 보완
  
  - Android OS의 빌드 도구로 채택
  
  - Groovy, Kotlin 문법 사용



- 블로그 필자가 Gradle을 선택한 이유:
  
  - xml로 관리되는 Maven에 비해 짧고 간결
  
  - Java용인 Maven에 비해 범용성이 넓음
  
  - 최소 2배에서 100배 정도의 성능차이



- Gradle에서도 기존 Groovy에서 Kotlin으로 변경하는 이유:
  
  - IDE와의 호환성으로, 오류 검사와 자동완성 기능
  
  - Groovy와 다른 코드스타일 제약



- 하지만 장점만 있는 건 아니고, 제약 사항이 있다.
  
  - 빌드 캐시가 Invalidation되거나 클린 빌드 시 Groovy DSL보다 느리다.
  
  - 새로운 라이브러리 버전 Inspection 기능 미지원
  
  - Gradle 5.0 부터 지원
  
  - Java 8 부터 가능


