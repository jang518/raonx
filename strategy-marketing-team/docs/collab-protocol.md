# docs/collab-protocol.md — 협업 프로토콜

knowledge_for_marketing 대시보드 프로젝트의 협업 규칙과 운영 정책.

`PROJECT.md`는 현황판이고, 이 문서는 규칙판이다.  
자주 보는 문서는 아니다. 새 actor 진입 시 1회 학습 후 필요할 때만 참조한다.

---

## 1. 역할과 기본 협업 모델

### Actor

| Actor | 기본 담당 | 주로 수정하는 파일 |
|---|---|---|
| Codex | Streamlit 대시보드 구현, CSV 로딩/검증, Docker Compose 운영 파일, 로컬 실행·렌더링 검증, 서버 반영 스크립트 보완 | `app/`, `scripts/`, `data/`, `output/execution_plan_*.md`, `output/handoff_*.md`, `output/analysis_summary_*.md`, `requirements.txt`, `Dockerfile`, `docker-compose.yml` |
| Claude Code | 1차 계획, 화면/데이터 해석 기준 설계, 고객 니즈-기능 매칭 리스크 점검, 최종 문서화, Codex 산출물 검토 | `output/dev_plan_*.md`, `output/questions_*.md`, `output/risk_notes_*.md`, `output/final_report_*.md` |
| User | 최종 판단, 우선순위 결정, 실데이터 제공, admin 권한 기준 결정, actor 간 분쟁 결정 | 없음 |

원칙:

- 사용자 요청이 기본 협업 모델보다 우선.
- Codex는 Claude Code가 작성 중인 최종 문서/리스크 노트를 임의 수정하지 않음.
- Claude Code는 구현 파일(코드/데이터/대시보드)을 직접 덮어쓰지 않음.
- `raw/`, `wiki/`, `export/`는 상위 지식 베이스 원천 영역이다. 대시보드 프로젝트 작업자가 임의 수정하지 않는다.
- 실제 고객사명, 담당자, 방문보고서 기반 내용은 내부 자료로 취급한다. 공개 저장소에 실데이터를 커밋하지 않는다.

### 기본 흐름

| 단계 | Owner | 내용 | 협업 문서 |
|---|---|---|---|
| 계획 | Claude Code | 대시보드 개발 전 목표·범위·화면 우선순위·데이터 관계·리스크·검증 기준 정리 | `output/dev_plan_*.md` |
| 보완 | Codex | 계획을 구현 가능하게 보완하고 실행 순서·파일 변경 범위 정리 | `output/execution_plan_*.md` |
| 구현 | Codex | Streamlit 앱, CSV 로딩, 검색/필터, 로그인/권한 구조, Docker Compose, robocopy 반영 보조 파일 구현 | 작업 파일 |
| 검증 | Codex | 로컬 실행, CSV 스키마 검증, 화면 렌더링, Docker 실행, 오류 검색 | `output/handoff_*.md` 또는 Handoff Log |
| 최종 문서화 | Claude Code | 구현 결과·리스크·운영 방법·다음 작업을 사용자용 문서로 정리 | `output/final_report_*.md` |

예외 (협업 문서 없이 바로 처리):

- 단순 문서 수정, 작은 오타 수정, 짧은 설명 보완
- 사용자가 도구를 지정해 즉시 처리하라고 한 작업
- 한 actor가 시작부터 끝까지 처리 가능한 작은 작업
- 파일 변경 없이 정보 확인만 하는 작업

actor 간 이견은 사용자가 직접 우선순위와 담당자를 지정해 해결한다.

---

## 2. PROJECT.md 갱신 방식

### 헤더 (Goal)

- 프로젝트의 최상위 목표 한 줄.
- 목표 자체가 바뀔 때만 수정.
- 데이터 상태·작업 phase 라벨은 헤더에 두지 않는다.
- 작업 phase는 Work Board의 `in_progress`가, 큰 결정은 Decisions가 담는다.

### Work Board

활성 작업(`in_progress`, `blocked`, `todo`)만 표시.

