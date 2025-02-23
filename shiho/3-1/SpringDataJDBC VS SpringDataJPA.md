# Spring Data JDBC
## 정의
JDBC(Java DataBase Connectivity) 기반 DB접근 하는 방법을 제공하는 프레임워크
Spring에서 제공하는 CRUD 기능을 활용하여 **쉽게 DB작성** 가능
## 특징
- JDBC 기반
- Spring의 CRUD Repository 지원
- 단순한 데이터 접근 방식
- 복잡한 관계형 매핑 지원 부족
- 트랜잭션 제어 명확(실행될 SQL을 직접 컨트롤 가능)
## 장점
- 설정이 간단
- 성능이 빠름
- SQL 최적화 기능
- Spring Boot와 쉽게 통합 가능
## 단점
- 복잡한 엔티티 관계 매핑 지원 부족
- 자동 SQL 생성 기능이 없음
- 엔티티 상태 관리 없음
# Spring Data JPA
## 정의
- JPA(Java Persistence API) 기반의 ORM(Object Relational Mapping)을 지원하는 프레임워크
- SQL을 직접 작성하지 않아도 엔티티(Entity)를 데이터베이스 테이블과 매핑하여 자동으로 SQL을 생성
## 특징
- ORM(Object Relational Mapping) 기반
- 자동 SQL 생성 기능 제공
- JPQL(Java Persistence Query Language) 지원(객체 중심 쿼리 작성 가능)
- 영속성 컨텍스트(Persistence Context) 관리(엔티티 상태 변경을 자동으로 감지하여 DB 반영)
- 지연 로딩(Lazy Loading) 지원(필요할 때만 데이터를 조회하여 성능 최적화 가능)
## 장점
- 자동 SQL 생성 기능 제공
- 객체 중심의 데이터 접근
- 엔티티 상태 자동 관리
- 캐싱 및 성능 최적화 가능
## 단점
- 학습 난이도가 높음
- Hibernate의 내부 처리 과정이 복잡
- 자동 SQL이 비효율적으로 생성될 가능성 있음
# 차이점
## JDBC
- 직관적이고 성능이 우수
- 간단한 CRUD작업에 적합
- 엔티티 간 관계를 정의하는 기능이 부족
- 영속성 컨텍스트 기능이 없음
### 좋은 상황
- SQL을 직접 컨트롤해야 할 때
- 단순한 CRUD 중심의 애플리케이션을 개발할 때
- 성능 최적화가 중요한 경우
## JPA
- 객체(Entity) 중심의 데이터 접근 방식
- SQL을 자동 생성
- 복잡한 엔티티 관계를 쉽게 다룰 수 있음
- Hibernate의 내부 처리로 인해 성능 최적화가 필요
### 좋은 상황
- 객체(Entity) 중심의 데이터 접근이 필요할 떄
- 엔티티 간의 복잡한 관계를 다뤄야 할 떄
- 개발 생산성을 높이고, SQL을 자동으로 관리하고 싶을 때