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
        ↓  서버 PC 전송
팀 공유 웹 대시보드
```

공통 기준:

- 파일명은 `docs/collab-protocol.md`의 파일 네이밍 규칙에 따라 `<prefix>_<slug>.md` 형식을 사용합니다.
- `PROJECT.md`가 현재 상태의 단일 진실 원천입니다.
- `dashboard/`가 대시보드 개발 기본 범위입니다.
- `raw/`, `wiki/`, `export/`는 원본 영역이며 사용자 지시 없이 수정하지 않습니다.
- 실제 고객 데이터 CSV는 Git에 커밋하지 않습니다.
- 모든 섹션을 반드시 채울 필요는 없습니다. 해당 내용이 없으면 `해당 없음`이라고 표시합니다.

---

## 인덱스

| 문서 | Owner | 트리거 | 섹션 |
|---|---|---|---|
| `dev_plan_<slug>.md` | Claude Code | 복잡한 개발·분석 작업 전 1차 계획 | 1 |
| `questions_<slug>.md` | Claude Code | 대시보드 화면, 마케팅 활용, 고객 미팅 준비 질문 설계 | 2 |
| `risk_notes_<slug>.md` | Claude Code | 데이터·권한·보안·해석·표시 리스크 점검 | 3 |
| `final_report_<slug>.md` | Claude Code | 사용자 또는 팀 공유용 최종 요약 보고서 | 4 |
| `handoff_<slug>.md` | Claude Code | Claude Code 세션 인계 | 5 |
| `execution_plan_<slug>.md` | Codex | Claude Code 1차 계획 보완 및 구현 계획 | 6 |
| `handoff_<slug>.md` | Codex | Codex 작업 인계 | 7 |
| `analysis_summary_<slug>.md` | Codex | CSV·대시보드·기능 구현 결과 분석 요약 | 8 |
| `data_schema_<slug>.md` | Codex | CSV 스키마와 관계 정의 정리 | 9 |
| `deploy_flow_<slug>.md` | Codex | 서버 반영 흐름 또는 운영 절차 정리 | 10 |

---

## 1. `dev_plan_<slug>.md` — Claude Code

```md
# dev_plan_<slug>.md

## Goal

이번 작업으로 무엇을 만들거나 바꿀지 한두 문장으로 작성합니다.

예:

- Home 화면 1차 버전을 설계한다.
- 마케팅 활용과 고객 미팅 준비를 모두 지원하도록 화면별 핵심 질문을 정리한다.
- `customer_needs.csv`와 `need_value_matching.csv`를 연결해 고객 니즈-기능 매칭 화면을 구현할 수 있도록 개발 범위를 정리한다.
- 서버 PC 운영 전 `incoming/` → `data/` → `backup/` 반영 흐름과 검증 항목을 정의한다.

## Background

왜 이 작업이 필요한지 작성합니다.

포함할 내용:

- 사용자 요청 요약
- 관련 문서: `PROJECT.md`, `business-context.md`, `DESIGN.md`, `AGENTS.md`, `CLAUDE.md`, `docs/collab-protocol.md`
- 마케팅 활용 목적
- 고객 미팅 준비 목적
- 이전 작업 또는 Handoff Log와의 연결

## Scope

이번 작업에 포함할 일을 작성합니다.

예:

- `PROJECT.md` Decisions 기준 확인
- `business-context.md`의 데이터 해석 기준 확인
- `DESIGN.md`의 화면·문구 기준 확인
- CSV 4개 구조 확인
- 화면별 요구사항 정리
- Codex가 구현할 파일 범위 제안
- 검증 기준 정의

## Out of Scope

이번 작업에서는 하지 않을 일을 작성합니다.

예:

- 실제 고객 데이터 원본 수정
- `raw/`, `wiki/`, `export/` 직접 수정
- 서버 PC 운영 파일 즉시 변경
- 로그인 방식 확정 전 인증 구조 구현
- admin 수정값 저장 방식 확정 전 수정 기능 구현
- 사용자가 승인하지 않은 고객 정보 비식별화/삭제
- 고객 구매 가능성, 예산, 도입 의지 추정