- 완료 작업은 표에서 제거하고 Handoff Log로 옮긴다.
- 활성 작업이 없으면 표는 비어있는 게 정상.
- 작업 본문은 짧게.
- 긴 계획은 `output/dev_plan_*.md`.
- `data/`, `app/`, `scripts/`, `output/`, Docker 관련 파일을 생성·수정하는 작업은 짧아도 Work Board에 등록한다.

**락 적용 기준 (Work Board 행을 만드는 조건)**

| 작업 유형 | Work Board | Handoff Log |
|---|---|---|
| 산출물 생성·수정 (`app/*`, `data/*`, `scripts/*`, `output/*`, Docker/requirements 파일) | 필수 (짧아도 행 등록) | 종료 시 1줄 |
| 메타 문서 수정 (메인 직접 수정 화이트리스트 파일) | 생략 가능 | 종료 시 1줄 |
| 즉시 처리 (인사·응답·정보 조회, 파일 변경 없음) | 생략 | 생략 |

이유:

- 충돌 위험이 가장 큰 영역은 `app/*`, `scripts/*`, `data/*`, `output/*` 산출물이다.
- 같은 slug 안에서 Codex와 Claude Code가 동시에 만질 가능성이 있는 파일은 반드시 락으로 보호한다.
- 메타 문서는 화이트리스트로 동시 수정 가정이 약하고, Handoff Log만으로 변경 추적 가능하다.

**Status 본질과 전환 규칙**

| Status | 본질 | 다른 actor에게 주는 신호 | 파일 락 |
|---|---|---|---|
| `todo` | 작업 정의됐고 외부 의존성 없는데 시작 전 | "곧 시작 예정" | 없음 |
| `in_progress` | actor가 능동적으로 작업 중 | "이 파일 만지지 마" | 있음 (`Files locked` 기재) |
| `blocked` | 외부 input(사용자 결정·다른 actor 산출물·데이터 도착) 없이 진행 불가 | "이거 풀리기 전엔 시작·재개 불가" | 없음 |
| `done` | 완료 | (표에서 제거 후 Handoff Log로 이동) | - |

전환 규칙:

- `todo` → `in_progress`: 작업 시작 시점. 이 프로젝트는 단일 사용자 중심 순차 작업이므로 보통 `todo`를 거치지 않고 바로 `in_progress`로 간다.
- `in_progress` → `blocked`: 작업 중 외부 input 필요해짐. **`Files locked` 비우고 락 해제**(파일을 안 만지니까).
- `blocked` → `in_progress`: 외부 input 도착. 락 재취득.
- `in_progress` → `done`: 작업 완료. 표에서 행 제거 + Handoff Log 1줄.
- `in_progress`는 푯말("회의실 사용 중"), `blocked`는 안내문("회의 자체가 손님 도착 대기로 못 열림"). 둘 다 표에 같이 있지만 다른 actor가 받는 신호와 락 여부가 다르다.

**blocked 풀기 흐름** (Owner=User blocked 항목 기준)

사용자가 결정을 한 줄로 알리면 actor가 다음을 한 번에 처리한다:

1. 해당 행을 Work Board에서 제거
2. 결정 내용을 Decisions에 1줄 추가 (말미에 ID 풀림 표시, 예: `(W2 풀림, YYYY-MM-DD)`)
3. Handoff Log에 처리 1줄 (`changed: W2/W3 풀림 + Decisions 추가 / verified: ... / next: ...`)

사용자가 "결정 보류" 또는 "당분간 안 정함"이라고 하면 행만 제거하고 Decisions 추가는 생략한다(필요해지면 다시 등록).  
사용자 결정 사항이 핵심 룰(`business-context.md`, `DESIGN.md` 등)과 충돌 가능성이 있으면 처리 직후 사용자에게 짧게 짚는다(자동 침묵 금지).  
Owner가 User가 아닌 blocked(다른 actor 산출물 대기·데이터 도착 대기)는 해당 actor의 작업 종료 시점에 함께 풀린다.

작성 템플릿:

```md
| ID | Status | Owner | Task | Files locked | Slug | Last update |
|---|---|---|---|---|---|---|
| W10 | in_progress | Codex | Home 화면 KPI 카드 구현 | app/main.py, app/components/kpi.py | dashboard-v1 | 2026-06-04 14:00 |
| W11 | blocked | User | 로그인 계정 정책 결정 | - | auth-v1 | 2026-06-04 |
```

