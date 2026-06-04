# docs/templates.md — 협업 문서 표준 구조

`knowledge_for_marketing/dashboard` 프로젝트에서 Claude Code와 Codex가 만드는 모든 협업/결과물 문서의 표준 구조다.  
이 프로젝트의 목적은 고객 방문보고서에서 파악한 니즈와 소프트웨어 기능을 연결해, 영업·엔지니어가 고객 미팅 전 빠르게 준비할 수 있는 사내 Streamlit 웹 대시보드를 구축하는 것이다.

파일명은 `docs/collab-protocol.md` §3 `파일 네이밍 규칙`에 따라 slug를 붙인다.  
모든 섹션을 반드시 채울 필요는 없다. 비워둘 때는 `해당 없음`이라고 표시한다.

---

## 인덱스

| 문서 | Owner | 트리거 | 섹션 번호 |
|---|---|---|---|
| `dev_plan_<slug>.md` | Claude Code | 복잡한 개발/분석 작업 전 1차 계획 | 1 |
| `questions_<slug>.md` | Claude Code | 대시보드 화면/분석 프레임/의사결정 질문 설계 | 2 |
| `risk_notes_<slug>.md` | Claude Code | 데이터/권한/보안/해석 리스크 점검 | 3 |
| `final_report_<slug>.md` | Claude Code | 사용자 또는 팀 공유용 최종 요약 보고서 원본. 필요 시 PDF로 변환 | 4 |
| `handoff_<slug>.md` (Claude) | Claude Code | Claude Code 세션 인계 | 5 |
| `execution_plan_<slug>.md` | Codex | Claude Code 1차 계획 보완 및 구현 계획 | 6 |
| `handoff_<slug>.md` (Codex) | Codex | Codex 작업 인계 | 7 |
| `analysis_summary_<slug>.md` | Codex | CSV/대시보드/기능 구현 결과 분석 요약 | 8 |

---

## 1. dev_plan_<slug>.md (Claude Code)

