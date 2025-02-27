# Spring Validation
## 정의
- 입력 값 검증을 위한 Spring 라이브러리
- Java Bean Validation 표준을 사용
- @Valid, @NotNull, @Size 등의 애노테이션을 활용하여 간단한 검증 가능
## 특징
- 애노테이션 기반 검증 (간편한 선언형 방식)
- DTO 검증 지원 (@Valid 및 @Validated 사용 가능)
- 커스텀 검증 가능 (ConstraintValidator 인터페이스 활용)
## 장점
- **간결한 코드**로 유효성 검사를 쉽게 적용 가능
- **표준화된 검증 방식**으로 여러 프레임워크에서 사용 가능
- **Spring과 자연스럽게 통합**되어 컨트롤러, 서비스 계층에서 검증 가능
## 단점
- **복잡한 검증 로직**을 적용하려면 커스텀 검증 로직을 직접 구현해야 함 
- **런타임 검증**이므로 정적 분석보다 오류 발견이 늦어질 수 있음
# Javax,Jakarta
## 정의
- Javax: 기존 Java EE에서 사용되던 패키지 명칭
- Jakarta: Java EE가 Eclipse Foundation으로 이관되면서 변경된 패키지 명칭
- Spring Boot 3.x부터 Jakarta를 기본적으로 사용
## 특징
- javax 패키지는 더 이상 업데이트되지 않으며, 최신 스프링 프로젝트에서는 jakarta 사용이 권장됨
- 이전 버전 호환성이 필요하면 javax를 유지, 최신 스펙을 활용하려면 jakarta로 마이그레이션 필요