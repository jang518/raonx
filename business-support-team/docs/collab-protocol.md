# docs/collab-protocol.md — 협업 프로토콜

오브온 프로젝트의 협업 규칙과 운영 정책. `PROJECT.md`는 현황판이고, 이 문서는 규칙판이다.

자주 보는 문서는 아니다. 새 actor 진입 시 1회 학습 후 필요할 때만 참조한다.

## 1. 역할과 기본 협업 모델

### Actor

| Actor | 기본 담당 | 주로 수정하는 파일 |
|---|---|---|
| Codex | 구현, 데이터/코드/대시보드/렌더링 검증, Claude Code 계획 보완 | `data/`, `scripts/`, `output/execution_plan_<slug>.md`, `output/handoff_<slug>.md`, `output/dashboard.html`, `output/analysis_summary_<slug>.md`, `output/promo_image_*.png` |
| Claude Code | 1차 계획, 전략 해석, 대표 질문 설계, 리스크 점검, 최종 문서 | `output/dev_plan_<slug>.md`, `output/questions_<slug>.md`, `output/risk_notes_<slug>.md`, `output/final_report_<slug>.md` |
| User | 최종 판단, 우선순위, actor 간 분쟁 결정, 실데이터 제공 | 없음 |

원칙:
- 사용자 요청이 기본 협업 모델보다 우선.
- Codex는 Claude Code가 작성 중인 최종 문서/리스크 노트를 임의 수정하지 않음.
- Claude Code는 구현 파일(코드/데이터/대시보드)을 직접 덮어쓰지 않음.

### 기본 흐름

| 단계 | Owner | 내용 | 협업 문서 |
|---|---|---|---|
| 계획 | Claude Code | 복잡한 개발/분석 작업 전 목표·범위·리스크·검증 기준 정리 | `output/dev_plan_<slug>.md` |
| 보완 | Codex | 계획을 구현 가능하게 보완 | `output/execution_plan_<slug>.md` |
| 구현 | Codex | 데이터/코드/대시보드/이미지 작업 | 작업 파일 |
| 검증 | Codex | 파일 검사, 렌더링, 오류 검색 | `output/handoff_<slug>.md` 또는 Handoff Log |
| 최종 문서화 | Claude Code | 대표용 최종 결과물 작성 | `output/final_report_<slug>.md` |

예외 (협업 문서 없이 바로 처리):
- 단순 문서 수정, 작은 샘플 데이터 보정, 긴급 수정
- 사용자가 도구를 지정해 즉시 처리하라고 한 작업
- 한 actor가 시작부터 끝까지 처리 가능한 작업

actor 간 이견은 사용자가 직접 우선순위와 담당자를 지정해 해결한다.

## 2. PROJECT.md 갱신 방식

### 헤더 (Goal)

- 프로젝트의 최상위 목표 한 줄. 목표 자체가 바뀔 때만 수정.
- 데이터 상태·작업 phase 라벨은 헤더에 두지 않는다. 작업 phase는 Work Board의 `in_progress`가, 큰 결정은 Decisions가 담는다.

### Work Board

활성 작업(`in_progress`, `blocked`, `todo`)만 표시.

- 완료 작업은 표에서 제거하고 Handoff Log로 옮긴다.
- 활성 작업이 없으면 표는 비어있는 게 정상.
- 작업 본문은 짧게. 긴 계획은 `output/dev_plan_<slug>.md`.

**락 적용 기준 (Work Board 행을 만드는 조건)**

| 작업 유형 | Work Board | Handoff Log |
|---|---|---|
| 산출물 생성·수정 (`output/*`, `data/*`, `scripts/*`) | 필수 (짧아도 행 등록) | 종료 시 1줄 |
| 메타 문서 수정 (메인 직접 수정 화이트리스트 파일) | 생략 | 종료 시 1줄 |
| 즉시 처리 (인사·응답·정보 조회, 파일 변경 없음) | 생략 | 생략 |

이유: 충돌 위험이 가장 큰 영역은 `output/*`·`data/*` 산출물(같은 slug 안에서 Codex와 Claude Code가 동시에 만질 수 있음). 메타 문서는 화이트리스트로 동시 수정 가정이 약하고, Handoff Log만으로 변경 추적 충분.

**Status 본질과 전환 규칙**