## Files to Modify

Codex 또는 후속 작업자가 수정할 수 있는 파일을 작성합니다.

예:

- `dashboard/*`
- `dashboard/pages/*`
- `dashboard/components/*`
- `dashboard/services/*`
- `scripts/validate_csv.py`
- `requirements.txt`
- `Dockerfile`
- `docker-compose.yml`
- `output/analysis_summary_<slug>.md`

## Files Not to Touch

건드리면 안 되는 파일 또는 폴더를 작성합니다.

예:

- `raw/`
- `wiki/`
- `export/`
- 실제 운영 CSV 원본
- 서버 PC 운영 데이터
- 다른 actor가 `PROJECT.md`의 `Files locked`에 잡아둔 파일

## Data Assumptions

현재 기준 데이터와 아직 확인이 필요한 데이터를 분리합니다.

예:

- 기준 CSV: `customer_needs.csv`, `customer_strategy.csv`, `need_value_matching.csv`, `software_features.csv`
- 실제 파일명과 컬럼명은 실제 CSV 확인 후 확정한다.
- `dashboard_visible=FALSE` 또는 이에 준하는 값은 기본 화면에서 숨긴다.
- `sensitivity_level=비공개`는 숨김 기준이 아니라 경고 배지 기준이다.
- `related_need_keywords`는 직접 FK가 아니라 느슨한 키워드 연결이다.

## User Decisions Needed

사용자 결정이 필요한 항목을 작성합니다.

예:

- admin 로그인 방식
- viewer 접근 방식
- admin 수정값 저장 위치
- 서버 PC 전송 방식: robocopy / 공유 폴더 / 수동 복사
- 실제 CSV 파일명과 컬럼명 확정
- 마케팅 활용 후보를 별도 화면으로 둘지, Home과 기존 화면에 포함할지

## Proposed Steps

Codex가 실행할 순서를 작성합니다.

1. `PROJECT.md`와 `Files locked` 확인
2. 필요한 파일 락 등록
3. CSV 파일명, 헤더, 관계 정의 확인
4. 최소 기능 단위로 구현
5. `dashboard_visible`, `sensitivity_level`, `review_status`, `confidence` 표시 기준 적용
6. 로컬 실행 또는 정적 검증
7. 결과 문서 작성
8. Handoff Log 작성 및 파일 락 해제

## Risks

작업 전 예상 리스크를 작성합니다.

- 데이터 해석 리스크
- 고객 정보 노출 리스크
- 검토 필요 항목을 확정 추천처럼 표현할 리스크
- 마케팅 활용 후보를 확정 캠페인 또는 확정 수요처럼 표현할 리스크
- `dashboard_visible=FALSE` 데이터 노출 리스크
- `sensitivity_level=비공개` 항목 경고 누락 리스크
- 파일 충돌 리스크
- 과잉 구현 리스크
- `DESIGN.md` 기준 미준수 리스크

## Verification

Codex가 확인해야 할 기준을 작성합니다.

- CSV 4개 로딩 가능 여부
- 필수 컬럼 존재 여부
- 관계 키 연결 가능 여부
- `dashboard_visible` 필터 적용 여부
- `sensitivity_level` 경고 배지 표시 여부
- `review_status`와 `confidence` 배지 표시 여부
- Streamlit 로컬 실행 여부
- 실제 고객 데이터가 Git에 커밋되지 않았는지 확인
- Docker 관련 파일이 있으면 `docker compose config` 또는 동등한 검증 수행

## Handoff Needed

작업 종료 후 이어받을 actor와 산출물을 작성합니다.

예:

- Codex가 `execution_plan_<slug>.md` 작성 후 구현
- Codex가 구현 후 `analysis_summary_<slug>.md`와 `handoff_<slug>.md` 작성
- Claude Code가 구현 결과를 검토해 `risk_notes_<slug>.md` 또는 `final_report_<slug>.md` 작성
```

---

## 2. `questions_<slug>.md` — Claude Code

```md
# questions_<slug>.md

## Decision Context

이번에 답해야 할 의사결정을 한 줄로 작성합니다.

