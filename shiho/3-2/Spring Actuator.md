# Spring Actuator
## 정의
- Spring Boot 애플리케이션의 상태 및 메트릭을 모니터링하는 라이브러리
- /actuator 엔드포인트를 통해 애플리케이션 정보를 제공
- 기본적으로 Health, Metrics, Info, Loggers 등 다양한 엔드포인트 지원
## 특징
- 애플리케이션 모니터링 기능 제공
- 다양한 엔드포인트 지원 (/actuator/health, /actuator/metrics 등)
- 외부 모니터링 도구(Prometheus, Grafana 등)와 연동 가능
## 장점s
- 운영 환경에서 서비스 상태를 쉽게 확인 가능 
- 커스텀 엔드포인트 추가 가능 (필요한 정보만 노출 가능) 
- Prometheus, Zipkin, ELK 등과 연동하여 실시간 모니터링 가능
## 단점
- 보안 설정 필요 (기본적으로 중요한 정보가 노출될 가능성이 있음) 
- 리소스 사용 증가 (지속적인 모니터링으로 인해 시스템 리소스를 소모할 수 있음)
# Prometheus
## 정의
- 오픈소스 모니터링 및 경고 시스템
- 시계열(time-series) 데이터 저장 및 조회 기능 제공
- Spring Actuator와 연동하여 애플리케이션 메트릭 수집 가능
## 특징
- Pull 방식으로 데이터를 가져와 저장
- **PromQL(Prometheus Query Language)**을 사용하여 데이터 조회 및 분석 가능
- Exporter를 통해 다양한 시스템과 연동 가능 (Node Exporter, JMX Exporter 등)
## 장점
- 확장성이 뛰어나며 클라우드 네이티브 환경에서 사용하기 적합 
- 다양한 시각화 도구(Grafana 등)와 쉽게 통합 가능 
- 경량화된 설계로 빠른 데이터 수집 및 처리 가능
## 단점
- 데이터 저장 용량이 증가할 경우 성능 튜닝 필요 
- Push 방식 지원이 제한적이며, 일부 환경에서는 설정이 복잡할 수 있음