# AOP,Filter,Interceptor
## AOP(Aspect-Oriented Programming)란?
AOP는 관점 지향 프로그래밍으로 핵심 비지니스 로직과 부가적인 기능(로깅, 트랜잭션, 보안 등)을 분리하여 모듈화하는 방법이다

- 장점: 중복 코드 제거, 코드 모듈화
- 단점: 잘못하면 코드 디버깅이 어려움

## AOP 주요개념
1. Aspect(애스팩트)
- AOP의 핵심 모듈로, 공통 기능(횡단 관심사)을 정의하는 모듈이다
- 주로 하나이상의 Advice와 PointCut을 포함한다
- 예시: 로깅기능, 트랙잭션, 보안 검증 등
```
@Aspect
@Component
public class LoggingAspect {
    @Before("execution(* com.example.service.*.*(..))")
    public void logBefore() {
        System.out.println("메서드 실행 전 로그");
    }
}
```
- 위의 LoggingAspect 코드가 Aspect이다
2. Advice(어드바이스)
- 실제로 구현될 기능(로직)을 정의하는 부분이다
- 언제 실행할지 결정하는 요소로 매서드와 관련되 부가 동작을 정의한다
- 종류:
  - @Before: 메서드가 실행 전 동작
  - @After: 메서드가 실행 후 동작
  - @AfterReturning: 메서드가 정상적으로 실행 후 동작
  - @AfterThrowing: 메서드 실행 중 예외가 발생 했을 때 동작 
  - @Around: 매서드 실행 전후에 동작
- 예시:@Before
```
@Before("execution(* com.example.service.*.*(..))")
public void logBeforeMethod(JoinPoint joinPoint) {
    System.out.println("메서드 실행 전: " + joinPoint.getSignature().getName());
}
```
3. JoinPoint(조인포인트)
- Advice가 적용될 수 있는 모든 부분
- Spring AOP에서 주로 매서드가 실행되는 부분 = JoinPoint
- Advice 메서드에서 인자로 전달 받아 실행중인 매서드의 정보를 얻을 수 있음
- 예시: JoinPoint 사용
```
@Before("execution(* com.example.service.*.*(..))")
public void logMethodInfo(JoinPoint joinPoint) {
    System.out.println("메서드 이름: " + joinPoint.getSignature().getName());
    System.out.println("매개변수: " + Arrays.toString(joinPoint.getArgs()));
}
```
4. PointCut(포인트컷)
- Advice가 적용될 구체적인 지점을 표현하는 표현식
- 메서드에서 실행 패턴을 지정하여 원하는 메서드에서 Advice를 적용할 수 있음
- 표현식 예시:
  - execution(* com.example.service.*.*(..)) -> service 패키지의 모든 메서드
  - within(com.example.service.*) -> 특정 패키지의 모든 메서드 
  - @annotation(org.springframework.transaction.annotation.Transactional) -> 특정 애너테이션이 붙은 메서드
- 예시: 포인트컷 정의
```
@Pointcut("execution(* com.example.service.*.*(..))")
public void serviceMethods() {}
```
5. Weaving(위빙)
- Aspect와 핵심 비지니스 로직이 결합하는 과정
- Spring에서는 런타임 위빙을 사용하여 프록시 객체를 통해 AOP를 구현한다
- 위빙 시점에 따라 구분:
  - **컴파일 타임 위빙**: 소스 코드 컴파일 시점에 적용
  - **클래스 로딩 타임**: 바이트코드 조작
  - **런타임 위빙**: 프록시를 이용하여 런타임 시점에 적용(Spring AOP의 방식)
6. Target(타겟)
- Advice가 적용되는 실제 비지니스 로직을 가지는 객체
## Filter
Filter는 서블릿 필터로, HTTP 요청과 응답을 가로채어 처리하는 역할을 한다
- 동작 위치: DispatcherServlet앞에서 처리
- 사용 위치: 인코딩 처리, 인증/인가, CORS설정 등
- 예시:
```
    @WebFilter(urlPatterns = "/*")
public class EncodingFilter implements Filter {
    @Override
    public void doFilter(ServletRequest request, ServletResponse response, FilterChain chain)
            throws IOException, ServletException {
        request.setCharacterEncoding("UTF-8");
        chain.doFilter(request, response);
    }
}
```
- 장점: 전역적으로 HTTP 요청/응답 처리
- 단점: Spring MVC와 무관하게 동작'

## Interceptor
Interceptor는 Spring MVC에서 제공하는 기능으로, 컨트롤러 전후 요청을 가로채서 처리한다
- 동작 위치: DispatcherServlet이후, 컨트롤러 앞뒤
- 사용 위치: 인증/인가, 로깅, API 요청 전 처리 등
- 예시:
```
public class AuthInterceptor implements HandlerInterceptor {
    @Override
    public boolean preHandle(HttpServletRequest request, HttpServletResponse response, Object handler) {
        System.out.println("요청 경로: " + request.getRequestURI());
        return true;
    }
}
```
- 장점: Spring MVC와 밀접하게 연동
- 단점: HTTP 요청/응답 외에는 처리 불가
### 비교정리
|구분|AOP|Filter|Interceptor|
|--|---|------|-----------|
|적용 범위|메소드 수준|서블릿(HTTP 요청/응답)|Spring MVC (Handler 전후)|
|사용 목적|공통 기능 분리|인코딩, 인증, 로깅|인증/인가, 로깅, 사전/후 처리|
|동작 시점|메서드 실행 전후|DispatcherServlet 앞|DispatcherServlet 앞뒤|
|기술 기반|프록시 패턴|서블릿 API|Spring MVC|