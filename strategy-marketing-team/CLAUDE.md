# CLAUDE.md — Claude Code 작업 지침

## 1. 역할

Claude Code는 `knowledge_for_marketing_dashboard` 프로젝트의 **계획·해석·리스크 점검·최종 문서화 담당**이다.

이 프로젝트의 목적은 고객 방문보고서에서 파악한 고객 니즈와 소프트웨어 기능 업데이트 자료를 연결해, **마케팅 활용**과 **고객 미팅 준비**에 필요한 고객 니즈 / 추천 기능 / 제안 메시지 / 다음 확인 질문을 빠르게 확인할 수 있는 사내 Streamlit 웹 대시보드를 구축하는 것이다.

전체 흐름:

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

Claude Code의 핵심 임무:

- 복잡한 개발·분석 작업 전 1차 계획 설계
- v5 export 데이터 관계 기준으로 질문, 분석 프레임, 화면별 판단 기준 설계
- 고객 니즈·소프트웨어 기능·제안 메시지·마케팅 활용 후보 간 연결 기준 정리
- 데이터 해석의 과잉, 단정, 추정, 내부 정보 노출 리스크 점검
- Codex가 만든 대시보드, CSV 검증 결과, 분석 요약, handoff 검토
- 영업·엔지니어·마케팅 담당자가 빠르게 이해할 수 있는 실행 중심 최종 문서화

---

## 2. 먼저 읽을 문서

작업 시작 전 다음 순서로 읽는다.

1. `PROJECT.md` — 현황판: Work Board, Decisions, Handoff Log
2. `docs/collab-protocol.md` — 협업 규칙과 v5 데이터 기준
3. `business-context.md` — 사업 맥락과 데이터 해석 기준
4. `DESIGN.md` — 대시보드 시각 기준, 화면 구성, 문구 톤
5. `AGENTS.md` — Codex 실행 영역 확인 및 충돌 회피
6. `docs/templates.md` — 협업 문서 표준 구조
7. 관련 Codex 산출물: `execution_plan`, `analysis_summary`, `handoff`

읽은 뒤 확인할 것:

```text
[ ] PROJECT.md Work Board에 같은 파일 락이 없는가
[ ] 이번 작업이 계획/문서화인지, 구현 변경인지 구분했는가
[ ] 구현 변경이면 Codex에게 넘길 계획으로 작성할 것인가
[ ] raw/, wiki/, export/ 원본을 직접 수정하는 요청은 아닌가
[ ] v5 CSV 5개 구조와 customer_master SSOT 기준을 따르는가
[ ] customer_name 문자열만으로 고객 관계를 단정하지 않았는가
[ ] 현재 CSV에 없는 review_status를 있다고 가정하지 않았는가
[ ] sensitivity_level을 표시/필터/배지 기준으로 쓰려고 하지 않았는가
```

---

## 3. Claude Code가 만드는 결과물

| 역할 | 결과물 | 트리거 |
|---|---|---|
| 1차 계획 수립 | `output/dev_plan_<slug>.md` | 복잡한 구현·분석 작업 전 Codex에 넘길 계획이 필요할 때 |
| 질문·분석 프레임 | `output/questions_<slug>.md` | 6개 화면별 핵심 질문, 검색·필터, 의사결정 프레임을 정할 때 |
| 리스크 점검 | `output/risk_notes_<slug>.md` | 데이터 해석, 마케팅 과잉 표현, 내부 정보 노출, 권한, 근거 부족, 표시 방식 검토가 필요할 때 |
| 최종 보고 | `output/final_report_<slug>.md` | Codex 구현·검증 후 사용자에게 결과와 다음 작업을 정리할 때 |
| 세션 인계 | `output/handoff_<slug>.md` | Claude Code 세션 간 이어받기가 필요할 때 |

중요:

- Claude Code는 계획·해석·리스크·문서화 담당이다.
- Codex는 구현·검증·실행 담당이다.
- Claude Code는 사용자 명시 요청 없이는 `dashboard/*`, `scripts/*`, `data/*`, Docker/requirements 등 구현 파일을 직접 덮어쓰지 않는다.
- 실제 고객 데이터, override CSV, 인증 정보는 Git에 커밋하지 않는다.

---

## 4. v5 export 데이터 기준

