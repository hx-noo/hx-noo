# 선케어 제품ㆍ성분 분석 서비스 - 구현 기여 및 문제 해결

## 1. 로그인·JWT 인증

- **구현 배경:** 프로필 조회·수정 요청에서 로그인한 사용자를 확인하고 일관된 인증 기준 적용 필요
- **설계·구현:** `bcrypt` 해시 검증 후 JWT 발급 · 인증 처리가 API마다 흩어지지 않도록 토큰 검증을 공통 미들웨어로 분리
- **결과:** 검증한 사용자 정보를 `req.user`로 전달해 프로필 API에 활용 · 로그인 응답에서 `password_hash` 제외

[관련 커밋 — JWT 인증 미들웨어 적용 ↗](https://github.com/2026-SMHRD-KDT-HealthCare-5/Sunsu_Care/commit/72a7c91d1ff27a704f43187f6bc71d689a1190ab)

## 2. 성분 정제·관계 데이터 구축

- **구현 배경:** 제품 성분명의 괄호·공백 등 표기 차이로 식약처 성분 정보와의 단순 비교에 한계
- **설계·구현:** 괄호·번호·공백 정리 후 비교하고 미매칭 항목은 공백 제거 후 재비교 · 제품별 성분 조회에 활용하도록 제품·성분 ID로 관계 저장
- **결과:** 제품 **84개**의 관계 데이터 **3,708건** 구축 · 고유 성분명 **725/815개, 약 89.0% 매칭**

수치는 포트폴리오의 최종 집계 기준이며, 3,708건은 제품–성분의 연결 건수입니다.

[관련 커밋 — 성분명 정제·제품별 성분 저장 ↗](https://github.com/2026-SMHRD-KDT-HealthCare-5/Sunsu_Care/commit/219e9f8b1eb5348dab6d1a627e9e268316ba2f6b)

## 3. Service·Repository 계층 분리

- **구현 배경:** Service에 비밀번호 검증·JWT 발급과 SQL 작성·실행이 섞여 서로 다른 역할을 함께 관리
- **설계·구현:** 인증 규칙과 DB 쿼리를 분리해 수정할 수 있도록 SQL·DB 접근을 Repository로 이동
- **결과:** Service는 인증·결과 판단, Repository는 사용자 조회·저장을 담당하도록 책임 구분

[관련 커밋 — 인증 Service·Repository 분리 ↗](https://github.com/2026-SMHRD-KDT-HealthCare-5/Sunsu_Care/commit/e938f0600d86a81785a0383a4ee97f5665a1c1a1)

## 4. 중복 가입 요청의 응답 오류 해결

- **문제:** 중복 이메일 가입 요청 시 오류 응답 대신 연결 종료
- **원인:** `mysql2/promise` 모듈에 callback 방식의 호출·결과 처리 혼용
- **해결:** DB 오류가 응답 처리까지 전달되도록 `async/await`·`try/catch`로 통일 · 중복 가입은 서버 오류와 구분해 `ER_DUP_ENTRY`를 **409**로 응답
- **검증:** Thunder Client에서 신규 가입 **201**, 중복 가입 **409**와 JSON 응답 확인

[관련 커밋 — 비동기 호출·오류 전달 방식 통일 ↗](https://github.com/2026-SMHRD-KDT-HealthCare-5/Sunsu_Care/commit/a59cfb2a9301e1e1d2e644acf9ed8575db98f474)