예:

- 마케팅 활용과 고객 미팅 준비를 위해 Home 화면에서 어떤 정보를 먼저 보여줘야 하는가?
- 영업·엔지니어가 고객 미팅 전 어떤 고객 니즈와 추천 기능을 빠르게 확인해야 하는가?
- 반복적으로 등장하는 고객 니즈를 어떻게 마케팅 콘텐츠 후보로 보여줘야 하는가?
- 고객 니즈-기능 매칭의 신뢰도와 검토 상태를 어떻게 표현해야 안전한가?

## Key Questions

대시보드 설계 또는 구현 전에 답해야 할 질문 3~7개를 작성합니다.

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
   - 액션: Home 요약 또는 마케팅 활용 후보 섹션 구성

3. 고객 니즈와 기능은 어떤 키로 연결되는가?
   - 데이터: `need_value_matching.csv.need_id`, `software_features.csv.feature_id`
   - 목적: 고객 미팅 준비
   - 액션: 니즈-기능-가치 매칭 화면 구현

4. 검토가 필요한 매칭은 어떻게 표시할 것인가?
   - 데이터: `need_value_matching.csv.review_status`, `confidence`
   - 목적: 마케팅 활용 + 고객 미팅 준비
   - 액션: 검토 필요 배지, 필터, 경고 표시

5. 비공개 또는 대시보드 표시 제외 데이터는 어떻게 처리할 것인가?
   - 데이터: `sensitivity_level`, `dashboard_visible`
   - 목적: 보안·운영
   - 액션: `dashboard_visible=FALSE`는 기본 숨김, `sensitivity_level=비공개`는 경고 배지 표시

## Analysis Frame

질문을 어떤 프레임으로 볼 것인지 작성합니다.

예:

- 고객 기준: `customer_name`
- 산업군 기준: `industry`
- 니즈 기준: `need_id`, `need_keyword`, `need_summary`
- 기능 기준: `feature_id`, `software`, `feature_name`
- 매칭 기준: `match_id`, `review_status`, `confidence`
- 전략 기준: `strategy_id`, `main_needs`, `recommended_software`, `next_action`
- 마케팅 기준: 반복 니즈, 가치 메시지, 소프트웨어별 강점, 콘텐츠 후보

## Screen Mapping

각 질문이 어떤 화면과 연결되는지 작성합니다.

| 질문 | 화면 | 목적 |
|---|---|---|
| 최근 니즈 확인 | Home | 마케팅 활용 + 미팅 준비 |
| 반복 니즈 확인 | Home 또는 마케팅 활용 섹션 | 마케팅 활용 |
| 고객별 니즈 검색 | 고객 니즈 검색 | 미팅 준비 |
| 니즈-기능 연결 | 니즈-기능-가치 매칭 | 미팅 준비 |
| 소프트웨어별 가치 확인 | 소프트웨어별 가치 맵 | 마케팅 활용 + 미팅 준비 |

## Out of Scope Questions

이번 작업에서는 답하지 않을 질문을 작성합니다.

예:

- 고객의 실제 구매 가능성 추정
- 예산 또는 도입 의지 추정
- 고객사 내부 의사결정 구조 추정
- 외부 인터넷 기반 고객사 최신 뉴스 자동 수집
- 사내 인증 체계와 연동한 SSO 로그인
- 확정 캠페인 문구 자동 생성
```

---

## 3. `risk_notes_<slug>.md` — Claude Code

```md
# risk_notes_<slug>.md

## Top Risks

가장 큰 리스크 3~5개를 작성합니다.

각 리스크는 다음 구조를 사용합니다.

- 문제
- 발생 조건
- 영향
- 완화 방법

### 1. 고객 정보 노출 리스크

- 문제: 대시보드에 고객사명, 담당자, 방문보고서 기반 정보가 표시된다.
- 발생 조건: 사내망 외부 접근, 로그인 미설정, `sensitivity_level=비공개` 데이터 노출.
- 영향: 고객 정보 또는 내부 영업 정보 노출.
- 완화: 사내망 접근 제한, 로그인 적용, `dashboard_visible` 필터, 비공개 배지 표시.