### 파일 락

- Work Board의 `in_progress` 상태가 곧 파일 락이다.
- `Files locked` 컬럼에 잡은 파일을 기재한다.
- 다른 actor가 같은 파일을 잡고 있으면 그 작업이 끝날 때까지 시작하지 않는다.
- 작업이 끝나면 Work Board 행을 제거 = 락 자동 해제.
- 락이 풀리지 않은 채 다른 actor가 만져야 하면 사용자에게 알리고 결정을 기다린다.
- 락 잡은 actor의 세션이 중단되면 다음 세션은 락 해제 후 재진입한다.

### Decisions

협업 정책이나 합의를 1줄로 기록.  
한 번 정하면 거의 불변.  
자주 바뀌는 항목은 Decisions가 아니라 Work Board에 둔다.

기록 예시:

```md
- 대시보드는 사내망에서만 사용하고, 로그인 기능을 둔다. admin은 수정 가능, viewer는 조회 전용.
- `sensitivity_level`은 `공개 가능`, `비공개` 두 값으로 표시한다.
- 운영 서버는 Windows 11이며 Docker Compose로 실행한다.
```

### Handoff Log

작업 종료 시 1줄만. 최근 10개 유지.

형식:

```md
[YYYY-MM-DD HH:mm] actor / changed: ... / verified: ... / next: ...
```

`actor`는 `Codex`, `Claude Code`, `User` 중 하나.  
작업명은 `changed:`에 짧게.

---

## 3. 파일 네이밍 규칙

협업 문서와 역할별 결과물 문서는 작업 식별을 위해 slug를 붙인다.  
`ls`만으로 어느 작업의 산출물인지 보이게 한다.

### 슬러그 규칙

- 형식: `prefix_slug.ext`
- slug는 kebab-case, 영문 소문자 + 숫자 + 하이픈만.
- 한 작업 = 한 slug 공유.
- slug 예시: `dashboard-v1`, `local-streamlit`, `docker-compose-v1`, `auth-v1`, `robocopy-sync`.
- 같은 작업의 후속이면 동일 slug 유지, 다른 작업이면 새 slug.

### 협업 인계 문서

| 문서 | Owner | 용도 |
|---|---|---|
| `output/dev_plan_*.md` | Claude Code | Codex가 구현할 1차 계획 |
| `output/execution_plan_*.md` | Codex | Claude Code 계획을 보완한 실행 계획 |
| `output/handoff_*.md` | Codex 또는 현재 작업자 | 다음 담당자가 이어받기 위한 인수인계 |

### 역할별 결과물 문서

| 문서 | 생성 트리거 | Owner |
|---|---|---|
| `output/questions_*.md` | 화면 질문 / 검색·필터 / 의사결정 프레임 설계 | Claude Code |
| `output/risk_notes_*.md` | 데이터/보안/해석/권한 리스크 점검 | Claude Code |
| `output/analysis_summary_*.md` | Codex 데이터 분석 요약 | Codex |
| `output/final_report_*.md` | 사용자용 최종 구현·운영 보고서 | Claude Code |
| `output/dashboard_*.html` 또는 Streamlit 화면 캡처 | 대시보드 검증 결과 | Codex |
| `output/analysis_summary_*.md` | CSV 스키마 검증 결과 | Codex |

각 문서의 본문 섹션 구조는 `docs/templates.md` §1~8 참조.

### 앱/운영 파일

| 파일/폴더 | Owner | 용도 |
|---|---|---|
| `app/main.py` | Codex | Streamlit 앱 진입점 |
| `app/pages/*.py` | Codex | 화면별 페이지 |
| `app/components/*.py` | Codex | 공통 UI 컴포넌트 |
| `app/services/*.py` | Codex | 데이터 로딩·권한·댓글 저장 등 서비스 로직 |
| `scripts/validate_csv.py` | Codex | CSV 스키마 검증 |
| `scripts/sync_data.py` | Codex | 서버 반영 보조 로직 |
| `requirements.txt` | Codex | Python 의존성 |
| `Dockerfile` | Codex | 운영 컨테이너 이미지 |
| `docker-compose.yml` | Codex | Windows 11 서버 운영용 Compose 설정 |

