# 📋 Gopang MSA Architecture

**Gopang**은 **Spring Boot** 기반의 마이크로서비스 아키텍처(MSA) 프로젝트로,  
Eureka Server를 통한 서비스 디스커버리, Spring Cloud Config Server를 통한 설정 관리,  
API Gateway Server를 통한 트래픽 라우팅 등을 적용하여 확장성과 유연성을 극대화했습니다.  
본 문서에서는 프로젝트 구조, 실행 방법, 사용된 기술 스택 및 주의사항을 소개합니다.

---

## 📖 참고 사이트

- **Tistory 블로그**: MSA 설계 및 구현 과정에서 얻은 인사이트와 학습 내용을 제 블로그에 정리해 두었습니다.  
  더 자세한 정보와 개발 여정을 확인하고 싶다면, [Tistory 블로그](https://myinfo503.tistory.com/)를 방문해 주세요!

---

## 🚀 프로젝트 개요

이 프로젝트는 MSA 원칙을 준수하여 마이크로서비스들 간의 책임을 명확히 분리하고,  
각 서비스가 독립적으로 배포 및 확장이 가능하도록 설계되었습니다.  
다양한 서비스들은 Docker Compose로 컨테이너화하여 손쉽게 관리할 수 있으며,  
각 마이크로서비스는 **Eureka Server**에 등록되어 **Gateway Server**를 통해 라우팅됩니다.

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
서비스 디스커버리를 담당하며, 각 마이크로서비스들이 Eureka Server에 등록되고 Eureka Client로 동작합니다.
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
   - 서비스 간의 결합도를 낮추고 독립적으로 배포 가능.
   - Eureka, Gateway, Config Server 등으로 구성된 Spring Cloud 생태계 활용.

2. **Docker & Docker Compose**  
   - 각 마이크로서비스를 도커 컨테이너로 패키징하여 배포 용이성 강화.
   - `docker-compose.yml` 하나로 모든 서비스 일괄 실행 가능.

3. **CI/CD 파이프라인 (Jenkinsfile)**  
   - Jenkinsfile을 통해 코드 변경 시 자동 빌드 및 배포 설정.
   - 테스트, 빌드, 배포의 자동화를 통한 안정적 릴리즈 지원.

4. **확장성 및 유연성**  
   - 트래픽 폭주 시 마이크로서비스 단위로 스케일 아웃 가능.
   - 특정 서비스 장애 발생 시 다른 서비스에 영향 최소화.

---

## 🎥 데모 (예시)

> 실제 실행 화면 GIF나 스크린샷을 첨부하세요.  
> 예) API 호출 예시, Docker Compose 실행 화면, Eureka Dashboard, Gateway 라우팅 등.

---

## 🛠️ 프로젝트를 진행하며 느낀 점

- MSA 환경에서 **서비스 간 통신**(RestTemplate, FeignClient 등)의 중요성을 깨달았습니다.  
- **배포 자동화**(CI/CD)와 **컨테이너 오케스트레이션**(Docker Compose, Kubernetes 등)의 필요성을 체감했습니다.  
- 마이크로서비스 개별 기능 구현뿐 아니라, **관찰성**(Observability)과 **모니터링**(Prometheus, Grafana)을 도입하는 것이 향후 과제입니다.

---

## 🛠️ 사용된 기술

- **Spring Boot** / **Spring Cloud** (Eureka, Config, Gateway 등)
- **Gradle**: 빌드 및 의존성 관리
- **Docker & Docker Compose**: 컨테이너 및 오케스트레이션
- **Jenkins**: CI/CD 파이프라인 구현
- (선택) **Nginx**: Reverse Proxy 및 정적 파일 서빙

---

## 🔒 보안 및 주의사항

1. **Github**에 소스코드를 공개할 때, 민감 정보(데이터베이스 비밀번호, JWT Secret 등)를 `.gitignore`나 **Vault** 등에 안전하게 보관하세요.
2. **AWS Key**나 **SMTP 비밀번호**와 같은 자격 증명 정보를 환경 변수로 관리하세요.
3. MSA 환경에서 서비스 간 통신 시 인증/인가를 고려하세요. (JWT, OAuth2 등)

---

## 🛠️ 추가 참고

- 현재 프로젝트는 **개발 및 테스트 단계**입니다.

---

## 📧 문의

- 프로젝트 관련 문의: [이메일](mailto:jinsu8828@gmail.com)  
- 자세한 설명 및 기술 블로그: [Tistory 블로그](https://myinfo503.tistory.com/)