### 2. 데이터 관계 오해 리스크

- 문제: CSV 관계를 잘못 해석하면 잘못된 추천 기능이 표시될 수 있다.
- 발생 조건: `customer_name` 문자열 불일치, `related_need_keywords`를 직접 FK로 오해.
- 영향: 마케팅 자료 또는 고객 미팅 준비 자료의 신뢰도 하락.
- 완화: `need_id`, `feature_id` 기반 관계 우선 사용, 느슨한 연결은 보조 정보로 표시.

### 3. 검토 상태/신뢰도 해석 리스크

- 문제: `review_status`와 `confidence`가 정확성을 보장하는 것으로 오해될 수 있다.
- 발생 조건: 배지 설명 부족, 필터 기준 불명확.
- 영향: 검토가 필요한 매칭을 확정 정보처럼 사용.
- 완화: `검토 필요/검토 완료`, `높음/중간/낮음` 의미를 UI와 문서에 명시.

### 4. 마케팅 과잉 해석 리스크

- 문제: 반복 니즈나 가치 메시지 후보를 확정 캠페인 또는 확정 고객 수요처럼 표현할 수 있다.
- 발생 조건: 마케팅 활용 후보에 검토 상태와 근거가 함께 표시되지 않음.
- 영향: 과장된 마케팅 메시지 또는 부정확한 제안서 작성.
- 완화: 마케팅 활용 후보는 “후보”, “검토 필요”, “근거 확인 필요”로 표현.

## Data Caveats

데이터의 한계를 작성합니다.

- CSV 4개는 `export/`에서 생성된 대시보드용 구조화 데이터다.
- 원본 지식은 `wiki/`와 방문보고서에 있다.
- `customer_name` 연결은 문자열 기반이므로 표기 차이에 취약하다.
- `related_need_keywords`는 직접 FK가 아니라 키워드 기반 참고 연결이다.
- `dashboard_visible` 값은 실제 CSV 값을 확인해야 한다.
- `sensitivity_level=비공개`는 숨김 기준이 아니라 경고 기준이다.

## Security / Privacy Notes

보안·개인정보·민감 정보 관점의 주의사항을 작성합니다.

- 실제 고객 데이터 CSV는 Git에 커밋하지 않는다.
- 고객사명과 담당자 정보는 미팅 준비 목적에 필요한 범위에서만 표시한다.
- `sensitivity_level=비공개` 항목은 경고 배지로 표시한다.
- 서버 운영 로그에 고객 정보 원문을 과도하게 남기지 않는다.

## UI / Design Risks

`DESIGN.md` 기준과 관련된 리스크를 작성합니다.

- 차트를 과도하게 넣어 핵심 검색과 판단이 안 보이는 문제.
- 검토 필요/비공개/낮은 confidence 상태가 눈에 띄지 않는 문제.
- 마케팅 활용 후보와 고객 미팅 준비 항목이 구분되지 않는 문제.
- 내부 시스템이 외부 홍보 페이지처럼 보이는 문제.

## Recommended Actions

다음 조치를 작성합니다.

1. 검토 필요 항목을 Home에서 먼저 보이게 한다.
2. 마케팅 활용 후보에는 근거 데이터와 검토 상태를 함께 표시한다.
3. `dashboard_visible` 필터와 `sensitivity_level` 배지를 우선 구현한다.
4. 실제 운영 전 CSV 검증 결과를 `analysis_summary_<slug>.md`에 남긴다.
```

---

## 4. `final_report_<slug>.md` — Claude Code

```md
# final_report_<slug>.md

## 한 줄 결론

이번 작업 결과를 한 문장으로 작성합니다.

예:

- 이번 작업은 마케팅 활용과 고객 미팅 준비를 모두 지원하는 대시보드 1차 구조를 정의한 것이다.

## 왜 그런가

판단 근거를 작성합니다.

- `PROJECT.md`의 목표와 Decisions
- `business-context.md`의 데이터 해석 기준
- `DESIGN.md`의 화면·문구 기준
- Codex의 `analysis_summary_<slug>.md`
- 실제 CSV 검증 결과