### CSV 파일 규칙

운영 입력 CSV 4개:

```text
customer_needs.csv
customer_strategy.csv
need_value_matching.csv
software_features.csv
```

추가 manifest:

```text
dashboard_manifest.json
```

원칙:

- 실제 CSV는 상위 `export/`에서 생성되고, 서버 PC `incoming/`으로 robocopy 전송된다.
- 대시보드 개발 폴더의 `data/`에는 샘플 데이터 또는 서버에서 읽을 복사본만 둔다.
- 실제 고객 데이터 CSV는 기본적으로 Git에 커밋하지 않는다.
- Git에 포함해야 하는 경우 반드시 사용자에게 확인한다.
- CSV 헤더는 실제 파일을 기준으로 사용한다. 이미지·문서에서 보이는 컬럼명을 임의 보정하지 않는다.

### 라이프사이클

- 작업이 끝나 살아있는 인계 문서가 아니게 되면 `dev_plan_*.md`, `execution_plan_*.md`, `handoff_*.md`는 `archive/YYYY-MM-DD-*/`로 이동.
- 역할별 결과물 문서(`final_report`, `risk_notes`, `questions`, `analysis_summary`, `analysis_summary`)는 `output/`에 유지.
- 새 버전이 필요하면 새 slug.
- 새 작업 시작 시 새 slug.
- 기존 인계 문서를 덮어쓰지 않는다.

---

## 4. 협업 환경 정책

협업 모델은 **옵션 B (PROJECT.md 중앙 락, 메인 SSOT)**.

이유:

- 소규모 (actor 3명: Claude Code, Codex, User)
- 단일 사용자가 5분 안에 현황 파악 필요
- PR/CI 없는 환경
- Knowledge work + Streamlit 개발 특성상 동시 병렬보다 직렬 작업이 효율적
- 고객 데이터와 내부 영업 전략 자료가 포함되므로 충돌·오염·실데이터 커밋을 줄여야 함
- 사용자 인지 부하와 의사결정 의존성 때문에 병렬은 제한적으로만 사용

actor 4명+ 또는 동시 작업 빈번 시 재검토.

### 작업 환경

| Actor | 기본 환경 | 격리 환경 |
|---|---|---|
| Codex | 메인 디렉토리에서 직접 (Work locally) | 큰 작업 / 사용자 명시 지시 시 채팅 시작 전 "Start in → New worktree" |
| Claude Code | 백그라운드 세션은 워크트리 자동 (시스템 가이드라인) | 라이브 세션은 메인 직접 가능 |

Codex의 "Start in" 메뉴는 채팅 시작 전에 환경을 사전 선택한다.

작업 중간에 "Handoff to worktree"로 환경을 바꾸면 메인을 `git reset --hard HEAD`로 정리하여 미커밋 변경을 모두 날린다.  
부득이한 중간 전환이 필요하면 먼저 `git commit` 또는 `git stash -u`로 보호한다.

### 화이트리스트 (메인 SSOT)

다음 파일은 **메인이 단일 진실 원천(SSOT)**이다.

- `PROJECT.md` (락의 SSOT)
- `CLAUDE.md`, `AGENTS.md` (메타 작업 지침)
- `business-context.md`, `DESIGN.md` (사업/시각 기준)
- `docs/collab-protocol.md`, `docs/templates.md` (협업 메타 규칙)
- `.gitignore`, `.gitattributes` 같은 git 설정 파일
- `README.md` (프로젝트 설명)

작업 방식 (세션 종류별):

- **라이브 세션 / Codex 메인 작업**: 메인에서 직접 수정.
- **Claude Code 백그라운드 세션**: 도구가 자동으로 워크트리에 격리한다. 화이트리스트 파일은 아래 "cp 동기화 패턴"으로 작업한다(룰 충돌이 아니라 정식 절차).
- **Codex "Start in → New worktree"**: 채팅 시작 전 사전 격리이므로 워크트리가 곧 작업 base. cp 동기화 불필요, 종료 시 cp로 메인 적용.

