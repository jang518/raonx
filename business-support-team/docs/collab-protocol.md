# docs/collab-protocol.md — 협업 프로토콜

경영지원팀 CAE 매출 대시보드 프로젝트의 협업 규칙과 운영 정책. `PROJECT.md`는 현황판이고, 이 문서는 규칙판이다.

자주 보는 문서는 아니다. 새 actor 진입 시 1회 학습 후 필요할 때만 참조한다.

## 1. 역할과 기본 협업 모델

### Actor

| Actor | 기본 담당 | 주로 수정하는 파일 |
|---|---|---|
| Codex | 구현, Excel 데이터 처리, 코드/대시보드/로그인/배포 구성, 렌더링 검증, Claude Code 계획 보완 | `data/`, `scripts/`, `app/`, `server/`, `dashboard/`, `config/`, `deploy/`, `output/execution_plan_<slug>.md`, `output/handoff_<slug>.md`, `output/analysis_summary_<slug>.md`, `output/data_quality_<slug>.md`, `output/ops_guide_<slug>.md` |
| Claude Code | 1차 계획, 데이터 해석 기준 설계, 경영진/경영지원팀 질문 설계, 리스크 점검, 최종 문서화 | `output/dev_plan_<slug>.md`, `output/questions_<slug>.md`, `output/risk_notes_<slug>.md`, `output/dashboard_review_<slug>.md`, `output/config_review_<slug>.md`, `output/deploy_review_<slug>.md`, `output/final_report_<slug>.pdf` |
| User | 최종 판단, 우선순위, actor 간 분쟁 결정, 실데이터 제공, 운영 환경 정보 제공 | 없음 |

원칙:

* 사용자 요청이 기본 협업 모델보다 우선.
* Codex는 Claude Code가 작성 중인 최종 문서/리스크 노트/질문 설계 문서를 임의 수정하지 않음.
* Claude Code는 구현 파일(코드/데이터/대시보드/배포 파일)을 직접 덮어쓰지 않음.
* 실제 Excel 원천 파일은 직접 덮어쓰지 않음.
* 실제 고객사명, 계약 금액, 담당자 정보, 로그인 정보, 사내 IP/포트 등 민감 정보는 공개 저장소에 커밋하지 않음.
* 대시보드는 경영진과 경영지원팀이 사내에서 로그인 후 조회하는 용도다.
* Windows 11 운영 PC + Docker Compose 배포는 확정된 운영 방향이다.

### 기본 흐름

| 단계 | Owner | 내용 | 협업 문서 |
|---|---|---|---|
| 계획 | Claude Code | 복잡한 개발/분석 작업 전 목표·범위·리스크·검증 기준 정리 | `output/dev_plan_<slug>.md` |
| 보완 | Codex | 계획을 구현 가능하게 보완 | `output/execution_plan_<slug>.md` |
| 구현 | Codex | Excel 파싱, 데이터 정규화, 설정 파일, 웹 대시보드, 로그인, Docker 배포 구성 작업 | 작업 파일 |
| 검증 | Codex | 파일 검사, Excel 파싱 검증, 데이터 집계 검증, 렌더링, 로그인, Docker 구성 확인 | `output/handoff_<slug>.md` 또는 Handoff Log |
| 리뷰 | Claude Code | 구현 결과의 데이터 해석, 보안, 운영, 설정 구조 리스크 점검 | `output/risk_notes_<slug>.md`, `output/dashboard_review_<slug>.md`, `output/deploy_review_<slug>.md` |
| 최종 문서화 | Claude Code | 경영진/경영지원팀용 최종 결과물 작성 | `output/final_report_<slug>.pdf` |

예외 (협업 문서 없이 바로 처리):

* 단순 문서 수정, 작은 샘플 데이터 보정, 긴급 수정
* 사용자가 도구를 지정해 즉시 처리하라고 한 작업
* 한 actor가 시작부터 끝까지 처리 가능한 작업

actor 간 이견은 사용자가 직접 우선순위와 담당자를 지정해 해결한다.

## 2. PROJECT.md 갱신 방식

### 헤더 (Goal)

