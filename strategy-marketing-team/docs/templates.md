# docs/templates.md — 협업 문서 표준 구조

`knowledge_for_marketing_dashboard` 프로젝트에서 Claude Code와 Codex가 만드는 협업 문서와 결과물 문서의 표준 구조입니다.

이 프로젝트의 목적은 고객 방문보고서에서 파악한 고객 니즈와 소프트웨어 기능 업데이트 자료를 연결해, **마케팅 활용**과 **고객 미팅 준비**에 필요한 고객 니즈 / 추천 기능 / 제안 메시지 / 다음 확인 질문을 빠르게 확인할 수 있는 사내 Streamlit 웹 대시보드를 구축하는 것입니다.

기본 흐름:

```text
방문보고서 · 기능 업데이트 자료
        ↓  Claude 분석·정리
wiki/ 지식 위키
        ↓  구조화 데이터 추출
export CSV 5개
        ↓  로컬 대시보드 개발
robocopy로 서버 PC에 필요 파일만 전송
        ↓
사내망 내부 팀 공유 웹 대시보드
```

공통 기준:

- `PROJECT.md`가 현재 상태의 단일 진실 원천입니다.
- `docs/collab-protocol.md`가 협업 규칙과 v5 데이터 기준의 단일 진실 원천입니다.
- `dashboard/`가 대시보드 개발 기본 범위입니다.
- `raw/`, `wiki/`, `export/`는 원본 영역이며 사용자 지시 없이 수정하지 않습니다.
- 실제 고객 데이터 CSV와 override CSV는 Git에 커밋하지 않습니다.
- 계정 인증 정보는 코드와 저장소에 포함하지 않습니다.
- 모든 산출물은 v5 export CSV 5개와 `customer_master.csv` SSOT 구조를 기준으로 작성합니다.
- `review_status`는 1차 범위에서 제외합니다.
- `sensitivity_level`은 내부 직원용 대시보드의 표시/필터/배지 기준으로 사용하지 않습니다.

---

## 인덱스

| 문서 | Owner | 트리거 |
|---|---|---|
| `dev_plan_<slug>.md` | Claude Code | 복잡한 개발·분석 작업 전 1차 계획 |
| `questions_<slug>.md` | Claude Code | 대시보드 화면, 마케팅 활용, 고객 미팅 준비 질문 설계 |
| `risk_notes_<slug>.md` | Claude Code | 데이터·권한·내부 정보·해석·표시 리스크 점검 |
| `final_report_<slug>.md` | Claude Code | 사용자 또는 팀 공유용 최종 요약 보고서 |
| `execution_plan_<slug>.md` | Codex | Claude Code 1차 계획 보완 및 구현 계획 |
| `analysis_summary_<slug>.md` | Codex | CSV·대시보드·기능 구현 결과 분석 요약 |
| `data_schema_<slug>.md` | Codex | 현재 CSV와 목표 export 스키마 정리 |
| `deploy_flow_<slug>.md` | Codex | robocopy 기반 서버 반영 흐름 또는 운영 절차 정리 |
| `handoff_<slug>.md` | Claude Code / Codex | 세션 또는 작업 인계 |

---

## 1. `dev_plan_<slug>.md` — Claude Code