| Status | 본질 | 다른 actor에게 주는 신호 | 파일 락 |
|---|---|---|---|
| `todo` | 작업 정의됐고 외부 의존성 없는데 시작 전 | "곧 시작 예정" | 없음 |
| `in_progress` | actor가 능동적으로 작업 중 | "이 파일 만지지 마" | 있음 (`Files locked` 기재) |
| `blocked` | 외부 input(사용자 결정·다른 actor 산출물·데이터 도착) 없이 진행 불가 | "이거 풀리기 전엔 시작·재개 불가" | 없음 |
| `done` | 완료 | (표에서 제거 후 Handoff Log로 이동) | - |

전환 규칙:

- `todo` → `in_progress`: 작업 시작 시점. 오브온은 단일 actor 순차 작업이라 보통 `todo`를 거치지 않고 바로 `in_progress`로 간다.
- `in_progress` → `blocked`: 작업 중 외부 input 필요해짐. **`Files locked` 비우고 락 해제**(파일을 안 만지니까).
- `blocked` → `in_progress`: 외부 input 도착. 락 재취득.
- `in_progress` → `done`: 작업 완료. 표에서 행 제거 + Handoff Log 1줄.

`in_progress`는 푯말("회의실 사용 중"), `blocked`는 안내문("회의 자체가 손님 도착 대기로 못 열림"). 둘 다 표에 같이 있지만 다른 actor가 받는 신호와 락 여부가 다르다.

**blocked 풀기 흐름** (Owner=User blocked 항목 기준)

사용자가 결정을 한 줄로 알리면 actor가 다음을 한 번에 처리한다:

1. 해당 행을 Work Board에서 제거
2. 결정 내용을 Decisions에 1줄 추가 (말미에 ID 풀림 표시, 예: `(W2 풀림, YYYY-MM-DD)`)
3. Handoff Log에 처리 1줄 (`changed: W2/W3 풀림 + Decisions 추가 / verified: ... / next: ...`)

사용자가 "결정 보류" 또는 "당분간 안 정함"이라고 하면 행만 제거하고 Decisions 추가는 생략한다(필요해지면 다시 등록). 사용자 결정 사항이 핵심 룰(`business-context.md` 등)과 충돌 가능성이 있으면 처리 직후 사용자에게 짧게 짚는다(자동 침묵 금지).

Owner가 User가 아닌 blocked(다른 actor 산출물 대기·데이터 도착 대기)는 해당 actor의 작업 종료 시점에 함께 풀린다.

작성 템플릿:

```
| ID | Status | Owner | Task | Files locked | Slug | Last update |
|---|---|---|---|---|---|---|
| W10 | in_progress | Codex | 5월 데이터 정리 | data/monthly_ops.xlsx | sales-may | 2026-05-15 14:00 |
| W11 | blocked | User | 실데이터 형식 결정 | - | - | 2026-05-13 |
```

### 파일 락

- Work Board의 `in_progress` 상태가 곧 파일 락이다. `Files locked` 컬럼에 잡은 파일을 기재한다.
- 다른 actor가 같은 파일을 잡고 있으면 그 작업이 끝날 때까지 시작하지 않는다.
- 작업이 끝나면 Work Board 행을 제거 = 락 자동 해제.
- 락이 풀리지 않은 채 다른 actor가 만져야 하면 사용자에게 알리고 결정을 기다린다.
- 락 잡은 actor의 세션이 중단되면 다음 세션은 락 해제 후 재진입한다.

### Decisions

협업 정책이나 합의를 1줄로 기록. 한 번 정하면 거의 불변. 자주 바뀌는 항목은 Decisions가 아니라 Work Board에 둔다.

### Handoff Log

작업 종료 시 1줄만. 최근 10개 유지.

형식: `[YYYY-MM-DD HH:mm] actor / changed: ... / verified: ... / next: ...`

`actor`는 `Codex`, `Claude Code`, `User` 중 하나. 작업명은 `changed:`에 짧게.

## 3. 파일 네이밍 규칙

협업 문서와 역할별 결과물 문서는 작업 식별을 위해 slug를 붙인다. `ls`만으로 어느 작업의 산출물인지 보이게 한다.

### 슬러그 규칙

- 형식: `<doc_type>_<slug>.<ext>`
- slug는 kebab-case, 영문 소문자 + 숫자 + 하이픈만.
- 한 작업 = 한 slug 공유.
- slug 예시: `dashboard-v1`, `sales-q2-analysis`, `instagram-summary-may`.
- 같은 작업의 후속이면 동일 slug 유지, 다른 작업이면 새 slug.