이유:

- 락 동기화 보장
- 사용자 미커밋 변경과의 충돌 회피
- 메인 기준 문서의 일관성 유지

### 격리 환경 작업 파일

워크트리에서 자유롭게 작업:

- `app/` (Streamlit 앱 코드)
- `data/` (샘플 데이터, 로컬 개발용 데이터)
- `scripts/` (검증/동기화/운영 보조 코드)
- `output/` (산출물 문서, 화면 캡처, 검증 결과)
- `archive/` (보관 자료)
- `assets/` (로고, 이미지, 아이콘 등 정적 리소스)

주의:

- `raw/`, `wiki/`, `export/`는 상위 지식 베이스 영역이다. 대시보드 프로젝트 내부 작업에서 직접 수정하지 않는다.
- `export/`의 CSV 4개와 `dashboard_manifest.json`은 robocopy 전송 대상이다. 생성/갱신 책임은 상위 위키/export 흐름에 있다.
- 서버 PC의 `incoming/`, `data/`, `backup/`, `logs/`는 운영 영역이다. 개발 중에는 경로를 문서화하고, 실제 운영 파일 변경은 사용자 승인 후 진행한다.

### 백그라운드 세션의 화이트리스트 작업 — cp 동기화 패턴

Claude Code 백그라운드 세션은 도구 자동 격리로 워크트리에서 작업한다.  
화이트리스트 파일을 수정해야 할 때는 다음 8단계 절차를 따른다.

> **⚠️ 우회 금지**
>
> cp 동기화는 백그라운드 세션 화이트리스트 작업의 **유일한 정식 절차**다.
> 어떤 이유로도 우회하지 않는다.
> 산출물 작업 시 가장 먼저 PROJECT.md를 cp 동기화해서 Work Board 락을 메인에 visible하게 만들어야 다른 actor 충돌을 막을 수 있다.
>
> 금지되는 우회 패턴:
>
> - PROJECT.md를 워크트리로 cp하지 않고 산출물만 작성
> - Work Board 락 등록을 생략하고 작업 진행
> - 스크립트로 메인 파일 in-place replace
> - 산출물 작성 완료 후에야 PROJECT.md를 한 번에 편집
>
> 차단 발생 시 처리는 아래 "cp가 차단될 때" 절 참조.

```text
1. EnterWorktree 진입 (도구 자동 처리)

2. 메인 → 워크트리 cp 동기화
   - 작업 대상 화이트리스트 파일만 cp (전체 일괄 cp 금지)
   - 예: cp /.../knowledge_for_marketing/dashboard/PROJECT.md /.../.claude/worktrees/<id>/PROJECT.md

3. 워크트리에서 편집 (Edit/Write 도구 정상 작동)

4. ⭐ cp 적용 직전 — 메인 최신 재확인 (race window 닫기)
   - rtk git log -1 --format="%ar %an : %s" PROJECT.md
   - 대상 파일별 동일
   - 본인 작업 시작(절차 2) 이후 타 actor commit 발견 시:
     a. cp 메인 → 워크트리 (메인 최신 받아오기, 본인 워크트리 변경 일시 손실)
     b. 메인 최신 위에 본인 변경을 Edit/Write로 재적용
     c. (a)부터 재실행
   - 발견 없음 → 다음 단계 진행

5. ⭐ Handoff Log 카운트 의무 단계
   - Handoff Log에 새 항목을 추가하는 작업이면 항상 메인 PROJECT.md 현재 Handoff Log 개수를 카운트
   - rtk grep -c "^\[20" PROJECT.md
   - 추가 후 개수가 11 이상이면 archive 트리거 발동 — §6 "Handoff Log 자동 archive" 체크리스트대로 같은 작업 안에서 archive 처리
   - 카운트 생략 금지
   - archive를 별도 작업으로 미루는 것도 금지

6. 워크트리 → 메인 cp 적용
   - 편집 완료된 파일(+ archive 트리거 시 신규 archive 파일)만 cp
   - 사용자 미커밋 변경분(화이트리스트 외)은 그대로 보존됨

7. ExitWorktree(action=remove)로 워크트리 정리

8. 메인에서 30초 내 commit
   - 동시 편집 방지 룰 준수
   - "PROJECT.md / 화이트리스트 동시 편집 방지" 절차를 따른다
```