## 이번 작업에서 바뀐 것

변경된 파일과 내용을 요약합니다.

| 파일 | 변경 내용 |
|---|---|
| `dashboard/*` | 화면 구현 또는 수정 내용 |
| `scripts/*` | 검증 또는 보조 스크립트 |
| `docs/*` | 문서 기준 정리 |
| `output/*` | 분석·인수인계 산출물 |

## 대시보드에서 가장 먼저 보여줘야 할 정보

마케팅 활용과 고객 미팅 준비 관점으로 나눠 작성합니다.

### 마케팅 활용

- 반복적으로 등장하는 니즈 키워드
- 고객군 또는 산업군별 관심 주제
- 소프트웨어별 가치 메시지 후보
- 세미나, 기술 콘텐츠, 제안서 소재 후보

### 고객 미팅 준비

- 최근 고객 니즈
- 고객사별 대응 전략
- 추천 기능과 가치 메시지
- 다음 미팅 추천 질문
- 검토 필요 또는 낮은 confidence 항목

## 사용 CSV와 핵심 컬럼

| CSV | 핵심 컬럼 | 사용 목적 |
|---|---|---|
| `customer_needs.csv` | `need_id`, `customer_name`, `need_keyword`, `need_summary` | 고객 니즈 검색 |
| `need_value_matching.csv` | `need_id`, `feature_id`, `review_status`, `confidence` | 니즈-기능 매칭 |
| `software_features.csv` | `feature_id`, `software`, `feature_name`, `value_keywords` | 소프트웨어별 가치 맵 |
| `customer_strategy.csv` | `customer_name`, `approach_details`, `suggested_questions`, `next_action` | 고객사별 대응 전략 |

## 데이터 해석 리스크

- 고객 구매 가능성, 예산, 도입 의지는 추정하지 않는다.
- 검토 필요 매칭은 확정 추천처럼 표현하지 않는다.
- 마케팅 활용 후보는 확정 캠페인이 아니라 후보로 표현한다.
- `related_need_keywords`는 직접 연결이 아니라 참고 연결이다.

## 권한과 운영 기준

- `admin`: 사용자, 데이터 수정·검토 상태 관리·운영 반영.
- `viewer`: 팀원, 조회 전용.
- 1차 개발에는 viewer 코멘트 기능을 포함하지 않는다.
- admin 수정 기능은 저장 방식 확정 후 별도 작업으로 진행한다.

## 서버 반영 방식

기본 흐름:

```text
incoming/ → 검증 → backup/ → data/ → Docker 재시작 또는 갱신 확인
```

전송 방식은 사용자 결정에 따른다.

## 검증 결과

Codex의 검증 결과 또는 직접 확인 결과를 작성합니다.

- CSV 로딩:
- 필수 컬럼:
- 관계 검증:
- Streamlit 실행:
- Docker 검증:
- Git 실데이터 포함 여부:

## 남은 확인 사항

- admin 로그인 방식
- viewer 접근 방식
- admin 수정값 저장 위치
- 서버 전송 방식
- 실제 CSV 파일명과 컬럼명 차이

## 다음 작업

1. 다음 작업 1
2. 다음 작업 2
3. 다음 작업 3
```

---

## 5. `handoff_<slug>.md` — Claude Code

```md
# handoff_<slug>.md

## Current Goal

현재 목표를 작성합니다.

## Files Reviewed

검토한 파일을 작성합니다.

- `PROJECT.md`
- `business-context.md`
- `DESIGN.md`
- `AGENTS.md`
- `docs/collab-protocol.md`
- 관련 `analysis_summary` 또는 `execution_plan`

## What Was Decided

결정된 내용을 작성합니다.

## Open Risks

남아 있는 리스크를 작성합니다.

- 데이터 해석 리스크
- 마케팅 과잉 해석 리스크
- 고객 정보 노출 리스크
- 권한/로그인 미확정 리스크
- 서버 반영 리스크

## User Decisions Needed

사용자 결정이 필요한 항목을 작성합니다.

## Next Draft Needed

