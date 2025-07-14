# 정리: 2-1 ~ 3-3

---

## 규칙(2-1)

### SOLID

- **S(단일 책임 원칙)** : 1클래스 1역할  
- **O(개방/폐쇄)** : 코드 변경 시 기존 코드 변경X  
- **L(리스코프 치환)** : 자식 클래스는 부모 역할을 대체가능해야 함  
- **I(인터페이스 분리)** : 클라에서 사용하지 않는 인터페이스에 의존 X  
- **D(의존 역전)** : 구현보단 추상화

---

### KISS (Keep It Simple, Stupid)

"단순하게 유지해라, 멍청아"  
- 코드 단순주의의 원칙 (유지보수 / 가독성 목적)

---

### DRY (Don’t Repeat Yourself)

"코드 반복하지마!!!!!"  
- 코드 반복 혐오주의의 원칙  
- 중복 코드는 클래스로, 유지보수 목적

---

### YAGNI (You Ain’t Gonna Need It)

"이거 당장 필요해? 아니면 만들지 마 !!!!!!!!"  
- 개발 시간 절약의 원칙 (개발 시간 관리 목적)

---

### SW License란?

소프트웨어의 저작권자(개발자, 회사)가 해당 소프트웨어를 다른 사람이 사용할 때  
허용되는 범위, 조건, 제약을 명시해둔 문서

---

#### 라이선스의 종류

- **프리웨어(Freeware)** : 무료 사용 가능, 대부분 수정/재배포 금지  
- **쉐어웨어(Shareware)** : 무료 체험판(이후 유료 구매 필요), 기능 or 사용기간 제한  
- **오픈소스 라이선스 (Open Source License)** : 소스코드를 공개하고, 누구나 사용/수정/재배포 가능

---

#### 오픈소스 라이선스 종류 요약

| 종류 | 상업적 사용 | 소스 공개 의무 | 요약 |
|------|-------------|----------------|------|
| MIT | O | X | 사용/수정/재배포 자유, 표시만 하면 됨 |
| Apache 2.0 | O | X | MIT + 특허 보호 조항 |
| GPL | O | O | 사용하면 소스코드 공개 의무 |
| LGPL | O | 조건부 | GPL보다 덜 엄격, 라이브러리 수정시에만 공개 |
| BSD | O | X | MIT와 비슷, 자유로운 사용과 배포 가능 |

---

## Spring Security(2-3)

### Filter

Filter는 HTTP 요청/응답을 가로채서 가공하거나 검사하는 역할을 한다.  
이 단계에서 로그인 처리, 권한 검사, JWT 해석 등을 수행한다.  

처리 순서는 다음과 같다:  
**클라이언트 → FilterChain → SecurityFilter → Controller**

---

### JWT

JWT는 서버가 사용자를 인증한 후, 인증 정보를 서명된 문자열 토큰으로 만들어 클라이언트에게 전달하는 방식이다.  
클라이언트는 이후 요청마다 이 토큰을 Authorization 헤더에 담아서 서버에 보낸다.  
서버는 이 토큰만으로 DB 조회 없이 사용자 정보를 파악할 수 있다.

JWT는 세 부분으로 구성된다.

1. **Header**: 예를 들어, `eyJhbGciOiJIUzI1NiJ9`는 사용된 알고리즘 정보를 담는다.  
2. **Payload**: 예시로, `eyJ1c2VySWQiOjEyMywgcm9sZSI6IlVTRVIifQ`는 사용자 정보 등을 포함한다.  
3. **Signature**: `SflKxwRJSMeKKF2QT4fwpMeJf36POk6yJV_adQssw5c`는 토큰의 위조를 방지하기 위한 서명이다.

---

### Session

Session은 서버가 사용자 정보를 메모리에 저장하고, 클라이언트는 쿠키를 통해 식별되는 방식이다.  
작동 방식은 다음과 같다.

