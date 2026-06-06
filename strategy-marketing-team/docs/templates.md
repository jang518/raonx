# docs/templates.md — 협업 문서 표준 구조

`knowledge_for_marketing_dashboard` 프로젝트에서 Claude Code와 Codex가 만드는 협업 문서와 결과물 문서의 표준 구조입니다.

이 프로젝트의 목적은 고객 방문보고서에서 파악한 고객 니즈와 소프트웨어 기능 업데이트 자료를 연결해, **마케팅 활용**과 **고객 미팅 준비**에 필요한 고객 니즈 / 추천 기능 / 제안 메시지 / 다음 확인 질문을 빠르게 확인할 수 있는 사내 Streamlit 웹 대시보드를 구축하는 것입니다.

기본 흐름:

```text
방문보고서 · 기능 업데이트 자료
        ↓  Claude 분석·정리
wiki/ 지식 위키
        ↓  구조화 데이터 추출
export CSV 4개
        ↓  로컬 대시보드 개발
robocopy로 서버 PC에 필요 파일만 전송
        ↓
사내망 내부 팀 공유 웹 대시보드
```

공통 기준:

- 파일명은 `docs/collab-protocol.md`의 파일 네이밍 규칙에 따라 `<prefix>_<slug>.md` 형식을 사용합니다.
- `PROJECT.md`가 현재 상태의 단일 진실 원천입니다.
- `dashboard/`가 대시보드 개발 기본 범위입니다.
- `raw/`, `wiki/`, `export/`는 원본 영역이며 사용자 지시 없이 수정하지 않습니다.
- 실제 고객 데이터 CSV와 override CSV는 Git에 커밋하지 않습니다.
- 계정 인증 정보는 코드와 저장소에 포함하지 않습니다.
- 현재 실제 CSV와 목표 export 생성 규칙을 구분해서 작성합니다.
- `review_status`는 1차 범위에서 제외합니다.
- `sensitivity_level`은 내부 직원용 대시보드의 표시/필터/배지 기준으로 사용하지 않습니다.
- 모든 섹션을 반드시 채울 필요는 없습니다. 해당 내용이 없으면 `해당 없음`이라고 표시합니다.

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

- 6개 화면 기준으로 마케팅 활용과 고객 미팅 준비를 모두 지원하도록 핵심 질문을 정리한다.
- `customer_needs.csv`와 `need_value_matching.csv`를 연결해 고객 니즈-기능 매칭 화면을 구현할 수 있도록 개발 범위를 정리한다.
- 사내망 내부 계정 로그인과 admin override CSV 저장 방식을 구현할 수 있도록 범위를 정의한다.
- robocopy 기반 서버 PC 반영 흐름과 검증 항목을 정의한다.

## Background

- 사용자 요청 요약
- 관련 문서: `PROJECT.md`, `business-context.md`, `DESIGN.md`, `AGENTS.md`, `CLAUDE.md`, `docs/collab-protocol.md`
- 마케팅 활용 목적
- 고객 미팅 준비 목적
- 현재 실제 CSV와 목표 export 생성 규칙의 차이
- 이전 작업 또는 Handoff Log와의 연결

## Scope

- `PROJECT.md` Decisions 기준 확인
- `business-context.md`의 데이터 해석 기준 확인
- `DESIGN.md`의 화면·문구 기준 확인
- 현재 CSV 4개 구조 확인
- 목표 export 생성 규칙 확인: `customer_id` 추가, `dashboard_visible` TRUE/FALSE 유지
- 6개 화면별 요구사항 정리
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

## Files to Modify

- `dashboard/*`
- `dashboard/pages/*`
- `dashboard/components/*`
- `dashboard/services/*`
- `scripts/validate_csv.py`
- `scripts/sync_data.py`
- `requirements.txt`
- `Dockerfile`
- `docker-compose.yml`
- `output/analysis_summary_<slug>.md`

## Files Not to Touch

- `raw/`
- `wiki/`
- `export/`
- 실제 운영 CSV 원본
- admin override CSV 실데이터
- 계정 인증 정보 파일
- 서버 PC 운영 데이터
- 다른 actor가 `PROJECT.md`의 `Files locked`에 잡아둔 파일

## Data Assumptions

현재 실제 CSV:

- `customer_needs.csv`
- `software_features.csv`
- `need_value_matching.csv`
- `customer_strategy.csv`

현재 주의:

- 현재 CSV에는 `customer_id`가 없다.
- 현재 CSV에는 `review_status`가 없다.
- 현재 `sensitivity_level` 컬럼은 있으나 대시보드 표시/필터/배지 기준으로 사용하지 않는다.
- 현재 `dashboard_visible`은 `TRUE/FALSE` 체계다.

목표 export 생성 규칙:

- `customer_id`를 생성한다.
- `dashboard_visible`은 `TRUE/FALSE`로 유지한다.
- 매칭 신뢰도는 현재 존재하는 `confidence`를 사용한다.
- admin 수정값은 override CSV에 저장한다.

## User Decisions Needed

- override CSV 파일명과 저장 위치
- admin 수정 이력 보관 범위
- 계정 목록 관리 방식
- customer_id 생성 규칙

## Proposed Steps

1. `PROJECT.md`와 `Files locked` 확인
2. 필요한 파일 락 등록
3. 현재 CSV 파일명, 헤더, 관계 정의 확인
4. 목표 export 스키마와 차이 정리
5. 최소 기능 단위로 구현
6. `dashboard_visible`, `confidence` 표시 기준 적용
7. admin/viewer 권한 기준 적용
8. 로컬 실행 또는 정적 검증
9. 결과 문서 작성
10. Handoff Log 작성 및 파일 락 해제

## Risks

- 데이터 해석 리스크
- 고객 정보 노출 리스크
- 계정 관리 리스크
- override CSV가 Git에 커밋될 리스크
- 현재 CSV에 없는 `customer_id`, `review_status`를 있다고 가정할 리스크
- 마케팅 활용 후보를 확정 캠페인 또는 확정 수요처럼 표현할 리스크
- `dashboard_visible=FALSE` 데이터 노출 리스크
- 과잉 구현 리스크

## Verification

- CSV 4개 로딩 가능 여부
- 필수 컬럼 존재 여부
- 현재 CSV와 목표 export 스키마 차이 기록 여부
- 관계 키 연결 가능 여부
- `dashboard_visible` 필터 적용 여부
- `confidence` 배지 표시 여부
- `review_status`를 사용하지 않았는지 여부
- `sensitivity_level`을 화면 표시/필터/배지 기준으로 사용하지 않았는지 여부
- admin/viewer 권한 분리 여부
- override CSV가 원본 CSV를 직접 수정하지 않는지 여부
- Streamlit 로컬 실행 여부
- 실제 고객 데이터, override CSV, 계정 인증 정보가 Git에 커밋되지 않았는지 확인
```

---

## 2. `questions_<slug>.md` — Claude Code

```md
# questions_<slug>.md

## Decision Context

이번에 답해야 할 의사결정을 한 줄로 작성합니다.

예:

- 마케팅 활용과 고객 미팅 준비를 위해 Home 화면에서 어떤 정보를 먼저 보여줘야 하는가?
- 별도 마케팅 활용 화면에서 어떤 반복 니즈와 가치 메시지 후보를 보여줘야 하는가?
- 고객 니즈-기능 매칭의 confidence를 어떻게 표현해야 안전한가?

## Key Questions

각 질문은 다음을 포함합니다.

- 어떤 데이터로 답하는가
- 어떤 화면 또는 액션과 연결되는가
- 마케팅 활용인지, 고객 미팅 준비인지, 둘 다인지

예:

1. 최근 고객 니즈 중 가장 먼저 보여줄 항목은 무엇인가?
   - 데이터: `customer_needs.csv`
   - 목적: 마케팅 활용 + 고객 미팅 준비
   - 액션: Home 화면 카드/목록 구성

2. 반복적으로 등장하는 니즈는 어떤 마케팅 콘텐츠 후보가 될 수 있는가?
   - 데이터: `customer_needs.csv.need_keyword`, `software_features.csv.value_keywords`
   - 목적: 마케팅 활용
   - 액션: 마케팅 활용 화면 구성

3. 고객 니즈와 기능은 어떤 키로 연결되는가?
   - 데이터: `need_value_matching.csv.need_id`, `software_features.csv.feature_id`
   - 목적: 고객 미팅 준비
   - 액션: 니즈-기능-가치 매칭 화면 구현