* 프로젝트의 최상위 목표 한 줄. 목표 자체가 바뀔 때만 수정.
* 데이터 상태·작업 phase 라벨은 헤더에 두지 않는다. 작업 phase는 Work Board의 `in_progress`가, 큰 결정은 Decisions가 담는다.
* 운영 환경의 핵심 전제가 바뀌면 `PROJECT.md`의 운영 환경/배포 방향 문구를 수정한다.

  * 예: Windows 11 PC → 별도 서버
  * 예: Docker Compose 배포 → 별도 서버 또는 Windows 서비스 방식으로 변경
  * 예: 사내 전용 → 외부 접근 필요

### Work Board

활성 작업(`in_progress`, `blocked`, `todo`)만 표시.

* 완료 작업은 표에서 제거하고 Handoff Log로 옮긴다.
* 활성 작업이 없으면 표는 비어있는 게 정상.
* 작업 본문은 짧게. 긴 계획은 `output/dev_plan_<slug>.md`.
* 산출물 작업은 짧아도 Work Board 행을 만든다.
* 메타 문서 단순 수정은 Work Board 없이 Handoff Log만 남긴다.

**락 적용 기준 (Work Board 행을 만드는 조건)**

| 작업 유형 | Work Board | Handoff Log |
|---|---|---|
| 산출물 생성·수정 (`output/*`, `data/*`, `scripts/*`, `app/*`, `server/*`, `dashboard/*`, `config/*`, `deploy/*`) | 필수 (짧아도 행 등록) | 종료 시 1줄 |
| 메타 문서 수정 (메인 직접 수정 화이트리스트 파일) | 생략 | 종료 시 1줄 |
| 즉시 처리 (인사·응답·정보 조회, 파일 변경 없음) | 생략 | 생략 |

이유: 충돌 위험이 가장 큰 영역은 `output/*`, `data/*`, `scripts/*`, `app/*`, `server/*`, `dashboard/*`, `config/*`, `deploy/*` 산출물이다. 같은 slug 안에서 Codex와 Claude Code가 순차로 이어받거나, Codex가 설정/앱/배포 파일을 동시에 다룰 수 있으므로 파일 락이 필요하다. 메타 문서는 화이트리스트로 동시 수정 가정이 약하고, Handoff Log만으로 변경 추적이 충분하다.

**Status 본질과 전환 규칙**

| Status | 본질 | 다른 actor에게 주는 신호 | 파일 락 |
|---|---|---|---|
| `todo` | 작업 정의됐고 외부 의존성 없는데 시작 전 | "곧 시작 예정" | 없음 |
| `in_progress` | actor가 능동적으로 작업 중 | "이 파일 만지지 마" | 있음 (`Files locked` 기재) |
| `blocked` | 외부 input(사용자 결정·다른 actor 산출물·데이터 도착·운영 환경 확인) 없이 진행 불가 | "이거 풀리기 전엔 시작·재개 불가" | 없음 |
| `done` | 완료 | 표에서 제거 후 Handoff Log로 이동 | - |

전환 규칙:

* `todo` → `in_progress`: 작업 시작 시점. 이 프로젝트는 단일 actor 순차 작업이 기본이라 보통 `todo`를 거치지 않고 바로 `in_progress`로 간다.
* `in_progress` → `blocked`: 작업 중 외부 input 필요해짐. **`Files locked` 비우고 락 해제**한다.
* `blocked` → `in_progress`: 외부 input 도착. 락 재취득.
* `in_progress` → `done`: 작업 완료. 표에서 행 제거 + Handoff Log 1줄.

`in_progress`는 푯말("회의실 사용 중"), `blocked`는 안내문("회의 자체가 손님 도착 대기로 못 열림"). 둘 다 표에 같이 있지만 다른 actor가 받는 신호와 락 여부가 다르다.

**blocked 풀기 흐름** (Owner=User blocked 항목 기준)

사용자가 결정을 한 줄로 알리면 actor가 다음을 한 번에 처리한다:

1. 해당 행을 Work Board에서 제거
2. 결정 내용을 Decisions에 1줄 추가

   * 예: `- Docker 배포는 Windows 11 PC에서 Docker Desktop + WSL 2 기반으로 진행한다. (W2 풀림, YYYY-MM-DD)`
3. Handoff Log에 처리 1줄 추가

   * 예: `changed: W2 풀림 + Decisions 추가 / verified: 사용자 결정 반영 / next: docker-deploy 작업 재개`

사용자가 "결정 보류" 또는 "당분간 안 정함"이라고 하면 행만 제거하고 Decisions 추가는 생략한다. 필요해지면 다시 등록한다.