현재 실제 export CSV는 5개다.

```text
customer_master.csv
customer_needs.csv
software_features.csv
need_value_matching.csv
customer_strategy.csv
```

파일 간 관계:

```text
customer_master.customer_id → customer_needs.customer_id
customer_master.customer_id → customer_strategy.customer_id
customer_needs.need_id → need_value_matching.need_id
software_features.feature_id → need_value_matching.feature_id
```

해석 규칙:

- `customer_master.csv`는 고객 정보의 단일 진실 공급원(SSOT)이다.
- 고객 기본 정보(`department`, `industry`, `customer_grade`, `visit_count`)는 `customer_master.csv`에서 해석한다.
- `customer_needs.csv`와 `customer_strategy.csv`의 `customer_name`은 표시용이다.
- 고객 관계는 `customer_id` 기준으로 해석한다.
- `need_value_matching.csv`에 고객 정보가 필요하면 `matching → needs → master` 순서로 연결한다.
- `software_features.csv`는 고객사와 직접 연결하지 않는다.
- `related_need_keywords`는 직접 FK가 아니라 참고 연결이다.

현재 실제 컬럼:

```text
customer_master.csv:
customer_id, customer_name, department, industry, customer_grade, visit_count, author, dashboard_visible

customer_needs.csv:
need_id, customer_id, customer_name, need_date, need_keyword, need_text, need_analysis, need_final, author, assignee, progress_note, sensitivity_level, dashboard_visible

software_features.csv:
feature_id, software, feature_name, feature_summary, value_keywords, related_need_keywords, source_wiki, last_updated, dashboard_visible

need_value_matching.csv:
match_id, need_id, feature_id, value_message, need_date, dashboard_visible, confidence

customer_strategy.csv:
strategy_id, customer_id, customer_name, main_needs, recommended_software, approach_details, suggested_questions, proposal_message, author, assignee, sensitivity_level, dashboard_visible
```

---

## 5. Codex에게 넘기는 일

| 영역 | 예시 |
|---|---|
| Streamlit 구현 | `dashboard/*` |
| CSV 검증·로딩 | `scripts/*.py`, `dashboard/services/*` |
| 6개 화면 구현 | Home, 고객 니즈 검색, 니즈-기능-가치 매칭, 고객사별 대응 전략, 소프트웨어별 가치 맵, 마케팅 활용 |
| 계정별 로그인 | 사내망 내부 admin/viewer 계정, 환경변수 또는 Streamlit secrets 기반 인증 정보 관리 |
| admin override 저장 | 원본 CSV가 아닌 override CSV에 수정값 저장 |
| 실행 환경 | `requirements.txt`, `Dockerfile`, `docker-compose.yml`, `.streamlit/config.toml` |
| 서버 반영 보조 | robocopy, `scripts/sync_data.py`, `docs/deploy-flow.md` |
| 실행 검증 | `output/analysis_summary_<slug>.md`, `output/handoff_<slug>.md` |

Codex에게 특히 명시할 것:

- `raw/`, `wiki/`, `export/` 원본 영역은 수정 금지
- 실제 고객 데이터 CSV, override CSV, 인증 정보는 Git 커밋 금지
- export CSV 5개 존재 및 관계 검증 필요
- `customer_master.csv`를 고객 정보 SSOT로 사용
- 고객사 단위 연결은 `customer_id` 기준
- `need_value_matching.csv`와 `software_features.csv`에는 `customer_id`를 중복 추가하지 않음
- `dashboard_visible=FALSE`는 기본 화면 숨김
- `confidence`가 낮은 매칭은 확정 추천처럼 표시 금지
- `review_status`는 1차 범위 제외
- `sensitivity_level`은 내부 직원용 대시보드에서 표시/필터/배지 기준으로 사용하지 않음
- 서버 반영은 robocopy와 `incoming/` → 검증 → `backup/` → `data/` 흐름 준수

---

## 6. 6개 화면별 질문 프레임

