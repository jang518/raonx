# AGENTS.md — Codex 작업 지침

## 1. 역할

Codex는 `knowledge_for_marketing_dashboard` 프로젝트의 **실행 담당**이다.

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

Codex의 핵심 임무:

- v5 export CSV 5개를 기준으로 Streamlit 대시보드 개발
- CSV 로딩, 스키마 검증, 관계 무결성 검증, 화면 렌더링, 오류 검증 수행
- 6개 화면 구현: Home, 고객 니즈 검색, 니즈-기능-가치 매칭, 고객사별 대응 전략, 소프트웨어별 가치 맵, 마케팅 활용
- 사내망 내부 계정 로그인 구현: admin은 조회·수정 가능, 그 외 계정은 viewer 조회 전용
- admin 수정값을 원본 CSV가 아니라 override CSV에 저장
- Docker 기반 로컬/서버 실행 환경 구성
- robocopy 기반 서버 PC 반영 흐름 보조 스크립트 또는 운영 문서 작성
- 다음 actor가 이어받을 수 있도록 `analysis_summary` 또는 `handoff` 작성

---

## 2. 먼저 읽을 문서

작업 시작 전 다음 순서로 읽는다.

1. `PROJECT.md` — 현황판: Work Board, Decisions, Handoff Log
2. `docs/collab-protocol.md` — 협업 규칙: 역할, 파일 락, 네이밍, 환경, archive, 서버 반영
3. `business-context.md` — 사업 맥락, 데이터 해석 기준, 금지 사항
4. `DESIGN.md` — 대시보드 시각 기준, 화면 구성, 문구 톤
5. `CLAUDE.md` — Claude Code 영역 확인 및 충돌 회피
6. `docs/templates.md` — 협업 문서 표준 구조
7. 관련 `dev_plan_<slug>.md`, Handoff Log, 기존 Codex 산출물

작업 전 확인:

```text
[ ] PROJECT.md Work Board에 같은 파일 락이 없는가
[ ] 수정 대상이 dashboard/, scripts/, output/ 중 어디인가
[ ] raw/, wiki/, export/를 직접 수정하는 작업은 아닌가
[ ] 실제 고객 데이터, override CSV, 인증 정보가 Git에 커밋될 가능성이 있는가
[ ] v5 CSV 5개 구조와 customer_master SSOT 기준을 따르는가
[ ] 필요한 정보가 없으면 추론하지 않고 질문 또는 blocked 처리할 것인가
```

---

## 3. v5 export 데이터 기준

현재 실제 export CSV는 5개다.

```text
customer_master.csv
customer_needs.csv
software_features.csv
need_value_matching.csv
customer_strategy.csv
```

### 3.1 파일별 역할

| 파일 | 역할 | 핵심 키 |
|---|---|---|
| `customer_master.csv` | 고객 정보 SSOT. 고객 ID, 표준 고객사명, 부서, 산업군, 고객 등급, 방문 횟수, 작성자, 표시 여부 관리 | `customer_id` |
| `customer_needs.csv` | 고객 니즈 데이터. 고객 기본 정보는 master 참조 | `need_id`, `customer_id` |
| `software_features.csv` | 소프트웨어 기능과 가치 키워드 데이터 | `feature_id` |
| `need_value_matching.csv` | 니즈와 기능의 가치 매칭 데이터 | `match_id`, `need_id`, `feature_id` |
| `customer_strategy.csv` | 고객사별 대응 전략, 추천 소프트웨어, 다음 질문, 제안 메시지 | `strategy_id`, `customer_id` |

### 3.2 관계 규칙

```text
customer_master.customer_id → customer_needs.customer_id
customer_master.customer_id → customer_strategy.customer_id
customer_needs.need_id → need_value_matching.need_id
software_features.feature_id → need_value_matching.feature_id
```

구현 규칙:

