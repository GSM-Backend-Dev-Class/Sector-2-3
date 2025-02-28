# Filter
Spring Security는 다양한 필터를 제공하며 보안 관련 요청을 처리하는데 사용된다

# Spring Security의 필터 체인 구조
Spring Security는 여러 개의 필터를 **FilterChainProxy**라는 객체를 통해 실행합니다
1. 사용자가 요청을 보냄 (`/login`, `/api/resource` 등)
2. Spring Security의 **FilterChainProxy**가 요청을 가로챔
3. 등록된 필터들이 순차적으로 실행됨
4. 필터에서 인증이 완료되면 **SecurityContext**에 저장
5. 필터 체인이 끝나면 컨트롤러가 요청을 처리함

# Spring Security의 주요 필터 목록
Spring Security는 수십 개의 필터를 기본적으로 제공하지만, 주로 사용하는 필터를 정리하면 다음과 같습니다
| 필터 이름 | 설명 |
| -------- | ---- |
| SecurityContextPersistenceFilter | 인증 정보를 `SecurityContextHolder`에 저장하고 관리 | 
| UsernamePasswordAuthenticationFilter | 기본 폼 로그인 처리 (ID/PW) | 
| BasicAuthenticationFilter | HTTP Basic 인증 처리 |
| BearerTokenAuthenticationFilter | JWT와 같은 Bearer Token 인증 처리 |
| JwtAuthenticationFilter | 커스텀 JWT 인증 필터 (직접 구현 가능) |
| CorsFilter | CORS 정책을 처리 |
| LogoutFilter | 로그아웃 요청 처리 |

## 💡Spring Security의 기본 필터 체인 순서
1. `CorsFilter`
2. `SecurityContextPersistenceFilter`
3. `UsernamePasswordAuthenticationFilter
`
4. `BasicAuthenticationFilter`
5. `BearerTokenAuthenticationFilter`
6. `ExceptionTranslationFilter`
7. `FilterSecurityInterceptor`

# 필터 동작 과정
Spring Security의 필터들은 FilterChain을 통해 차례로 실행됩니다

예를 들어 JWT 인증을 적용한 API의 동작 과정을 살펴보자 
1. 사용자가 `/api/data` 요청을 보냄
2. `SecurityContextPersistenceFilter`가 기존의 인증 정보(SecurityContext)가 있는지 확인
3. `BearerTokenAuthenticationFilter`가 요청 헤더에서 `Authorization: Bearer <JWT>` 확인
4. `JwtAuthenticationFilter`에서 토큰을 검증하고 유효하면 인증 정보를 **SecurityContext**에 저장
5. 컨트롤러에서 요청을 정상적으로 처리

# 요약
| 개념 | 설명 |
| --- | --- |
| FilterChainProxy | 여러 개의 필터를 관리하는 핵심 객체 |
| SecurityContextPersistenceFilter | 인증 정보를 SecurityContextHolder에 저장 | 
| UsernamePasswordAuthenticationFilter | 	기본적인 로그인 처리 필터 |
| JwtAuthenticationFilter | JWT 토큰을 검증하는 필터 (직접 구현 가능) |
| BearerTokenAuthenticationFilter | Bearer Token 인증 처리 | 
| CorsFilter | CORS 설정을 처리하는 필터 |