# docs/collab-protocol.md — 협업 프로토콜

`knowledge_for_marketing_dashboard` 프로젝트의 협업 규칙과 운영 정책이다.

`PROJECT.md`는 현황판이고, 이 문서는 규칙판이다. 새 actor가 진입할 때 1회 학습하고, 이후에는 Work Board 갱신·파일 락·데이터 보호·서버 반영 기준이 필요할 때 참조한다.

이 프로젝트의 목적은 고객 방문보고서에서 파악한 고객 니즈와 소프트웨어 기능 업데이트 자료를 연결해, **마케팅 활용**과 **고객 미팅 준비**에 필요한 고객 니즈 / 추천 기능 / 제안 메시지 / 다음 확인 질문을 빠르게 확인할 수 있는 사내 Streamlit 웹 대시보드를 구축하는 것이다.

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

---

## 1. 단일 진실 원천과 문서 역할

| 문서 | 역할 | 갱신 원칙 |
|---|---|---|
| `PROJECT.md` | 현재 작업 상태, 결정사항, Handoff Log를 관리하는 현황판 | 작업 시작·종료·결정 발생 시 갱신 |
| `docs/collab-protocol.md` | actor 간 협업 방식, 파일 락, archive, 서버 반영 규칙 | 운영 규칙이 바뀔 때만 갱신 |
| `AGENTS.md` | Codex 실행 지침 | 구현·검증·데이터 처리 규칙 변경 시 갱신 |
| `CLAUDE.md` | Claude Code 계획·해석·문서화 지침 | 질문 설계·리스크 검토 방식 변경 시 갱신 |
| `business-context.md` | 사업 맥락과 데이터 해석 기준 | 고객 니즈·소프트웨어 기능 해석 기준 변경 시 갱신 |
| `DESIGN.md` | 대시보드 화면·문구·시각 기준 | UI/UX 방향 변경 시 갱신 |
| `docs/templates.md` | 협업 산출물 표준 양식 | 산출물 형식 변경 시 갱신 |

원칙:

- `PROJECT.md`가 현재 상태의 단일 진실 원천이다.
- `docs/collab-protocol.md`는 규칙의 단일 진실 원천이다.
- 사용자 요청이 모든 기본 규칙보다 우선한다.
- 사용자가 명시하지 않은 내용은 추론하지 않는다. 필요한 정보가 없으면 질문하거나, “확정 필요” 상태로 남긴다.

---

## 2. Actor 역할

| Actor | 기본 담당 | 주로 수정하는 파일 |
|---|---|---|
| User | 최종 판단, 우선순위 결정, 실데이터 제공, admin 계정 기준 결정, actor 간 분쟁 결정 | 없음 |
| Claude Code | 1차 계획, 화면별 핵심 질문 설계, 데이터 해석 기준 수립, 마케팅 과잉 표현 점검, 리스크 점검, 최종 문서화 | `output/dev_plan_*.md`, `output/questions_*.md`, `output/risk_notes_*.md`, `output/final_report_*.md` |
| Codex | Streamlit 대시보드 구현, CSV 로딩/검증, 계정별 로그인 구현, admin override CSV 저장, Docker 실행 환경, robocopy 서버 반영 보조 | `dashboard/*`, `scripts/*`, `output/execution_plan_*.md`, `output/analysis_summary_*.md`, `output/handoff_*.md`, `requirements.txt`, `Dockerfile`, `docker-compose.yml` |

역할 분리 원칙:

- Claude Code는 계획·해석·리스크·문서화 담당이다.
- Codex는 구현·검증·실행 담당이다.
- Claude Code는 사용자 명시 요청 없이는 코드, 데이터, 대시보드 파일을 직접 덮어쓰지 않는다.
- Codex는 사용자 명시 요청 없이는 Claude Code의 최종 문서, 리스크 노트, 질문 설계 문서를 덮어쓰지 않는다.
- actor 간 이견은 User가 결정한다.

---

## 3. 프로젝트 범위와 데이터 경계

### 3.1 로컬 개발·관리 영역

사용자 컴퓨터의 기본 구조는 다음을 기준으로 한다.

```text
knowledge_for_marketing/
├── raw/          원본 자료 (읽기 전용)
├── wiki/         정리 지식 페이지
├── export/       대시보드용 CSV 4개
├── dashboard/    대시보드 개발 파일
├── scripts/      export 재생성 또는 보조 스크립트
└── logs/         작업 로그
```

