# 정의
- 애플리케이션을 개발할때 사용하는 디자인 패턴들중 하나
- Model,View,Controller 로 구분이 됨 
# 사용이유
- 각 컴포넌트가 서로 분리되어 각자의 역할에 집중할 수 있음
- 유지보수가 쉬움
- 중복코드 제거
- 애플리케이션의 확장성,유연성이 증가
# 기본적인 구조
## DispatcherServlet
### 정의
- 요청이 들어오면 어떤 컨트롤러에게 위임할지 결정
- 모든 클라이언트의 요청을 받고, 세부경로를 각 컨트롤러에게 뿌리는 역할
## HandlerMapping
### 정의
- 웹/클라이언트의 요청과 해당요청을 처리하는 Controller의 매핑을 담당하는 인터페이스
## Spring Context
### 정의
- 스프링이 관리하는 빈들이 담겨있는 컨테이너
# other
## 서블릿(servlet)
### 정의
- 자바를 사용하여 웹을 만들기 위해 필요한 기술
- 클라이언트가 어떠한 요청을 하면 그에 대한 결과를 다시 전송해주는 자바 프로그램
### 특징
- 클라이언트의 요청에 대해 동적으로 작동하는 웹 어플리케이션 컴포넌트
- html을 사용하여 요청에 응답
- Java Thread를 이용하여 동작
- MVC패턴에서 Controller로 이용
- HTTP프로토콜 서비스를 지원하는 javax.servlet.http.HttpServlet 클래스를 상속받음
- UDP보다 처리 속도가 느리다.
- HTML 변경 시 Servlet을 재컴파일해야 하는 단점