- 고객 기본 정보는 `customer_master.csv`를 기준으로 가져온다.
- 고객사 단위 JOIN은 `customer_name`이 아니라 `customer_id`를 우선 사용한다.
- `customer_needs.customer_name`, `customer_strategy.customer_name`은 표시용으로만 사용한다.
- `need_value_matching.csv`에 `customer_id`를 추가하지 않는다.
- 매칭 화면에서 고객 정보가 필요하면 `matching → needs → master` 순서로 연결한다.
- `software_features.csv`에 `customer_id`를 추가하지 않는다.
- `related_need_keywords`는 직접 FK가 아니라 참고 연결로만 사용한다.

### 3.3 현재 실제 컬럼

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

### 3.4 표시 기준

- `dashboard_visible=FALSE` 데이터는 기본 화면에서 숨긴다.
- `confidence`는 매칭 신뢰도 표시 기준으로 사용한다.
- `review_status`는 1차 범위에서 제외한다.
- `sensitivity_level`은 화면 표시/필터/배지 기준으로 사용하지 않는다.

---

## 4. Codex가 만드는 결과물

| 역할 | 결과물 | 트리거 |
|---|---|---|
| 실행 계획 보완 | `output/execution_plan_<slug>.md` | Claude Code의 `dev_plan_<slug>.md`를 받은 후 |
| CSV 구조 검증 | `output/analysis_summary_<slug>.md` | export CSV 5개 구조 확인, 필수 컬럼·결측·중복·FK 검증 시 |
| 데이터 스키마 정리 | `docs/data-schema.md` 또는 `output/data_schema_<slug>.md` | 실제 CSV와 대시보드 입력 스키마 정리 시 |
| 데이터 처리 스크립트 | `scripts/*.py` | CSV 로딩, 검증, 전처리, robocopy/서버 반영 보조가 필요할 때 |
| Streamlit 앱 | `dashboard/*` | 대시보드 화면 구현 요청 시 |
| 실행 환경 | `requirements.txt`, `Dockerfile`, `docker-compose.yml`, `.streamlit/config.toml` | 로컬/서버 실행 환경 구성 시 |
| 권한/override 구현 | `dashboard/services/*`, `data/overrides/*` 후보 | 계정별 로그인, admin 수정 저장 기능 구현 시 |
| 서버 반영 문서 | `docs/deploy-flow.md` 또는 `output/analysis_summary_<slug>.md` | robocopy 및 `incoming/` → `data/` → `backup/` 운영 흐름 정리 시 |
| 인수인계 | `output/handoff_<slug>.md` | 작업 종료 후 다음 actor가 이어받아야 할 때 |

---

## 5. 대시보드 구현 범위

| 화면 | Codex 구현 관점 | 주요 연결 |
|---|---|---|
| Home | 최근 고객 니즈, 자주 등장한 니즈, 낮은 confidence, 마케팅 후보 요약 | 5개 CSV 전체 요약 |
| 고객 니즈 검색 | 고객사·부서·산업군·키워드 필터와 검색 결과 테이블 구현 | master + needs |
| 니즈-기능-가치 매칭 | 니즈별 추천 기능, 제안 메시지, 기대 가치, 근거, confidence 표시 | needs + matching + features + master |
| 고객사별 대응 전략 | 고객사별 추천 접근 방향, 다음 미팅 추천 질문, 담당자 메모 표시 | master + strategy + needs |
| 소프트웨어별 가치 맵 | 소프트웨어 기능별 연결 가능한 고객 니즈와 가치 포인트 표시 | features + matching + needs + master |
| 마케팅 활용 | 반복 니즈, 고객군/산업군별 관심 주제, 가치 메시지 후보, 콘텐츠 후보 표시 | needs + matching + features + master |

화면 구현 원칙:

- 고객 기본 정보는 `customer_master.csv` 기준으로 표시한다.
- 낮은 confidence와 데이터 누락은 숨기지 않는다.
- 현재 CSV에는 `review_status`가 없으므로, 구현 시 없는 컬럼을 가정하지 않는다.
- `dashboard_visible=FALSE` 데이터는 기본 화면에서 숨긴다.
- `sensitivity_level`은 화면 필터/배지 기준으로 사용하지 않는다.
- 마케팅 활용 후보는 확정 캠페인이나 확정 고객 수요처럼 표현하지 않는다.

