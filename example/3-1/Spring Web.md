## Spring Web 정리

### 1. Spring Web 개요
Spring Web은 Spring Framework에서 웹 애플리케이션을 개발하기 위한 모듈이다. 주로 Spring MVC(Model-View-Controller) 아키텍처를 기반으로 동작하며, RESTful API 개발을 지원한다.

### 2. Spring Web의 주요 특징
1. **MVC 아키텍처 기반**
   - Model, View, Controller 패턴을 사용하여 웹 애플리케이션을 구조적으로 개발할 수 있다.
2. **RESTful API 개발 지원**
   - `@RestController`와 같은 어노테이션을 활용하여 간편하게 REST API를 개발할 수 있다.
3. **유연한 요청 처리 방식 제공**
   - `@RequestMapping`, `@GetMapping`, `@PostMapping` 등을 활용하여 HTTP 요청을 쉽게 매핑할 수 있다.
4. **다양한 View 지원**
   - JSP, Thymeleaf, FreeMarker 등의 다양한 템플릿 엔진을 지원한다.
5. **내장 서버 지원 (Spring Boot의 경우)**
   - Tomcat, Jetty, Undertow 등의 내장 서버를 활용하여 별도의 WAS 없이 실행할 수 있다.
6. **데이터 바인딩 및 검증 기능 제공**
   - `@Valid`, `@Validated` 등을 활용하여 데이터 유효성 검사를 수행할 수 있다.

### 3. Spring Web의 주요 컴포넌트
#### 1) DispatcherServlet
- Spring MVC의 핵심 역할을 수행하는 프론트 컨트롤러로, 모든 HTTP 요청을 받아 적절한 컨트롤러로 전달하고 결과를 반환하는 역할을 한다.

#### 2) Controller (@Controller, @RestController)
- 사용자의 요청을 처리하는 컴포넌트이다.
- `@Controller`는 View를 반환할 때 사용하고, `@RestController`는 JSON 또는 XML 데이터를 반환할 때 사용한다.

#### 3) RequestMapping (@RequestMapping, @GetMapping, @PostMapping 등)
- 특정 URL과 컨트롤러 메서드를 매핑하는 어노테이션이다.
- HTTP 요청 방식(GET, POST 등)에 따라 다르게 동작할 수 있다.

#### 4) ModelAndView
- 컨트롤러에서 데이터(Model)와 View 정보를 함께 전달하는 객체이다.

#### 5) ViewResolver
- 컨트롤러에서 반환한 View 이름을 실제 View 파일(JSP, Thymeleaf 등)로 변환하는 역할을 수행한다.

### 4. Spring Web과 Spring Boot Web 차이점
| 비교 항목  | Spring Web (Spring MVC) | Spring Boot Web |
|------------|----------------------|----------------|
| 설정 방식  | XML, Java Config 필요 | 자동 설정 (Spring Boot Starter) |
| 서버 실행  | 외부 톰캣 필요 | 내장 서버 (Tomcat 기본 제공) |
| REST API  | 직접 설정 필요 | `@RestController`로 간편하게 개발 가능 |
| 의존성 관리 | 수동 설정 | `spring-boot-starter-web` 하나로 해결 |

### 5. REST API 개발 예제
```java
@RestController
@RequestMapping("/api/users")
public class UserController {

    private final UserService userService;

    @GetMapping("/{id}")
    public ResponseEntity<User> getUser(@PathVariable Long id) {
        return ResponseEntity.ok(userService.getUserById(id));
    }

    @PostMapping
    public ResponseEntity<User> createUser(@RequestBody User user) {
        return ResponseEntity.status(HttpStatus.CREATED).body(userService.createUser(user));
    }

    @DeleteMapping("/{id}")
    public ResponseEntity<Void> deleteUser(@PathVariable Long id) {
        userService.deleteUser(id);
        return ResponseEntity.noContent().build();
    }
}
```

### 6. Spring Web과 Spring WebFlux 차이
| 비교 항목 | Spring Web (Spring MVC) | Spring WebFlux |
|-----------|----------------------|--------------|
| 실행 방식 | 동기(Blocking) | 비동기(Non-Blocking) |
| 주요 어노테이션 | `@RestController`, `@RequestMapping` | `@RestController`, `@RequestMapping` |
| 서버 지원 | Tomcat, Jetty | Netty, Undertow, Tomcat |
| 데이터 처리 | `List<>`, `Optional<>` | `Mono<>`, `Flux<>` |

### 7. Spring Web을 활용한 프로젝트 구조 예시
```
spring-web-demo/
│── src/main/java/com/example/
│   │── controller/
│   │   ├── UserController.java
│   │── service/
│   │   ├── UserService.java
│   │── repository/
│   │   ├── UserRepository.java
│   │── model/
│   │   ├── User.java
│── src/main/resources/templates/
│   │── home.html
│── pom.xml (Maven 설정)
```

### 8. 정리
1. Spring Web은 MVC 패턴을 기반으로 웹 애플리케이션을 개발하는 모듈이다.
2. DispatcherServlet을 활용하여 요청을 처리하며, 다양한 View 기술을 지원한다.
3. RESTful API 개발이 가능하며, `@RestController`를 사용하면 더욱 간편하다.
4. Spring Boot Web을 활용하면 내장 서버(Tomcat)와 자동 설정을 이용하여 빠르게 개발할 수 있다.
5. Spring Web은 동기 방식으로 동작하며, WebFlux는 비동기 방식으로 동작한다.

