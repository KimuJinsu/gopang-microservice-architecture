# 📋 Gopang MSA Architecture

**Gopang**은 **Spring Boot** 기반의 **마이크로서비스 아키텍처(MSA)** 프로젝트로,  
Eureka Server를 통한 서비스 디스커버리, Spring Cloud Config Server를 통한 설정 관리,  
API Gateway Server를 통한 트래픽 라우팅 등을 적용하여 **확장성과 유연성**을 극대화했습니다.  
본 문서에서는 프로젝트 구조, 실행 방법, 사용된 기술 스택, **관찰성(Observability)** 적용 예시 및 주의사항을 소개합니다.

---

## 📖 참고 사이트

이 프로젝트는 개발 과정에서 다양한 기술과 개념을 이해하고 구현하기 위해 **제 개인 Tistory 블로그**를 참조하며 진행되었습니다.  
블로그에는 프로젝트 진행 중 배운 기술 스택이 상세히 기록되어 있습니다.  
더 많은 정보와 저의 개발 여정을 확인하고 싶으시다면, 저의 [Tistory 블로그](https://myinfo503.tistory.com/)를 방문해 주세요!

---

## 🚀 프로젝트 개요

이 프로젝트는 MSA 원칙을 준수하여 **마이크로서비스 간의 책임**을 명확히 분리하고,  
각 서비스가 **독립적으로 배포** 및 **확장이 가능**하도록 설계되었습니다.  
다양한 서비스들은 **Docker Compose**로 컨테이너화하여 손쉽게 관리할 수 있으며,  
각 마이크로서비스는 **Eureka Server**에 등록되어 **Gateway Server**를 통해 라우팅됩니다.

**핵심 목표**  
1) 서비스 간 결합도 감소 및 독립 배포  
2) 구성(설정) 관리의 중앙 집중화  
3) API Gateway를 통한 효율적인 요청 라우팅  
4) 분산 환경에서 **관찰성(Observability)** 및 **모니터링** 도입

---

## 📂 프로젝트 구조

아래는 각 서비스 및 주요 디렉토리 구조입니다.

```bash
gopang_msa_without_oauth2-main/
├── .github/
├── .gradle/
├── .settings/
├── configserver/       # Spring Cloud Config Server
├── docker-compose/     # Docker Compose 설정
├── eurekaserver/       # Netflix Eureka Server (서비스 디스커버리)
├── gatewayserver/      # Spring Cloud Gateway (API Gateway)
├── gradle/             # Gradle Wrapper 관련
├── itemserver/         # Item(상품) 관련 마이크로서비스
├── nginx-server/       # Reverse Proxy or Nginx 설정 (선택적으로 사용)
├── orderserver/        # Order(주문) 관련 마이크로서비스
├── paymentserver/      # Payment(결제) 관련 마이크로서비스
├── .classpath
├── .gitignore
├── .project
├── build.gradle
├── gradlew
├── gradlew.bat
├── Jenkinsfile         # CI/CD 파이프라인 설정
└── README.md
```

### 1. **Config Server**  
공통 설정(Config)들을 중앙에서 관리하고 각 마이크로서비스가 필요 시 가져갑니다.
- **주요 폴더**: `src/main/java/.../config`  
- **기능**:
  - Git 저장소 혹은 로컬 폴더에서 설정 가져오기  
  - 각 마이크로서비스에 환경 변수(.yml 또는 .properties) 동적 전달

### 2. **Eureka Server**  
서비스 디스커버리를 담당하며, 각 마이크로서비스들은 Eureka Server에 등록되고 Eureka Client로 동작합니다.
- **기능**:
  - 서비스 등록 및 해제  
  - 서비스 인스턴스 상태 확인  
  - 로드 밸런싱에 필요한 서비스 목록 제공

### 3. **Gateway Server**  
클라이언트 요청을 받아 적절한 마이크로서비스로 라우팅합니다.
- **기능**:
  - 스프링 클라우드 게이트웨이를 통한 API 라우팅  
  - 필터를 사용한 공통 로직(인증, 로깅 등) 처리  
  - Eureka Server와 연동하여 동적 라우팅

