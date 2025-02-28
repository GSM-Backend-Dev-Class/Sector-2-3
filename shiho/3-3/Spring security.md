# Spring security
## 정의
- Spring 기반 애플리케이션에서 **인증**(Authentication),**인가**(Authorization, 권한 부여)를 담당하는 보안 프레임워크
## 특징
- **인증,인가** 지원(사용자 로그인 및 권한 관리 기능 제공 / JWT, OAuth2, SAML 등 다양한 인증 방식 지원)
- **세션 관리 및 CSRF 방어** (동시 세션 제어 / CSRF 공격 방어)
- **패스워드 암호화** 지원 
- **보안 정책** 및 **필터 기반** 아키텍처 (Spring Security Filter Chain을 통해 요청을 보호)
- Method-Level Security 지원 (@PreAuthorize, @PostAuthorize, @Secured등 어노테이션을 통한 세밀한 권한 제어 가능)
## 장점
- **강력한 보안** 기능 제공 (기본적으로 다양한 보안 위협에 대한 방어 기능 내장)
- **확장성**과 **유연성** (다양한 인증 방식과 결합 가능 / 커스텀 필터 및 설정을 통해 원하는 방식으로 확장 가능)
- **Spring과**의 완벽한 **통합** (Spring Boot 및 Spring MVC와 자연스럽게 통합됨)
- **세밀한 보안 정책** 적용 가능 (URL 기반, 메소드 기반, API 기반 등 다양한 방식으로 권한 제어 가능)
## 단점
- 설정이 복잡할 수 있음 (기본 설정은 간단하지만, OAuth2, JWT 등을 사용하면 설정이 복잡해질 수 있음)
- 초기 학습 곡선이 가파름 (필터 체인, 보안 설정, 사용자 정의 설정 등을 이해하는 데 시간이 걸릴 수 있음)
- 성능 이슈 가능성 (보안이 강화된 만큼, 잘못된 설정 시 성능 저하가 발생할 수 있음)
## 주요 구성요소
- SecurityContext (현재 인증된 사용자 정보를 저장하는 컨텍스트)
- Authentication(사용자 인증 정보를 포함)
- GrantedAuthority (사용자의 권한을 나타냄)
- UserDetailsService (사용자 정보를 로드하는 인터페이스)
- PasswordEncoder (비밀번호를 안전하게 저장하고 검증하는 암호화 모듈)

