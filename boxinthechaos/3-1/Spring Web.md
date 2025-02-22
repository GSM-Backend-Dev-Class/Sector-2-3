# SPRING WEB란?
Spring Web는 프레임워크로 웹을 더 편리하고 빠르게 만들 수 있는 다양한 도구를 제공해주는 도구이다

## Spring Web의 주요기능

### 1. Spring MVC (Model-View-Controller)
Spring Web은 Spring MVC 패턴을 기반으로 웹 애플리케이션을 개발할 수 있도록 한다
- `@Controller` : 웹 요청을 처리하는 컨트롤러 클래스
- `@RestController` : JSON 데이터를 반환하는 API 컨트롤러
- `@RequestMapping` / `@GetMapping` / `@PostMapping` : HTTP 요청 매핑

### ✅ 예시
```
@RestController
@RequestMapping("/api")
public class HelloController {

    @GetMapping("/hello")
    public ResponseEntity<String> sayHello() {
        return ResponseEntity.ok("Hello, Spring Web!");
    }
}
```

### 2. 내장형 웹 서버 지원
Spring Boot의 `spring-boot-starter-web`을 사용하면 Tomcat, Jetty, Undertow 같은 내장 웹 서버가 자동 실행됨
- 기본값 : Tomcat
- 설정 변경 가능 (`application.properties`에서 변경 가능)

`properties`
```
server.port=8081  # 서버 포트 변경
server.servlet.context-path=/app  # 컨텍스트 경로 변경
```

### 3. RESTful API 개발
Spring Web은 RESTful API를 쉽게 만들 수 있도록 지원한다
- `@GetMapping`/`@PostMapping`/`@PutMapping`/`DeleteMapping` 지원
- HTTP 요청/응답을 JSON으로 처리(`Jackson`라이브러리 기본 포함)
- **ResponseEntity()** 를 사용하여 상태 코드 반환 가능

### ✅ 예시
```
@RestController
@RequestMapping("/users")
public class UserController {

    @GetMapping("/{id}")
    public ResponseEntity<User> getUser(@PathVariable Long id) {
        User user = userService.findById(id);
        return ResponseEntity.ok(user); // 200 OK + JSON 응답
    }

    @PostMapping
    public ResponseEntity<User> createUser(@RequestBody User user) {
        User createdUser = userService.save(user);
        return ResponseEntity.status(HttpStatus.CREATED).body(createdUser);
    }
}
```

### 4. Exception Handling(예외 처리)
spring Web은 예외 발생 시 일관된 응답을 반환하도록 **예외 처리 기능**을 제공한다
- @ExceptionHandler
- @ControllerAdvice

### ✅ 예시
```
@ControllerAdvice
public class GlobalExceptionHandler {

    @ExceptionHandler(UserNotFoundException.class)
    public ResponseEntity<String> handleUserNotFoundException(UserNotFoundException ex) {
        return ResponseEntity.status(HttpStatus.NOT_FOUND).body(ex.getMessage());
    }
}
```

### 5. Spring Web과 Spring WebFlux 차이
| 특징 | Spring Web (MVC) | Spring WebFlux |
| ---- | -----------------| ---------------|
| 요청 처리 방식 | 동기 (Servlet 기반) | 비동기 (Reactive Streams) |
| 주요 API | `RestController`, `RequestMapping` | `RouterFunction`, `WebFlux` |
| 스레드 모델 | 블로킹 I/O (Tomcat, Jetty) | 논블로킹 I/O (Netty) |
| 적합한 사용 사례 | 전통적인 웹 애플리케이션 | 고성능, 대규모 트래픽 처리 (Microservices, WebSocket) |

# Spring Data JDBC vs Spring Data JPA
`Spring Data JDBC`와 `Spring Data JPA`는 `Spring Data` 프로젝트의 일부로, 데이터베이스와 상호작용하는 기능을 제공한다
- **Spring Data JDBC** : 간단하고 직관적인 JDBC 기반의 데이터 액세스를 제공
- **Spring Data JPA** : JPA (Java Persistence API) 기반 ORM(Object-Relational Mapping) 기술을 제공
둘 다 데이터베이스와의 상호작용을 돕지만, 내부 동작 방식과 사용 목적이 다르다