```md
# dev_plan_<slug>.md

## Goal

이번 작업으로 무엇을 만들거나 바꿀지 한두 문장으로 작성합니다.

예:

- v5 export CSV 5개 기준으로 6개 화면 요구사항을 정리한다.
- `customer_master.csv`를 고객 정보 SSOT로 사용해 고객 니즈 검색 화면을 설계한다.
- `need_value_matching.csv`를 `customer_needs.csv`, `software_features.csv`, `customer_master.csv`와 연결해 니즈-기능-가치 매칭 화면을 설계한다.
- robocopy 기반 서버 PC 반영 흐름과 CSV 관계 검증 항목을 정의한다.

## Background

- 사용자 요청 요약
- 관련 문서: `PROJECT.md`, `business-context.md`, `DESIGN.md`, `AGENTS.md`, `CLAUDE.md`, `docs/collab-protocol.md`
- 마케팅 활용 목적
- 고객 미팅 준비 목적
- v5 export CSV 5개 구조
- 이전 작업 또는 Handoff Log와의 연결

## Scope

- `PROJECT.md` Decisions 기준 확인
- `business-context.md`의 데이터 해석 기준 확인
- `DESIGN.md`의 화면·문구 기준 확인
- export CSV 5개 구조 확인
- `customer_master.csv` SSOT 기준 확인
- 6개 화면별 데이터 연결 요구사항 정리
- Codex가 구현할 파일 범위 제안
- 검증 기준 정의

## Out of Scope

- 실제 고객 데이터 원본 수정
- `raw/`, `wiki/`, `export/` 직접 수정
- 서버 PC 운영 파일 즉시 변경
- 외부 인터넷 접속 지원
- 외부 공개 수준의 인증/SSO 구현
- 원본 CSV 직접 수정 방식의 admin 저장
- `review_status` 1차 구현
- `sensitivity_level` 표시/필터/배지 구현
- 고객 구매 가능성, 예산, 도입 의지 추정

## Data Assumptions

현재 실제 CSV:

- `customer_master.csv`
- `customer_needs.csv`
- `software_features.csv`
- `need_value_matching.csv`
- `customer_strategy.csv`

관계:

```text
customer_master.customer_id → customer_needs.customer_id
customer_master.customer_id → customer_strategy.customer_id
customer_needs.need_id → need_value_matching.need_id
software_features.feature_id → need_value_matching.feature_id
```

주의:

- `customer_master.csv`는 고객 정보 SSOT다.
- `customer_needs.csv`, `customer_strategy.csv`의 `customer_name`은 표시용이다.
- `need_value_matching.csv`에는 `customer_id`가 없다.
- `software_features.csv`에는 `customer_id`가 없다.
- `review_status`는 1차 범위에서 제외한다.
- `sensitivity_level`은 화면 기준으로 사용하지 않는다.

## Verification

- CSV 5개 로딩 가능 여부
- 필수 컬럼 존재 여부
- `customer_master.customer_id` 고유성
- `customer_needs.customer_id` → `customer_master.customer_id` 관계 검증
- `customer_strategy.customer_id` → `customer_master.customer_id` 관계 검증
- `need_value_matching.need_id` → `customer_needs.need_id` 관계 검증
- `need_value_matching.feature_id` → `software_features.feature_id` 관계 검증
- `dashboard_visible` 필터 적용 여부
- `confidence` 배지 표시 여부
- `review_status`를 사용하지 않았는지 여부
- `sensitivity_level`을 화면 표시/필터/배지 기준으로 사용하지 않았는지 여부
- 실제 고객 데이터, override CSV, 계정 인증 정보가 Git에 커밋되지 않았는지 확인
```

---

## 2. `questions_<slug>.md` — Claude Code

```md
# questions_<slug>.md

## Decision Context

이번에 답해야 할 의사결정을 한 줄로 작성합니다.

예:

- customer_master를 기준으로 고객 니즈 검색 화면에서 어떤 고객 정보를 보여줘야 하는가?
- 니즈-기능-가치 매칭 화면에서 matching, needs, features, master를 어떤 순서로 연결해야 하는가?
- 소프트웨어별 가치 맵에서 특정 기능이 어떤 고객 니즈와 연결되는지 어떻게 보여줘야 하는가?

## Key Questions

1. 고객 기본 정보는 어디에서 가져오는가?
   - 데이터: `customer_master.csv`
   - 액션: 고객 카드, 고객 검색 필터, 고객사별 대응 전략 상단 정보 구성

2. 고객 니즈는 어떤 고객과 연결되는가?
   - 데이터: `customer_needs.customer_id → customer_master.customer_id`
   - 액션: 고객 니즈 검색, Home 최근 니즈, 마케팅 활용 후보 구성

3. 고객 니즈와 기능은 어떻게 연결되는가?
   - 데이터: `customer_needs.need_id → need_value_matching.need_id`, `software_features.feature_id → need_value_matching.feature_id`
   - 액션: 니즈-기능-가치 매칭 화면 구현

4. 고객사별 전략은 어떤 고객 기준으로 묶이는가?
   - 데이터: `customer_strategy.customer_id → customer_master.customer_id`
   - 액션: 고객사별 대응 전략 화면 구현

5. 소프트웨어별 가치 맵은 어떤 연결 구조를 따라야 하는가?
   - 데이터: `features → matching → needs → master`
   - 액션: 기능별 연결 고객 니즈 표시

## Screen Mapping

| 질문 | 화면 | 주요 데이터 |
|---|---|---|
| 최근 니즈 확인 | Home | master + needs |
| 반복 니즈 확인 | 마케팅 활용 | needs + matching + features + master |
| 고객별 니즈 검색 | 고객 니즈 검색 | master + needs |
| 니즈-기능 연결 | 니즈-기능-가치 매칭 | needs + matching + features + master |
| 소프트웨어별 가치 확인 | 소프트웨어별 가치 맵 | features + matching + needs + master |
| 고객별 접근 방향 | 고객사별 대응 전략 | master + strategy + needs |
```

---

## 3. `analysis_summary_<slug>.md` — Codex