### 협업 인계 문서

| 문서 | Owner | 용도 |
|---|---|---|
| `output/dev_plan_<slug>.md` | Claude Code | Codex가 구현할 1차 계획 |
| `output/execution_plan_<slug>.md` | Codex | Claude Code 계획을 보완한 실행 계획 |
| `output/handoff_<slug>.md` | Codex 또는 현재 작업자 | 다음 담당자가 이어받기 위한 인수인계 |

### 역할별 결과물 문서

| 문서 | 생성 트리거 | Owner |
|---|---|---|
| `output/questions_<slug>.md` | 대표 질문 / 분석 프레임 설계 | Claude Code |
| `output/risk_notes_<slug>.md` | 데이터/전략 리스크 점검 | Claude Code |
| `output/analysis_summary_<slug>.md` | Codex 데이터 분석 요약 | Codex |
| `output/final_report_<slug>.md` | 대표용 최종 전략보고서 | Claude Code |
| `output/dashboard.html` | 대시보드 (slug 생략, 단일 산출물) | Codex |
| `output/promo_image_<slug>_<NN>.png` | 프로모션 이미지 시안 | Codex |

각 문서의 본문 섹션 구조는 `docs/templates.md` §1~8 참조.

### 라이프사이클

- 작업이 끝나 살아있는 인계 문서가 아니게 되면 `dev_plan_<slug>.md`, `execution_plan_<slug>.md`, `handoff_<slug>.md`는 `archive/YYYY-MM-DD-<slug>/`로 이동.
- 역할별 결과물 문서(`final_report`, `risk_notes`, `questions`, `analysis_summary`, `dashboard.html`, `promo_image_*`)는 `output/`에 유지. 새 버전이 필요하면 새 slug.
- 새 작업 시작 시 새 slug. 기존 인계 문서를 덮어쓰지 않는다.

## 4. 협업 환경 정책

협업 모델은 **옵션 B (PROJECT.md 중앙 락, 메인 SSOT)**.

이유:
- 소규모 (actor 3명: Claude Code, Codex, User)
- 단일 사용자가 5분 안에 현황 파악 필요
- PR/CI 없는 환경
- Knowledge work 특성상 동시 병렬보다 직렬 작업이 효율적 (95%)
- 사용자 인지 부하와 의사결정 의존성 때문에 병렬은 거의 환상

actor 4명+ 또는 동시 작업 빈번 시 재검토.

### 작업 환경

| Actor | 기본 환경 | 격리 환경 |
|---|---|---|
| Codex | 메인 디렉토리에서 직접 (Work locally) | 큰 작업 / 사용자 명시 지시 시 채팅 시작 전 "Start in → New worktree" |
| Claude Code | 백그라운드 세션은 워크트리 자동 (시스템 가이드라인) | 라이브 세션은 메인 직접 가능 |

Codex의 "Start in" 메뉴는 채팅 시작 전에 환경을 사전 선택한다. 작업 중간에 "Handoff to worktree"로 환경을 바꾸면 메인을 `git reset --hard HEAD`로 정리하여 미커밋 변경을 모두 날린다. 부득이한 중간 전환이 필요하면 먼저 `git commit` 또는 `git stash`로 보호한다.

### 화이트리스트 (메인 SSOT)

다음 파일은 **메인이 단일 진실 원천(SSOT)**이다.

- `PROJECT.md` (락의 SSOT)
- `CLAUDE.md`, `AGENTS.md` (메타 작업 지침)
- `business-context.md`, `DESIGN.md` (사업/시각 기준)
- `docs/collab-protocol.md`, `docs/templates.md` (협업 메타 규칙)
- `.gitignore`, `.gitattributes` 같은 git 설정 파일

작업 방식 (세션 종류별):

- **라이브 세션 / Codex 메인 작업**: 메인에서 직접 수정.
- **Claude Code 백그라운드 세션**: 도구가 자동으로 워크트리에 격리한다. 화이트리스트 파일은 아래 "cp 동기화 패턴"으로 작업한다(룰 충돌이 아니라 정식 절차).
- **Codex "Start in → New worktree"**: 채팅 시작 전 사전 격리이므로 워크트리가 곧 작업 base. cp 동기화 불필요, 종료 시 cp로 메인 적용.

이유: 락 동기화 보장 + 사용자 미커밋 변경과의 충돌 회피.