4. 현재 CSV에 없는 `customer_id`는 어떻게 다룰 것인가?
   - 데이터: 현재 CSV 컬럼 목록, 목표 export 생성 규칙
   - 목적: 데이터 안정성
   - 액션: data_schema와 implementation note 작성

5. 대시보드 표시 제외 데이터는 어떻게 처리할 것인가?
   - 데이터: `dashboard_visible`
   - 목적: 내부 운영
   - 액션: `dashboard_visible=FALSE`는 기본 숨김

## Screen Mapping

| 질문 | 화면 | 목적 |
|---|---|---|
| 최근 니즈 확인 | Home | 마케팅 활용 + 미팅 준비 |
| 반복 니즈 확인 | 마케팅 활용 | 마케팅 활용 |
| 고객별 니즈 검색 | 고객 니즈 검색 | 미팅 준비 |
| 니즈-기능 연결 | 니즈-기능-가치 매칭 | 미팅 준비 |
| 소프트웨어별 가치 확인 | 소프트웨어별 가치 맵 | 마케팅 활용 + 미팅 준비 |
| 고객별 접근 방향 | 고객사별 대응 전략 | 미팅 준비 |
```

---

## 3. `risk_notes_<slug>.md` — Claude Code

```md
# risk_notes_<slug>.md

## Top Risks

### 1. 고객 정보 노출 리스크

- 문제: 대시보드에 고객사명, 담당자, 방문보고서 기반 정보가 표시된다.
- 발생 조건: 사내망 내부 사용자 범위가 불명확하거나 계정 인증 정보가 노출됨.
- 영향: 내부 영업 정보 노출.
- 완화: 사내망 내부 접속 전용, 계정별 로그인, Git에 인증 정보 커밋 금지.

### 2. 데이터 관계 오해 리스크

- 문제: CSV 관계를 잘못 해석하면 잘못된 추천 기능이 표시될 수 있다.
- 발생 조건: 현재 `customer_id` 부재, `customer_name` 문자열 불일치, `related_need_keywords`를 직접 FK로 오해.
- 영향: 마케팅 자료 또는 고객 미팅 준비 자료의 신뢰도 하락.
- 완화: 현재는 `need_id`, `feature_id` 기반 관계 우선 사용. export 개선 후 `customer_id` 기준 사용.

### 3. 현재 CSV와 목표 스키마 혼동 리스크

- 문제: 현재 CSV에는 `customer_id`, `review_status`가 없는데 구현에서 있는 것처럼 가정할 수 있다.
- 영향: 앱 오류 또는 잘못된 상태 표시.
- 완화: 현재 CSV 검증 결과를 `analysis_summary`에 남기고, `review_status`는 1차 범위에서 제외.

### 4. 마케팅 과잉 해석 리스크

- 문제: 반복 니즈나 가치 메시지 후보를 확정 캠페인 또는 확정 고객 수요처럼 표현할 수 있다.
- 완화: 마케팅 활용 후보는 “후보”, “검토 필요”, “근거 확인 필요”로 표현.

## Recommended Actions

1. 현재 CSV와 목표 export 스키마를 먼저 분리 정리한다.
2. 6개 화면 구현 전에 data_schema를 작성한다.
3. admin 수정 기능은 override CSV로만 저장한다.
4. 계정 인증 정보는 코드와 Git에 넣지 않는다.
5. robocopy 전송 대상은 서버 운영에 필요한 파일로 제한한다.
```

---

## 4. `execution_plan_<slug>.md` — Codex

```md
# execution_plan_<slug>.md

## Goal

Claude Code의 계획을 바탕으로 Codex가 실제로 구현하거나 검증할 목표를 작성합니다.

## Implementation Steps

1. Work Board에 `in_progress` 등록
2. 현재 CSV 파일명과 컬럼명 확인
3. 현재 CSV와 목표 export 스키마 차이 정리
4. 데이터 로딩 서비스 구현 또는 검증
5. 6개 화면 라우팅 구현
6. 계정별 로그인 구현
7. admin/viewer 권한 분리
8. admin override CSV 저장 방식 구현
9. confidence 배지와 데이터 품질 표시 적용
10. robocopy 서버 반영 흐름 문서화 또는 스크립트화
11. 실행 검증
12. `analysis_summary`와 `handoff` 작성
13. Work Board 락 해제