다음 Claude Code가 작성해야 할 문서를 작성합니다.

- `risk_notes_<slug>.md`
- `final_report_<slug>.md`
- `questions_<slug>.md`

## Lock State

- Work Board 상태:
- 현재 락 파일:
- 락 해제 여부:
```

---

## 6. `execution_plan_<slug>.md` — Codex

```md
# execution_plan_<slug>.md

## Goal

Claude Code의 계획을 바탕으로 Codex가 실제로 구현하거나 검증할 목표를 작성합니다.

## Inputs Reviewed

검토한 입력 문서를 작성합니다.

- `PROJECT.md`
- `docs/collab-protocol.md`
- `AGENTS.md`
- `business-context.md`
- `DESIGN.md`
- `CLAUDE.md`
- `output/dev_plan_<slug>.md`

## Files to Change

수정할 파일을 작성합니다.

예:

- `dashboard/app.py`
- `dashboard/pages/*.py`
- `dashboard/components/*.py`
- `dashboard/services/*.py`
- `scripts/validate_csv.py`
- `requirements.txt`

## Files Not to Touch

수정하지 않을 파일을 작성합니다.

- `raw/`
- `wiki/`
- `export/`
- 실제 운영 CSV 원본
- locked 상태의 다른 파일

## Implementation Steps

구현 순서를 작성합니다.

1. Work Board에 `in_progress` 등록
2. CSV 파일명과 컬럼명 확인
3. 데이터 로딩 서비스 구현 또는 검증
4. Home 화면 구성
5. 고객 니즈 검색 화면 구성
6. 니즈-기능-가치 매칭 화면 구성
7. 고객사별 대응 전략 화면 구성
8. 소프트웨어별 가치 맵 화면 구성
9. 마케팅 활용 후보 표시 방식 반영
10. 상태 배지와 민감도 경고 적용
11. 실행 검증
12. `analysis_summary`와 `handoff` 작성
13. Work Board 락 해제

## Validation Plan

검증 계획을 작성합니다.

- `python -m compileall dashboard scripts`
- `python scripts/validate_csv.py`
- `streamlit run dashboard/app.py`
- Docker 파일이 있을 경우 `docker compose config`

실제 파일 구조와 명령이 다르면 실제 구조에 맞게 조정합니다.

## Risks / Open Questions

- 실제 CSV 파일명이 문서 기준과 다른가?
- 필수 컬럼이 누락되었는가?
- 로그인 방식이 미확정인가?
- admin 수정값 저장 위치가 미확정인가?
- 마케팅 활용 후보를 어디에 표시할 것인가?

## Expected Outputs

- 구현 파일
- 검증 결과
- `output/analysis_summary_<slug>.md`
- `output/handoff_<slug>.md`
```

---

## 7. `handoff_<slug>.md` — Codex

```md
# handoff_<slug>.md

## What Changed

변경한 내용을 요약합니다.

## Files Changed

| 파일 | 변경 내용 |
|---|---|
| `dashboard/...` | ... |
| `scripts/...` | ... |
| `output/...` | ... |

## Verified

실행 또는 검증한 내용을 작성합니다.

- CSV 로딩:
- 필수 컬럼 검증:
- 관계 검증:
- Streamlit 실행:
- Docker 검증:
- Git 실데이터 포함 여부:

## Not Verified

검증하지 못한 내용을 작성합니다.

## Data Handling

- 실제 고객 데이터 사용 여부:
- 샘플/익명 데이터 사용 여부:
- `dashboard_visible` 처리:
- `sensitivity_level` 처리:
- `review_status` 처리:
- `confidence` 처리:

## Risks Carried Over

다음 actor에게 넘길 리스크를 작성합니다.

- 데이터 해석 리스크:
- 보안 리스크:
- 마케팅 과잉 해석 리스크:
- UI/UX 리스크:
- 서버 반영 리스크:

## Open Items

남은 작업을 작성합니다.

## Lock State

- Work Board 행 제거 여부:
- 남은 파일 락:

## Next Recommended Step

다음 권장 작업을 작성합니다.
```

---

## 8. `analysis_summary_<slug>.md` — Codex

```md
# analysis_summary_<slug>.md