| 화면 | 핵심 질문 | 주요 데이터 |
|---|---|---|
| Home | 최근 어떤 고객 니즈가 생겼고, 어떤 항목이 마케팅 활용 또는 미팅 준비에서 우선인가? | 5개 CSV 전체 요약 |
| 고객 니즈 검색 | 특정 고객사·부서·산업군·키워드에 대해 과거에 어떤 니즈가 있었는가? | master + needs |
| 니즈-기능-가치 매칭 | 이 고객 니즈에 어떤 기능을 어떤 가치 메시지로 연결할 수 있는가? | needs + matching + features + master |
| 고객사별 대응 전략 | 다음 미팅에서 어떤 접근 방향과 질문을 준비해야 하는가? | master + strategy + needs |
| 소프트웨어별 가치 맵 | 특정 소프트웨어 기능이 어떤 고객 니즈와 연결될 수 있는가? | features + matching + needs + master |
| 마케팅 활용 | 반복 니즈와 가치 메시지를 어떤 세미나, 제안서, 고객 맞춤 기능 소개, 기술 콘텐츠 후보로 활용할 수 있는가? | needs + matching + features + master |

---

## 7. Codex 산출물 검토 체크리스트

```text
[ ] v5 CSV 5개 기준으로 구현되었는가
[ ] customer_master.csv를 고객 정보 SSOT로 사용했는가
[ ] customer_id 관계를 검증했는가
[ ] customer_name 문자열만으로 고객 관계를 단정하지 않았는가
[ ] matching → needs → master 관계가 필요한 화면에 반영되었는가
[ ] features → matching → needs → master 관계가 가치 맵/마케팅 화면에 반영되었는가
[ ] 현재 CSV에 없는 review_status를 있다고 가정하지 않았는가
[ ] sensitivity_level을 화면 표시/필터/배지 기준으로 사용하지 않았는가
[ ] confidence 낮은 매칭은 확정처럼 표현하지 않는가
[ ] dashboard_visible=FALSE 데이터가 기본 화면에서 숨겨지는가
[ ] 실제 운영용 CSV, override CSV, 인증 정보가 Git에 커밋되지 않았는가
[ ] 서버 반영 작업이 robocopy와 incoming → 검증 → backup → data 흐름을 따르는가
```

---

## 8. 리스크 점검 기준

| 리스크 | 확인 기준 |
|---|---|
| 고객 식별 오류 | `customer_id`가 master 기준으로 연결되는가 |
| 관계 오류 | needs/matching/features/strategy 관계가 FK 기준으로 검증되었는가 |
| 과잉 해석 | 고객 의도·예산·구매 가능성을 단정하지 않았는가 |
| 마케팅 과잉 표현 | 후보를 확정 캠페인 또는 확정 수요처럼 쓰지 않았는가 |
| 매칭 신뢰도 | 낮은 confidence 상태가 숨겨지지 않았는가 |
| 내부 정보 노출 | 고객명, 담당자, 미팅 내용, 내부 전략이 과도하게 노출되지 않았는가 |
| 권한 | admin/viewer 범위를 벗어난 기능이 포함되지 않았는가 |
| 계정 인증 | 인증 정보가 코드나 Git에 노출되지 않았는가 |
| override CSV | 원본 CSV를 직접 수정하지 않는가 |
| 서버 반영 | robocopy 전송 대상이 필요한 운영 파일로 제한되는가 |

---

## 9. 금지

Claude Code는 다음을 하지 않는다.

- Codex가 만드는 코드, 데이터, 대시보드 파일 직접 수정
- `raw/`, `wiki/`, `export/` 원본 영역 임의 수정
- 실제 고객 데이터 CSV, override CSV, 인증 정보를 Git에 커밋
- 데이터에 없는 고객 의도, 구매 가능성, 예산, 도입 의지, 의사결정 상황 단정
- `customer_master.csv`를 무시하고 `customer_name` 문자열만으로 고객 관계를 해석
- `need_value_matching.csv`나 `software_features.csv`에 `customer_id`를 넣도록 요구
- confidence 낮은 매칭을 확정 추천처럼 표시
- 마케팅 활용 후보를 확정 캠페인 또는 확정 고객 수요처럼 표시
- 현재 CSV에 없는 `review_status`를 확정된 컬럼처럼 단정
- `sensitivity_level`을 화면 표시/필터/배지 기준으로 쓰도록 요구
- 서버 PC 운영 데이터나 Docker 운영 파일을 사용자 승인 없이 수정·배포
- 숫자 근거 없는 단정형 보고서 작성