### 4. **ItemServer**  
상품(아이템) 관련 기능을 담당합니다.
- **주요 내용**:
  - 상품 등록, 수정, 삭제, 조회 API 제공  
  - 데이터베이스 연동(상품 정보 저장)

### 5. **OrderServer**  
주문 관련 기능을 담당합니다.
- **주요 내용**:
  - 주문 생성, 주문 취소, 주문 조회  
  - 다른 서비스(예: ItemServer, PaymentServer)와 연동

### 6. **PaymentServer**  
결제 관련 기능을 담당합니다.
- **주요 내용**:
  - 결제 요청, 결제 승인/취소  
  - PG사 연동 혹은 가상 결제 모듈 제공

### 7. **Nginx-Server**  
(선택 사항) Reverse Proxy 또는 정적 자원 서빙을 위한 Nginx 설정 예시를 담고 있습니다.

### 8. **docker-compose**  
모든 서비스를 도커 컨테이너로 띄울 수 있도록 `docker-compose.yml` 파일을 제공합니다.
- **기능**:
  - 각 서비스(컨테이너) 간의 네트워크 설정  
  - 볼륨 마운트  
  - 환경 변수 설정

---

## 🚀 주요 기능 및 특징

1. **MSA 구조**  
   - 서비스 간 결합도 감소, 독립 배포 가능  
   - Eureka, Gateway, Config Server 등으로 구성된 Spring Cloud 생태계 활용

2. **Docker & Docker Compose**  
   - 각 마이크로서비스를 컨테이너로 패키징하여 배포 및 실행 환경 통일  
   - `docker-compose.yml` 하나로 모든 서비스 일괄 실행 가능

3. **CI/CD 파이프라인 (Jenkinsfile)**  
   - Jenkinsfile을 통해 코드 변경 시 자동 빌드 및 배포 설정  
   - 테스트, 빌드, 배포의 자동화를 통한 안정적 릴리즈 지원

4. **확장성 및 유연성**  
   - 트래픽 폭주 시 마이크로서비스 단위로 스케일 아웃 가능  
   - 특정 서비스 장애 발생 시 다른 서비스에 영향 최소화

5. **관찰성(Observability)**  
   - 분산 트레이싱 도구(예: Zipkin)로 서비스 간 호출 추적  
   - 로그 수집 및 시각화(예: ELK Stack)로 장애 원인 및 성능 분석 용이

---

## 🎥 데모 (예시)

### 1. **주문(Order) API 호출**  
아래 스크린샷은 **Postman**에서 `OrderServer`의 주문 생성 API를 호출한 모습입니다.  

