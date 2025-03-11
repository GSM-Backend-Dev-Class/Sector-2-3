# Spring Security란?
스프링 프레임워크 기반 에플리케이션에서 인증과 인가를 처리하기 위한 보안 프레임워크입니다

## 주요 특징
1. 인증(Authentication): 사용자가 누구인지 확인하는 과정입니다
2. 인가(Authorization): 인증된 사용자가 특정 권한에 접근할 수 있는 권한이 있는지 확인하는 과정입니다
3. 방어 메커니즘: CSRF 공격 방지, 세션 고정 보호, CORS 지원 등 다양한 보안 관련 기능을 제공합니다
4. 통합성: OAuth 2.0, JWT 등 다양한 인증 방식을 쉽게 통합할 수 있습니다
5. 커스터마이징: 보안 설정을 세밀하게 조정할 수 있어 다양한 요구사항을 반영할 수 있습니다

## Spring Security의 구성 요소
1. SecurityContext: 현재 인증된 사용자의 정보를 담고있는 컨텍스트입니다
2. AuthenticationManager: 사용자의 인증을 처리합니다
3. AuthenticationProvider: 특정 입력 방식(예: JWT, OAuth 등)을 처리합니다
4. UserDetailsService: 사용자 정보를 가져오는 서비스입니다
5. FilterChainProxy: 다양한 보안 필터를 체인 형태로 관리합니다
6. GrantedAuthority: 사용자가 가진 권한을 나타냅니다

## Spring Security를 사용하는 이유
- 강력한 보안성: 다양한 공격으로부터 애플리케이션을 보호합니다
- 유연한 인증/인가 처리: 여러 인증 방식을 쉽게 적용할 수 있습니다
- 스프링과의 강력한 통합: 스프링 부트와 함께 사용할 때 설정이 간편합니다