사용자 결정 사항이 핵심 룰(`PROJECT.md`, `business-context.md`, 보안 규칙 등)과 충돌 가능성이 있으면 처리 직후 사용자에게 짧게 짚는다. 자동 침묵 금지.

Owner가 User가 아닌 blocked(다른 actor 산출물 대기·데이터 도착 대기)는 해당 actor의 작업 종료 시점에 함께 풀린다.

### 파일 락

* Work Board의 `in_progress` 상태가 곧 파일 락이다.
* `Files locked` 컬럼에 잡은 파일을 기재한다.
* 다른 actor가 같은 파일을 잡고 있으면 그 작업이 끝날 때까지 시작하지 않는다.
* 작업이 끝나면 Work Board 행을 제거 = 락 자동 해제.
* 락이 풀리지 않은 채 다른 actor가 만져야 하면 사용자에게 알리고 결정을 기다린다.
* 락 잡은 actor의 세션이 중단되면 다음 세션은 락 해제 후 재진입한다.
* 실제 원천 Excel 파일은 락을 잡더라도 직접 덮어쓰지 않는다. 읽기 원본으로만 사용한다.

### Decisions

협업 정책이나 합의를 1줄로 기록. 한 번 정하면 거의 불변. 자주 바뀌는 항목은 Decisions가 아니라 Work Board에 둔다.

Decisions에 남길 만한 항목:

* 대시보드 사용자는 경영진 + 경영지원팀으로 제한
* 로그인 기능 포함
* Windows 11 운영 PC 사용
* Docker Compose 기반 배포 확정
* 원천 Excel 직접 수정 금지
* `팀별실적` + `목표` 중심 계산
* `실적G`는 초기 대시보드 계산 범위에서 제외
* 대시보드 항목은 설정 파일로 관리
* 실제 데이터 외부 공개 금지

### Handoff Log

작업 종료 시 1줄만. 최근 10개 유지.

형식:

`[YYYY-MM-DD HH:mm] actor / changed: ... / verified: ... / next: ...`

`actor`는 `Codex`, `Claude Code`, `User` 중 하나. 작업명은 `changed:`에 짧게.

## 3. 파일 네이밍 규칙

협업 문서와 역할별 결과물 문서는 작업 식별을 위해 slug를 붙인다. `ls`만으로 어느 작업의 산출물인지 보이게 한다.

### 슬러그 규칙

* 형식: `<doc_type>_<slug>.<ext>`
* slug는 kebab-case, 영문 소문자 + 숫자 + 하이픈만.
* 한 작업 = 한 slug 공유.
* slug 예시: `excel-profile`, `data-normalization`, `dashboard-mvp`, `login-auth`, `docker-deploy`, `config-driven-dashboard`, `monthly-refresh`
* 같은 작업의 후속이면 동일 slug 유지, 다른 작업이면 새 slug.

### 협업 인계 문서

| 문서 | Owner | 용도 |
|---|---|---|
| `output/dev_plan_<slug>.md` | Claude Code | Codex가 구현할 1차 계획 |
| `output/execution_plan_<slug>.md` | Codex | Claude Code 계획을 보완한 실행 계획 |
| `output/handoff_<slug>.md` | Codex 또는 현재 작업자 | 다음 담당자가 이어받기 위한 인수인계 |

### 역할별 결과물 문서

| 문서 | 생성 트리거 | Owner |
|---|---|---|
| `output/questions_<slug>.md` | 경영진/경영지원팀 질문 설계 | Claude Code |
| `output/risk_notes_<slug>.md` | 데이터/보안/운영/해석 리스크 점검 | Claude Code |
| `output/dashboard_review_<slug>.md` | 대시보드 화면·지표 검토 | Claude Code |
| `output/config_review_<slug>.md` | 설정 기반 구조 검토 | Claude Code |
| `output/deploy_review_<slug>.md` | Windows 11 PC + Docker/운영 배포 검토 | Claude Code |
| `output/excel_profile_<slug>.md` | Excel 구조 분석 | Codex |
| `output/data_quality_<slug>.md` | 데이터 품질 검증 | Codex |
| `output/analysis_summary_<slug>.md` | Codex 데이터 분석 요약 | Codex |
| `output/final_report_<slug>.pdf` | 경영진/경영지원팀용 최종 보고서 | Claude Code |
| `output/ops_guide_<slug>.md` | 운영/재시작/백업 가이드 | Codex |
| `app/*`, `server/*`, `dashboard/*` | 사내 웹 대시보드 구현 | Codex |
| `config/data-mapping.yml` | Excel 시트/컬럼/팀명 매핑 | Codex |
| `config/metrics.yml` | KPI/목표/기여도 계산식 | Codex |
| `config/dashboard.yml` | KPI/차트/테이블/필터 구성 | Codex |
| `deploy/compose.yaml` | Docker Compose 배포 구성 | Codex |
| `.env.example` | 환경 변수 예시 | Codex |
| `deploy/README.md` | 배포/운영 안내 | Codex |