1. 로그인에 성공하면 서버는 세션 ID와 사용자 정보를 메모리에 저장한다.  
2. 클라이언트는 쿠키에 `JSESSIONID`를 보관한다.  
3. 이후 요청 시, 서버는 쿠키에 포함된 세션 ID를 통해 사용자를 식별한다.

#### 단점

- 서버가 상태(state)를 기억해야 하기 때문에 상태 기반(stateful) 시스템이다.  
- 서버가 재시작되면 세션 정보가 날아간다.  
- 여러 서버를 사용하는 수평 확장 구조에 적합하지 않다.

---

### CORS (Cross-Origin Resource Sharing)

CORS는 서로 다른 도메인 간 요청을 허용할지 여부를 결정하는 브라우저의 보안 정책이다.  
기본적으로 브라우저는 출처(origin)가 다른 요청을 차단한다.

예시:  
프론트엔드: `http://localhost:3000`  
백엔드: `http://localhost:8080`  
→ CORS 오류 발생

#### 해결 방법

Spring에서는 WebMvcConfigurer를 설정하여 특정 origin의 요청을 허용할 수 있다.

---

### CSRF (Cross Site Request Forgery)

CSRF는 사용자가 의도하지 않은 요청을 다른 사이트에서 몰래 보내도록 유도하는 공격이다.  

예: 사용자가 로그인된 상태에서 다음과 같은 이미지 태그를 클릭하면 실제로 은행 송금 요청이 발생할 수 있다.  
`<img src="http://내은행.com/transfer?to=attacker&amount=10000">`

#### 방어 방법

- POST 요청에 CSRF 토큰이라는 숨겨진 값을 포함시킨다.  
- 서버는 이 값이 없거나 위조되었을 경우 요청을 차단한다.  
- Spring Security에서는 CSRF 보호가 기본으로 활성화되어 있다.

---

### OAuth

OAuth는 제3자 애플리케이션이 사용자의 비밀번호를 몰라도, 사용자의 리소스에 대한 접근 권한을 위임받을 수 있도록 해주는 인증 방식이다.

#### 예시: 구글 로그인 연동

1. 사용자가 웹사이트에서 Google 로그인을 시도하면, Google 로그인 페이지로 리디렉션된다.  
2. 사용자는 로그인하고, 해당 앱에 정보 제공을 동의한다.  
3. Google은 임시 인증 코드(Authorization Code)를 웹사이트에 전달한다.  
4. 웹사이트는 이 코드를 다시 Google에 보내 Access Token을 요청한다.  
5. 발급받은 Access Token으로 Google API를 호출해 사용자 정보를 얻는다.

→ 비밀번호 없이도 안전하게 사용자 인증을 위임할 수 있다.

---

## 트랜잭션(2-4)

### 개념

데이터베이스 작업을 하나의 논리적인 작업 단위로 묶은 것  
→ 전부 성공 or 실패하는 작업

예시:  
1. A 계좌에서 10000원 출금  
2. B 계좌에 10000원 입금  
→ 이 두 작업은 함께 성공하거나, 실패 시 모두 롤백되어야 함

---

### 4가지 성질 (ACID)

- **A (원자성)** : All or Nothing, 트랜잭션 안의 작업은 모두 성공/실패  
- **C (일관성)** : Before == After, 트랜잭션 전후의 DB상태가 일관되어야 함  
- **I (격리성)** : 다른 트랜잭션과 격리, 동시에 작업이 여러 개 실행돼도 서로 영향 X  
- **D (지속성)** : 성공한 결과는 영구 저장, 시스템 오류가 나도 성공한 트랜잭션 결과는 유지

---

## Docker(2-5)

### 개념

애플리케이션을 실행하는 데 필요한 모든 환경을 통째로 컨테이너로 감싸서 배포하고 실행할 수 있게 해주는 기술  
→ 약간 종합선물세트 같은 느낌

---

### 왜?

