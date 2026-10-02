# GildongE — AI 차량 어시스턴트 백엔드

**장치마다 형태가 다른 차량 데이터를 5개 도메인 REST API로 묶은 Spring Boot·MongoDB 백엔드**

[![Award](https://img.shields.io/badge/2025%20캡스톤디자인%20경진대회-심화부문%20은상-C0C0C0)](#성과)
[![Paper](https://img.shields.io/badge/한국정보기술학회-하계학술대회%20논문%20공저-blue)](#성과)
[![Stack](https://img.shields.io/badge/Spring%20Boot%203-Java%2017-6DB33F?logo=springboot&logoColor=white)](#기술-스택)
[![DB](https://img.shields.io/badge/MongoDB-document%20model-47A248?logo=mongodb&logoColor=white)](#설계-판단)

6인 학생 팀이 만든 AI 차량 어시스턴트(졸음 감지 장치, 모바일 앱, 차량 매뉴얼 질의응답)에서 **백엔드와 데이터베이스를 맡은 저장소**입니다.
장치·앱·차량 관리 기능에서 제각각 발생하는 데이터를 사용자 단위로 저장하고 일관된 REST API로 제공합니다.

- **기간** 2025.03 ~ 2025.06 · **팀** 학생 6인 · **담당** 차량 도메인 백엔드, MongoDB 문서 모델, 카카오 로그인 흐름, 주행 데이터 집계

![GildongE 프로젝트 포스터](gildongE_poster.jpg)

> 이 백엔드에 **SSE 기반 실시간 알림**을 얹은 확장본은 [gildonge-alert](https://github.com/jgjoe/gildonge-alert)에 있습니다.

---

## 주요 기능

| 영역 | 구현 내용 | 확인 위치 |
|---|---|---|
| 차량 | 차량·차종 등록, 조회, 수정, 삭제 | `controller/CarController`, `CarModelController` |
| 소모품 | 소모품 정보와 교체 예정일 관리 | `controller/ConsumableController` |
| 주행패턴 | 주행 점수 기록·조회, **사용자별 주간 평균 집계** | `DrivingPatternService`, `WeeklyAverageResponse` |
| 사용자 | 사용자 등록·조회·수정 | `UserController`, `UserService` |
| 카카오 로그인 | 인가 코드를 액세스 토큰으로 교환하고 카카오 사용자를 조회·등록 | `KakaoOAuthService`, `AuthController` |
| API 문서 | 주요 요청·응답 스키마와 오류 응답 정리 | `src/main/resources/static/openapi.yaml` |

## 설계 판단

### 출처마다 다른 데이터를 도메인으로 나눴다

차량 데이터는 **출처마다 형태가 다릅니다.** 졸음 감지 장치가 올리는 값, 앱에서 사용자가 입력하는 정비 기록,
주행 중 누적되는 점수가 같은 스키마에 들어가지 않습니다. 관계형 테이블로 고정하면 장치나 기능이 하나 추가될 때마다
스키마를 바꿔야 하고, 그렇다고 전부 한 컬렉션에 넣으면 조회가 무너집니다.

차량·차종·소모품·주행패턴·사용자를 각각의 도메인으로 분리해 저장 형태가 달라도 서로 영향을 주지 않게 하고,
앱에는 도메인별 REST API로 일관된 형태만 노출했습니다.

### MongoDB 문서 모델

장치와 기능별로 필드가 달라질 수 있는 차량 데이터를 수용하려고 문서형을 골랐습니다.
스키마 변경이 잦은 초기 프로토타입에서 **도메인 추가 비용을 낮추는 쪽**을 우선했습니다.

### 집계는 API가 책임진다

주행 점수는 기록만 쌓고 앱이 계산하게 두면 클라이언트마다 값이 달라집니다.
**사용자별 주간 평균 집계를 서버에서 계산해** 하나의 응답 형태로 내려보냈습니다.

### 계약을 문서로 고정

6인 팀이라 프론트·장치 쪽과 필드 이름 하나로도 어긋납니다.
OpenAPI 3.0으로 요청·응답과 오류 응답을 적어 두고 그것을 기준으로 맞췄습니다.

## 성과

- **2025 산학협력 캡스톤디자인 경진대회 심화캡스톤디자인 부문 은상** (경기대학교 소프트웨어중심대학)
- **2025 한국정보기술학회 하계 종합학술대회 논문 공저**

## 기술 스택

| 영역 | 기술 |
|---|---|
| 언어·프레임워크 | Java 17, Spring Boot 3 |
| 데이터 | MongoDB, Spring Data MongoDB |
| 인증 연동 | Kakao OAuth API |
| 문서화 | OpenAPI 3.0 |

## 실행

필수 환경변수:

```text
MONGODB_URI=mongodb+srv://...
KAKAO_CLIENT_ID=...
KAKAO_REDIRECT_URI=http://localhost:8080/api/auth/kakao/callback
```

```bash
cd gildongE
./gradlew bootRun
```

API 문서: `gildongE/src/main/resources/static/openapi.yaml`

## 만든 사람

**Jigwan Joe** — Backend

- GitHub: [@jgjoe](https://github.com/jgjoe)
- Email: jigwan.joe@gmail.com