## Validation Plan

- `python -m compileall dashboard scripts`
- `python scripts/validate_csv.py`
- `streamlit run dashboard/app.py`
- Docker 파일이 있을 경우 `docker compose config`
- Git에 실데이터, override CSV, 계정 인증 정보가 포함되지 않았는지 확인
```

---

## 5. `analysis_summary_<slug>.md` — Codex

```md
# analysis_summary_<slug>.md

## Summary

이번 분석 또는 구현 결과를 한 문단으로 요약합니다.

## Current CSV Check

| CSV | 존재 여부 | 현재 컬럼 | 주요 이슈 |
|---|---|---|---|
| `customer_needs.csv` |  |  |  |
| `software_features.csv` |  |  |  |
| `need_value_matching.csv` |  |  |  |
| `customer_strategy.csv` |  |  |  |

## Target Schema Gap

| 항목 | 현재 | 목표 | 조치 |
|---|---|---|---|
| 고객 식별 | `customer_name` 중심 | `customer_id` 추가 | export 생성 규칙 업데이트 |
| 표시 여부 | `TRUE/FALSE` | `TRUE/FALSE` 유지 | 검증 |
| 매칭 신뢰도 | `confidence` 있음 | `confidence` 사용 | 배지/필터 표시 |
| 검토 상태 | 없음 | 1차 제외 | 구현하지 않음 |
| 민감도 | 컬럼 있음 | 화면 기준 미사용 | 표시/필터/배지 제외 |

## Dashboard Behavior

- `dashboard_visible=FALSE` 처리:
- `confidence` 배지:
- 계정별 로그인:
- admin/viewer 권한 분리:
- override CSV 저장:
- 마케팅 활용 화면:
- 고객 미팅 준비 정보 표시:

## Screens Implemented or Reviewed

| 화면 | 상태 | 메모 |
|---|---|---|
| Home |  |  |
| 고객 니즈 검색 |  |  |
| 니즈-기능-가치 매칭 |  |  |
| 고객사별 대응 전략 |  |  |
| 소프트웨어별 가치 맵 |  |  |
| 마케팅 활용 |  |  |

## Security / Data Notes

- 실제 고객 데이터 Git 커밋 여부:
- override CSV Git 커밋 여부:
- 계정 인증 정보 Git 커밋 여부:
- 로그에 고객 정보 포함 여부:
```

---

## 6. `data_schema_<slug>.md` — Codex

```md
# data_schema_<slug>.md

## Purpose

현재 CSV 4개와 목표 export 생성 규칙을 분리해 대시보드 구현 기준으로 정리합니다.

## Current Files Checked

| 파일 | 위치 | 확인 결과 |
|---|---|---|
| `customer_needs.csv` |  |  |
| `software_features.csv` |  |  |
| `need_value_matching.csv` |  |  |
| `customer_strategy.csv` |  |  |

## Target Export Rules

- `customer_id` 생성
- `dashboard_visible=TRUE/FALSE`
- `confidence` 사용
- `review_status` 1차 제외
- `sensitivity_level` 화면 기준 미사용

## Relationship Rules

- `need_value_matching.need_id` → `customer_needs.need_id`
- `need_value_matching.feature_id` → `software_features.feature_id`
- 향후 `customer_id` 생성 후 고객사 단위 연결에 우선 사용
- `related_need_keywords`는 참고 연결
```

---

## 7. `deploy_flow_<slug>.md` — Codex

```md
# deploy_flow_<slug>.md

## Purpose

robocopy를 사용해 사용자 컴퓨터에서 서버 PC로 대시보드 운영에 필요한 파일만 안전하게 반영하는 흐름을 정의합니다.

## Deploy Flow

```text
1. 사용자 컴퓨터에서 export CSV 4개와 대시보드 운영 파일 준비
2. robocopy로 서버 PC에 필요한 파일만 전송
3. incoming/ 파일명·개수·헤더 검증
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

## 8. Handoff Log 1줄 템플릿

```md
* [YYYY-MM-DD HH:mm] actor / changed: ... / verified: ... / next: ...
```

---

## 9. 공통 금지 표현

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