```md
# analysis_summary_<slug>.md

## Summary

이번 분석 또는 구현 결과를 한 문단으로 요약합니다.

## Current CSV Check

| CSV | 존재 여부 | 현재 컬럼 | 주요 이슈 |
|---|---|---|---|
| `customer_master.csv` |  |  |  |
| `customer_needs.csv` |  |  |  |
| `software_features.csv` |  |  |  |
| `need_value_matching.csv` |  |  |  |
| `customer_strategy.csv` |  |  |  |

## Relationship Check

| 관계 | 검증 결과 | 이슈 |
|---|---|---|
| `customer_master.customer_id → customer_needs.customer_id` |  |  |
| `customer_master.customer_id → customer_strategy.customer_id` |  |  |
| `customer_needs.need_id → need_value_matching.need_id` |  |  |
| `software_features.feature_id → need_value_matching.feature_id` |  |  |

## Dashboard Behavior

- `dashboard_visible=FALSE` 처리:
- `confidence` 배지:
- 계정별 로그인:
- admin/viewer 권한 분리:
- override CSV 저장:
- 마케팅 활용 화면:
- 고객 미팅 준비 정보 표시:

## Screens Implemented or Reviewed

| 화면 | 상태 | 주요 데이터 연결 | 메모 |
|---|---|---|---|
| Home |  | 5개 CSV 전체 요약 |  |
| 고객 니즈 검색 |  | master + needs |  |
| 니즈-기능-가치 매칭 |  | needs + matching + features + master |  |
| 고객사별 대응 전략 |  | master + strategy + needs |  |
| 소프트웨어별 가치 맵 |  | features + matching + needs + master |  |
| 마케팅 활용 |  | needs + matching + features + master |  |

## Security / Data Notes

- 실제 고객 데이터 Git 커밋 여부:
- override CSV Git 커밋 여부:
- 계정 인증 정보 Git 커밋 여부:
- 로그에 고객 정보 포함 여부:
```

---

## 4. `data_schema_<slug>.md` — Codex

```md
# data_schema_<slug>.md

## Purpose

v5 export CSV 5개와 파일 간 관계를 대시보드 구현 기준으로 정리합니다.

## Current Files Checked

| 파일 | 위치 | 확인 결과 |
|---|---|---|
| `customer_master.csv` |  |  |
| `customer_needs.csv` |  |  |
| `software_features.csv` |  |  |
| `need_value_matching.csv` |  |  |
| `customer_strategy.csv` |  |  |

## Relationship Rules

- `customer_master.customer_id` → `customer_needs.customer_id`
- `customer_master.customer_id` → `customer_strategy.customer_id`
- `customer_needs.need_id` → `need_value_matching.need_id`
- `software_features.feature_id` → `need_value_matching.feature_id`
- `customer_name`은 표시용
- `related_need_keywords`는 참고 연결
```

---

## 5. `deploy_flow_<slug>.md` — Codex

```md
# deploy_flow_<slug>.md

## Purpose

robocopy를 사용해 사용자 컴퓨터에서 서버 PC로 대시보드 운영에 필요한 파일만 안전하게 반영하는 흐름을 정의합니다.

## Deploy Flow

```text
1. 사용자 컴퓨터에서 export CSV 5개와 대시보드 운영 파일 준비
2. robocopy로 서버 PC에 필요한 파일만 전송
3. incoming/ 파일명·개수·헤더·관계 검증
4. 기존 data/를 backup/으로 복사
5. incoming/ 검증본을 data/로 반영
6. 데이터 새로고침 또는 Docker 컨테이너 재시작
7. logs/에 반영 결과 기록
```

## Exclude From Server Copy

- `raw/`
- 전체 `wiki/`
- 개발 로그
- 로컬 캐시
- 계정 인증 정보
- Git 내부 파일
```

---

## 6. 공통 금지 표현

- “이 고객은 구매 가능성이 높습니다.”
- “이 고객은 반드시 이 솔루션을 도입할 것입니다.”
- “예산이 충분할 것으로 보입니다.”
- “고객 내부 의사결정이 완료된 것으로 보입니다.”
- “마케팅 캠페인으로 확정합니다.”
- “근거는 없지만 이 기능을 추천합니다.”

권장 표현:

- “이 항목은 마케팅 콘텐츠 후보로 검토할 수 있습니다.”
- “이 매칭은 confidence가 낮으므로 담당자 확인 후 활용하세요.”
- “다음 미팅에서 현재 업무 병목과 자동화 범위를 확인하는 질문이 적합합니다.”
- “이 가치 메시지는 관련 니즈와 기능 근거를 확인한 뒤 제안서에 활용하세요.”
