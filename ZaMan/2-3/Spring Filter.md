# Spring Filter
Spring에서 Filter는 클라이언트의 요청이 **서블릿(Servlet)**에 도달하기 전에, 또는 응답이 클라이언트에게 전달되기 전에 요청/응답을 처리할 수 있는 기능입니다.

Spring MVC 처리 전에 필터가 작동하고 요청과 응답을 변경하거나 거를 수 있습니다.
### Filter 작동 순서
    Client → Filter → DispatcherServlet → Controller → DispatcherServlet → Filter → Client