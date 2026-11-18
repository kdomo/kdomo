<div align="center">
<a href="mailto:dongho5088@naver.com">
<img src="https://img.shields.io/badge/mail-ffffff?style=flat-square&logo=Gmail&logoColor=000000"/></a>
<a href="https://instagram.com/d._.omo">
<img src="https://img.shields.io/badge/Instagram-ffffff?style=flat-square&logo=Instagram&logoColor=000000"/></a>
</div>

<br/>

### Open Source Contributions

**[Armeria](https://github.com/line/armeria)** · LINE

- [#6975](https://github.com/line/armeria/pull/6975) - Let GrpcService specify a maximum bound for grpc-timeout (2026.10)
  - 서버가 클라이언트의 `grpc-timeout` 요청을 신뢰할지 무시할지만 선택할 수 있어, 신뢰할 수 없는 클라이언트의 과도한 타임아웃 요구를 제한할 수단이 없던 문제. 공개 API `GrpcTimeoutPolicy`를 설계해 상한 / 오프셋 / 메서드별 결정을 표현 가능하게 함
  - 메인테이너 리뷰로 설계 3회 조정 - 네이밍, 무한 타임아웃을 sentinel 대신 `null`로 표현(grpc-java의 `@Nullable Deadline`, Envoy의 필드 존재 방식 선례 조사 후 제안), `withOffset` 인터페이스 메서드를 팩토리 인자로 흡수
  - 리뷰 반영 중 음수 헤더로 서버 내부 신호를 흉내낼 수 있는 경로와 `grpc-timeout: 0S`의 의미가 바뀌던 회귀를 발견, 세 커밋에서 동작을 비교 실행해 검증 후 수정
  - `new feature` / Merged into `main` / Milestone `1.42.0` / 릴리스 노트 기여자 등재 / +806 -41 (7 files)
- [#7133](https://github.com/line/armeria/pull/7133) - Delegate renderStatus when composing a ServiceErrorHandler (2026.11)
  - `ServiceErrorHandler.orElse()`가 람다를 반환해, `@FunctionalInterface`의 기본 구현(null 반환)이 쓰이면서 합성된 핸들러가 `renderStatus()`를 양쪽 어디에도 위임하지 않던 버그. 호출부에 `assert response != null`이 있어 `HttpStatusException`이 500으로 끝나던 문제
  - 다른 작업 중 테스트 6건이 400 대신 500을 반환해 발견. 베이스라인 비교로 원인을 특정하고, 변경 범위를 좁혀가며 핸들러에 들어오는 예외를 확인한 뒤 `ServerErrorHandler.orElse()`와 대조해 차이를 규명
  - 원래 작업과 독립적인 버그라 별도 PR로 분리
  - `defect` / Merged into `main` / Milestone `1.43.1`
- [#7051](https://github.com/line/armeria/issues/7051) - `grpc-timeout` is applied from the service invocation, not from when the request arrived (2026.10)
  - 클라이언트가 전파한 데드라인이 요청 도착 시점이 아니라 서비스 호출 시점부터 측정되는 문제 제기. 400ms 선행 지연 + 1000ms 타임아웃 조건에서 1416ms 발동을 실측하고 `SET_FROM_START` 적용 시 1022ms로 정렬됨을 확인
  - `defect` 분류 / 해결되며 close
- [#7089](https://github.com/line/armeria/pull/7089) - Fail an exhausted gRPC deadline with DEADLINE_EXCEEDED (2026.10, 리뷰 중)
  - 데드라인이 이미 소진된 요청이 응답 시작 전에 취소되어 HTTP 503(`UNAVAILABLE`)으로 나가던 문제. 재시도가 안전하다는 뜻이라 느린 파이프라인이 재시도로 번지는 상황
  - 메인테이너가 제안한 `ServiceErrorHandler` 방식을 구현해본 뒤, gRPC 호출이 이미 응답을 소유해 해당 예외가 핸들러에 닿지 못함을 측정으로 보이고 좁은 수정으로 정리

**[Spring Boot](https://github.com/spring-projects/spring-boot)**

- [#51568](https://github.com/spring-projects/spring-boot/pull/51568) - Some assertion messages name the wrong parameter (2026.09)
  - 메서드에 존재하지 않는 파라미터를 가리키던 Assert 진단 메시지 3곳 수정
  - `type: bug` / Merged into `4.0.x` / Milestone `4.0.9`
- [#51562](https://github.com/spring-projects/spring-boot/issues/51562) - `OAuth2ResourceServerProperties.Jwt` has a setter that does not match its getter (2026.09)
  - getter/setter 프로퍼티 이름 불일치로 문서에 없는 설정 키가 바인딩되던 문제 제보
  - `type: bug` / Milestone `4.1.2`
- [#51509](https://github.com/spring-projects/spring-boot/pull/51509) - Customizer null checks in HTTP client builders assert the wrong attribute (2026.08)
  - `mergedCustomizers`가 파라미터 대신 필드를 검증해 null 인자가 걸러지지 않던 문제 수정
  - `type: bug` / Merged into `4.0.x` / Milestone `4.0.9`
- [#34868](https://github.com/spring-projects/spring-boot/pull/34868) - Use banner location constant (2023.04)
  - 하드코딩된 배너 경로 문자열을 `DEFAULT_BANNER_LOCATION` 상수로 대체
  - 반영됨 (팀이 직접 커밋으로 적용 후 PR close)

**[Spring Data MongoDB](https://github.com/spring-projects/spring-data-mongodb)**

- [#5242](https://github.com/spring-projects/spring-data-mongodb/pull/5242) - Remove redundant assertion in EncryptionOptions constructor (2026.09)
  - `@Nullable`인 `queryableEncryptionOptions` 대신 `key`를 중복 검증하던 assertion 제거
  - Merged into `main` / Back ported to `5.1.x`, `5.0.x` / Milestone `5.0.8`

**[Spring Data Commons](https://github.com/spring-projects/spring-data-commons)**

- [#3540](https://github.com/spring-projects/spring-data-commons/pull/3540) - Fix null check in withStringMatcher (2026.09)
  - `withStringMatcher`가 파라미터 `defaultStringMatcher` 대신 `ignoredPaths`를 검증해 null 인자가 걸러지지 않던 문제 수정
  - `type: bug` / 반영됨 (팀이 직접 커밋으로 적용 후 PR close, 커밋 작성자 유지) / Milestone `4.0.9`

### Work Experience

#### 바틀 (BOTTLE)
`개발팀 / 팀장` · 2024.11 ~ 2026.07

**자사 제품 — [잉크챗 (INK Chat)](https://inkchat.ai)** · AI 캐릭터 채팅 · 백엔드 / 웹 / 앱 단독 개발

- 헥사고날 5-모듈 Spring Boot(Java 17) 백엔드 단독 설계 — 44개 도메인 · REST API 426 · 엔티티 114 (커밋의 92%)
- Claude / GPT / Gemini / Grok 멀티 LLM 라우팅(가용성 폴백 · refusal 감지) + Prompt Caching으로 캐시 히트율 48.6%, 토큰 비용 절반 절감
- 결제 멱등성(Redis SETNX + PortOne / Google Play / Apple 검증) 결제 1만 건 무사고, MySQL 읽기·쓰기 분리로 채팅 1,117만 건 운영
- React 19 웹(최근 12개월 방문자 22.6만+) + React Native 앱 단독 개발, GitHub Actions / Docker / AWS Blue-Green 무중단 배포 자체 구축

**외주 프로젝트** · 기획 / 백엔드 / 프론트엔드 / 인프라 전 영역 단독

- **[과기부 전국민 AI 경진대회](https://aichallenge4all.or.kr/)** · 정부 주관 공공 웹 서비스 `단독`
  - [챔피언](https://ai-champion.or.kr/) / [루키](https://ai-rookie.or.kr/) / [오류찾기](https://ai-checker.or.kr/) 등 5개 사이트 멀티테넌트 포털 (API 218 · DB 40), 방문자 52만+ / PV 370만+ 운영
  - TOTP 2FA · IP 화이트리스트 · 감사 로그 등 공공급 보안, R2 업로드 · 심사 자동배정 · Cron 통계 자동화
- **마리아병원 재진 예약** · [일산](https://rilsan.mariababy.com/) / [평촌](https://rpc.mariababy.com/) 2개 지사 `단독`
  - 의사별 동적 타임슬롯 예약 엔진 + 카카오 알림톡 자동화(Vercel Cron), 2지사 합산 방문자 8.2만+
- **[워크랩스튜디오 보행분석](https://worklab-client-web.vercel.app/)** · 교보생명 사내벤처 `단독`
  - Spring Boot / 웹 / React Native 앱 3-tier 단독 구축(API 160+) + Naver ClovaStudio LLM 피드백, NCP 배포
- **[사람인HS CRM 재구축](https://hsdb.co.kr/)** · 레거시 CRM AI 기반 재설계 `단독`
  - 안드로이드 통화 녹음 → 번호 매칭 → STT → AI 분석 파이프라인 + GPT 이력서 자동 파싱 / 인재 검색

#### [SCK Corp](https://sckcorp.co.kr/)
`개발팀 / 인턴` · 2024.01 ~ 2024.04

- Microsoft Azure / Office 365 구독 자동화 플랫폼 `IT Marketplace` 백엔드 개발 (구독 생성/갱신/취소/정산)
- Vue.js 기반 구독 관리 UI 개발, MySQL 설계 및 트랜잭션 안정성 확보
- Azure VM / Docker 인프라 운영, GitLab CI 파이프라인 구축, Power Automate(RPA) 반복 업무 자동화

#### [트라이캐치](https://www.trycatch.kr/)
`AI플랫폼팀` · 2022.05 ~ 2022.07

- [KT 사내 챗봇](https://ibot.kt.com/client/pc-web/chat.html) Solr 기반 검색 엔진 백엔드 개발
- Thymeleaf 관리자 CMS 구현(학습 데이터 CRUD / 검색 / 시각화), PostgreSQL / Redis 캐시 활용
- Jenkins / GitLab CI 파이프라인 구축

### Education

- [남서울대학교](http://nsu.ac.kr/) - 컴퓨터소프트웨어학과 (2016.03 ~ 2022.02)
- [KH정보교육원](https://khedu.co.kr/) - Java Web 개발자 양성 과정 (2021.07 ~ 2022.02)
- [제로베이스](https://zero-base.co.kr/) - 백엔드 스쿨 4기 (2022.08 ~ 2023.02), 수료생 상위 10% 우수자

### Community

- [Depromeet (디자이너와 프로그래머가 만났을 때)](https://www.depromeet.com/)
  - 13기 - 위치 기반 Share Play 서비스 [Whatnow](https://github.com/depromeet/Whatnow-Api) 백엔드 (2023.04 ~ 2023.07)
  - 14기 - 하루 10분 습관 만들기 [10MM](https://github.com/depromeet/10mm-server) 백엔드, **최우수상** (2023.11 ~ 2024.02)
  - 15기 - 운영진 (2024.06 ~ 2024.09)
- [UNITHON (유니톤)](https://www.unit.center/unithon)
  - 10기 - AI 영화 추천 서비스 [극락무비](https://www.unit.center/affb8c54-6a6f-4003-bb54-24a99d94ca9a) 백엔드 (2023.09)
  - 11기 - 스태프 (2024.04)
- [테오콘](https://www.teoconf.com) - 1회차, [2회차](https://jumpit.saramin.co.kr/contents/428) 운영진 (2023.02 ~ 2023.03)
- 테오의 스프린트 14기 - 실패 기록 서비스 [오늘의 실패](https://github.com/TodaysFail) 백엔드 (2023.02)
- 대한상공회의소 취업캠프 for KDT 멘토 (2024.06)

### Certification

- 정보처리기사 (2022.11)

<div align="center">
<br/>
<img src="https://img.shields.io/badge/Java-ffffff?style=flat-square&logo=openjdk&logoColor=000000"/>
<img src="https://img.shields.io/badge/Spring%20Boot-ffffff?style=flat-square&logo=springboot&logoColor=000000"/>
<img src="https://img.shields.io/badge/JPA-ffffff?style=flat-square&logo=hibernate&logoColor=000000"/>
<br/>
<img src="https://img.shields.io/badge/React-ffffff?style=flat-square&logo=react&logoColor=000000"/>
<img src="https://img.shields.io/badge/TypeScript-ffffff?style=flat-square&logo=typescript&logoColor=000000"/>
<img src="https://img.shields.io/badge/Next.js-ffffff?style=flat-square&logo=nextdotjs&logoColor=000000"/>
<br/>
<img src="https://img.shields.io/badge/MySQL-ffffff?style=flat-square&logo=mysql&logoColor=000000"/>
<img src="https://img.shields.io/badge/PostgreSQL-ffffff?style=flat-square&logo=postgresql&logoColor=000000"/>
<img src="https://img.shields.io/badge/Redis-ffffff?style=flat-square&logo=redis&logoColor=000000"/>
<br/>
<img src="https://img.shields.io/badge/AWS-ffffff?style=flat-square&logo=amazonwebservices&logoColor=000000"/>
<img src="https://img.shields.io/badge/Docker-ffffff?style=flat-square&logo=docker&logoColor=000000"/>
<img src="https://img.shields.io/badge/NGINX-ffffff?style=flat-square&logo=nginx&logoColor=000000"/>
<br/>
<img src="https://img.shields.io/badge/GitHub%20Actions-ffffff?style=flat-square&logo=githubactions&logoColor=000000"/>
<img src="https://img.shields.io/badge/Jenkins-ffffff?style=flat-square&logo=jenkins&logoColor=000000"/>
<img src="https://img.shields.io/badge/Grafana-ffffff?style=flat-square&logo=grafana&logoColor=000000"/>
<img src="https://img.shields.io/badge/Prometheus-ffffff?style=flat-square&logo=prometheus&logoColor=000000"/>
</div>