---

## 6. CSV 검증 기준

Codex는 CSV 로딩 또는 대시보드 구현 전 다음을 검증한다.

```text
[ ] export CSV 5개가 모두 존재하는가
[ ] 각 CSV의 필수 컬럼이 v5 기준과 일치하는가
[ ] customer_master.customer_id가 고유한가
[ ] customer_needs.customer_id가 모두 customer_master.customer_id에 존재하는가
[ ] customer_strategy.customer_id가 모두 customer_master.customer_id에 존재하는가
[ ] need_value_matching.need_id가 모두 customer_needs.need_id에 존재하는가
[ ] need_value_matching.feature_id가 모두 software_features.feature_id에 존재하는가
[ ] dashboard_visible 값이 처리 가능한가
[ ] confidence 값이 표시 가능한가
[ ] 실제 고객 데이터 CSV와 override CSV가 Git에 포함되지 않았는가
```

---

## 7. 권한과 수정 기능

| 권한 | 대상 | 가능 작업 |
|---|---|---|
| `admin` | admin 계정 | 조회, 데이터 수정, 매칭 신뢰도/메모 등 운영 정보 관리, 운영 반영 |
| `viewer` | admin 이외 계정 | 조회 전용 |

규칙:

- 모든 사용자는 계정별로 로그인한다.
- 인증 정보는 코드에 하드코딩하지 않고, 환경변수 또는 Streamlit secrets 등 외부 설정으로 관리한다.
- 대시보드는 사내망 내부 전용이며 외부 인터넷 접속은 범위에서 제외한다.
- admin 수정값은 원본 CSV가 아니라 별도 override CSV에 저장한다.
- viewer는 데이터 수정, 상태 변경, override 저장을 할 수 없다.

---

## 8. 금지

Codex는 다음을 하지 않는다.

- `raw/`, `wiki/`, `export/` 원본 영역 임의 수정
- 실제 고객 데이터, override CSV, 인증 정보를 Git에 커밋
- `customer_master.csv`를 무시하고 `customer_name` 문자열만으로 고객 관계를 연결
- `need_value_matching.csv` 또는 `software_features.csv`에 `customer_id` 중복 추가
- 현재 CSV에 없는 `review_status`를 있다고 가정
- `sensitivity_level`을 화면 필터/배지 기준으로 사용
- 방문보고서나 wiki에 없는 고객 의도, 예산, 구매 가능성 추정
- confidence 낮은 매칭을 확정 추천처럼 표시
- 마케팅 활용 후보를 확정 캠페인 또는 확정 고객 수요처럼 표현
- 서버 운영 파일을 사용자 승인 없이 변경
- Work Board 락 등록 생략 후 산출물 작성
- 덮어쓰기, force push, 무단 reset

---

## 9. 작업 종료 체크리스트

```text
[ ] 수정 파일 목록 확인
[ ] raw/, wiki/, export/ 원본 영역을 수정하지 않았는가
[ ] 실제 고객 데이터, override CSV, 인증 정보가 Git에 포함되지 않았는가
[ ] CSV 5개 구조와 관계 검증 결과를 남겼는가
[ ] customer_master.csv를 고객 정보 SSOT로 사용했는가
[ ] customer_id 관계 검증 결과를 남겼는가
[ ] review_status를 사용하지 않았는가
[ ] sensitivity_level을 화면 필터/배지 기준으로 사용하지 않았는가
[ ] dashboard_visible/confidence 표시 기준을 지켰는가
[ ] 마케팅 활용 후보를 확정 캠페인처럼 표현하지 않았는가
[ ] Work Board 락을 해제했는가
[ ] Handoff Log를 남겼는가
[ ] commit 했는가
```