규칙:

- 원본 파일과 대시보드 개발은 사용자 컴퓨터에서 관리한다.
- `raw/`, `wiki/`, `export/`는 대시보드 개발 중 직접 수정하지 않는다.
- `dashboard/`는 Streamlit 대시보드 개발의 기본 작업 범위다.
- `scripts/`는 export 재생성, CSV 검증, robocopy 전송 보조 스크립트 영역이다.
- `logs/`는 작업 이력 기록용이며, 고객 정보가 포함될 수 있으므로 외부 공유하지 않는다.

### 3.2 서버 운영 영역

서버 PC의 기본 구조는 다음을 기준으로 한다.

```text
marketing_llmwiki/
├── dashboard/    Docker 대시보드 실행 파일
├── incoming/     robocopy로 전달받는 임시 데이터
├── data/         Docker 대시보드가 실제로 읽는 데이터
├── backup/       기존 data 백업
└── logs/         반영 작업 로그
```

규칙:

- 서버 PC는 사내망 내부 운영 영역이다.
- 외부 인터넷 접속은 프로젝트 범위에서 제외한다.
- robocopy를 이용해 서버 PC에 웹 기반 대시보드 운영에 필요한 파일만 복사한다.
- 원본 `raw/`, 전체 `wiki/`, 불필요한 개발 로그는 서버 전송 대상에서 제외한다.
- 서버 반영 기본 흐름은 `incoming/` 수신 → 검증 → 기존 `data/` 백업 → `data/` 교체 → 데이터 새로고침 또는 Docker 재시작 → 로그 기록이다.

### 3.3 Git 커밋 금지 기준

다음은 기본적으로 Git에 커밋하지 않는다.

- 실제 고객 방문보고서 원문
- 실제 고객사별 내부 메모
- 실데이터 CSV 원본
- override CSV 중 고객 정보가 포함된 파일
- 계정 인증 정보, Streamlit secrets, 환경변수 파일
- 담당자 개인 정보 또는 개인 연락처
- 서버 운영 중 생성된 로그 중 고객 정보가 포함된 파일
- 임시 데이터, 백업 데이터, 로컬 실행 캐시

Git에 포함해야 할 가능성이 있으면 반드시 사용자에게 확인한다.

---

## 4. 기본 협업 흐름

| 단계 | Owner | 내용 | 산출물 |
|---|---|---|---|
| 1. 계획 | Claude Code | 목적, 범위, 화면별 질문, 데이터 관계, 검증 기준 정리 | `output/dev_plan_<slug>.md` |
| 2. 실행 계획 보완 | Codex | 구현 순서, 수정 파일, 실행 명령, 검증 방법 정리 | `output/execution_plan_<slug>.md` |
| 3. 구현 | Codex | Streamlit 앱, CSV 로딩, 검색·필터, 계정별 로그인, override CSV, Docker/robocopy 보조 구현 | `dashboard/*`, `scripts/*`, 설정 파일 |
| 4. 검증 | Codex | CSV 스키마, 화면 렌더링, 로컬 실행, Docker 실행 여부 확인 | `output/analysis_summary_<slug>.md`, `output/handoff_<slug>.md` |
| 5. 리스크 점검 | Claude Code | 데이터 해석, 마케팅 과잉 표현, 내부 정보 노출, 권한, 근거 부족 표현 점검 | `output/risk_notes_<slug>.md` |
| 6. 최종 문서화 | Claude Code | 사용자용 구현 결과, 운영 방법, 다음 작업 정리 | `output/final_report_<slug>.md` |

---

## 5. PROJECT.md 갱신 방식

`PROJECT.md`는 Work Board, Decisions, Handoff Log 중심으로 운영한다.

### 5.1 Work Board

활성 작업만 표시한다.

| Status | 의미 | 파일 락 | 사용 기준 |
|---|---|---|---|
| `todo` | 작업 정의됨, 아직 시작 전 | 없음 | 곧 수행할 작업 |
| `in_progress` | actor가 능동적으로 작업 중 | 있음 | 같은 파일 동시 수정 방지 |
| `blocked` | 외부 input 없이는 진행 불가 | 없음 | 사용자 결정, 데이터 도착, 다른 actor 산출물 대기 |
| `done` | 완료 | 없음 | 표에서 제거하고 Handoff Log로 이동 |

