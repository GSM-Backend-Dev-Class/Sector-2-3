# Spring MVC 기본구조
Spring MVC의 기본구조는 다음과 같다
1. DispatcherServlet
2. HandlerMapping
3. Controller
4. Model and View
5. ViewResolver
6. View
## DispatcherServlet(디스패처 서블릿)
- Spring MVC에서 프론트 컨트롤러의 역활을 맏는다
- 모든 요청을 가장 먼저 받아 처리하고, 이후 적절한 컨트롤러로 전달한다
- `web.xml`또는 `@SpringBootApplication`을 통해서 설정된다
- DispatcherServlet 등록 예시 (web.xml)
```
<servlet>
    <servlet-name>dispatcher</servlet-name>
    <servlet-class>org.springframework.web.servlet.DispatcherServlet</servlet-class>
</servlet>
<servlet-mapping>
    <servlet-name>dispatcher</servlet-name>
    <url-pattern>/</url-pattern>
</servlet-mapping>
```
## HandlerMapping (핸들러 매핑)
- DispatcherServlet이 요청을 받으면 어떤 컨트롤러가 처리할지 정하는 역활을 한다
- URL이나 애너테이션 기반으로 매핑한다
- Spring boot에서는 주로 `@RequestMapping`이나 `@GetMapping`등을 사용한다
- 예시:
```
@RestController
public class HomeController {
    @GetMapping("/home")
    public String home() {
        return "Hello, Spring MVC!";
    }
}
```
## Controller (컨트롤러)
- 클라이언트의 요청을 실제로 처리하는 핵심 구성 요소
- 요청데이터를 가공하거나 서비스 계층과 상호작용하여 비지니스 로직을 처리합니다
- 결과 데이터를 Model 객체에 담아 뷰로 전달한다
- 예시:
```
@Controller
public class UserController {

    @GetMapping("/user")
    public String getUser(Model model) {
        model.addAttribute("name", "홍길동");
        return "user";
    }
}
```
## Model and View (모델과 뷰)
- Model: 데이터를 담아 뷰에 전달하는 역활을 한다
- View: 화면에 데이터를 표시하는 부분이다 주로 JSP, Thymeleaf등을 사용합니다
- 컨트롤러 매서드를 반환값이 뷰 이름이 되며, 모델 속성은 뷰에 전달됩니다

## ViewResolver (뷰 리졸버)
- 논리적 뷰 이름을 실제 뷰로 변환하는 역할을 합니다
- 보통 뷰의 경로나 파일 확장자를 설정하여 뷰 이름을 완성합니다
- 예시:
```
spring:
  mvc:
    view:
      prefix: /WEB-INF/views/
      suffix: .jsp
```
## View (뷰)
- 최종적으로 사용자에게 HTML 등의 화면을 렌더링하여 보여줍니다
- JSP, Thymeleaf, Freemarker 등이 많이 사용됩니다
- 예시:
```
<!DOCTYPE html>
<html>
<head>
    <title>사용자 정보</title>
</head>
<body>
    <h1>사용자 이름: ${name}</h1>
</body>
</html>
```
### Spring MVC 흐름 요약
1. 클라이언트 요청 → DispatcherServlet
2. DispatcherServlet → HandlerMapping → 컨트롤러 검색
3. DispatcherServlet → Controller (비즈니스 로직 수행)
4. Controller → Model and View 반환
5. DispatcherServlet → ViewResolver (뷰 이름을 실제 경로로 변환)
6. ViewResolver → View 렌더링
7. 클라이언트에게 응답 전송
## Spring Context의 주요 역할
1. 빈(Bean) 관리
    - Spring 애플리케이션의 모든 빈을 관리한다
    - 빈의 생명주기와 의존성(DI)을 주입한다
2. 빈 검색과 주입
    - 필요한 빈을 이름 또는 타입으로 검색하여 주입할 수 있습니다
3. 설정 관리
    - XML 또는 자바 기반 설정을 통해 애플리케이션 환경을 관리합니다
4. 이벤트 관리
    - 애플리케이션 이벤트를 발생시키고 이를 청취하는 리스너를 관리합니다