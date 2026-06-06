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
export CSV 4개
        ↓  로컬 대시보드 개발
robocopy로 서버 PC에 필요 파일만 전송
        ↓
팀 공유 웹 대시보드
```

Codex의 핵심 임무:

- `export/`에서 생성된 대시보드용 CSV 4개를 기준으로 Streamlit 대시보드 개발
- Claude Code의 1차 계획을 실행 가능하게 보완
- CSV 로딩, 데이터 검증, 화면 렌더링, 오류 검증 수행
- 6개 화면 구현: Home, 고객 니즈 검색, 니즈-기능-가치 매칭, 고객사별 대응 전략, 소프트웨어별 가치 맵, 마케팅 활용
- 계정별 로그인 구현: admin은 조회·수정 가능, 그 외 계정은 viewer 조회 전용
- admin 수정값을 원본 CSV가 아니라 override CSV에 저장
- Docker 기반 로컬/서버 실행 환경 구성
- robocopy 기반 서버 PC 반영 흐름 보조 스크립트 또는 운영 문서 작성
- 다음 actor가 이어받을 수 있도록 `analysis_summary` 또는 `handoff` 작성

역할 분리:

- Claude Code는 계획·해석·리스크 점검·최종 문서화 담당이다.
- Codex는 구현·검증·실행 담당이다.
- Codex는 사용자 명시 요청 없이는 Claude Code의 `dev_plan`, `questions`, `risk_notes`, `final_report`를 덮어쓰지 않는다.

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

읽은 뒤 확인할 것:

```text
[ ] PROJECT.md Work Board에 같은 파일 락이 없는가
[ ] 사용자 요청이 기존 규칙보다 우선하는 부분이 있는가
[ ] 수정 대상이 dashboard/, scripts/, output/ 중 어디인가
[ ] raw/, wiki/, export/를 직접 수정하는 작업은 아닌가
[ ] 실제 고객 데이터, override CSV, 비밀번호/secrets가 Git에 커밋될 가능성이 있는가
[ ] 필요한 정보가 없으면 추론하지 않고 질문 또는 blocked 처리할 것인가
```

---

## 3. Codex가 만드는 결과물

| 역할 | 결과물 | 트리거 |
|---|---|---|
| 실행 계획 보완 | `output/execution_plan_<slug>.md` | Claude Code의 `dev_plan_<slug>.md`를 받은 후 |
| CSV 구조 검증 | `output/analysis_summary_<slug>.md` | `export/` CSV 4개 구조 확인, 필수 컬럼·결측·중복 검증 시 |
| 데이터 스키마 정리 | `docs/data-schema.md` 또는 `output/data_schema_<slug>.md` | 실제 CSV와 목표 스키마 차이 정리 시 |
| 데이터 처리 스크립트 | `scripts/*.py` | CSV 로딩, 검증, 전처리, robocopy/서버 반영 보조가 필요할 때 |
| Streamlit 앱 | `dashboard/*` | 대시보드 화면 구현 요청 시 |
| 실행 환경 | `requirements.txt`, `Dockerfile`, `docker-compose.yml`, `.streamlit/config.toml` | 로컬/서버 실행 환경 구성 시 |
| 권한/override 구현 | `dashboard/services/*`, `data/overrides/*` 후보 | 계정별 로그인, admin 수정 저장 기능 구현 시 |
| 서버 반영 문서 | `docs/deploy-flow.md` 또는 `output/analysis_summary_<slug>.md` | robocopy 및 `incoming/` → `data/` → `backup/` 운영 흐름 정리 시 |
| 인수인계 | `output/handoff_<slug>.md` | 작업 종료 후 다음 actor가 이어받아야 할 때 |

중요:

- `PROJECT.md` 기준으로 대시보드 개발 기본 범위는 `dashboard/`이다.
- 기존 문서에 `app/` 표현이 남아 있어도 새 작업은 `dashboard/` 기준으로 진행한다.
- `raw/`, `wiki/`, `export/`는 원본 영역이므로 사용자 지시 없이는 직접 수정하지 않는다.
- 실제 고객 데이터, override CSV, 비밀번호/secrets는 Git에 커밋하지 않는다.

---

## 4. 실제 CSV 기준

현재 실제 export CSV는 다음 4개다.

```text
customer_needs.csv
software_features.csv
need_value_matching.csv
customer_strategy.csv
```

현재 실제 컬럼:

| 파일 | 컬럼 |
|---|---|
| `customer_needs.csv` | `need_id`, `customer_name`, `industry`, `need_date`, `need_keyword`, `need_text`, `need_analysis`, `need_final`, `author`, `assignee`, `progress_note`, `sensitivity_level`, `dashboard_visible`, `customer_grade`, `visit_count` |
| `software_features.csv` | `feature_id`, `software`, `feature_name`, `feature_summary`, `value_keywords`, `related_need_keywords`, `source_wiki`, `last_updated`, `dashboard_visible` |
| `need_value_matching.csv` | `match_id`, `need_id`, `feature_id`, `value_message`, `need_date`, `dashboard_visible`, `confidence` |
| `customer_strategy.csv` | `strategy_id`, `customer_name`, `main_needs`, `recommended_software`, `approach_details`, `suggested_questions`, `proposal_message`, `author`, `assignee`, `sensitivity_level`, `dashboard_visible`, `customer_grade`, `visit_count` |

현재 CSV 기준 주의:

- 현재 CSV에는 `customer_id`가 없다.
- 현재 CSV에는 `review_status`가 없다.
- 현재 `sensitivity_level` 값은 `민감/내부` 체계다.
- 현재 `dashboard_visible` 값은 `TRUE/FALSE` 체계다.

목표 export 생성 규칙:

- `customer_id` 컬럼을 생성한다.
- `sensitivity_level`은 `일반/주의/비공개` 3단계로 생성한다.
- legacy 값 매핑 기본 제안: `내부 → 일반`, `민감 → 주의`, `비공개`는 명시된 경우에만 부여.
- `dashboard_visible`은 `TRUE/FALSE`로 유지한다.
- `review_status`는 현재 CSV에 없으므로 override CSV 또는 향후 export 생성 규칙으로 관리한다.

---

## 5. Claude Code에게 남기는 일

아래 문서는 Claude Code 담당이다.

| 문서 | 목적 |
|---|---|
| `output/dev_plan_<slug>.md` | 복잡한 작업 전 1차 계획 |
| `output/questions_<slug>.md` | 대시보드 핵심 질문, 화면별 판단 기준 |
| `output/risk_notes_<slug>.md` | 데이터 해석, 마케팅 과잉 표현, 보안, 권한 리스크 점검 |
| `output/final_report_<slug>.md` | 의사결정/공유용 최종 정리 |

Codex는 위 문서를 사용자 명시 요청 없이 만들거나 덮어쓰지 않는다.

---

## 6. 개발 작업 시작 순서

기본 흐름:

1. `PROJECT.md` Work Board 확인
2. 관련 `dev_plan_<slug>.md` 또는 사용자 요청 확인
3. 수정 파일과 건드리지 말 파일 분리
4. 산출물 작업이면 Work Board에 `in_progress` 행 추가
5. `Files locked`에 수정 대상 명시
6. 구현 또는 검증 수행
7. 실행 테스트, CSV 검증, 오류 검색
8. 결과 문서 작성
9. Work Board 행 제거
10. Handoff Log 1줄 추가
11. commit

단, `dashboard/*`, `scripts/*`, `output/*`, `data/*`, Docker/requirements 파일을 수정하면 짧은 작업이어도 Work Board 락을 잡는다.

---

## 7. 대시보드 구현 범위

1차 대시보드는 6개 화면을 기본 범위로 한다.

| 화면 | Codex 구현 관점 |
|---|---|
| Home | 최근 고객 니즈, 자주 등장한 니즈, 낮은 confidence, 마케팅 후보 요약을 카드/표로 표시 |
| 고객 니즈 검색 | 고객사·산업군·키워드 필터와 검색 결과 테이블 구현 |
| 니즈-기능-가치 매칭 | 니즈별 추천 기능, 제안 메시지, 기대 가치, 근거, confidence 표시 |
| 고객사별 대응 전략 | 고객사별 추천 접근 방향, 다음 미팅 추천 질문, 담당자 메모 표시 |
| 소프트웨어별 가치 맵 | 소프트웨어 기능별 연결 가능한 고객 니즈와 가치 포인트 표시 |
| 마케팅 활용 | 반복 니즈, 고객군/산업군별 관심 주제, 가치 메시지 후보, 세미나/제안서/기술 콘텐츠 후보 표시 |

화면 구현 원칙:

- 마케팅 활용과 고객 미팅 준비에 모두 도움이 되어야 한다.
- `검토 필요`, `신뢰도 낮음`, `데이터 누락`은 숨기지 않는다.
- 현재 CSV에는 `review_status`가 없으므로, 구현 시 없는 컬럼을 가정하지 않는다.
- `dashboard_visible=FALSE` 데이터는 기본 화면에서 숨긴다.
- `sensitivity_level=비공개` 데이터는 숨김 기준이 아니라 경고 배지로 표시한다.
- 현재 legacy 값 `민감/내부`도 읽을 수 있어야 하며, 목표 값은 `일반/주의/비공개`다.
- 마케팅 활용 후보는 확정 캠페인이나 확정 고객 수요처럼 표현하지 않는다.
- 색상, 레이아웃, 문구 톤은 `DESIGN.md`를 따른다.

---

## 8. 권한과 수정 기능

현재 권한 기준:

| 권한 | 대상 | 가능 작업 |
|---|---|---|
| `admin` | admin 계정 | 조회, 데이터 수정, 검토 상태 관리, 매칭 신뢰도 관리, 운영 반영 |
| `viewer` | admin 이외 계정 | 조회 전용 |

Codex 구현 기준:

- 모든 사용자는 계정별로 로그인한다.
- 접속은 비밀번호 입력 후 가능하게 한다.
- 비밀번호는 코드에 하드코딩하지 않고, 환경변수 또는 Streamlit secrets 등 외부 설정으로 관리한다.
- 1차 구현에서 인증은 사내망 내부 운영 보조 수준으로 본다.
- 외부 공개 수준의 강한 인증/권한 관리는 별도 보안 검토 전까지 범위에서 제외한다.
- admin 수정값은 원본 CSV가 아니라 별도 override CSV에 저장한다.
- override CSV에는 최소한 수정자, 수정 시각, 대상 ID, 변경 전/후 값이 남아야 한다.
- viewer는 데이터 수정, 상태 변경, override 저장을 할 수 없다.

---

## 9. 서버 반영 작업 기준

서버 PC 운영 구조:

```text
marketing_llmwiki/
├── dashboard/
├── incoming/
├── data/
├── backup/
└── logs/
```

서버 반영 기본 흐름:

```text
1. 사용자 컴퓨터에서 export CSV 4개와 대시보드 운영 파일 준비
2. robocopy로 서버 PC에 필요한 파일만 전송
3. incoming/ 파일명·개수·헤더 검증
4. 기존 data/를 backup/으로 복사
5. incoming/ 검증본을 data/로 반영
6. 대시보드 데이터 새로고침 또는 Docker 컨테이너 재시작
7. logs/에 반영 결과 기록
```

Codex 기준:

- 전송 방식은 robocopy로 한다.
- 서버에는 웹 기반 대시보드 운영에 필요한 파일만 복사한다.
- 원본 `raw/`, 전체 `wiki/`, 불필요한 개발 로그는 서버 전송 대상에서 제외한다.
- 서버 반영 스크립트에는 가능하면 dry-run 또는 검증 모드를 둔다.
- 검증 실패 시 기존 `data/`를 교체하지 않는다.
- CSV만 바뀐 경우 데이터 새로고침 기능을 우선 사용한다.
- 코드 변경 또는 장애 대응 시 Docker 컨테이너 재시작도 사용한다.
- 운영 로그에는 고객 정보 원문을 과도하게 남기지 않는다.

---

## 10. 작업 환경과 워크트리

상세 기준은 `docs/collab-protocol.md`를 따른다.

핵심:

- Codex 기본은 main 직접 작업이다.
- 작업 규모에 따라 판단하되, 실제 Streamlit 구현처럼 변경 파일이 많은 작업은 worktree 사용을 권장한다.
- 큰 작업 또는 사용자 명시 지시가 있으면 채팅 시작 전 `Start in → New worktree`를 선택한다.
- 작업 중간 `Handoff to worktree`는 사용하지 않는다.
- 중간 전환이 필요하면 먼저 `git commit` 또는 `git stash -u`로 보호한다.
- 산출물 작업은 Work Board에 락을 먼저 visible하게 만든 뒤 시작한다.

원본 영역:

```text
raw/
wiki/
export/
```

원본 영역은 사용자 지시 없이 수정하지 않는다.

---

## 11. 금지

Codex는 다음을 하지 않는다.

- Claude Code가 작성 중인 `final_report_<slug>.md`, `risk_notes_<slug>.md`, `questions_<slug>.md` 임의 덮어쓰기
- `raw/`, `wiki/`, `export/` 원본 영역 임의 수정
- 실제 고객 데이터, override CSV, 비밀번호/secrets를 Git에 커밋
- 방문보고서나 wiki에 없는 고객 의도, 예산, 구매 가능성 추정
- 고객사의 미공개 전략, 예산, 조직 상황 단정
- 검토 필요 또는 confidence 낮은 매칭을 확정 추천처럼 표시
- 마케팅 활용 후보를 확정 캠페인 또는 확정 고객 수요처럼 표현
- 개인정보, 개인 연락처, 민감한 개인 정보 수집 또는 노출
- 사용자가 제공하지 않은 CSV 스키마를 확정된 것처럼 단정
- 외부 고객사/소프트웨어 로고 또는 시각 정체성 무단 복제
- 사용자 수정 중이거나 Work Board에서 locked 된 파일 덮어쓰기
- 서버 운영 파일을 사용자 승인 없이 변경
- Work Board 락 등록 생략 후 산출물 작성
- 작업 중간 `Handoff to worktree` 사용
- 덮어쓰기, force push, 무단 reset

---

## 12. 작업 종료 체크리스트

작업 종료 전 반드시 확인한다.

```text
[ ] 수정 파일 목록 확인
[ ] raw/, wiki/, export/ 원본 영역을 수정하지 않았는가
[ ] 실제 고객 데이터, override CSV, secrets가 Git에 포함되지 않았는가
[ ] CSV 파일명·컬럼명을 임의 보정하지 않았는가
[ ] 현재 CSV에 없는 customer_id/review_status를 있다고 가정하지 않았는가
[ ] 실행 또는 검증 결과를 남겼는가
[ ] dashboard_visible/sensitivity_level/confidence 표시 기준을 지켰는가
[ ] 마케팅 활용 후보를 확정 캠페인처럼 표현하지 않았는가
[ ] Work Board 락을 해제했는가
[ ] Handoff Log를 남겼는가
[ ] 다음 작업 또는 확인 필요 항목을 명시했는가
[ ] commit 했는가
```