Work Board에 반드시 등록하는 작업:

- `dashboard/*` 생성·수정
- `scripts/*` 생성·수정
- `output/*` 산출물 생성·수정
- `data/*` 샘플 데이터 생성·수정
- Docker 관련 파일 생성·수정
- `requirements.txt` 등 실행 환경 파일 생성·수정
- 서버 반영 스크립트 또는 운영 절차 파일 생성·수정

### 5.2 파일 락

- Work Board의 `in_progress` 상태가 파일 락이다.
- `Files locked` 컬럼에 실제 수정할 파일 또는 폴더를 명시한다.
- 다른 actor가 같은 파일을 `in_progress`로 잡고 있으면 시작하지 않는다.
- 작업이 끝나면 Work Board 행을 제거한다.
- 작업 중 외부 input이 필요해지면 `blocked`로 바꾸고 `Files locked`를 비운다.

### 5.3 Handoff Log

작업 종료 시 1줄로 기록한다.

```md
[YYYY-MM-DD HH:mm] actor / changed: ... / verified: ... / next: ...
```

최근 10개만 유지한다.

---

## 6. 파일 네이밍 규칙

### 6.1 slug 규칙

- 형식: `prefix_<slug>.md`
- slug는 kebab-case를 사용한다.
- 영문 소문자, 숫자, 하이픈만 사용한다.
- 한 작업은 하나의 slug를 공유한다.

예시:

```text
dashboard-foundation
streamlit-app-foundation
server-deploy-flow
permission-review
export-schema-update
```

### 6.2 협업 문서

| 문서 | Owner | 용도 |
|---|---|---|
| `output/dev_plan_<slug>.md` | Claude Code | Codex가 구현할 1차 계획 |
| `output/execution_plan_<slug>.md` | Codex | 구현 가능한 실행 계획 |
| `output/handoff_<slug>.md` | 현재 작업자 | 다음 actor가 이어받기 위한 인수인계 |
| `output/questions_<slug>.md` | Claude Code | 화면별 핵심 질문, 검색·필터, 의사결정 프레임 |
| `output/risk_notes_<slug>.md` | Claude Code | 데이터/내부 정보/해석/권한 리스크 점검 |
| `output/analysis_summary_<slug>.md` | Codex | CSV 구조, 데이터 검증, 구현 결과 요약 |
| `output/final_report_<slug>.md` | Claude Code | 사용자용 최종 구현·운영 보고서 |

### 6.3 앱·운영 파일

| 파일/폴더 | Owner | 용도 |
|---|---|---|
| `dashboard/` | Codex | Streamlit 대시보드 개발 기본 폴더 |
| `dashboard/app.py` 또는 `dashboard/streamlit_app.py` | Codex | Streamlit 앱 진입점 |
| `dashboard/pages/*` | Codex | 6개 화면별 페이지 |
| `dashboard/components/*` | Codex | 공통 UI 컴포넌트 |
| `dashboard/services/*` | Codex | CSV 로딩, 권한, 상태 관리 서비스 로직 |
| `data/overrides/*` | Codex | admin 수정값 저장 후보. 실데이터는 Git 커밋 금지 |
| `scripts/validate_csv.py` | Codex | CSV 스키마 검증 |
| `scripts/sync_data.py` | Codex | robocopy 또는 서버 반영 보조 로직 |
| `requirements.txt` | Codex | Python 의존성 |
| `Dockerfile` | Codex | 운영 컨테이너 이미지 |
| `docker-compose.yml` | Codex | 서버 PC 운영용 Compose 설정 |

기존 문서에 `app/`가 남아 있을 경우, 새 작업에서는 `dashboard/` 기준으로 해석한다.

---

## 7. CSV / 데이터 기준

현재 실제 export CSV 4개:

```text
customer_needs.csv
software_features.csv
need_value_matching.csv
customer_strategy.csv
```

현재 실제 CSV에는 `customer_id`가 없다. export 생성 규칙 개선 전에는 해당 컬럼이 있다고 가정하지 않는다.

목표 export 생성 규칙:

- 고객 식별용 `customer_id` 컬럼을 생성한다.
- `dashboard_visible`은 `TRUE/FALSE` 체계를 유지한다.
- `confidence`는 현재 존재하는 매칭 신뢰도 표시 기준으로 사용한다.
- admin 수정값은 원본 CSV가 아니라 override CSV에 저장한다.

제외 기준:

- `review_status`는 1차 범위에서 제외한다.
- `sensitivity_level`은 내부 직원용 대시보드에서 표시/필터/배지 기준으로 사용하지 않는다.
- `sensitivity_level` 값 변환은 진행하지 않는다.

규칙:

- CSV 헤더는 실제 파일을 기준으로 사용한다.
- 컬럼명을 추론해 임의 보정하지 않는다.
- 누락 컬럼은 코드에서 조용히 추정하지 않고 검증 결과에 기록한다.
- 데이터에 없는 고객 의도, 예산, 구매 가능성, 내부 의사결정 상황은 추정하지 않는다.
- `dashboard_visible=FALSE` 데이터는 기본 화면에서 숨긴다.
- 실제 고객 데이터 CSV와 override CSV는 기본적으로 Git에 커밋하지 않는다.

---

## 8. 대시보드 화면 범위

1차 대시보드는 다음 6개 화면을 기본 범위로 한다.

| 화면 | 목적 |
|---|---|
| Home | 최근 고객 니즈, 자주 등장한 니즈, 낮은 confidence, 마케팅 후보 요약 확인 |
| 고객 니즈 검색 | 고객사·산업군·키워드로 니즈 검색 |
| 니즈-기능-가치 매칭 | 고객 니즈에 연결된 추천 기능, 제안 메시지, 기대 가치 확인 |
| 고객사별 대응 전략 | 추천 접근 방향, 다음 미팅 추천 질문 확인 |
| 소프트웨어별 가치 맵 | 소프트웨어 기능 목록과 연결 가능한 고객 니즈 확인 |
| 마케팅 활용 | 반복 니즈, 산업군/고객군별 관심 주제, 가치 메시지, 세미나/제안서/콘텐츠 후보 확인 |

UI 문구 원칙:

- 마케팅 활용과 고객 미팅 준비에 모두 도움이 되어야 한다.
- 고객 니즈 → 추천 기능 → 제안 가치 → 다음 확인 질문 흐름이 보여야 한다.
- 단순 데이터 나열보다 “어떤 준비 또는 활용을 해야 하는가”가 드러나야 한다.
- 낮은 confidence를 숨기지 않는다.
- 마케팅 활용 후보를 확정 캠페인 또는 확정 고객 수요처럼 표현하지 않는다.
- 근거 없는 단정형 문구를 쓰지 않는다.
- 한국어 UI 문구를 기본으로 한다.

---

## 9. 권한과 수정 범위

현재 권한은 계정별 로그인 기반 2단계로 본다.

| 권한 | 대상 | 가능 작업 |
|---|---|---|
| `admin` | admin 계정 | 조회, 데이터 수정, 매칭 신뢰도/메모 등 운영 정보 관리, 운영 반영 |
| `viewer` | admin 이외 계정 | 대시보드 조회 전용 |

규칙:

- 사용자는 계정별로 로그인한다.
- 접속은 계정과 비밀번호 입력 후 가능하게 한다.
- 인증 정보는 코드에 하드코딩하지 않고, 환경변수 또는 Streamlit secrets 등 외부 설정으로 관리한다.
- 대시보드는 사내망 내부 전용이며 외부 인터넷 접속은 범위에서 제외한다.
- admin 수정값은 원본 CSV가 아니라 별도 override CSV에 저장한다.
- override CSV에는 최소한 수정자, 수정 시각, 대상 ID, 변경 전/후 값이 남아야 한다.
- viewer는 데이터 수정, 상태 변경, override 저장을 할 수 없다.

---

## 10. 서버 반영 흐름

서버 반영 기본 흐름:

```text
1. 사용자 컴퓨터에서 export CSV 4개와 대시보드 운영 파일 준비
2. robocopy로 서버 PC의 incoming/ 또는 운영 반영용 폴더에 필요한 파일만 전송
3. incoming/ 파일명·개수·헤더 검증
4. 기존 data/를 backup/으로 복사
5. incoming/ 검증본을 data/로 반영
6. 대시보드 데이터 새로고침 또는 Docker 컨테이너 재시작
7. logs/에 반영 결과 기록
```