### 격리 환경 작업 파일

워크트리에서 자유롭게 작업:

- `data/` (스프레드시트, 원천 데이터)
- `scripts/` (계산/분석 코드)
- `output/` (산출물 문서, 대시보드, 이미지)
- `archive/` (보관 자료)

### 백그라운드 세션의 화이트리스트 작업 — cp 동기화 패턴

Claude Code 백그라운드 세션은 도구 자동 격리로 워크트리에서 작업한다. 화이트리스트 파일을 수정해야 할 때는 다음 7단계 절차를 따른다.

> **⚠️ 우회 금지**
>
> cp 동기화는 백그라운드 세션 화이트리스트 작업의 **유일한 정식 절차**다. 어떤 이유로도 우회하지 않는다. 산출물 작업 시 가장 먼저 PROJECT.md를 cp 동기화해서 Work Board 락을 메인에 visible하게 만들어야 다른 actor 충돌을 막을 수 있다.
>
> 금지되는 우회 패턴:
> - PROJECT.md를 워크트리로 cp하지 않고 산출물만 작성
> - Work Board 락 등록을 생략하고 작업 진행
> - 스크립트로 메인 파일 in-place replace
> - 산출물 작성 완료 후에야 PROJECT.md를 한 번에 편집
>
> 차단 발생 시 처리는 아래 "cp가 차단될 때" 절 참조.