## Summary

이번 분석 또는 구현 결과를 한 문단으로 요약합니다.

## Inputs

사용한 입력 파일과 데이터 위치를 작성합니다.

- CSV 위치:
- 사용한 CSV:
- manifest 사용 여부:
- 샘플 데이터 여부:

## CSV Check

| CSV | 존재 여부 | 필수 컬럼 | 주요 이슈 |
|---|---|---|---|
| `customer_needs.csv` |  |  |  |
| `customer_strategy.csv` |  |  |  |
| `need_value_matching.csv` |  |  |  |
| `software_features.csv` |  |  |  |

## Relationship Check

| 관계 | 확인 결과 | 이슈 |
|---|---|---|
| `need_value_matching.need_id` → `customer_needs.need_id` |  |  |
| `need_value_matching.feature_id` → `software_features.feature_id` |  |  |
| `customer_strategy.customer_name` → `customer_needs.customer_name` |  |  |
| `software_features.related_need_keywords` ↔ `customer_needs.need_keyword` |  | 느슨한 키워드 연결 |

## Dashboard Behavior

- `dashboard_visible=FALSE` 처리:
- `sensitivity_level=비공개` 표시:
- `review_status` 배지:
- `confidence` 배지:
- 검색/필터 동작:
- 마케팅 활용 후보 표시:
- 고객 미팅 준비 정보 표시:

## Screens Implemented or Reviewed

| 화면 | 상태 | 메모 |
|---|---|---|
| Home |  |  |
| 고객 니즈 검색 |  |  |
| 니즈-기능-가치 매칭 |  |  |
| 고객사별 대응 전략 |  |  |
| 소프트웨어별 가치 맵 |  |  |

## Commands Run

```text
실행한 명령을 여기에 기록
```

## Results

- 성공한 것:
- 실패한 것:
- 오류 메시지:
- 재현 방법:

## Security / Data Notes

- 실제 고객 데이터 Git 커밋 여부:
- 민감 정보 표시 방식:
- 로그에 고객 정보 포함 여부:

## Open Issues

남은 이슈를 작성합니다.

## Next Step

다음 작업자를 위한 권장 작업을 작성합니다.
```

---

## 9. `data_schema_<slug>.md` — Codex

```md
# data_schema_<slug>.md

## Purpose

CSV 4개와 manifest의 실제 구조를 확인하고 대시보드 구현 기준으로 정리합니다.

## Files Checked

| 파일 | 위치 | 확인 결과 |
|---|---|---|
| `customer_needs.csv` |  |  |
| `customer_strategy.csv` |  |  |
| `need_value_matching.csv` |  |  |
| `software_features.csv` |  |  |
| `dashboard_manifest.json` |  |  |

## Columns

### `customer_needs.csv`

| 컬럼 | 의미 | 필수 여부 | 메모 |
|---|---|---|---|
| `need_id` |  |  |  |
| `customer_name` |  |  |  |

### `customer_strategy.csv`

| 컬럼 | 의미 | 필수 여부 | 메모 |
|---|---|---|---|
| `strategy_id` |  |  |  |
| `customer_name` |  |  |  |

### `need_value_matching.csv`

| 컬럼 | 의미 | 필수 여부 | 메모 |
|---|---|---|---|
| `match_id` |  |  |  |
| `need_id` |  |  |  |
| `feature_id` |  |  |  |

### `software_features.csv`

| 컬럼 | 의미 | 필수 여부 | 메모 |
|---|---|---|---|
| `feature_id` |  |  |  |
| `software` |  |  |  |

## Relationship Rules

- `need_value_matching.need_id` → `customer_needs.need_id`
- `need_value_matching.feature_id` → `software_features.feature_id`
- `customer_strategy.customer_name` → `customer_needs.customer_name`
- `software_features.related_need_keywords` ↔ `customer_needs.need_keyword`는 참고 연결

## Display Rules

- `dashboard_visible=FALSE` 또는 이에 준하는 값은 기본 화면에서 숨김.
- `sensitivity_level=비공개`는 경고 배지로 표시.
- `review_status`는 검토 상태 배지로 표시.
- `confidence`는 신뢰도 배지로 표시.