규칙:

- 서버 전송 방식은 robocopy를 사용한다.
- 서버에는 웹 기반 대시보드 운영에 필요한 파일만 복사한다.
- 검증 실패 시 기존 `data/`를 교체하지 않는다.
- 서버 반영 스크립트는 실제 서버 파일을 변경하기 전에 dry-run 또는 검증 모드를 제공해야 한다.
- CSV만 바뀐 경우 데이터 새로고침 기능을 우선 사용한다.
- 코드 변경 또는 장애 대응 시 Docker 컨테이너 재시작도 사용한다.
- 서버 운영 로그에는 고객 정보 원문을 과도하게 남기지 않는다.

---

## 11. 협업 환경 정책

협업 모델은 **옵션 B: `PROJECT.md` 중앙 락, main SSOT**를 사용한다.

작업 환경:

| Actor | 기본 환경 | 격리 환경 |
|---|---|---|
| Codex | main 디렉토리에서 직접 작업 | 큰 작업 또는 사용자 명시 지시 시 채팅 시작 전 `Start in → New worktree` |
| Claude Code | 백그라운드 세션은 워크트리 자동 | 라이브 세션은 main 직접 가능 |

실제 Streamlit 구현처럼 변경 파일이 많은 작업은 작업 규모별로 판단하되, worktree 사용을 권장한다.

---

## 12. 데이터 해석 제한

사용 가능 정보:

- 방문보고서에 기록된 고객사명, 산업군, 관심 주제, 고객 니즈, 기술 이슈
- 위키에 정리된 고객별 니즈, 소프트웨어 기능, 니즈-기능 매칭, 대응 전략
- export CSV에 포함된 `confidence`, 담당자 입력값, 노출 여부
- override CSV에 기록된 admin 수정값
- 사용자가 명시적으로 제공한 공개 자료 또는 내부 사용이 허용된 자료

금지:

- 방문보고서나 위키에 없는 고객 의도 추정
- 고객사의 미공개 전략, 예산, 조직 상황 단정
- 낮은 confidence 매칭을 확정처럼 표시
- 마케팅 활용 후보를 확정 캠페인 또는 확정 고객 수요처럼 표현
- 개인정보, 개인 연락처, 민감한 개인 정보 수집 또는 노출
- 외부 공개 자료와 내부 방문보고서를 섞으면서 출처를 흐리게 표현

추천 문장은 다음 구조를 따른다.

```text
고객 니즈 근거 + 연결 기능 + 다음 행동
```

마케팅 활용 문장은 다음 구조를 따른다.

```text
반복 니즈 근거 + 관련 기능 + 가치 메시지 후보 + 활용 전 확인 사항
```

---

## 13. Archive Rule

- `PROJECT.md` Handoff Log는 최근 10개만 유지한다.
- 11번째 항목을 추가하려는 actor가 archive를 수행한다.
- archive 파일명은 `archive/handoff-YYYY-MM-DD.md` 형식을 사용한다.
- 완료된 `dev_plan_*`, `execution_plan_*`, `handoff_*`는 필요 시 `archive/YYYY-MM-DD-<slug>/`로 이동한다.
- `questions_*`, `risk_notes_*`, `analysis_summary_*`, `final_report_*`는 역할별 결과물이므로 기본적으로 `output/`에 유지한다.

---

## 14. 작업 전 체크리스트

작업 시작 전:

```text
[ ] PROJECT.md를 읽고 Work Board 충돌 여부 확인
[ ] 수정할 파일이 다른 actor에게 locked 되어 있지 않은지 확인
[ ] 원본 영역(raw/, wiki/, export/)을 건드리는 작업인지 확인
[ ] 실제 고객 데이터, override CSV, 인증 정보가 Git에 커밋될 가능성이 있는지 확인
[ ] 필요한 사용자 결정이 없으면 바로 진행, 있으면 질문 또는 blocked 처리
[ ] slug 결정
[ ] 산출물 작업이면 Work Board 행 등록
```

작업 종료 전:

```text
[ ] 수정 파일 확인
[ ] Git에 실데이터, override CSV, 인증 정보가 포함되지 않았는지 확인
[ ] 검증 결과 기록
[ ] Work Board 락 해제
[ ] Handoff Log 1줄 추가
[ ] 필요한 다음 작업 명시
[ ] commit
```