## Spring Data JDBC란?
Spring Data JDBC는 JDBC API를 간편하게 사용할 수 있도록 도와주는 기술이다
- ORM 기능 없이 SQL을 직접 실행
  - **ORM** : SQL을 직접 작성하지 않아도 데이터를 저장하고 불러올 수 있도록 도와주는 기술  
- 단순한 CRUD 작업에 최적화됨
- JPA보다 가벼운 메모리 사용량과 빠른 실행 속도

## ✅ Gradle 의존성
```
dependencies {
    implementation 'org.springframework.boot:spring-boot-starter-data-jdbc'
    runtimeOnly 'org.mariadb.jdbc:mariadb-java-client' // MariaDB 사용 시
}
```

## ✅ 예시
```
@Repository
public interface UserRepository extends CrudRepository<User, Long> {
    List<User> findByName(String name);
}
```

### 📌 특징
- SQL을 직접 사용 (자동 쿼리 생성 기능 지원)
- 관계형 데이터베이스의 연관 관계(1:N, N:M) 처리가 제한적
- 영속성 컨텍스트(X), Lazy Loading(X), 변경 감지(X)
- 단순한 CRUD 작업에 적합

## Spring Data JPA란?
Spring Data JPA는 JPA를 기반으로 한 ORM(Object-Relational Mapping) 기술을 제공한다
- 객체와 테이블을 매핑하여 자동으로 SQL 생성
- EntityManager 및 영속성 컨텍스트 사용
- Lazy Loading(지연 로딩), 변경 감지, 캐싱 등 다양한 기능 지원

## ✅ Gradle 의존성
```
dependencies {
    implementation 'org.springframework.boot:spring-boot-starter-data-jpa'
    runtimeOnly 'org.mariadb.jdbc:mariadb-java-client' // MariaDB 사용 시
}
```

## ✅ 예시
```
@Entity
public class User extends BaseIdxEntity {
    private String name;
    private String email;
}

@Repository
public interface UserRepository extends JpaRepository<User, Long> {
    List<User> findByName(String name);
}
```

### 📌 특징
- 객체 중심의 데이터베이스 접근 방식
- **연관 관계 매핑(1:1, 1:N, N:M)** 을 쉽게 처리 가능
- Lazy Loading, 변경 감지, 캐싱 등 다양한 기능 지원
- 복잡한 쿼리를 JPA Query Language (JPQL)로 작성 가능

## Spring Data JDBC vs. Spring Data JPA 차이점
| 비교항목 | Spring Data JDBC | Spring Data JPA |
| ------- | ---------------- | --------------- |
| 기반 기술	| 순수 JDBC 기반| JPA (Hibernate) 기반 |
| SQL 처리 방식	| SQL을 직접 실행 (자동 생성 가능) | 객체 중심 ORM 매핑 (JPQL 사용) |
| 영속성 컨텍스트 | ❌ 없음 | ✅ 있음 (변경 감지) |
| Lazy Loading (지연 로딩) | ❌ 지원 안 함 | ✅ 지원 |
| 변경 감지 (Dirty Checking) | ❌ 없음 (수동 업데이트 필요)	 | ✅ 자동 변경 감지 | 
| 성능 | 빠르고 가벼움 | 성능 최적화 필요 (Lazy Loading 활용) |
| 복잡한 쿼리 | 직접 SQL 작성 필요 | JPQL, QueryDSL 지원 |
| 적합한 경우 | 단순한 CRUD, 빠른 성능이 필요한 경우 | 	복잡한 관계형 데이터 처리 |

## 언제 어떤 걸 사용해야 할까?
| Spring Data JDBC | Spring Data JPA |
| ---------------- | --------------- |
| 빠르고 단순한 CRUD | 	ORM 기반 객체 매핑 | 
| SQL을 직접 사용 | JPQL 사용 가능 | 
| 영속성 컨텍스트 X | 영속성 컨텍스트 O |
| Lazy Loading X | Lazy Loading O | 
| 변경 감지 X | 변경 감지 O | 
| 성능이 중요한 경우 적합 | 복잡한 연관 관계가 필요할 때 적합 | 