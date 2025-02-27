# CORS
## 정의
- **보안 정책을 우회**하여 다른 도메인에서 리소스를 요청할 수 있도록 허용하는 **HTTP 헤더 기반의 보안 메커니즘**
## 특징
- 기본적으로 웹 브라우저는 다른 출처(도메인, 프로토콜, 포트)의 리소스 요청을 차단하는 SOP(Same-Origin Policy)를 따름 
- CORS는 서버에서 허용할 출처를 명시하여 특정 요청을 허용할 수 있도록 함
- HTTP 응답 해더에 `Access-Control-Allow-Origin`을 설정하여 접근해야함
- Preflight Request(사전 요청): OPTION메서드를 사용하여 서버가 실제 요청을 허용하는지 확인하는 과정이 포함될 수 있슴
## 장점
- 보안 정책을 유지하면서 특정한 교차 출처 요청을 허용 가능
- API 서버를 분리하여 프론트엔드와 백엔드가 독립적으로 운영 가능
- 다양한 도메인 간의 안전한 데이터 교환 가능
## 단점
- 잘못된 설정 시 보안 취약점 발생 가능
- 설정이 복잡할 수 있으며, 브라우저와 서버 간의 호환성 문제 발생 가능
- `Access-Control-Allow-Origin`설정 시, 모든 도메인에서 접근 가능하여 보안 위험 증가
## 작동방식
### 단순요청
- GET, POST, HEAD 메서드만 사용
- 요청 헤더에 Authorization, Custom Headers 등이 포함되지 않음  
응답예시  
```Access-Control-Allow-Origin: https://test.com```
### 사전 요청
- 브라우저가 서버에 OPTIONS 요청을 먼저 보내서 허용 여부를 확인
- PUT, DELETE 등의 메서드 사용  
요청 예시  
```
OPTIONS/data HTTP/1.1
Origin:https://test.com
Access-Control-Request-Method: DELETE
Access-Control-Request-Headers: Authorization
```  
서버 응답  
```
HTTP/1.1 200 OK
Access-Control-Allow-Origin: https://example.com
Access-Control-Allow-Methods: GET, POST, DELETE
Access-Control-Allow-Headers: Authorization
```
# CSRF
## 정의
- 사용자가 신뢰하는 웹사이트에서 악의적인 요청을 보내도록 유도하여 피해를 입히는 공격 기법
## 특징
- 공격자는 피해자의 인증된 세션을 악용하여 원치 않는 요청을 실행
- 일반적으로 로그인된 상태에서 피해자가 악성 링크를 클릭하면 발.
- 피해자의 쿠키 정보를 자동으로 전송하는 웹 브라우저의 동작을 이용
- 주요 공격 대상: 온라인 뱅킹, 결제 시스템, 개인정보 변경 기능 등
## 장점(공격자 입장)
- 사용자 인증 정보를 악용하여 보안 시스템을 우회 가능
- 브라우저의 자동 쿠키 전송을 이용하여 인증 우회 가능
- 별도의 스크립트 실행 없이 단순한 링크 클릭만으로 공격 가능
## 단점(피해자 입장)
- 사용자의 동의 없이 중요한 작업이 실행될 위험이 있음
- 악의적인 공격으로 금전적 피해나 데이터 유출 가능
- 특정 웹사이트에 대한 신뢰를 떨어뜨릴 수 있음
## 방지법
- CSRF 토큰 사용: 요청마다 검증 가능한 CSRF 토큰을 포함
- SameSite 쿠키 설정: SameSite=strict 또는 SameSite=lax로 설정하여 타 사이트 요청 차단
- Referer / Origin 검증: 요청의 Referer 또는 Origin 헤더를 체크하여 신뢰할 수 있는 출처인지 확인
- 사용자 재인증 요구: 중요한 작업 전, 비밀번호 입력 등의 재인증 요청