```md
# dev_plan_<slug>.md

## Goal
이번 작업으로 무엇을 만들거나 바꿀지 한두 문장.

예:
- Home 화면 1차 버전을 설계한다.
- `customer_needs.csv`와 `need_value_matching.csv`를 연결해 고객 니즈-기능 매칭 화면을 구현할 수 있도록 개발 범위를 정리한다.
- Docker Compose 기반 운영 배포 전 필요한 파일 구조와 검증 항목을 정의한다.

## Scope
포함할 일.

예:
- `business-context.md`, `DESIGN.md`, `AGENTS.md`, `CLAUDE.md`, `docs/collab-protocol.md` 기준 확인
- 4개 CSV 구조 확인
- 화면별 요구사항 정리
- Codex가 구현할 파일 범위 제안
- 검증 기준 정의

## Out of Scope
이번 작업에서는 하지 않을 일. 과잉 구현 방지.

예:
- 실제 고객 데이터 원본 수정
- `raw/`, `wiki/`, `export/` 직접 수정
- 서버 PC 배포
- 로그인 계정 정책 확정 전 권한 기능 과잉 구현
- 사용자가 승인하지 않은 고객 정보 비식별화/삭제

## Files
- 수정 예상 파일
  - `docs/dashboard-requirements_<slug>.md`
  - `docs/data-schema_<slug>.md`
  - `app/main.py`
  - `app/pages/*.py`
  - `scripts/*.py`
- 건드리면 안 되는 파일
  - 상위 폴더의 `raw/`, `wiki/`, `export/`, `scripts/`, `logs/`
  - 실제 운영 CSV 원본
  - 다른 actor가 `PROJECT.md`의 `Files locked`에 잡아둔 파일
- 새로 생성할 파일
  - slug 적용 파일명 사용

## Proposed Steps
Codex가 실행할 순서. 단계별로 짧게.

1. `PROJECT.md`와 `Files locked` 확인
2. 필요한 파일 락 등록
3. CSV 헤더와 관계 정의 확인
4. 최소 기능 단위로 구현
5. 로컬 실행 또는 정적 검증
6. 결과 파일 생성
7. Handoff Log 작성 및 파일 락 해제

## Risks
- 데이터 해석 리스크
  - `customer_name` 문자열 매칭 오류
  - `related_need_keywords`는 느슨한 연결이므로 직접 FK처럼 해석하는 오류
  - `dashboard_visible=FALSE` 데이터 노출 또는 `sensitivity_level=비공개` 항목을 경고 없이 표시할 위험
- 권한 리스크
  - admin만 수정 가능해야 하며, viewer는 조회만 가능
- 파일 충돌 가능성
  - Codex와 Claude Code가 같은 파일을 동시에 수정할 위험
- 과잉 구현 가능성
  - 1차 개발에서 로컬 조회 화면, 로그인/권한, Docker 배포, robocopy 반영을 한 번에 과잉 구현하려는 위험
- 시각/톤 일관성 리스크
  - `DESIGN.md`의 라온엑스 CI 색상, 로고, 컴포넌트 기준 미준수

## Verification
Codex가 확인해야 할 기준.

- `customer_needs.csv`, `customer_strategy.csv`, `need_value_matching.csv`, `software_features.csv` 로딩 가능 여부
- 필수 컬럼 존재 여부
- 관계 키 연결 가능 여부
- `dashboard_visible` 필터 적용 여부
- `sensitivity_level` 표시 여부
- Streamlit 로컬 실행 여부
- Docker 관련 파일이 있으면 `docker compose config` 또는 동등한 검증 수행
- 실제 CSV가 Git에 커밋되지 않도록 `.gitignore` 확인

## Handoff Needed
- 작업 끝나고 누가 이어받는지
- 어떤 산출물을 누구에게 넘기는지
- 최종 문서화가 필요한지
```

---

## 2. questions_<slug>.md (Claude Code)

```md
# questions_<slug>.md

## Decision Context
이번에 답해야 할 의사결정 한 줄.

예:
- 영업·엔지니어가 고객 미팅 전 어떤 고객 니즈와 추천 기능을 빠르게 확인해야 하는가?
- Home 화면에서 어떤 정보를 먼저 보여줘야 고객 미팅 준비 시간이 줄어드는가?
- 고객 니즈-기능 매칭의 신뢰도와 검토 상태를 어떻게 표현해야 안전한가?

## Key Questions
대시보드 설계/구현 전에 답해야 할 질문 3~7개. 각 질문은:
- 어떤 데이터로 답하는가
- 어떤 화면 또는 액션과 연결되는가

예:
1. 최근 고객 니즈 중 미팅 준비에 가장 먼저 보여줄 항목은 무엇인가?
   - 데이터: `customer_needs.csv`
   - 액션: Home 화면 카드/목록 구성
2. 고객 니즈와 기능은 어떤 키로 연결되는가?
   - 데이터: `need_value_matching.csv.need_id`, `software_features.csv.feature_id`
   - 액션: 니즈-기능-가치 매칭 화면 구현
3. 검토가 필요한 매칭은 어떻게 표시할 것인가?
   - 데이터: `need_value_matching.csv.review_status`, `confidence`
   - 액션: 검토 필요 배지, 필터, 경고 표시
4. 고객사별 대응 전략은 어떤 기준으로 요약할 것인가?
   - 데이터: `customer_strategy.csv`
   - 액션: 고객사별 대응 전략 화면 구성
5. 비공개 또는 대시보드 표시 제외 데이터는 어떻게 처리할 것인가?
   - 데이터: `sensitivity_level`, `dashboard_visible`
   - 액션: `dashboard_visible=FALSE`는 기본 숨김, `sensitivity_level=비공개`는 경고 배지 표시

## Analysis Frame
질문을 어떤 프레임으로 본 것인지.

예:
- 고객 기준: `customer_name`
- 니즈 기준: `need_id`, `need_keyword`, `need_summary`
- 기능 기준: `feature_id`, `software`, `feature_name`
- 매칭 기준: `match_id`, `review_status`, `confidence`
- 전략 기준: `strategy_id`, `main_needs`, `recommended_software`, `next_action`

## Out of Scope Questions
이번 작업에서는 답하지 않을 질문. 다음 작업으로 미룸.

예:
- 고객의 실제 구매 가능성 추정
- 예산 또는 도입 의지 추정
- 고객사 내부 의사결정 구조 추정
- 외부 인터넷 기반 고객사 최신 뉴스 자동 수집
- 사내 인증 체계와 연동한 SSO 로그인
```

---

## 3. risk_notes_<slug>.md (Claude Code)

```md
# risk_notes_<slug>.md

## Top Risks
가장 큰 리스크 3개. 각 리스크는:
- 무엇이 문제인가
- 어떤 데이터/가정에서 발생하는가
- 발생하면 어떤 결과로 이어지는가
- 어떻게 완화할 수 있는가

### 1. 고객 정보 노출 리스크
- 문제: 대시보드에 고객사명과 담당자 정보가 표시된다.
- 발생 조건: 사내망이 아닌 외부 접근, 로그인 미설정, `sensitivity_level=비공개` 데이터 노출
- 결과: 고객 정보 또는 내부 영업 정보 노출
- 완화: 사내망 접근 제한, 로그인 적용, `dashboard_visible` 필터, 비공개 배지 표시

### 2. 데이터 관계 오해 리스크
- 문제: CSV 관계를 잘못 해석하면 잘못된 추천 기능이 표시될 수 있다.
- 발생 조건: `customer_name` 문자열 불일치, `related_need_keywords`를 직접 FK로 오해
- 결과: 고객 미팅 준비 자료의 신뢰도 하락
- 완화: `need_id`, `feature_id` 기반 관계 우선 사용, 느슨한 연결은 보조 정보로만 표시

### 3. 검토 상태/신뢰도 해석 리스크
- 문제: `review_status`와 `confidence`가 검토 완료 또는 정확성을 보장하는 것으로 오해될 수 있다.
- 발생 조건: 배지 설명 부족, 필터 기준 불명확
- 결과: 검토가 필요한 매칭을 확정 정보처럼 사용
- 완화: `검토 필요/검토 완료`, `높음/중간/낮음` 의미를 UI에 명시

## Data Caveats
샘플/실제 구분, 누락 데이터, 추정 영역.

- 4개 CSV는 `export/`에서 생성된 대시보드용 구조화 데이터다.
- 원본 지식은 `wiki/`가 기준이며, 대시보드는 조회와 탐색을 위한 화면이다.
- `customer_strategy.csv`는 `customer_name` 문자열 기준으로 연결되므로 완전 일치가 필요하다.
- `software_features.related_need_keywords`는 `customer_needs.need_keyword`와 느슨하게 연결된다.
- `review_status`와 `confidence`는 사용자의 직접 검토/판단 항목이며 자동 사실 검증 결과가 아니다.

## Interpretation Limits
이 데이터로 말할 수 있는 것 / 없는 것.

### 말할 수 있는 것
- 특정 고객사가 과거 보고서에서 어떤 니즈를 보였는지
- 특정 니즈에 어떤 기능이 매칭되어 있는지
- 특정 기능이 어떤 니즈 유형과 연결될 수 있는지
- 어떤 매칭이 검토 필요 상태인지
- 어떤 고객 대응 전략이 정리되어 있는지

### 말할 수 없는 것
- 고객의 실제 구매 가능성
- 고객 예산
- 고객의 도입 의지
- 미팅 성사 가능성
- 경쟁사 대비 우위의 객관적 검증
- 보고서에 없는 고객 내부 사정

## Open Questions for User
사용자 결정이 필요한 항목.

- 로그인 계정은 하드코딩, `.env`, 별도 파일, 또는 다른 인증 방식 중 무엇을 사용할 것인가?
- 로그인 기능은 로컬 1차 개발부터 포함할 것인가, 운영 배포 직전 필수 구현으로 분리할 것인가?
- admin이 대시보드 화면에서 직접 수정할 항목은 `review_status`, `confidence`까지인지, 향후 다른 컬럼까지 확장할 것인지?
- admin 수정값은 CSV/Excel 원본에 반영할 것인지, 별도 운영 파일 또는 DB에 저장할 것인지?
- `dashboard_visible=FALSE` 데이터를 admin 검토 화면에서 볼 수 있게 할 것인지, 아니면 모든 화면에서 제외할 것인지?
```

---

## 4. final_report_<slug>.md (Claude Code)

팀장 또는 프로젝트 담당자가 5분 안에 현재 상태와 다음 액션을 판단할 수 있게 **핵심 → 근거 → 액션** 순서로 구성한다.  
`final_report_<slug>.md`를 원본으로 작성하고, 사용자 요청 또는 공유 필요 시 `final_report_<slug>.pdf`로 변환한다. `business-context.md`의 의사결정 기준을 따르되, PDF 변환을 고려한 시각·구조 요소(표지·프로젝트 상태 Snapshot·화면 구성·데이터 흐름·리스크·액션 플랜)를 추가한다.

### 페이지 구성 (총 7~9p 권장)

| # | 페이지 | 분량 | 핵심 콘텐츠 |
|---|---|---|---|
| 1 | 표지 + Executive Summary | 1p | 한 줄 결론 · 핵심 액션 3개 미리보기 |
| 2 | 프로젝트 상태 한눈에 | 1p | 데이터 준비 상태 · 화면 개발 상태 · 운영 준비 상태 |
| 3 | 데이터 구조와 연결 관계 | 1p | CSV 4개 · PK/FK · 느슨한 연결 · 주의사항 |
| 4 | 대시보드 화면 구성 | 1~2p | Home · 고객 니즈 검색 · 매칭 · 전략 · 소프트웨어 가치 맵 |
| 5 | 권한/보안/운영 기준 | 1p | admin/viewer · 로그인 · 사내망 · sensitivity 표시 |
| 6 | 리스크 3개 + 대응 | 1p | 데이터 해석 · 고객 정보 · 운영 반영 리스크 |
| 7 | 실행 액션 플랜 | 1p | 우선순위 · 담당 · 시점 · 산출물 |
| 8 | Appendix | 1p | 데이터 출처 · 컬럼 정의 · 다음 개발 범위 |

각 페이지 끝에 `---`(수평선) 또는 `<div style="page-break-after: always;"></div>`로 page break 표시. Codex가 PDF 변환 시 실제 페이지 분할 처리.

### 템플릿 본문

```md
# [보고서 제목 — 예: knowledge_for_marketing 대시보드 1차 개발 보고서]

**기간**: YYYY-MM-DD ~ YYYY-MM-DD  
**데이터 출처**: `export/customer_needs.csv`, `export/customer_strategy.csv`, `export/need_value_matching.csv`, `export/software_features.csv`  
**작성**: Claude Code / 작성일 YYYY-MM-DD

---

## Executive Summary

### 한 줄 결론
[현재 개발/검토 상태와 다음 의사결정 한 줄. 단정형 과장 금지]

### 핵심 액션 3개 미리보기
1. [동사형 액션] — 담당 / 시점
2. [동사형 액션] — 담당 / 시점
3. [동사형 액션] — 담당 / 시점

---

## 1. 프로젝트 상태 한눈에

### 상태 Snapshot

| 영역 | 현재 상태 | 다음 액션 | 리스크 |
|---|---|---|---|
| 원본 자료 | raw 수집 완료 | 유지 관리 | 원본 직접 수정 금지 |
| 지식 위키 | wiki 170페이지 정리 완료 | 필요 시 갱신 | 구조 일관성 |
| export CSV | 4개 CSV 생성 | 검증/서버 반영 | 컬럼 변경 리스크 |
| 대시보드 | 개발 예정/진행 중 | Streamlit 1차 구현 | 권한/보안 |
| 서버 운영 | Windows 11 + Docker Compose 예정 | 배포 구성 | 사내망 접근 제한 |

### 한 문장 요약
[현재 상태를 한 문장으로. 숫자 또는 파일 기준 포함]

---

## 2. 데이터 구조와 연결 관계

### 사용 CSV

| 파일 | 주요 키 | 목적 |
|---|---|---|
| `customer_needs.csv` | `need_id` | 고객 니즈 원천 구조화 데이터 |
| `customer_strategy.csv` | `strategy_id`, `customer_name` | 고객사별 대응 전략 |
| `need_value_matching.csv` | `match_id`, `need_id`, `feature_id` | 니즈-기능-가치 매칭 |
| `software_features.csv` | `feature_id` | 소프트웨어 기능 목록과 가치 키워드 |

### 관계 요약

1. `need_value_matching.need_id` → `customer_needs.need_id`
2. `need_value_matching.feature_id` → `software_features.feature_id`
3. `customer_strategy.customer_name` → `customer_needs.customer_name`
4. `software_features.related_need_keywords` ↔ `customer_needs.need_keyword`는 느슨한 키워드 연결

### 주의사항
- `customer_name`은 문자열 완전 일치가 필요하다.
- `related_need_keywords`는 직접 FK가 아니다.
- `dashboard_visible=FALSE` 데이터는 기본 표시 대상에서 제외한다.

---

## 3. 대시보드 화면 구성

### 화면 우선순위

| 우선순위 | 화면 | 핵심 목적 | 주요 데이터 |
|---|---|---|---|
| 1 | Home | 최근 니즈, 검토 필요, 주요 현황 요약 | 전체 CSV |
| 2 | 고객 니즈 검색 | 고객사·산업군·키워드 기반 검색 | `customer_needs.csv` |
| 3 | 니즈-기능-가치 매칭 | 고객 니즈와 추천 기능 연결 | `need_value_matching.csv`, `software_features.csv` |
| 4 | 고객사별 대응 전략 | 추천 접근 방향, 다음 미팅 질문 | `customer_strategy.csv` |
| 5 | 소프트웨어별 가치 맵 | 기능과 연결 가능한 니즈 탐색 | `software_features.csv` |

[화면 1. Home 와이어프레임 placeholder]

---

## 4. 권한/보안/운영 기준

### 권한

| 역할 | 가능 작업 |
|---|---|
| admin | 데이터 검토, 수정, 운영 반영 |
| viewer | 조회 전용 |

### 보안 기준
- 사내 네트워크에서만 접근
- 로그인 필요
- 고객사명과 담당자는 표시 가능
- `sensitivity_level=공개 가능/비공개`는 표시
- `sensitivity_level=비공개`는 경고 배지로 표시하고, 실제 숨김은 `dashboard_visible` 기준으로 처리

### 운영 방식
- 개발: 사용자 PC
- 운영: Windows 11 서버 PC
- 실행: Docker Compose
- 접속: `http://서버PC_IP:포트`
- 데이터 전송: `robocopy`

---

## 5. 리스크 3개 + 대응

| # | 리스크 | 영향도 | 트리거 신호 | 대응 액션 | 모니터링 지표 |
|---|---|---|---|---|---|
| 1 | 고객 정보 노출 | 상 | 외부망 접근, 로그인 미설정 | 사내망 제한, 로그인 적용 | 접근 경로 점검 |
| 2 | 매칭 오해 | 중 | 잘못된 `customer_name` 또는 `feature_id` 연결 | 데이터 검증 스크립트 | 매칭 누락/중복 수 |
| 3 | 검토 상태 오해 | 중 | `검토 필요` 항목이 확정처럼 표시 | 배지/설명 강화 | 검토 필요 건수 |

상세는 `risk_notes_<slug>.md` 참조.

---

## 6. 바로 실행할 액션 플랜

| 우선순위 | 액션 | 담당 | 시점 | 측정 기준 | 의존성 |
|---|---|---|---|---|---|
| 1 | Home 화면 1차 구현 | Codex | YYYY-MM-DD | 로컬 Streamlit 실행 | CSV 로딩 |
| 2 | 고객 니즈 검색 구현 | Codex | YYYY-MM-DD | 검색 결과 정상 표시 | 컬럼 검증 |
| 3 | 니즈-기능 매칭 구현 | Codex | YYYY-MM-DD | `need_id`, `feature_id` 연결 | 관계 검증 |
| 4 | Docker Compose 구성 | Codex | YYYY-MM-DD | 서버 실행 가능 | requirements 확정 |
| 5 | robocopy 반영 흐름 문서화 | Claude Code/Codex | YYYY-MM-DD | 전송 파일 목록 명확화 | 서버 폴더 구조 |

각 액션은 감성 단어가 아닌 동사로 시작한다.

---

## 7. Appendix

### 데이터 출처
- `export/customer_needs.csv`
- `export/customer_strategy.csv`
- `export/need_value_matching.csv`
- `export/software_features.csv`
- `export/dashboard_manifest.json`

### 해석 한계
- 답할 수 있는 것: 고객 니즈, 기능 매칭, 대응 전략, 검토 상태
- 답할 수 없는 것: 구매 가능성, 예산, 도입 의지, 고객 내부 사정

### 다음 개발 범위
- 로컬 Streamlit 실행
- Docker Compose 운영
- robocopy 반영
- 로그인/권한
- admin 수정 기능 저장 방식
```

### 시각·서식 가이드

- **색상 · 여백 · 폰트**: `DESIGN.md` 토큰 따름.
- **로고**: `assets/logo/`의 라온엑스 로고 사용 기준 따름.
- **차트 제목은 해석형**: `매칭 현황` ❌ / `검토 필요 매칭이 남아 있어 사용자 확인 필요` ✅
- **표 · 차트 placeholder**: 본문에서 `[차트 1. ...]`, `[표 1. ...]` 형식으로 명시. Codex가 PDF 변환 시 실제 시각 자료로 교체.
- **페이지 break**: 각 `## ` 헤딩 앞에 `---`(수평선). 강제 break 필요 시 `<div style="page-break-after: always;"></div>`.
- **폰트 권장**: 본문 11pt · 헤딩 14~18pt · 캡션 9pt.
- **분량 통제**: 표지 + Executive Summary와 프로젝트 상태 Snapshot은 각 1페이지 안에 들어가게.
- **민감도 표시**: `sensitivity_level`은 숨기지 말고 배지로 표시한다. 비공개 항목의 실제 숨김 여부는 `dashboard_visible` 기준으로 처리한다.
- **단정 금지**: 고객의 구매 가능성, 예산, 도입 의지 추정 금지.

---

## 5. handoff_<slug>.md (Claude Code → 다음 actor)

```md
# handoff_<slug>.md

## Status
이 작업의 현재 상태. 어디까지 끝났고 어디서 멈췄는지.

예:
- Home 화면 설계 완료
- 데이터 관계 검토 완료
- Codex 구현 전 확인 필요 항목 2개 남음

## What Changed
바뀐 파일과 변경 요점.

| 파일 | 변경 요점 |
|---|---|
| `docs/dashboard-requirements_<slug>.md` | 화면 요구사항 추가 |
| `docs/data-schema_<slug>.md` | CSV 관계 정의 추가 |
| `PROJECT.md` | Work Board 상태 업데이트 |

## Verified
Claude Code가 확인한 사실관계/일관성.

- 4개 CSV 파일명 확인
- 대시보드 5개 화면 우선순위 확인
- admin/viewer 권한 구조 확인
- 사내망 + 로그인 + Docker Compose 운영 기준 확인
- `robocopy` 전송 대상 확인

## Open Items
다음 actor가 이어받을 일.

- Codex가 구현 계획 작성
- 필요한 파일 락 등록
- 로컬 Streamlit 실행 검증
- CSV 로딩 검증

## Risks Carried Over
아직 해소되지 않은 리스크.

- 실제 CSV 데이터 커밋 금지 확인 필요
- `sensitivity_level=비공개` 데이터는 경고 배지로 표시하고, `dashboard_visible=FALSE` 데이터는 기본 화면에서 숨기는지 구현 확인 필요
- admin 수정 기능의 저장 방식 후속 결정 필요

## Lock State
잡고 있던 파일 락의 현재 상태. 모두 해제했어야 함.

예:
- `docs/dashboard-requirements_<slug>.md` — 해제
- `PROJECT.md` — 해제
```

---

## 6. execution_plan_<slug>.md (Codex)

```md
# execution_plan_<slug>.md

## Plan Source
참조한 `dev_plan_<slug>.md` 또는 사용자 요청.

예:
- `output/dev_plan_home-dashboard-v1.md`
- 사용자 요청: Home 화면 1차 구현

## Scope Diff
`dev_plan`에서 빠진 범위, 추가/축소가 필요한 항목.

예:
- 로그인 기능은 운영 전 필수이나, 1차 로컬 구현에서는 placeholder 또는 간단 인증으로 범위를 제한한다.
- viewer 코멘트 기능은 제외한다. admin 수정 기능은 저장 방식 확정 후 후속 작업으로 분리한다.
- 실제 CSV 대신 `data/sample/` 또는 사용자가 제공한 개발용 CSV로 검증한다.

## File Plan
- 수정할 파일과 변경 요점
- 새로 만들 파일 (slug 적용 파일명)
- 락을 잡을 파일

| 파일 | 작업 | 락 필요 |
|---|---|---|
| `app/main.py` | Streamlit 진입점 작성 | 예 |
| `app/pages/01_home.py` | Home 화면 작성 | 예 |
| `scripts/load_data.py` | CSV 로딩 유틸 작성 | 예 |
| `requirements.txt` | 의존성 정의 | 예 |
| `Dockerfile` | Docker 실행 환경 정의 | 예/후속 |
| `docker-compose.yml` | 운영 실행 구성 | 예/후속 |

## Implementation Steps
구현 순서. 단계별로 짧게.

1. `PROJECT.md` 확인
2. `Files locked` 등록
3. CSV 경로와 필수 컬럼 정의
4. 데이터 로딩 유틸 작성
5. Home 화면 구현
6. 검색/필터 기본 구조 구현
7. 로컬 Streamlit 실행 확인
8. 결과 요약 작성
9. Handoff Log 작성 및 락 해제

## Verification Plan
각 단계 후 어떤 검사를 할지.

- 파일 검사: 생성/수정 파일 목록 확인
- Python 검사: import 오류 확인
- CSV 검사: 필수 컬럼 존재 여부 확인
- Streamlit 검사: 앱 실행 및 화면 렌더링 확인
- 보안 검사: 실제 CSV가 Git에 포함되지 않는지 확인
- 디자인 검사: `DESIGN.md` 색상/로고/배지 기준 준수

## Risks Identified
`dev_plan`에 없거나 보완된 리스크.

- Windows 경로 처리 오류
- 한글 CSV 인코딩 문제
- 빈 값 또는 누락 컬럼 처리 오류
- `customer_name` 문자열 연결 실패
- Docker에서 파일 경로가 로컬과 달라지는 문제

## Handoff Trigger
어떤 조건이면 다음 actor에게 넘기는지.

- 로컬 Streamlit 앱이 실행됨
- 필수 CSV 로딩 검증 완료
- 화면 1차 렌더링 완료
- 남은 리스크가 `handoff_<slug>.md`에 기록됨
```

---

## 7. handoff_<slug>.md (Codex → 다음 actor)

```md
# handoff_<slug>.md

## Status
이 작업의 현재 상태. 어디까지 끝났고 어디서 멈췄는지.

예:
- Home 화면 1차 구현 완료
- 로컬 Streamlit 실행 확인 완료
- Docker Compose 구성은 후속 작업 필요

## What Changed
바뀐 파일과 변경 요점. 줄/파일 단위로 짧게.

| 파일 | 변경 요점 |
|---|---|
| `app/main.py` | Streamlit 앱 진입점 추가 |
| `app/pages/01_home.py` | Home 화면 카드/표 추가 |
| `scripts/load_data.py` | CSV 로딩 함수 추가 |
| `requirements.txt` | streamlit, pandas 추가 |

## Verified
실제 수행한 검증:

- 파일 검사
  - 어떤 명령으로 무엇을 확인했는지
- 렌더링
  - 브라우저/Streamlit에서 확인한 것
- 데이터 검증
  - CSV 행 수
  - 필수 컬럼 존재 여부
  - 누락/이상치
- 권한/보안
  - admin/viewer 기능이 있으면 기본 동작 확인
  - 실제 CSV가 Git에 포함되지 않는지 확인

## Open Items
다음 actor가 이어받을 일.

- Claude Code 리스크 검토
- 사용자 피드백 반영
- Docker Compose 구성
- 로그인 기능 구현
- admin 수정값 저장 방식 결정

## Risks Carried Over
아직 해소되지 않은 리스크.

- 로그인/사내망 적용 전 `sensitivity_level=비공개` 항목 접근 제어 미완료 가능성
- `customer_name` 불일치 데이터 존재 가능성
- 서버 PC Windows 11 Docker 환경에서 경로 문제 가능성

## Lock State
잡고 있던 파일 락의 현재 상태. 모두 해제했어야 함.

예:
- `app/main.py` — 해제
- `app/pages/01_home.py` — 해제
- `scripts/load_data.py` — 해제
```

---

## 8. analysis_summary_<slug>.md (Codex)

```md
# analysis_summary_<slug>.md

## Data Scope
사용한 데이터 범위. 샘플/실제 명시.

예:
- 사용 파일:
  - `customer_needs.csv`
  - `customer_strategy.csv`
  - `need_value_matching.csv`
  - `software_features.csv`
- 기준 폴더: `data/` 또는 `export/`
- 데이터 성격: 실제/샘플/개발용 중 하나 명시
- 행 수:
  - `customer_needs.csv`: N행
  - `customer_strategy.csv`: N행
  - `need_value_matching.csv`: N행
  - `software_features.csv`: N행

## Key Findings
숫자로 확인 가능한 발견 5~7개. 각 발견:
- 근거 지표
- 가공 방식
- 신뢰 수준 / 한계

예:
1. 고객 니즈 총 N건 중 `dashboard_visible=TRUE`는 N건이다.
2. `review_status=검토 필요` 매칭은 N건이다.
3. `confidence=높음` 매칭은 N건이다.
4. 가장 많이 등장한 `need_keyword`는 [키워드]이다.
5. 가장 많이 연결된 소프트웨어는 [소프트웨어명]이다.
6. `customer_name` 기준으로 전략 데이터가 없는 고객은 N개다.
7. `feature_id` 매칭 누락은 N건이다.

## Trends
시계열/고객/소프트웨어/검토 상태별 패턴.

예:
- `report_date` 기준 최근 니즈 증가 패턴
- 산업군별 주요 니즈 경향
- 소프트웨어별 연결 니즈 분포
- 검토 필요 매칭의 집중 영역

## Anomalies
이상치, 누락, 추정으로 채운 영역.

예:
- `need_id`가 없는 매칭
- `feature_id`가 `software_features.csv`에 없는 매칭
- `customer_name` 표기 불일치
- `dashboard_visible` 값 누락
- `sensitivity_level` 값 누락 또는 예상 외 값
- `review_status` 값 누락 또는 예상 외 값

## What This Does NOT Show
이 데이터로 답할 수 없는 질문. 추정 금지 영역.

- 고객의 실제 구매 가능성
- 고객 예산
- 고객의 도입 의지
- 고객 내부 의사결정 구조
- 고객 만족도 또는 매출 영향
- 경쟁사 대비 객관적 우위

## Suggested Frame for Final Report
Claude Code가 `final_report_<slug>.md` 작성 시 참고할 비교 프레임.

예:
- 고객 기준: 고객별 주요 니즈와 추천 기능
- 니즈 기준: 반복 등장 니즈와 연결 가능한 소프트웨어
- 기능 기준: 기능별 연결 니즈와 가치 메시지
- 검토 기준: `review_status`, `confidence` 기반 검토 우선순위
- 운영 기준: robocopy 전송, Docker Compose 실행, 사내망 접근, 로그인 적용
```
