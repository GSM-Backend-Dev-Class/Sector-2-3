# CORS (Cross-Origin Resource Sharing)
CORS는 웹 브라우저에서 다른 도메인(origin)의 리소스를 요청할 때 발생하는 보안 정책이다

## CORS란?
웹 애플리케이션이 다른 출처(도메인, 프로토콜, 포트)의 리소스를 요청할 경우 **브라우저가 이를 차단하는 보안 정책**

## ✅CORS 차단 예제
```
Origin: https://example.com
Request URL: https://api.anotherdomain.com/data
Response: Blocked by CORS policy
```
위 요청에서 `example.com`에서 `api.anotherdomain.com`으로 요청했기 때문에 **CORS 정책에 의해 차단됨**

## 해결 방법
1. 서버에서 CORS 허용하기 (Spring Boot)
```
@Configuration
public class CorsConfig {

    @Bean
    public WebMvcConfigurer corsConfigurer() {
        return new WebMvcConfigurer() {
            @Override
            public void addCorsMappings(CorsRegistry registry) {
                registry.addMapping("/**")
                        .allowedOrigins("https://example.com") // 허용할 도메인
                        .allowedMethods("GET", "POST", "PUT", "DELETE") // 허용할 HTTP 메서드
                        .allowedHeaders("*"); // 허용할 헤더
            }
        };
    }
}
```

2. 컨트롤러에서 허용
```
@RestController
@RequestMapping("/api")
@CrossOrigin(origins = "https://example.com") // 특정 도메인 허용
public class MyController {
    @GetMapping("/data")
    public String getData() {
        return "CORS 허용됨!";
    }
}
```

3. 프론트엔드에서 요청 헤더 추가 (Fetch API)
```
fetch("https://api.anotherdomain.com/data", {
    method: "GET",
    headers: {
        "Origin": "https://example.com"
    }
});
```

## CORS 정리
- ✅ **CORS는 브라우저의 보안 정책**으로, 기본적으로 **다른 도메인(origin)의 요청**을 차단함
- ✅ 서버에서 `@CrossOrigin` 또는 `CorsRegistry`를 설정하면 허용 가능
- ✅ **브라우저에서만 적용**되며, Postman 같은 클라이언트 툴에서는 영향을 받지 않음

# CSRF (Cross-Site Request Forgery)
**CSRF**는 공격자가 사용자의 인증된 세션을 이용하여 **사용자 모르게 요청을 보내는 공격 기법**

## CSRF란?
공격자가 사용자를 속여서 **의도하지 않은 요청을 특정 웹사이트에 보내도록 유도하는 공격**

## CSRF 공격 예제
✅ 공격 시나리오
1. 사용자가 `https://bank.com`에 로그인하여 세션이 유지된 상태
2. 공격자가 악성 웹사이트에 아래 코드를 삽입
3. 사용자가 해당 악성 사이트에 방문하면, **사용자 모르게 요청이 전송됨**
4. 결과적으로 사용자의 계좌에서 다른 계좌로 송금됨

```
<img src="https://bank.com/transfer?to=attacker&amount=10000">
```
이 경우, 브라우저는 `bank.com`에 이미 로그인된 상태이므로 **자동으로 요청을 보내버린다**

## CSRF 방어 방법
1. ✅ CSRF Token 사용
서버가 요청할 때 임의의 토큰을 생성하여 폼에 포함하고, 요청 시 해당 토큰을 검사하는 방식
    - Spring Security에서 CSRF 설정
기본적으로 Spring Security는 CSRF 보호를 활성화한다
```
@Configuration
@EnableWebSecurity
public class SecurityConfig {
    @Bean
    public SecurityFilterChain securityFilterChain(HttpSecurity http) throws Exception {
        http
            .csrf(csrf -> csrf.disable()) // CSRF 보호 비활성화 (API 서버의 경우)
            .authorizeHttpRequests(auth -> auth.anyRequest().authenticated());

        return http.build();
    }
}
```
2. ✅ Referer / Origin 검증
서버에서 `Referer` 또는 `Origin` 헤더를 확인하여 신뢰할 수 있는 요청인지 확인
```
@Override
protected void doFilterInternal(HttpServletRequest request, HttpServletResponse response, FilterChain filterChain)
        throws ServletException, IOException {
    String origin = request.getHeader("Origin");
    if (origin == null || !origin.equals("https://trusted-site.com")) {
        response.sendError(HttpServletResponse.SC_FORBIDDEN, "CSRF 공격 의심");
        return;
    }
    filterChain.doFilter(request, response);
}
```

## CSRF 정리
- ✅ **CSRF는 사용자의 인증된 세션을 이용한 공격**으로, 사용자가 모르는 사이에 요청이 전송됨
- ✅ Spring Security는 **기본적으로 CSRF 방어 기능을 활성화**함
- ✅ API 서버는 `.csrf().disable()`로 비활성화할 수 있음
- ✅ **CSRF Token** 또는 **Origin 검증**으로 방어 가능

# CORS vs CSRF 비교
| 개념 | CORS | CSRF |
| --- | ---- | ---- |
| 설명 | 브라우저가 **다른 도메인의 요청을 차단**하는 보안 정책 | 공격자가 사용자의 **인증된 세션을 악용하여 요청**을 보내는 공격 |
| 목적 | 다른 출처의 리소스 접근 제한 | 사용자의 의도치 않은 요청 방지 |
| 대상 | 클라이언트(브라우저)에서 실행됨 | 	서버에서 발생하는 공격 | 
| 방어 방법 | `@CrossOrigin`, CORS 설정 변경 | 	CSRF Token, Referer 검증 |