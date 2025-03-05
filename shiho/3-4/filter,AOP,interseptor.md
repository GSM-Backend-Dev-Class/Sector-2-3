# filter
## 정의
- Servlet 단위에서 실행되는 요청과 응답을 거른뒤 정제하는 역할
## 특징
- DispatcherServlet이전에 실행이 됨
- 필터가 동작하도록 지정된 자원의 앞단에서 요청내용을 변경하거나, 여러가지 체크가능
- 응답 내용까지 변경가능
## 메서드
- init() = 필터 인스턴스 초기화
- doFilter() = 전/후 처리
- destroy() = 필터 인스턴스 종료
# interseptor
## 정의
- 요청에 대한 작업 전/후로 가로채는 것
## 특징
- Spring context 외부에 존재해 스프링과 무관한 자원에 대해 동작
- DistpatcherServlet이 컨트롤러를 호출하기 전/후로 끼어들어 Spring context 내부에서 Controller에 요청,응답에 대해 처리
## 메서드
- preHandler() = 컨트롤러 메서드가 실행되기 전
- postHanler() = 컨트롤러 메서드 실행직 후 view페이지 렌더링 되기 전
- afterCompletion() = view페이지가 렌더링 되고 난 후
# AOP
## 정의
- OOP를 보완하기 위한 개념
- 중복을 줄일수 없는 부분을 줄이기 위한 관점에서 바라보고 처리하는 것
## 특징
- 로깅,트랜잭션,에러 처리등 비지니스단의 메서드에서 좀더 세밀하게 조정하고 싶을 때 사용
- Interceptor나 Filter와는 달리 메소드 전후의 지점에 자유롭게 설정이 가능
- 주소,파라미터,에노테이션 등 다양한 방법으로 대상을 지정할 수 있다
