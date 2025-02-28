# Spring Boot Actuator
## Spring Boot Actuator란?
Spring Boot **Actuator는 애플리케이션의 상태 및 메트릭을 모니터링하고 관리**할 수 있도록 도와주는 기능이다
- 별도의 설정 없이 **운영 환경에서 서버 상태를 쉽게 확인**할 수 있음
- **헬스 체크(Health Check), 로그, 메트릭, 환경 정보 등 제공**
- Spring Boot 2.x 이상에서는 **Spring Boot Starter Actuator**를 추가하면 바로 사용 가능
## Actuator 의존성 추가
Spring Boot에서 Actuator를 사용하려면 `spring-boot-starter-actuator` 의존성을 추가하면 된다
```
<dependency>
    <groupId>org.springframework.boot</groupId>
    <artifactId>spring-boot-starter-actuator</artifactId>
</dependency>
```
## Actuator 기본 설정
`application.properties` 또는 `application.yml`에서 설정을 추가할 수 있다
### ✅ 모든 엔드포인트 활성화 (`application.properties`)
```
management.endpoints.web.exposure.include=*
```
- 기본적으로 몇 개의 엔드포인트만 노출됨 (`health`, `info` 등)
- `*`을 사용하면 모든 **Actuator 엔드포인트를 활성화**할 수 있음
- 특정 엔드포인트만 활성화하고 싶다면, `include=health,info`처럼 설정 가능

## 주요 Actuator 엔드포인트
Spring Boot Actuator에는 다양한 **내장 엔드포인트**가 있다
| 엔드포인트 | 설명 |
| --------- | ---- |
| `/actuator/health` | 애플리케이션 상태 체크 (헬스 체크) |
| `/actuator/info` | 애플리케이션 정보 출력 |
| `/actuator/metrics` | JVM, CPU, 메모리, GC 등의 성능 메트릭 제공 |
| `/actuator/env` | 환경 변수 조회 |
| `/actuator/beans` | 스프링 빈 목록 출력 |
| `/actuator/mappings` | 컨트롤러 매핑 정보 확인 |
| `/actuator/loggers` | 로깅 레벨 설정 및 변경 |
| `/actuator/threaddump` | 현재 실행 중인 스레드 정보 제공 |
| `/actuator/httptrace` | 최근 HTTP 요청 정보 제공 |
| `/actuator/shutdown` | 애플리케이션 종료 (기본적으로 비활성화) |

## 주요 Actuator 엔드포인트 사용 예시
### ✅ /actuator/health (애플리케이션 상태 확인)
```
{
  "status": "UP"
}
```
- 🚀 **"UP"** 이면 애플리케이션이 정상적으로 동작 중
- 🔴 "DOWN"이면 장애가 발생한 상태

💡 특정 DB, Redis 등의 상태도 확인 가능!
`management.endpoint.health.show-details=always
`
```
{
  "status": "UP",
  "components": {
    "diskSpace": { "status": "UP" },
    "db": { "status": "UP" }
  }
}
```
### `/actuator/info` (애플리케이션 정보)
애플리케이션 정보를 추가하려면 `application.properties`에서 설정 가능
```
management.endpoint.info.enabled=true
info.app.name=MySpringApp
info.app.version=1.0.0
info.app.description=Spring Boot Actuator Example
```
📌 `/actuator/info` 호출 결과
```
{
  "app": {
    "name": "MySpringApp",
    "version": "1.0.0",
    "description": "Spring Boot Actuator Example"
  }
}
```
### ✅ /actuator/metrics (서버 성능 모니터링)
- `/actuator/metrics` -> 사용 가능한 모든 메트릭 목록 확인
- `/actuator/metrics/jvm.memory.used` -> JVM 메모리 사용량 확인
```
{
  "name": "jvm.memory.used",
  "measurements": [
    { "statistic": "COUNT", "value": 350000000 }
  ]
}
```

## Actuator 보안 설정 (엔드포인트 보호)
일반적으로 Actuator는 **운영 환경에서 민감한 정보를 포함**할 수 있으므로,
보안 설정을 추가하는 것이 중요하다
### ✅ Actuator 엔드포인트를 특정 사용자만 접근 가능하도록 설정
**Spring Security를 함께 사용하여 엔드포인트 보호 가능** (`application.properties`)
```
management.endpoints.web.exposure.include=*
management.endpoints.web.base-path=/monitoring
management.endpoint.shutdown.enabled=true
```
- **📌 모든 엔드포인트를** `/monitoring` **경로 아래 노출**
- 📌 `/actuator/shutdown`을 활성화하여 원격 종료 가능
### ✅ 특정 엔드포인트를 비활성화
```
management.endpoint.env.enabled=false
management.endpoint.beans.enabled=false
```
- 📌 `/actuator/env`, `/actuator/beans`를 비활성화하여 보안 강화 가능

## Prometheus란?
Prometheus는 **오픈소스 모니터링 및 경고 시스템**으로, **서버, 애플리케이션, 데이터베이스 등의 상태를 실시간으로 수집**하고 분석할 수 있는 도구다

## 주요 특징
- ✅**Pull 방식 데이터 수집** -> 대상 시스템에서 직접 메트릭을 가져옴
- ✅ **시계열 데이터 저장** ->  CPU 사용량, HTTP 요청 수 등의 데이터를 저장
- ✅ **PromQL 지원** -> 데이터를 쿼리하고 분석하는 전용 언어 제공
- ✅ **Grafana 연동 가능** -> 데이터를 시각적으로 확인 가능
- ✅ **Spring Boot Actuator와 통합 가능** -> `/actuator/prometheus`에서 메트릭 제공