각 문서의 본문 섹션 구조는 `docs/templates.md` 참조.

### 라이프사이클

* 작업이 끝나 살아있는 인계 문서가 아니게 되면 `dev_plan_<slug>.md`, `execution_plan_<slug>.md`, `handoff_<slug>.md`는 `archive/YYYY-MM-DD-<slug>/`로 이동.
* 역할별 결과물 문서(`final_report`, `risk_notes`, `questions`, `analysis_summary`, `dashboard_review`, `config_review`, `deploy_review`, `data_quality`, `ops_guide`)는 `output/`에 유지. 새 버전이 필요하면 새 slug.
* 앱/서버/설정/배포 파일은 현재 운영 버전을 유지한다.
* 새 작업 시작 시 새 slug. 기존 인계 문서를 덮어쓰지 않는다.
* 실제 원천 Excel은 `data/raw/`에 보관하고, 정제 결과는 `data/processed/`, DB, 캐시 파일 등으로 분리한다.
* 실제 데이터가 포함된 파일은 공개 저장소에 커밋하지 않는다.

## 4. 협업 환경 정책

협업 모델은 **옵션 B (PROJECT.md 중앙 락, 메인 SSOT)**.

이유:

* 소규모 actor 구조: Claude Code, Codex, User
* 단일 사용자가 빠르게 현황 파악 필요
* PR/CI 없는 초기 개발 환경
* 경영지원팀 대시보드 구축은 동시 병렬보다 직렬 작업이 안전
* 실제 Excel 데이터, 로그인, 배포 설정 등 민감 영역이 있어 충돌과 유출 방지가 중요
* 사용자 인지 부하와 의사결정 의존성 때문에 병렬은 예외로 제한

actor 4명+ 또는 동시 작업 빈번 시 재검토.

### 작업 환경

| Actor | 기본 환경 | 격리 환경 |
|---|---|---|
| Codex | 메인 디렉토리에서 직접 (Work locally) | 큰 작업 / 사용자 명시 지시 시 채팅 시작 전 "Start in → New worktree" |
| Claude Code | 백그라운드 세션은 워크트리 자동 | 라이브 세션은 메인 직접 가능 |

격리 작업 원칙:

* 화이트리스트 파일(`PROJECT.md`, `CLAUDE.md`, `AGENTS.md`, `business-context.md`, `DESIGN.md`, `docs/collab-protocol.md`, `docs/templates.md`, `.gitignore`, `.gitattributes`)은 메인이 단일 진실 원천이다.
* 산출물 작업이라도 먼저 `PROJECT.md` Work Board에 락을 등록해 다른 actor에게 visible하게 만든 뒤 진행한다.
* 워크트리 작업 시에는 작업 대상 화이트리스트 파일만 동기화하고, 적용 직전 메인 최신 상태를 재확인한다.
* 작업 중간에 환경을 바꾸는 방식은 피한다. 필요한 경우 먼저 현재 변경사항을 안전하게 저장한 뒤 사용자 결정에 따른다.
* 워크트리 격리 영역은 `data/`, `scripts/`, `output/`, `archive/`, `app/`, `server/`, `dashboard/`, `config/`, `deploy/`다.

### 안전장치

1. 격리 환경 진입 전 메인 PROJECT.md Work Board에 작업 행을 먼저 등록(Status `in_progress`, `Files locked` 명시).
2. 격리 환경 종료 시 cp/머지 대상에서 메인 직접 수정 화이트리스트를 제외.
3. 사용자 라이브 세션 중 백그라운드 multi-session 자제. 사용자 명시 지시 시 예외.
4. 실제 Excel 원천 파일과 민감 설정 파일은 커밋 대상인지 반드시 확인.
5. `.env`, DB 파일, 원천 Excel, 운영 로그, 사내 IP/포트가 포함된 문서는 `.gitignore` 대상인지 확인.

### PROJECT.md / 화이트리스트 동시 편집 방지