![Order API Call via Postman](https://user-images.githubusercontent.com/xxxxxxx/postman_order_api.png)  
<sup>(예시 이미지를 삽입해주세요)</sup>

- **Request Body**  
  ```json
  {
    "user_id": "9",
    "ReqItemOrder": [
      {
        "itemId": 1,
        "amount": 2
      },
      {
        "itemId": 2,
        "amount": 2
      }
    ],
    "reciever_name": "서성원",
    "reciever_phone": "01062789022",
    ...
  }
  ```
  - 유저 정보, 주문하려는 상품 목록, 수령인 정보 등을 JSON 형태로 전달

### 2. **분산 트레이싱(Zipkin) 화면**  
아래는 `gopang-order` → `gopang-item` 간 API 호출 내역이 **Zipkin**에서 추적된 결과입니다.  

![Zipkin Trace](https://user-images.githubusercontent.com/xxxxxxx/zipkin_trace.png)  
<sup>(Zipkin에서 스팬(Span)들이 순차적으로 호출되는 모습)</sup>

- **Span 정보**  
  - `gopang-order: /api/v1/order` 호출 시 내부적으로 `gopang-item: /api/v1/order/item`을 호출  
  - 총 소요 시간, 각 호출 간 겹치는 구간 등을 시각화해 성능 및 장애 지점 분석에 활용

### 3. **로그 수집(Kibana) 예시**  
아래는 **Kibana** 대시보드에서 `gopang-gateway`, `gopang-order`, `gopang-item` 등의 로그가 수집된 모습입니다.

![Kibana Logs](https://user-images.githubusercontent.com/xxxxxxx/kibana_logs.png)  
<sup>(ELK 스택 예시 화면: Logstash → Elasticsearch → Kibana)</sup>

- **로그 레벨**: TRACE, INFO, ERROR 등  
- **검색/필터링**: 특정 기간, 특정 서비스 이름, 요청 ID 등으로 빠른 장애 파악 가능

---

## 🛠️ 프로젝트를 진행하며 느낀 점

- **MSA 환경에서 서비스 간 통신(RestTemplate, FeignClient 등)의 중요성**  
  - 마이크로서비스 간 API 호출 시 발생하는 예외 처리, 타임아웃, 재시도 메커니즘 등에 대한 이해 필요  
  - 회로 차단 패턴(Hystrix), Resilience4j 등 도입 검토

- **배포 자동화(CI/CD)와 컨테이너 오케스트레이션(Docker Compose, Kubernetes 등)의 필요성**  
  - 프로젝트 초기에는 수동 배포를 진행했으나, 서비스가 많아질수록 자동화가 필수적임을 느낌  
  - Jenkinsfile을 통해 빌드–배포 과정을 자동화하고, 추후 Kubernetes 환경으로 확장 계획

- **관찰성(Observability) 도입**  
  - Zipkin을 통한 **분산 트레이싱**과 ELK Stack/Kibana를 통한 **로그 수집**은 운영 상 크게 도움이 됨  
  - 추가로 Prometheus + Grafana로 **메트릭 모니터링**을 하면 리소스 사용량, 응답 시간 등을 시각화 가능

- **보안 강화 필요**  
  - MSA 간 통신 시 **JWT** 혹은 **OAuth2** 등을 통한 인증/인가 고려  
  - 민감 정보(DB 암호, API Key 등)는 `.gitignore`나 Vault 같은 안전한 스토리지에 보관

---

## 🛠️ 사용된 기술

- **Spring Boot / Spring Cloud**  
  - Eureka, Config, Gateway 등 Netflix OSS 및 Spring Cloud 생태계
- **Gradle**  
  - 빌드 및 의존성 관리
- **Docker & Docker Compose**  
  - 컨테이너를 통한 배포 및 환경 통일
- **Jenkins**  
  - CI/CD 파이프라인 구현
- (선택) **Nginx**  
  - Reverse Proxy 또는 정적 파일 서빙
- **Zipkin / ELK Stack**  
  - 분산 트레이싱(Zipkin), 로그 수집·분석(Elasticsearch + Logstash + Kibana)

---

## 🔒 보안 및 주의사항

1. **소스코드 공개 시 민감 정보 보호**  
   - DB 비밀번호, JWT Secret, OAuth Key 등은 절대 노출 금지  
   - `.gitignore`나 환경 변수, Vault 등을 통해 안전하게 관리

2. **MSA 인증/인가**  
   - 서비스 간 통신 시 인증 토큰(JWT) 필수 검증  
   - 필요한 경우 게이트웨이 레벨에서 공통 필터를 통해 인증/인가 구현

3. **자격 증명 분리**  
   - AWS Key, SMTP 비밀번호 등은 CI/CD 서버(Jenkins)나 Secret Manager를 활용  
   - Dev/Prod 등 환경별 Secret 정책 구분

4. **무중단 배포 전략**  
   - 트래픽이 많은 환경에서는 Blue-Green, Rolling Update 등 적용 고려  
   - 장애 발생 시 빠른 롤백(rollback) 메커니즘 필요

---

## 🛠️ 추가 참고

- 현재 프로젝트는 **개발 및 테스트 단계**입니다.  

---

## 📧 문의

- 프로젝트 관련 문의: [이메일](mailto:jinsu8828@gmail.com)  
- 자세한 설명 및 기술 블로그: [Tistory 블로그](https://myinfo503.tistory.com/)
