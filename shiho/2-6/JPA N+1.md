# 정의
- ORM 기술에서 특정 객체를 대상으로 수행한 쿼리가 해당 객체가 가지고 있는 연관관계 또한 조회하면서 **N번째 추가적인 쿼리를 발생**
- 연관된 엔터티를 조회할 때 발생하는 불필요한 추가 쿼리 문제
```
ORM: 쿼리언어를 사용하지않고 프로그래망 언어로 데이터베이스를 관리할 수 있게해주는 것 
```
# 원인
- 관계형 데이터베이스와 객체지향 언어간의 **패러다임 차이**
- 객체: 연관관계를 통해 레퍼런스를 가지고 있으면 **언제든지 메모리 내**에서 Random Access를 통해 **연관 객체에 접근가능**
- RDB: **Select 쿼리**를 **통해서만** **조회가능**
# 문제 해결법
## Fetch join
- @query를 사용해 한 번의 JOIN으로 모든 데이터를 조회하는 방법
- 예시
```Java
@Query("SELECT u FROM User u JOIN FETCH u.posts")
List<User> findAllWithPosts();
```
- 실행 쿼리문
```sql
SELECT u.*, p.* FROM users u 
JOIN posts p ON u.id = p.user_id;
```
### Fetch join vs Join
- Fetch Join: ORM에서의 사용을 전제로 **DB Schema**를 **Entity**로 **자동 변환** 해주고 **영속성 컨텍스트에 영속화** 해줌
- Join: 단순히 데이터를 조회 하는 개념으로 **영속성 컨텍스트나 Entity와는 무관**
## EntityGraph 사용
- @EntityGraph를 사용하면 **fetch join**과 **유사한 효과**를 낼 수 있음
- **JPQL 없이**도 Fetch Join처럼 동작
```Java
@EntityGraph(attributePaths = {"posts"})
@Query("SELECT u FROM User u")
List<User> findAllWithPosts();
```
## Batch Size 설정 (IN 절 활용)
- @OneToMany(fetch = FetchType.LAZY) 관계에서 hibernate.default_batch_fetch_size를 설정하면, N번 실행되던 쿼리를 IN 절을 사용한 쿼리 몇 개로 줄일 수 있음
- 짧게말해 **쿼리를 훨신 줄일수 있음**
- 모든 데이터를 한꺼번에 가져오지 않고, **적당한 크기로 최적화가능**
- 너무 큰 값을 설정하면 성능이 저하
```
spring.jpa.properties.hibernate.default_batch_fetch_size=100
```
```sql
SELECT * FROM posts WHERE user_id IN (1, 2, 3, ..., 100);
```