archive 파일은 `.gitignore`의 `archive/*` 정책으로 untracked 유지(보존 목적이지 git 추적 대상 아님).

절차 4번이 옵션 P의 핵심이다.  
워크트리 작업 시간(수 분~수십 분)만큼 race window가 길어지므로, cp 적용 직전 한 번 더 메인 최신을 확인해 그 사이의 타 actor commit을 놓치지 않는다.

Codex의 "Start in → New worktree" 종료 시 cp 적용 단계에도 동일하게 적용한다(초기 cp 동기화만 생략, 종료 시 재확인은 필수).

원칙:

- **cp는 동기화·적용 목적으로만**: 메인 최신 상태를 워크트리 base로 받아오고, 편집 후 메인에 되돌려놓는 정식 절차다.
- **워크트리에서 처음부터 만들기 금지**: cp 동기화 없이 워크트리에서 새로 작성하면 메인 최신 상태(다른 actor 변경분, 사용자 미커밋분)를 잃는다.
- **작업 대상만 cp**: 전체 화이트리스트를 일괄 cp하면 불필요한 동기화로 race window가 넓어진다.

### cp가 차단될 때

cp 동기화는 정식 절차이므로 도구가 차단했다면 false positive 가능성이 높다.  
절대 우회하지 않는다.

```text
1. 재시도 1회 — 동일 cp 명령을 한 번 더 실행한다.
   ├─ 통과: cp 동기화 패턴 진행.
   └─ 실패: 2번으로.

2. 사용자에게 즉시 보고
   - "cp가 차단됐다. 화이트리스트 cp는 정식 절차이므로 우회하지 않는다. 권한 허용 또는 다른 결정 필요" 한 줄 보고.

3. 사용자 결정 대기
   - 권한 허용 / 룰 변경 / 작업 보류 중 사용자가 선택할 때까지 대기.

4. 권한 허용되면 정식 cp 동기화 패턴으로 즉시 복귀.
   룰 변경되면 그 룰 따름.
```

**금지되는 우회 (반복 강조)**

- 화이트리스트 cp 없이 산출물만 작성하고 락 등록을 생략
- 스크립트로 메인 파일 in-place replace
- PROJECT.md 직접 편집 회피 후 작업 완료 시점에 한 번에 처리
- "이번에는 어차피 단일 작업이니까" 같은 이유로 락 등록 생략

cp 동기화는 락 등록 visibility + race 방지 + 사용자 미커밋 보존 세 가지를 한 번에 보장한다.  
우회는 셋 중 하나 이상을 잃는다.

### Codex 워크트리 진입 (안전한 흐름)

```text
1. Codex 채팅 시작 전 "Start in" 메뉴 클릭
2. "New worktree" 선택 → 메인의 복사본이 새 디렉토리에 생성됨
3. 메인 디렉토리는 절대 건드리지 않음
```

사전 선택이라 메인의 미커밋 변경에 영향을 주지 않는다.

### 중간 "Handoff to worktree" 사용 금지

작업 중간에 "Handoff to worktree"는 메인을 `git reset --hard HEAD`로 정리한다.  
미커밋 변경과 untracked 파일이 모두 사라진다.

부득이하게 중간 전환이 필요하면:

```text
1. git status 로 미커밋/untracked 확인
2. 즉시 git commit 또는 git stash -u
3. working tree clean 확인 후 Handoff
```

권장:

- 처음부터 "Start in → New worktree"로 시작해 중간 Handoff를 아예 안 쓴다.

### 격리 작업 종료 흐름

```text
1. 워크트리에서 작업 완료
2. 메인 직접 수정 화이트리스트 외 파일만 cp 또는 git merge로 메인 적용
3. 메인 PROJECT.md 락 해제(Work Board 행 제거) + Handoff Log 1줄 추가
4. 워크트리 정리 (ExitWorktree(remove) 또는 git worktree remove)
5. 메인에서 즉시 git commit
```