## Data Quality Issues

| 이슈 | 영향 | 권장 조치 |
|---|---|---|
|  |  |  |

## Open Questions

사용자 확인이 필요한 항목을 작성합니다.
```

---

## 10. `deploy_flow_<slug>.md` — Codex

```md
# deploy_flow_<slug>.md

## Purpose

서버 PC에서 대시보드 데이터와 코드가 안전하게 반영되도록 운영 흐름을 정의합니다.

## Local Structure

```text
knowledge_for_marketing/
├── raw/
├── wiki/
├── export/
├── dashboard/
├── scripts/
└── logs/
```

## Server Structure

```text
marketing_llmwiki/
├── dashboard/
├── incoming/
├── data/
├── backup/
└── logs/
```

## Deploy Flow

```text
1. 사용자 컴퓨터에서 export CSV 4개 생성
2. 서버 PC의 incoming/으로 전송
3. incoming/ 파일명·개수·헤더 검증
4. 기존 data/를 backup/으로 복사
5. incoming/ 검증본을 data/로 반영
6. Docker 대시보드 재시작 또는 자동 갱신 확인
7. logs/에 반영 결과 기록
```

## Transfer Method

전송 방식은 사용자 결정에 따릅니다.

- robocopy:
- 공유 폴더:
- 수동 복사:
- 기타:

확정되지 않았으면 `확정 필요`라고 작성합니다.

## Validation Before Replace

- CSV 4개 존재 여부
- manifest 존재 여부
- 인코딩 확인
- 필수 컬럼 확인
- 관계 키 확인
- 파일 크기 또는 빈 파일 여부 확인
- 검증 실패 시 기존 `data/` 교체 금지

## Backup Rule

- 기존 `data/`는 교체 전 `backup/`에 보관한다.
- 백업 폴더명은 날짜/시간을 포함한다.
- 백업 실패 시 배포를 중단한다.

## Logging Rule

- 반영 시각
- 반영 파일명
- 검증 결과
- 성공/실패 여부
- 고객 정보 원문은 과도하게 남기지 않음

## Rollback

문제 발생 시 복구 절차를 작성합니다.

## Open Questions

- 서버 PC 경로 확정 여부
- 전송 방식 확정 여부
- Docker 재시작 방식
- 운영 담당자
```

---

## 11. Handoff Log 1줄 템플릿

`PROJECT.md` Handoff Log에는 아래 형식으로 작성합니다.

```md
* [YYYY-MM-DD HH:mm] actor / changed: ... / verified: ... / next: ...
```

예:

```md
* [2026-06-06 00:00] Codex / changed: Home 화면 1차 구현 및 CSV 로딩 서비스 추가 / verified: Streamlit 로컬 실행, CSV 4개 로딩, dashboard_visible 필터 확인 / next: Claude Code가 risk_notes_dashboard-foundation.md 작성
```

---

## 12. 공통 금지 표현

마케팅 활용 또는 고객 미팅 준비 문서에서 다음 표현은 사용하지 않습니다.

- “이 고객은 구매 가능성이 높습니다.”
- “이 고객은 반드시 이 솔루션을 도입할 것입니다.”
- “예산이 충분할 것으로 보입니다.”
- “고객 내부 의사결정이 완료된 것으로 보입니다.”
- “검토 상태가 없지만 바로 제안해도 됩니다.”
- “마케팅 캠페인으로 확정합니다.”
- “근거는 없지만 이 기능을 추천합니다.”
- “비공개 항목이지만 일반 화면에 노출해도 됩니다.”

권장 표현:

- “이 항목은 마케팅 콘텐츠 후보로 검토할 수 있습니다.”
- “이 매칭은 검토 필요 상태이므로 담당자 확인 후 활용하세요.”
- “다음 미팅에서 현재 업무 병목과 자동화 범위를 확인하는 질문이 적합합니다.”
- “이 가치 메시지는 관련 니즈와 기능 근거를 확인한 뒤 제안서에 활용하세요.”