```
1. EnterWorktree 진입 (도구 자동 처리)
2. 메인 → 워크트리 cp 동기화
   - 작업 대상 화이트리스트 파일만 cp (전체 일괄 cp 금지)
   - 예: cp /…/obon-dashboard/PROJECT.md /…/.claude/worktrees/<name>/PROJECT.md
3. 워크트리에서 편집 (Edit/Write 도구 정상 작동)
4. ⭐ cp 적용 직전 — 메인 최신 재확인 (race window 닫기)
   - rtk git log -1 --format="%ar %an : %s" PROJECT.md (대상 파일별 동일)
   - 본인 작업 시작(절차 2) 이후 타 actor commit 발견 시:
     a. cp 메인 → 워크트리 (메인 최신 받아오기, 본인 워크트리 변경 일시 손실)
     b. 메인 최신 위에 본인 변경을 Edit/Write로 재적용
     c. (a)부터 재실행. 통과 시 다음 단계로.
   - 발견 없음 → 다음 단계 진행.
5. ⭐ Handoff Log 카운트 의무 단계
   - Handoff Log에 새 항목을 추가하는 작업이면 **항상** 메인 PROJECT.md 현재 Handoff Log 개수를 카운트.
   - rtk grep -c "^\[20" PROJECT.md
   - 추가 후 개수가 **11 이상이면 archive 트리거 발동** — §6 "Handoff Log 자동 archive"의 체크리스트대로 같은 작업 안에서 archive 처리.
   - 카운트 생략 금지. archive를 별도 작업으로 미루는 것도 금지.
6. 워크트리 → 메인 cp 적용
   - 편집 완료된 파일(+ archive 트리거 시 신규 archive 파일)만 cp. 사용자 미커밋 변경분(화이트리스트 외)은 그대로 보존됨.
7. ExitWorktree(action=remove)로 워크트리 정리
8. 메인에서 30초 내 commit (동시 편집 방지 룰 준수, 위 "PROJECT.md / 화이트리스트 동시 편집 방지" 절차). archive 파일은 `.gitignore`의 `archive/*` 정책으로 untracked 유지(보존 목적이지 git 추적 대상 아님).
```

절차 4번이 옵션 P의 핵심이다. 워크트리 작업 시간(수 분~수십 분)만큼 race window가 길어지므로, cp 적용 직전 한 번 더 메인 최신을 확인해 그 사이의 타 actor commit을 놓치지 않는다. Codex의 "Start in → New worktree" 종료 시 cp 적용 단계에도 동일하게 적용한다(초기 cp 동기화만 생략, 종료 시 재확인은 필수).

원칙:

- **cp는 동기화·적용 목적으로만**: 메인 최신 상태를 워크트리 base로 받아오고, 편집 후 메인에 되돌려놓는 정식 절차다.
- **워크트리에서 처음부터 만들기 금지**: cp 동기화 없이 워크트리에서 새로 작성하면 메인 최신 상태(다른 actor 변경분, 사용자 미커밋분)를 잃는다.
- **작업 대상만 cp**: 전체 화이트리스트를 일괄 cp하면 불필요한 동기화로 race window가 넓어진다.

### cp가 차단될 때

cp 동기화는 정식 절차이므로 도구가 차단했다면 false positive 가능성이 높다. 절대 우회하지 않는다.

```
1. 재시도 1회 — 동일 cp 명령을 한 번 더 실행한다.
   ├─ 통과: cp 동기화 패턴 진행.
   └─ 실패: 2번으로.
2. 사용자에게 즉시 보고 — "cp가 차단됐다. 화이트리스트 cp는 정식 절차이므로
   우회하지 않는다. 권한 허용 또는 다른 결정 필요" 한 줄 보고.
3. 사용자 결정 대기 — 권한 허용/룰 변경/작업 보류 중 사용자가 선택할 때까지 대기.
4. 권한 허용되면 정식 cp 동기화 패턴으로 즉시 복귀. 룰 변경되면 그 룰 따름.
```

**금지되는 우회 (반복 강조)**

- 화이트리스트 cp 없이 산출물만 작성하고 락 등록을 생략
- 스크립트로 메인 파일 in-place replace
- PROJECT.md 직접 편집 회피 후 작업 완료 시점에 한 번에 처리
- "이번에는 어차피 단일 작업이니까" 같은 이유로 락 등록 생략

cp 동기화는 락 등록 visibility + race 방지 + 사용자 미커밋 보존 세 가지를 한 번에 보장한다. 우회는 셋 중 하나 이상을 잃는다.

### 🟢 Codex 워크트리 진입 (안전한 흐름)

```
1. Codex 채팅 시작 전 "Start in" 메뉴 클릭
2. "New worktree" 선택 → 메인의 복사본이 새 디렉토리에 생성됨
3. 메인 디렉토리는 절대 건드리지 않음
```

사전 선택이라 메인의 미커밋 변경에 영향을 주지 않는다.

### 🚨 중간 "Handoff to worktree" 사용 금지

작업 중간에 "Handoff to worktree"는 메인을 `git reset --hard HEAD`로 정리한다. 미커밋 변경과 untracked 파일이 모두 사라진다.

부득이하게 중간 전환이 필요하면:

```
1. git status 로 미커밋/untracked 확인
2. 즉시 git commit 또는 git stash -u
3. working tree clean 확인 후 Handoff
```

권장: 처음부터 "Start in → New worktree"로 시작해 중간 Handoff를 아예 안 쓴다.

### 격리 작업 종료 흐름

```
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

### PROJECT.md / 화이트리스트 동시 편집 방지

메인 직접 수정 화이트리스트 파일(특히 `PROJECT.md`)은 양쪽 actor가 동시에 read–edit–write 하면 마지막 저장자가 앞 변경을 덮어쓴다. Codex와 Claude Code 모두 다음 3단계 절차를 거친다.

**1. 편집 전 — 활성 작업 감지**

```
rtk git log -1 --format="%ar %an : %s" PROJECT.md
```

(대상 파일별로 동일하게 실행)

| 결과 timestamp | 의미 | 행동 |
|---|---|---|
| `0~30 seconds ago` | 거의 확실히 다른 actor 작업 중 | 60초 대기 후 재확인 |
| `1~5 minutes ago` (타 actor) | 진행 중일 수 있음 | 30초 대기 후 1회 재확인 |
| `10 minutes ago`+ 또는 본인 commit | 안전 | 진행 |

2회 연속 활성 신호면 사용자에게 "다른 actor 작업 중으로 보임, 대기/강제 진행" 묻고 결정 따른다. actor가 본인뿐이라고 확신해도 절차는 동일하게 거친다(백그라운드 세션 누락 방지).

**2. 편집 전 — working tree 확인**

```
rtk git status
```

본인이 만든 미커밋만 있어야 한다. 다른 actor의 미커밋 변경 흔적(낯선 파일, 본인 작업 범위 밖 modified)이 보이면 멈추고 사용자에게 보고한다.

**3. 편집 후 — 즉시 commit**

- 편집 직후 30초 내 `rtk git commit`. 지연 금지(지연 시간만큼 race window가 열린다).
- 작업 단위로 여러 화이트리스트 파일을 함께 수정했으면 한 번에 묶어 commit한다(원자성).
- push/pull 충돌 시 `rtk git pull --rebase` 또는 수동 머지. **덮어쓰기·force push 금지**.

같은 규칙은 `PROJECT.md` 외 화이트리스트 전체(`CLAUDE.md`, `AGENTS.md`, `business-context.md`, `DESIGN.md`, `docs/collab-protocol.md`, `docs/templates.md`, `.gitignore`, `.gitattributes`)에 적용한다. 변경 빈도가 가장 높은 `PROJECT.md`가 가장 자주 충돌하므로 `PROJECT.md`를 기본 점검 대상으로 본다.

## 5. 분쟁 처리 & self-handoff

actor 간 이견은 사용자가 직접 결정한다. 절차:

1. 이견 핵심 정리 → 사용자 보고.
2. 사용자 결정 전까지 충돌 영역 작업 멈춤 (락 해제 = Work Board 행 제거).
3. 사용자 지정 결정을 `PROJECT.md` Decisions에 1줄 추가.

세션 A→B 인계 시: `handoff_<slug>.md` 작성(`docs/templates.md` §5 또는 §7), 락 모두 해제, 메인 PROJECT.md 갱신, 메인 commit.

## 6. Archive Rule

### Handoff Log 자동 archive

- **트리거**: `PROJECT.md` Handoff Log가 **10개를 초과**하는 시점(=11번째 항목을 추가하려는 순간).
- **처리자**: 새 Handoff Log 항목을 추가하는 actor. archive 처리는 그 작업의 일부로 수행한다(별도 작업으로 미루지 않는다).
- **이동량**: 가장 오래된 항목부터 **남은 항목이 5개가 될 때까지** 일괄 이동. 결과적으로 새 항목까지 더해 6개로 마감.
- **파일명**: `archive/handoff-YYYY-MM-DD.md` (이동 작업 수행일 기준). 같은 일자에 두 번째 archive가 발생하면 `-2`, 세 번째는 `-3` 접미사.
- **PROJECT.md 표기**: Handoff Log 섹션 최상단에 한 줄 인용(`> 이전 항목 N건(시작~끝)은 archive/<파일명>으로 이동.`) 남긴다. 두 번째 archive가 발생하면 인용 줄을 한 줄에 두 절로 이어 적는다(`이전 N건(...)은 archive/...md로 이동. 추가 M건(...)은 archive/...-2.md로 이동.`).
- archive 파일 구조는 `# Handoff Log Archive — YYYY-MM-DD` 헤더 + 이동 대상 범위 + `## Archived Entries`에 원문 그대로 나열.

**✅ 필수 카운트 체크리스트**

Handoff Log에 새 항목을 추가하기 **직전** 매번 다음을 실행한다. 생략 금지.

```
1. 현재 PROJECT.md Handoff Log 개수 카운트
   - rtk grep -c "^\[20" PROJECT.md
   - 또는 메인 PROJECT.md를 Read해서 시각 패턴 직접 카운트
2. 추가 후 개수 계산: 현재 N + 1
3. 결과 처리
   - N+1 ≤ 10: 항목만 추가
   - N+1 = 11 이상: 트리거 발동
     a. 같은 작업 안에서 archive 절차 함께 수행
     b. 가장 오래된 (N+1 - 5)개 항목을 archive/handoff-YYYY-MM-DD[-N].md로 이동
     c. PROJECT.md Handoff Log 인용 줄 갱신
     d. 본 archive 작업 자체를 새 Handoff Log 항목으로 추가
4. cp 적용 직전 메인 최신 재확인 시점에 한 번 더 카운트(다른 actor가 그 사이 추가했을 가능성)
```

이 체크는 cp 동기화 패턴 §4 "워크트리 → 메인 cp 적용" **직전 단계로 의무화**된다. 카운트 생략으로 11번째를 그냥 추가하는 패턴은 룰 위반이다.

### 협업 인계 문서 archive

- 완료된 협업 인계 문서(`dev_plan_<slug>`, `execution_plan_<slug>`, `handoff_<slug>`)는 `archive/YYYY-MM-DD-<slug>/` 디렉토리로 이동.
- 처리 시점은 해당 slug의 모든 산출물 단계가 끝나고 다음 작업으로 넘어갈 때.
- 역할별 결과물 문서(`final_report`, `risk_notes`, `questions`, `analysis_summary`, `dashboard.html`, `promo_image_*`)는 `output/`에 유지. 새 버전이 필요하면 새 slug.
- `PROJECT.md`에는 위치와 상태만 남긴다.