- 내 컴퓨터는 되는데, 서버에선 안 됨  
- 실행하기 위한 환경 설정이 복잡함 (Java, Python, DB 등)  
- 배포 시 개발자마다 환경이 달라 오류 발생

---

### 용어

- **Image** : 앱, 환경을 포함한 실행 가능한 패키지  
- **Container** : 이미지로부터 만들어진 실행 중인 인스턴스  
- **Dockerfile** : 이미지 생성을 위한 설정 파일 (설계도)  
- **Docker Hub** : 이미지 저장소

---

### 예시

Java 앱 실행을 위한 Dockerfile 예시

FROM openjdk:17  
COPY ./build/libs/app.jar /app.jar  
CMD ["java", "-jar", "/app.jar"]

이미지 빌드:  
docker build -t my-java-app .

컨테이너 실행:  
docker run -p 8080:8080 my-java-app

---

## Docker Compose

### 개념

여러 개의 컨테이너를 한 번에 정의하고 실행할 수 있게 도와주는 도구

---

### 왜?

하나의 프로젝트가 여러 개의 컨테이너를 가지게 될 때,  
각 컨테이너를 하나하나 실행하고 연결하는 것이 번거로워서

---

### 예시

docker-compose.yml 예시

version: "3"  
services:  
 backend:  
  build: ./backend  
  ports:  
   - "8080:8080"  
  depends_on:  
   - db  
 db:  
  image: mysql:8  
  environment:  
   MYSQL_ROOT_PASSWORD: root123  
   MYSQL_DATABASE: myapp  
  ports:  
   - "3306:3306"

실행: docker-compose up  
중지: docker-compose down

---

## Spring Libraries(Web, Data JPA)(3-1)

### -Spring Web-

@Controller, @RestController, @RequestMapping 등을 통해 웹 요청을 처리하고, 응답을 생성하는 기능을 제공하는 핵심 모듈

---

#### Gradle

implementation 'org.springframework.boot:spring-boot-starter-web'  
spring-web, spring-webmvc, tomcat 포함

---

#### 왜?

웹 애플리케이션에서 사용자 요청을 Java 코드로 처리하고, 결과를 브라우저에 돌려주는 역할을 자동화

---

#### 제공 기능

1. 웹 요청을 받아주는 기능  
2. 요청에 담긴 데이터를 꺼내주는 기능  
3. 응답을 만들어주는 기능

---

### Data JDBC vs Data JPA

| 항목 | JDBC | JPA |
|------|------|-----|
| 철학 | SQL 중심 (DB 기준) | 객체 중심 (Java 기준) |
| 연관관계 | 직접 처리 | 자동 처리 |
| 쿼리 성능 | 예측 쉬움 | 예측 어려움 |
| 난이도 | 낮음 | 높음 |
| 학습 곡선 | 완만 | 가파름 |
| 추천 대상 | CRUD API 서버 | 복잡한 도메인 |

---

## Spring Libraries(Validation, Actuator)(3-2)

### -Spring Validation-

입력값 검증을 어노테이션으로 자동화 (e.g., @NotBlank, @Email)

컨트롤러에서 @Valid를 사용해 DTO 검사 → 실패 시 자동 400 응답 반환

---

### -Spring Boot Actuator-

운영 중인 애플리케이션 상태를 실시간으로 확인  
health, info, metrics 등 다양한 엔드포인트 제공

---

## Spring Libraries(Security)(3-3)

### 개념

Spring 기반 인증, 인가, 보안 설정을 담당하는 프레임워크

---

### 기능

- 인증/인가  
- 비밀번호 암호화  
- CSRF, CORS  
- 세션, 토큰 로그인  
- 커스터마이징 가능

---

### 흐름

1. 로그인 요청  
2. 검증  
3. 세션 생성 or 토큰 발급  
4. 인증 확인  
5. 권한 처리

---

### 용어

- UserDetailsService  
- UserDetails  
- PasswordEncoder  
- Authentication  
- SecurityContext  
- FilterChain
