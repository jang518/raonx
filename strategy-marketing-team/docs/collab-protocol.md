# docs/collab-protocol.md — 협업 프로토콜

`knowledge_for_marketing_dashboard` 프로젝트의 협업 규칙과 운영 정책이다.

`PROJECT.md`는 현황판이고, 이 문서는 규칙판이다. 새 actor가 진입할 때 1회 학습하고, 이후에는 Work Board 갱신·파일 락·데이터 보호·서버 반영 기준이 필요할 때 참조한다.

이 프로젝트의 목적은 고객 방문보고서에서 파악한 고객 니즈와 소프트웨어 기능 업데이트 자료를 연결해, 영업·엔지니어가 고객 미팅 전 고객 니즈 / 추천 기능 / 제안 메시지 / 다음 미팅 질문을 빠르게 확인할 수 있는 사내 Streamlit 웹 대시보드를 구축하는 것이다.

기본 흐름은 다음과 같다.

```text
방문보고서 · 기능 업데이트 자료
        ↓  Claude 분석·정리
wiki/ 지식 위키
        ↓  구조화 데이터 추출
export CSV 4개
        ↓  서버 PC 전송
팀 공유 웹 대시보드
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
- 같은 내용이 여러 문서에 반복될 경우, `PROJECT.md`는 현재 결정만 짧게 기록하고 상세 규칙은 이 문서에 둔다.
- 사용자 요청이 모든 기본 규칙보다 우선한다.
- 사용자가 명시하지 않은 내용은 추론하지 않는다. 필요한 정보가 없으면 질문하거나, “확정 필요” 상태로 남긴다.

---

## 2. Actor 역할

| Actor | 기본 담당 | 주로 수정하는 파일 |
|---|---|---|
| User | 최종 판단, 우선순위 결정, 실데이터 제공, admin 권한 기준 결정, actor 간 분쟁 결정 | 없음 |
| Claude Code | 1차 계획, 화면별 핵심 질문 설계, 데이터 해석 기준 수립, 고객 니즈-기능 매칭 리스크 점검, 최종 문서화 | `output/dev_plan_*.md`, `output/questions_*.md`, `output/risk_notes_*.md`, `output/final_report_*.md` |
| Codex | Streamlit 대시보드 구현, CSV 로딩/검증, Docker 실행 환경, 서버 반영 보조 스크립트, 실행 검증 | `dashboard/*`, `scripts/*`, `output/execution_plan_*.md`, `output/analysis_summary_*.md`, `output/handoff_*.md`, `requirements.txt`, `Dockerfile`, `docker-compose.yml` |

역할 분리 원칙:

- Claude Code는 계획·해석·문서화 담당이다.
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

- `raw/`는 원본 자료 영역이다. 대시보드 개발 중 직접 수정하지 않는다.
- `wiki/`는 지식 위키 원본 영역이다. 대시보드 개발 중 직접 수정하지 않는다.
- `export/`는 위키에서 생성된 대시보드 입력 CSV 영역이다. 대시보드 개발 중 임의로 보정하지 않는다.
- `dashboard/`는 Streamlit 대시보드 개발의 기본 작업 범위다.
- `scripts/`는 export 재생성 또는 운영 보조 스크립트 영역이다. 원본 데이터 수정 스크립트는 사용자 승인 없이 만들지 않는다.
- `logs/`는 작업 이력 기록용이며, 고객 정보가 포함될 수 있으므로 외부 공유하지 않는다.

### 3.2 서버 운영 영역

서버 PC의 기본 구조는 다음을 기준으로 한다.

```text
marketing_llmwiki/
├── dashboard/    Docker 대시보드 실행 파일
├── incoming/     사용자 컴퓨터에서 전달받는 임시 데이터
├── data/         Docker 대시보드가 실제로 읽는 데이터
├── backup/       기존 data 백업
└── logs/         반영 작업 로그
```

규칙:

- 서버 PC는 운영 영역이다.
- `incoming/`은 임시 수신 영역이다.
- `data/`는 대시보드가 실제로 읽는 운영 데이터 영역이다.
- `backup/`은 기존 `data/`를 교체 전 보관하는 영역이다.
- `logs/`는 서버 반영 작업 이력이다.
- 서버 반영 기본 흐름은 `incoming/` 수신 → 검증 → 기존 `data/` 백업 → `data/` 교체 → 로그 기록이다.
- 서버 운영 파일 변경은 사용자 승인 후 진행한다.

### 3.3 Git 커밋 금지 기준

다음은 기본적으로 Git에 커밋하지 않는다.

- 실제 고객 방문보고서 원문
- 실제 고객사별 민감 메모
- 실데이터 CSV 원본
- 담당자 개인 정보 또는 개인 연락처
- 서버 운영 중 생성된 로그 중 고객 정보가 포함된 파일
- 임시 데이터, 백업 데이터, 로컬 실행 캐시

Git에 포함해야 할 가능성이 있으면 반드시 사용자에게 확인한다.

---

## 4. 기본 협업 흐름

복잡한 작업의 기본 흐름은 다음과 같다.

| 단계 | Owner | 내용 | 산출물 |
|---|---|---|---|
| 1. 계획 | Claude Code | 목적, 범위, 화면별 질문, 데이터 관계, 검증 기준 정리 | `output/dev_plan_<slug>.md` |
| 2. 실행 계획 보완 | Codex | 구현 순서, 수정 파일, 실행 명령, 검증 방법 정리 | `output/execution_plan_<slug>.md` |
| 3. 구현 | Codex | Streamlit 앱, CSV 로딩, 검색·필터, Docker/서버 반영 보조 구현 | `dashboard/*`, `scripts/*`, 설정 파일 |
| 4. 검증 | Codex | CSV 스키마, 화면 렌더링, 로컬 실행, Docker 실행 여부 확인 | `output/analysis_summary_<slug>.md`, `output/handoff_<slug>.md` |
| 5. 리스크 점검 | Claude Code | 데이터 해석, 보안, 권한, 근거 부족 표현 점검 | `output/risk_notes_<slug>.md` |
| 6. 최종 문서화 | Claude Code | 사용자용 구현 결과, 운영 방법, 다음 작업 정리 | `output/final_report_<slug>.md` |

예외적으로 생략 가능한 경우:

- 단순 오타 수정
- 짧은 문서 보완
- 파일 변경 없는 정보 확인
- 사용자가 특정 도구와 작업을 명시해 즉시 처리하라고 한 경우
- 한 actor가 시작부터 끝까지 처리 가능한 작은 작업

단, 산출물 파일을 생성·수정하면 짧은 작업이어도 Work Board 락 규칙은 따른다.

---

## 5. PROJECT.md 갱신 방식

`PROJECT.md`는 다음 3개 섹션 중심으로 운영한다.

1. Work Board
2. Decisions
3. Handoff Log

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

Work Board 생략 가능 작업:

- 메타 문서 단순 수정
- 파일 변경 없는 정보 확인
- 사용자에게 설명만 하는 응답

단, 메타 문서를 수정한 경우에도 Handoff Log는 남긴다.

### 5.2 파일 락

- Work Board의 `in_progress` 상태가 파일 락이다.
- `Files locked` 컬럼에 실제 수정할 파일 또는 폴더를 명시한다.
- 다른 actor가 같은 파일을 `in_progress`로 잡고 있으면 시작하지 않는다.
- 작업이 끝나면 Work Board 행을 제거한다.
- 작업 중 외부 input이 필요해지면 `blocked`로 바꾸고 `Files locked`를 비운다.
- 락이 남아 있는데 다른 actor가 반드시 수정해야 하면 사용자에게 알리고 결정을 받는다.

### 5.3 Decisions

Decisions에는 장기 유지되는 결정만 기록한다.

기록 대상:

- 데이터 표시 기준
- 권한 기준
- 서버 운영 방식
- 화면 범위
- Git 커밋 정책
- 민감 정보 처리 원칙
- 협업 모델 변경

기록하지 않는 항목:

- 일시적인 작업 진행 상황
- 단기 할 일
- 특정 파일의 미세 수정 내용

### 5.4 Handoff Log

작업 종료 시 1줄로 기록한다.

형식:

```md
[YYYY-MM-DD HH:mm] actor / changed: ... / verified: ... / next: ...
```

규칙:

- 최근 10개만 유지한다.
- 10개 초과 시 archive 규칙을 따른다.
- `changed:`에는 바뀐 내용을 짧게 쓴다.
- `verified:`에는 확인한 기준을 쓴다.
- `next:`에는 다음 actor 또는 다음 작업을 쓴다.

---

## 6. 파일 네이밍 규칙

### 6.1 slug 규칙

- 형식: `prefix_<slug>.md`
- slug는 kebab-case를 사용한다.
- 영문 소문자, 숫자, 하이픈만 사용한다.
- 한 작업은 하나의 slug를 공유한다.
- 같은 작업의 후속이면 동일 slug를 유지한다.
- 다른 작업이면 새 slug를 만든다.

예시:

```text
dashboard-foundation
streamlit-app-foundation
server-deploy-flow
permission-review
csv-schema-review
```

### 6.2 협업 문서

| 문서 | Owner | 용도 |
|---|---|---|
| `output/dev_plan_<slug>.md` | Claude Code | Codex가 구현할 1차 계획 |
| `output/execution_plan_<slug>.md` | Codex | 구현 가능한 실행 계획 |
| `output/handoff_<slug>.md` | 현재 작업자 | 다음 actor가 이어받기 위한 인수인계 |

### 6.3 역할별 결과물 문서

| 문서 | Owner | 용도 |
|---|---|---|
| `output/questions_<slug>.md` | Claude Code | 화면별 핵심 질문, 검색·필터, 의사결정 프레임 |
| `output/risk_notes_<slug>.md` | Claude Code | 데이터/보안/해석/권한 리스크 점검 |
| `output/analysis_summary_<slug>.md` | Codex | CSV 구조, 데이터 검증, 구현 결과 요약 |
| `output/final_report_<slug>.md` | Claude Code | 사용자용 최종 구현·운영 보고서 |

### 6.4 앱·운영 파일

`PROJECT.md` 기준으로 대시보드 개발 기본 범위는 `dashboard/`이다.

| 파일/폴더 | Owner | 용도 |
|---|---|---|
| `dashboard/` | Codex | Streamlit 대시보드 개발 기본 폴더 |
| `dashboard/app.py` 또는 `dashboard/streamlit_app.py` | Codex | Streamlit 앱 진입점 |
| `dashboard/pages/*` | Codex | 화면별 페이지 |
| `dashboard/components/*` | Codex | 공통 UI 컴포넌트 |
| `dashboard/services/*` | Codex | CSV 로딩, 권한, 상태 관리 서비스 로직 |
| `scripts/validate_csv.py` | Codex | CSV 스키마 검증 |
| `scripts/sync_data.py` | Codex | 서버 반영 보조 로직 |
| `requirements.txt` | Codex | Python 의존성 |
| `Dockerfile` | Codex | 운영 컨테이너 이미지 |
| `docker-compose.yml` | Codex | 서버 PC 운영용 Compose 설정 |

기존 문서에 `app/`가 남아 있을 경우, 새 작업에서는 `dashboard/` 기준으로 해석한다. 실제 저장 위치가 이미 `app/`로 구현되어 있다면, 이동 여부는 사용자 확인 후 결정한다.

---

## 7. CSV / 데이터 기준

대시보드 입력 데이터는 `export/`에서 생성된 CSV 4개를 기준으로 한다.

현재 문서상 운영 입력 CSV 이름은 다음을 기준으로 하되, 실제 파일명이 다르면 실제 `export/` 파일명을 우선한다.

```text
customer_needs.csv
customer_strategy.csv
need_value_matching.csv
software_features.csv
```

추가 manifest가 있는 경우:

```text
dashboard_manifest.json
```

CSV 정보 범주:

| 데이터 | 포함 정보 |
|---|---|
| 고객 니즈 데이터 | 고객사, 산업군, 방문/작성 시점, 니즈 키워드, 상세 니즈, 관련 보고서/위키 출처 |
| 소프트웨어 기능 데이터 | 소프트웨어명, 기능명, 기능 설명, 가치 포인트, 업데이트/정리 시점 |
| 니즈-기능-가치 매칭 데이터 | 고객 니즈, 추천 기능, 제안 메시지, 기대 가치, 매칭 신뢰도, 검토 상태 |
| 고객사별 대응 전략 데이터 | 고객사, 추천 접근 방향, 다음 미팅 추천 질문, 담당자 메모, 검토 상태 |

규칙:

- CSV 헤더는 실제 파일을 기준으로 사용한다.
- 컬럼명을 추론해 임의 보정하지 않는다.
- 누락 컬럼은 코드에서 조용히 추정하지 않고 검증 결과에 기록한다.
- 데이터에 없는 고객 의도, 예산, 구매 가능성, 내부 의사결정 상황은 추정하지 않는다.
- 매칭 근거가 약하면 `검토 필요` 또는 이에 준하는 상태로 표시한다.
- `dashboard_visible=FALSE` 데이터는 기본 화면에서 숨긴다.
- `sensitivity_level=비공개` 데이터는 숨김 기준이 아니라 경고 배지 기준으로 사용한다.
- 실제 고객 데이터 CSV는 기본적으로 Git에 커밋하지 않는다.

---

## 8. 대시보드 화면 범위

1차 대시보드는 다음 5개 화면을 기본 범위로 한다.

| 화면 | 목적 |
|---|---|
| Home | 최근 고객 니즈, 자주 등장한 니즈, 검토 필요 매칭 현황 확인 |
| 고객 니즈 검색 | 고객사·산업군·키워드로 니즈 검색 |
| 니즈-기능-가치 매칭 | 고객 니즈에 연결된 추천 기능, 제안 메시지, 기대 가치 확인 |
| 고객사별 대응 전략 | 추천 접근 방향, 다음 미팅 추천 질문 확인 |
| 소프트웨어별 가치 맵 | 소프트웨어 기능 목록과 연결 가능한 고객 니즈 확인 |

UI 문구 원칙:

- 영업·엔지니어가 고객 미팅 전 빠르게 이해할 수 있어야 한다.
- 고객 니즈 → 추천 기능 → 제안 가치 → 다음 미팅 질문 흐름이 보여야 한다.
- 단순 데이터 나열보다 “어떤 준비를 해야 하는가”가 드러나야 한다.
- 검토 필요 상태를 숨기지 않는다.
- 근거 없는 단정형 문구를 쓰지 않는다.
- 한국어 UI 문구를 기본으로 한다.

---

## 9. 권한과 수정 범위

현재 권한은 다음 2단계로 본다.

| 권한 | 대상 | 가능 작업 |
|---|---|---|
| `admin` | User | 데이터 수정, 검토 상태 관리, 매칭 신뢰도 관리, 운영 반영 |
| `viewer` | 팀원 | 대시보드 조회 |

규칙:

- 1차 개발에는 viewer 코멘트 기능을 포함하지 않는다.
- admin 수정 기능은 저장 방식이 확정된 이후 별도 작업으로 진행한다.
- 담당자 직접 입력, 매칭 신뢰도, 검토 상태 관리 기능은 필요 기능으로 보되 1차 구현 범위와 저장 위치는 사용자 확인 후 확정한다.
- 권한 방식이 확정되지 않았으면 UI/코드에서 임의 인증 방식을 구현하지 않는다.

확정 필요 항목 예시:

- admin 로그인 방식
- viewer 접근 방식
- admin 수정값 저장 위치
- 수정 이력 보관 방식
- 서버 PC에서 계정 정보를 관리할지 여부

---

## 10. 서버 반영 흐름

서버 반영 기본 흐름:

```text
1. 사용자 컴퓨터에서 export CSV 4개 생성
2. 서버 PC의 incoming/으로 전송
3. incoming/ 파일명·개수·헤더 검증
4. 기존 data/를 backup/으로 복사
5. incoming/ 검증본을 data/로 반영
6. Docker 대시보드 재시작 또는 자동 갱신 확인
7. logs/에 반영 결과 기록
```

규칙:

- 전송 방식은 사용자가 확정한 방식에 따른다.
- 현재 문서에서는 `incoming/` → `data/` → `backup/` 흐름만 고정한다.
- robocopy, 공유 폴더, 수동 복사 중 무엇을 사용할지는 사용자 결정 또는 별도 작업 문서에서 확정한다.
- 서버 반영 스크립트는 실제 서버 파일을 변경하기 전에 dry-run 또는 검증 모드를 제공해야 한다.
- 검증 실패 시 기존 `data/`를 교체하지 않는다.
- 서버 운영 로그에는 고객 정보 원문을 과도하게 남기지 않는다.

---

## 11. 협업 환경 정책

협업 모델은 **옵션 B: `PROJECT.md` 중앙 락, main SSOT**를 사용한다.

이유:

- actor가 User, Claude Code, Codex 중심으로 제한되어 있다.
- 사용자 한 명이 현황을 빠르게 파악해야 한다.
- 고객 데이터와 내부 영업 전략 자료가 포함된다.
- 동시 병렬 작업보다 순차 작업이 안전하다.
- 파일 충돌과 실데이터 커밋 위험을 줄여야 한다.

actor 4명 이상 또는 동시 작업이 잦아지면 협업 모델을 재검토한다.

### 11.1 작업 환경

| Actor | 기본 환경 | 격리 환경 |
|---|---|---|
| Codex | main 디렉토리에서 직접 작업 | 큰 작업 또는 사용자 명시 지시 시 채팅 시작 전 `Start in → New worktree` |
| Claude Code | 백그라운드 세션은 워크트리 자동 | 라이브 세션은 main 직접 가능 |

### 11.2 main SSOT 화이트리스트

다음 파일은 main이 단일 진실 원천이다.

```text
PROJECT.md
CLAUDE.md
AGENTS.md
business-context.md
DESIGN.md
docs/collab-protocol.md
docs/templates.md
README.md
.gitignore
.gitattributes
```

규칙:

- 위 파일은 동시 편집하지 않는다.
- 편집 직전 최신 상태를 확인한다.
- 편집 직후 즉시 commit한다.
- 덮어쓰기와 force push는 금지한다.

### 11.3 워크트리 사용 주의

- Codex에서 worktree가 필요하면 채팅 시작 전에 `Start in → New worktree`를 선택한다.
- 작업 중간 `Handoff to worktree`는 사용하지 않는다.
- 중간 전환이 필요하면 먼저 `git commit` 또는 `git stash -u`로 보호한다.
- 워크트리 종료 시 변경 파일을 main에 반영하고 Work Board 락을 해제한 뒤 commit한다.

---

## 12. 데이터 해석 제한

사용 가능 정보:

- 방문보고서에 기록된 고객사명, 산업군, 관심 주제, 고객 니즈, 기술 이슈
- 위키에 정리된 고객별 니즈, 소프트웨어 기능, 니즈-기능 매칭, 대응 전략
- export CSV에 포함된 검토 상태, 매칭 신뢰도, 담당자 입력값
- 사용자가 명시적으로 제공한 공개 자료 또는 내부 사용이 허용된 자료

금지:

- 방문보고서나 위키에 없는 고객 의도 추정
- 고객사의 미공개 전략, 예산, 조직 상황 단정
- 담당자가 확인하지 않은 매칭을 확정처럼 표시
- 개인정보, 개인 연락처, 민감한 개인 정보 수집 또는 노출
- 외부 공개 자료와 내부 방문보고서를 섞으면서 출처를 흐리게 표현
- 고객에게 실제 제안하기 전에 검토 필요 상태를 숨기는 표현

추천 문장은 다음 3가지를 함께 가져야 한다.

```text
고객 니즈 근거 + 연결 기능 + 다음 행동
```

---

## 13. 분쟁 처리

actor 간 이견은 User가 직접 결정한다.

절차:

1. 이견 핵심을 짧게 정리한다.
2. 충돌되는 파일 또는 판단 영역의 작업을 멈춘다.
3. Work Board에서 락을 해제하거나 `blocked`로 전환한다.
4. User에게 선택지를 제시한다.
5. User 결정 후 `PROJECT.md` Decisions에 기록한다.
6. 필요한 경우 Handoff Log를 남긴다.

자주 발생 가능한 분쟁:

| 분쟁 유형 | 기준 |
|---|---|
| 고객 데이터 표시 범위 | `business-context.md`와 사용자 결정 우선 |
| 비공개 데이터 표시 방식 | `sensitivity_level=비공개`는 경고 배지, 숨김은 `dashboard_visible` 기준 |
| 권한 범위 | admin은 User, viewer는 팀원 조회 전용 |
| admin 수정값 저장 위치 | 사용자 결정 전 구현하지 않음 |
| 검토 상태 표시 | CSV의 검토 상태와 신뢰도를 원본 기준으로 표시 |
| 디자인 톤 | `DESIGN.md`와 라온엑스 기준 우선 |
| 구현 우선순위 | `PROJECT.md` Work Board와 사용자 지시 우선 |

---

## 14. Archive Rule

### 14.1 Handoff Log archive

- `PROJECT.md` Handoff Log는 최근 10개만 유지한다.
- 11번째 항목을 추가하려는 actor가 archive를 수행한다.
- 가장 오래된 항목부터 남은 항목이 5개가 될 때까지 archive로 이동한다.
- archive 파일명은 다음 형식을 사용한다.

```text
archive/handoff-YYYY-MM-DD.md
archive/handoff-YYYY-MM-DD-2.md
```

archive 파일 구조:

```md
# Handoff Log Archive — YYYY-MM-DD

## Range

- From: ...
- To: ...

## Archived Entries

...
```

### 14.2 협업 인계 문서 archive

- 완료된 `dev_plan_*`, `execution_plan_*`, `handoff_*`는 필요 시 `archive/YYYY-MM-DD-<slug>/`로 이동한다.
- `questions_*`, `risk_notes_*`, `analysis_summary_*`, `final_report_*`는 역할별 결과물이므로 기본적으로 `output/`에 유지한다.
- 기존 인계 문서를 덮어쓰지 않는다.

---

## 15. 작업 전 체크리스트

작업 시작 전:

```text
[ ] PROJECT.md를 읽고 Work Board 충돌 여부 확인
[ ] 수정할 파일이 다른 actor에게 locked 되어 있지 않은지 확인
[ ] 원본 영역(raw/, wiki/, export/)을 건드리는 작업인지 확인
[ ] 실제 고객 데이터가 Git에 커밋될 가능성이 있는지 확인
[ ] 필요한 사용자 결정이 없으면 바로 진행, 있으면 질문 또는 blocked 처리
[ ] slug 결정
[ ] 산출물 작업이면 Work Board 행 등록
```

작업 종료 전:

```text
[ ] 수정 파일 확인
[ ] Git에 실데이터가 포함되지 않았는지 확인
[ ] 검증 결과 기록
[ ] Work Board 락 해제
[ ] Handoff Log 1줄 추가
[ ] 필요한 다음 작업 명시
[ ] commit
```

---

## 16. 이 문서의 변경 기준

이 문서는 다음 경우에만 수정한다.

- 협업 방식이 바뀌는 경우
- 파일 락 방식이 바뀌는 경우
- 로컬/서버 폴더 구조가 바뀌는 경우
- 데이터 보호 기준이 바뀌는 경우
- admin/viewer 권한 정책이 확정 또는 변경되는 경우
- 산출물 네이밍 규칙이 바뀌는 경우

단순 진행 상황은 이 문서가 아니라 `PROJECT.md` Work Board 또는 Handoff Log에 기록한다.