메인 직접 수정 화이트리스트 파일은 양쪽 actor가 동시에 read–edit–write 하면 마지막 저장자가 앞 변경을 덮어쓴다. Codex와 Claude Code 모두 편집 전 활성 작업과 working tree 상태를 확인하고, 편집 후 즉시 commit한다.

같은 규칙은 `PROJECT.md` 외 화이트리스트 전체에 적용한다. 변경 빈도가 가장 높은 `PROJECT.md`가 가장 자주 충돌하므로 `PROJECT.md`를 기본 점검 대상으로 본다.

## 5. 분쟁 처리 & self-handoff

actor 간 이견은 사용자가 직접 결정한다. 절차:

1. 이견 핵심 정리 → 사용자 보고.
2. 사용자 결정 전까지 충돌 영역 작업 멈춤.
3. 멈출 때는 락 해제 = Work Board 행 제거.
4. 사용자 지정 결정을 `PROJECT.md` Decisions에 1줄 추가.
5. 필요하면 Handoff Log에 결정 반영 1줄 추가.

분쟁 예시:

* Docker 배포 구현 방식이 Windows 11 운영 PC 환경 또는 회사 보안 정책과 맞지 않는 경우
* `실적G` 시트가 없는데 FCST 계산을 강제로 포함하려는 경우
* 월 목표가 없는데 월 목표 기반 진행률을 표시하려는 경우
* KPI/차트/테이블 항목을 설정 파일이 아니라 코드에 하드코딩하려는 경우
* 실제 원천 Excel 또는 민감 정보를 커밋하려는 경우
* 로그인 범위와 권한 구분이 불명확한 경우
* Windows 11 운영 PC 환경이 확인되지 않았는데 운영 배포 문서를 확정하려는 경우

세션 A→B 인계 시:

* `handoff_<slug>.md` 작성
* 락 모두 해제
* 메인 PROJECT.md 갱신
* 메인 commit

## 6. Archive Rule

### Handoff Log 자동 archive

* **트리거**: `PROJECT.md` Handoff Log가 **10개를 초과**하는 시점(=11번째 항목을 추가하려는 순간).
* **처리자**: 새 Handoff Log 항목을 추가하는 actor. archive 처리는 그 작업의 일부로 수행한다. 별도 작업으로 미루지 않는다.
* **이동량**: 가장 오래된 항목부터 **남은 항목이 5개가 될 때까지** 일괄 이동. 결과적으로 새 항목까지 더해 6개로 마감.
* **파일명**: `archive/handoff-YYYY-MM-DD.md` (이동 작업 수행일 기준). 같은 일자에 두 번째 archive가 발생하면 `-2`, 세 번째는 `-3` 접미사.
* **PROJECT.md 표기**: Handoff Log 섹션 최상단에 한 줄 인용을 남긴다.
* archive 파일 구조는 `# Handoff Log Archive — YYYY-MM-DD` 헤더 + 이동 대상 범위 + `## Archived Entries`에 원문 그대로 나열.

**필수 카운트 체크리스트**

Handoff Log에 새 항목을 추가하기 직전 매번 다음을 실행한다. 생략 금지.

1. 현재 PROJECT.md Handoff Log 개수 카운트
2. 추가 후 개수 계산: 현재 N + 1
3. N+1 ≤ 10이면 항목만 추가
4. N+1 = 11 이상이면 archive 절차를 같은 작업 안에서 수행
5. cp 적용 직전 메인 최신 재확인 시점에 한 번 더 카운트

### 협업 인계 문서 archive

* 완료된 협업 인계 문서(`dev_plan_<slug>`, `execution_plan_<slug>`, `handoff_<slug>`)는 `archive/YYYY-MM-DD-<slug>/` 디렉토리로 이동.
* 처리 시점은 해당 slug의 모든 산출물 단계가 끝나고 다음 작업으로 넘어갈 때.
* 역할별 결과물 문서(`final_report`, `risk_notes`, `questions`, `analysis_summary`, `dashboard_review`, `config_review`, `deploy_review`, `data_quality`, `ops_guide`)는 `output/`에 유지. 새 버전이 필요하면 새 slug.
* 현재 운영 중인 코드/설정/배포 파일(`app/*`, `server/*`, `dashboard/*`, `config/*`, `deploy/*`)은 archive하지 않는다.
* `PROJECT.md`에는 위치와 상태만 남긴다.