### 안전장치

1. 격리 환경 진입 전 메인 PROJECT.md Work Board에 작업 행을 먼저 등록(Status `in_progress`, `Files locked` 명시).
2. 격리 환경 종료 시 cp/머지 대상에서 메인 직접 수정 화이트리스트를 제외.
3. 사용자 라이브 세션 중 백그라운드 multi-session 자제 (사용자 명시 지시 시 예외).
4. 작업 시작 시 메인 화이트리스트 파일 내용을 워크트리에 동기화 (읽기 컨텍스트용; 수정은 메인).
5. 격리 환경 진입/종료 직전 즉시 `git commit` (reset 방지).
6. 실제 고객 데이터 CSV는 커밋 전 반드시 `.gitignore`와 `git status`를 확인한다.
7. `dashboard_visible=FALSE` 데이터는 기본 화면에서 숨기고, `sensitivity_level=비공개` 데이터는 표시하되 경고 배지를 붙인다.

### PROJECT.md / 화이트리스트 동시 편집 방지

메인 직접 수정 화이트리스트 파일(특히 `PROJECT.md`)은 양쪽 actor가 동시에 read–edit–write 하면 마지막 저장자가 앞 변경을 덮어쓴다.

Codex와 Claude Code 모두 다음 3단계 절차를 거친다.

**1. 편집 전 — 활성 작업 감지**

```bash
rtk git log -1 --format="%ar %an : %s" PROJECT.md
```

대상 파일별로 동일하게 실행.

| 결과 timestamp | 의미 | 행동 |
|---|---|---|
| `0~30 seconds ago` | 거의 확실히 다른 actor 작업 중 | 60초 대기 후 재확인 |
| `1~5 minutes ago` (타 actor) | 진행 중일 수 있음 | 30초 대기 후 1회 재확인 |
| `10 minutes ago`+ 또는 본인 commit | 안전 | 진행 |

2회 연속 활성 신호면 사용자에게 "다른 actor 작업 중으로 보임, 대기/강제 진행" 묻고 결정 따른다.  
actor가 본인뿐이라고 확신해도 절차는 동일하게 거친다(백그라운드 세션 누락 방지).

**2. 편집 전 — working tree 확인**

```bash
rtk git status
```

본인이 만든 미커밋만 있어야 한다.

다른 actor의 미커밋 변경 흔적(낯선 파일, 본인 작업 범위 밖 modified)이 보이면 멈추고 사용자에게 보고한다.

**3. 편집 후 — 즉시 commit**

- 편집 직후 30초 내 `rtk git commit`.
- 지연 금지(지연 시간만큼 race window가 열린다).
- 작업 단위로 여러 화이트리스트 파일을 함께 수정했으면 한 번에 묶어 commit한다(원자성).
- push/pull 충돌 시 `rtk git pull --rebase` 또는 수동 머지.
- **덮어쓰기·force push 금지**.

같은 규칙은 `PROJECT.md` 외 화이트리스트 전체(`CLAUDE.md`, `AGENTS.md`, `business-context.md`, `DESIGN.md`, `docs/collab-protocol.md`, `docs/templates.md`, `.gitignore`, `.gitattributes`, `README.md`)에 적용한다.

변경 빈도가 가장 높은 `PROJECT.md`가 가장 자주 충돌하므로 `PROJECT.md`를 기본 점검 대상으로 본다.

---

## 5. 분쟁 처리 & self-handoff

actor 간 이견은 사용자가 직접 결정한다.

절차:

1. 이견 핵심 정리 → 사용자 보고.
2. 사용자 결정 전까지 충돌 영역 작업 멈춤 (락 해제 = Work Board 행 제거).
3. 사용자 지정 결정을 `PROJECT.md` Decisions에 1줄 추가.

자주 발생할 수 있는 분쟁 예시:

| 분쟁 유형 | 판단 기준 |
|---|---|
| 고객 데이터 표시 범위 | `business-context.md`와 사용자 결정 우선 |
| `sensitivity_level=비공개` 표시 방식 | 숨기지 않고 경고 배지로 표시. 실제 숨김은 `dashboard_visible` 기준 우선 |
| admin/viewer 권한 범위 | admin은 사용자, viewer는 팀원 조회 전용 |
| admin 수정값 저장 위치 | 사용자가 지정한 저장 방식 우선. viewer 코멘트 기능은 현재 범위에서 제외 |
| 검토 상태 표시 방식 | CSV의 `review_status`, `confidence` 값을 원본 기준으로 표시 |
| 디자인 톤 | `DESIGN.md`와 라온엑스 CI 기준 우선 |
| 구현 우선순위 | Home 1순위, 나머지는 추천 우선순위 |

세션 A→B 인계 시:

- `handoff_*.md` 작성(`docs/templates.md` §5 또는 §7)
- 락 모두 해제
- 메인 PROJECT.md 갱신
- 메인 commit

---

## 6. Archive Rule

### Handoff Log 자동 archive

- **트리거**: `PROJECT.md` Handoff Log가 **10개를 초과**하는 시점(=11번째 항목을 추가하려는 순간).
- **처리자**: 새 Handoff Log 항목을 추가하는 actor.
- archive 처리는 그 작업의 일부로 수행한다(별도 작업으로 미루지 않는다).
- **이동량**: 가장 오래된 항목부터 **남은 항목이 5개가 될 때까지** 일괄 이동. 결과적으로 새 항목까지 더해 6개로 마감.
- **파일명**: `archive/handoff-YYYY-MM-DD.md` (이동 작업 수행일 기준). 같은 일자에 두 번째 archive가 발생하면 `-2`, 세 번째는 `-3` 접미사.
- **PROJECT.md 표기**: Handoff Log 섹션 최상단에 한 줄 인용(`> 이전 항목 N건(시작~끝)은 archive/<파일명>으로 이동.`) 남긴다. 두 번째 archive가 발생하면 인용 줄을 한 줄에 두 절로 이어 적는다(`이전 N건(...)은 archive/...md로 이동. 추가 M건(...)은 archive/...-2.md로 이동.`).
- archive 파일 구조는 `# Handoff Log Archive — YYYY-MM-DD` 헤더 + 이동 대상 범위 + `## Archived Entries`에 원문 그대로 나열.

**✅ 필수 카운트 체크리스트**

Handoff Log에 새 항목을 추가하기 **직전** 매번 다음을 실행한다.  
생략 금지.

```text
1. 현재 PROJECT.md Handoff Log 개수 카운트
   - rtk grep -c "^\[20" PROJECT.md
   - 또는 메인 PROJECT.md를 Read해서 시각 패턴 직접 카운트

2. 추가 후 개수 계산: 현재 N + 1

3. 결과 처리
   - N+1 ≤ 10:
     항목만 추가
   - N+1 = 11 이상:
     트리거 발동
     a. 같은 작업 안에서 archive 절차 함께 수행
     b. 가장 오래된 (N+1 - 5)개 항목을 archive/handoff-YYYY-MM-DD[-N].md로 이동
     c. PROJECT.md Handoff Log 인용 줄 갱신
     d. 본 archive 작업 자체를 새 Handoff Log 항목으로 추가

4. cp 적용 직전 메인 최신 재확인 시점에 한 번 더 카운트
   - 다른 actor가 그 사이 추가했을 가능성
```

이 체크는 cp 동기화 패턴 §4 "워크트리 → 메인 cp 적용" **직전 단계로 의무화**된다.  
카운트 생략으로 11번째를 그냥 추가하는 패턴은 룰 위반이다.

### 협업 인계 문서 archive

- 완료된 협업 인계 문서(`dev_plan_*`, `execution_plan_*`, `handoff_*`)는 `archive/YYYY-MM-DD-*/` 디렉토리로 이동.
- 처리 시점은 해당 slug의 모든 산출물 단계가 끝나고 다음 작업으로 넘어갈 때.
- 역할별 결과물 문서(`final_report`, `risk_notes`, `questions`, `analysis_summary`, `analysis_summary`, 화면 캡처)는 `output/`에 유지.
- 새 버전이 필요하면 새 slug.
- `PROJECT.md`에는 위치와 상태만 남긴다.
