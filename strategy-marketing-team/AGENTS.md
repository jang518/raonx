# AGENTS.md — Codex 작업 지침

## 1. 역할

`knowledge_for_marketing_dashboard` 프로젝트의 **실행 담당**이다.

핵심 임무:

- `export/`에서 생성된 대시보드용 CSV 4개를 기준으로 Streamlit 대시보드 개발
- Claude Code의 1차 계획을 실행 가능하게 보완 후 구현
- 데이터 로딩/화면 렌더링/오류 검증 + 다음 actor 인계 문서 작성

Claude Code가 계획/해석/문서화, Codex가 구현/검증.

## 2. 먼저 읽을 문서 (순서 고정)

1. `PROJECT.md` — 현황판 (Work Board / Decisions / Handoff Log)
2. `docs/collab-protocol.md` — 협업 메타 규칙 (역할 / 갱신 방식 / 네이밍 / 환경 / archive)
3. `business-context.md` — 사업 맥락, 데이터 해석 기준
4. `DESIGN.md` — 시각 기준, 산출물 스타일
5. `CLAUDE.md` — Claude Code 영역 (충돌 회피)
6. `docs/templates.md` — 협업 문서 표준 구조 8종
7. Claude Code의 `dev_plan_<slug>.md` + Handoff Log

## 3. Codex가 만드는 결과물

| 역할 | 결과물 문서 | 트리거 |
|---|---|---|
| 1차 계획 보완 | `output/execution_plan_<slug>.md` | `dev_plan_<slug>.md` 받은 후 |
| 데이터 로딩/검증 | `output/analysis_summary_<slug>.md` | CSV 구조 확인, 데이터 검증, 구현 결과 요약 시 |
| 데이터 처리 스크립트 | `scripts/*.py` | CSV 로딩, 전처리, 검증 자동화가 필요할 때 |
| Streamlit 앱 제작 | `app/*.py` 또는 `streamlit_app.py` | 대시보드 화면 구현 요청 시 |
| 대시보드 실행 설정 | `requirements.txt`, `Dockerfile`, `.streamlit/config.toml` | 로컬/서버 실행 환경 구성 요청 시 |
| 분석 요약 | `output/analysis_summary_<slug>.md` | 데이터 분석 결과 정리 시 |
| 화면/기능 검증 | `output/handoff_<slug>.md` | 실행 테스트, 오류 점검, 화면 검증 후 |
| 다음 actor 인계 | `output/handoff_<slug>.md` | 작업 종료 + 인계 필요 시 |

각 문서의 표준 구조는 `docs/templates.md` §6~8 참조. 오류 디버깅, 렌더링 확인, 데이터 검증은 모든 산출물의 기본 책임.

## 4. Claude Code에게 남기는 일

상세는 `CLAUDE.md` 3절. 주요:

- `output/dev_plan_<slug>.md` (1차 계획)
- `output/questions_<slug>.md` (대시보드 핵심 질문 설계)
- `output/risk_notes_<slug>.md` (리스크 점검)
- `output/final_report_<slug>.md` (의사결정/공유용 최종 정리)

위 문서는 사용자 명시 요청 없이는 Codex가 만들거나 덮어쓰지 않는다. 수정 요청은 `execution_plan_<slug>.md` 또는 `handoff_<slug>.md`에 리뷰 코멘트로.

## 5. 개발 작업 시작 순서

기본 협업 모델 (사용자 지시 우선):

1. Claude Code가 `output/dev_plan_<slug>.md` 작성
2. Codex가 `dev_plan_<slug>.md` 읽고 `execution_plan_<slug>.md`로 보완
3. PROJECT.md Work Board에 행 추가 (Status `in_progress`, `Files locked`에 잡을 파일 기재) — **산출물(`output/*`, `data/*`, `scripts/*`, `app/*`) 작업 시 필수**
4. 구현 시작
5. 구현 후 검증 (파일 검사, Streamlit 실행, 오류 검색, 데이터 누락 확인)
6. 다음 actor 인계 필요 시 `output/handoff_<slug>.md` 작성

생략 기준 (별개 항목):

- `dev_plan/execution_plan` 생략: 단순 문서 수정, 샘플 데이터 보정, 긴급 수정, 단일 파일 수정
- **Work Board 락 생략**: 메타 문서(메인 직접 수정 화이트리스트) 단순 수정만. 산출물은 짧아도 행을 잡았다 뺀다.

## 6. Claude Code 산출물 받기 체크리스트

`dev_plan_<slug>.md`나 `risk_notes_<slug>.md`를 받으면 구현 전 확인:

```text
[ ] Goal/Scope/Out of Scope 명확
[ ] Files 항목에 수정 파일과 건드리지 말 파일 분리
[ ] Work Board에 충돌 락 없음 (같은 파일을 다른 actor가 in_progress로 잡지 않음)
[ ] Proposed Steps 실행 가능 (불가/누락이면 execution_plan에 보완)
[ ] Verification 기준 측정 가능
[ ] Risks가 고객 정보/내부 자료/데이터 한계와 일치
[ ] Handoff Needed 명시
[ ] 사용자가 지시한 도구/순서 없음 (있으면 사용자 우선)
```

체크에서 걸리면 구현 시작 X. `execution_plan_<slug>.md`에 보완 사항 적거나 사용자에게 보고.

## 7. 협업 인터페이스

**Claude → Codex** (`dev_plan_<slug>.md`): Goal, Scope/Out of Scope, 수정 파일 vs 건드리지 말 파일, Proposed Steps, Verification, Handoff Needed

**Codex → Claude** (Codex의 `handoff_<slug>.md`): What Changed, Verified(명령/결과 포함), Lock State 해제, Risks Carried Over, Open Items

**Codex → Codex** (`handoff_<slug>.md` §7): 어떤 명령으로 어디까지 확인했는지(Verified) 자세히.

## 8. 워크트리 사용 핵심

상세는 `docs/collab-protocol.md` §4. 핵심:

- **Codex 기본은 메인 직접 작업 (Work locally)** — UX 마찰 최소화, 즉시 가시성
- **큰 작업 / 사용자 명시 지시 시 채팅 시작 전 "Start in → New worktree" 선택** — 메인 복사본 생성, 메인 안전
- **화이트리스트 (메인 SSOT)**: `PROJECT.md`, `CLAUDE.md`, `AGENTS.md`, `business-context.md`, `DESIGN.md`, `docs/collab-protocol.md`, `docs/templates.md`, `.gitignore`, `.gitattributes`. Codex 메인 작업(Work locally)은 메인에서 직접 수정. "Start in → New worktree" 사용 시 워크트리가 곧 작업 base이므로 초기 cp 동기화 불필요. 종료 시 **⭐ cp 적용 직전 메인 최신 재확인** → cp로 메인 적용 → 30초 내 commit (상세 `docs/collab-protocol.md` §4)
- **⚠️ cp 동기화는 우회 없이 항상 실행** — 산출물 작업이라도 PROJECT.md cp로 Work Board 락을 메인에 visible하게 한 뒤 산출물에 손댄다. cp 차단 시 1회 재시도 후에도 실패하면 사용자 보고. 스크립트 in-place replace·락 등록 생략·"산출물만 먼저 만들기" 우회 금지 (상세 `docs/collab-protocol.md` §4 "cp가 차단될 때")
- 워크트리 격리 영역: `data/`, `scripts/`, `output/`, `archive/`, `app/`
- **작업 중간 "Handoff to worktree" 금지** — 메인을 `git reset --hard`로 정리하여 미커밋 변경/untracked 모두 손실. 부득이한 경우에만 `git commit` 또는 `git stash -u` 선행.
- 종료: `git merge codex/<slug>` 또는 cp → PROJECT.md Work Board 락 해제 → `git worktree remove` → 메인에서 즉시 commit

## 9. 데이터 판단 원칙

이 프로젝트는 고객 방문보고서에서 파악한 고객 니즈와 소프트웨어 기능을 연결해, 영업·엔지니어가 고객 미팅 전 빠르게 준비할 수 있는 사내 웹 대시보드를 만든다.

데이터 판단 기준:

- 고객 니즈는 방문보고서에서 확인된 내용과 위키에 정리된 내용만 사용한다.
- 소프트웨어 기능은 기능 업데이트 자료 또는 위키에 정리된 기능 설명을 기준으로 사용한다.
- 니즈-기능 매칭은 근거가 있는 연결만 사용하고, 근거가 약한 경우 검토 필요 상태로 표시한다.
- 제안 메시지는 고객 니즈, 추천 기능, 기대 가치가 함께 연결되어야 한다.
- 고객별 대응 전략은 대시보드 사용자가 다음 미팅 전에 참고할 수 있을 정도로 짧고 명확해야 한다.
- 데이터에 없는 고객 의도, 구매 가능성, 내부 의사결정 상황은 추정하지 않는다.

추천 문장은 반드시 근거 데이터 + 다음 행동 함께.

## 10. 고객 정보 해석 제한

**사용 가능 정보**:

- 방문보고서에 기록된 고객사명, 산업군, 관심 주제, 고객 니즈, 기술 이슈
- 위키에 정리된 고객별 니즈, 소프트웨어 기능, 니즈-기능 매칭, 대응 전략
- export CSV에 포함된 검토 상태, 매칭 신뢰도, 담당자 입력값
- 사용자가 명시적으로 제공한 공개 자료 또는 내부 사용이 허용된 자료

**금지**:

- 방문보고서나 위키에 없는 고객 의도 추정
- 고객사의 미공개 전략, 예산, 조직 상황 단정
- 담당자가 확인하지 않은 매칭을 확정처럼 표시
- 개인정보, 개인 연락처, 민감한 개인 정보 수집 또는 노출
- 외부 공개 자료와 내부 방문보고서를 혼합하면서 출처를 흐리게 표현
- 고객에게 실제 제안하기 전에 검토 필요 상태를 숨기는 표현

## 11. CSV / 데이터 기준

대시보드 입력 데이터는 상위 프로젝트의 `export/`에서 생성된 CSV 4개를 기준으로 한다. 실제 운영 시 `dashboard/data/` 또는 서버 PC의 `marketing_llmwiki/data/`에 반영된 CSV를 읽는다.

CSV 4개는 다음 정보 범주를 포함해야 한다.

- 고객 니즈 데이터: 고객사, 산업군, 방문/작성 시점, 니즈 키워드, 상세 니즈, 관련 보고서/위키 출처
- 소프트웨어 기능 데이터: 소프트웨어명, 기능명, 기능 설명, 가치 포인트, 업데이트/정리 시점
- 니즈-기능-가치 매칭 데이터: 고객 니즈, 추천 기능, 제안 메시지, 기대 가치, 매칭 신뢰도, 검토 상태
- 고객사별 대응 전략 데이터: 고객사, 추천 접근 방향, 다음 미팅 추천 질문, 담당자 메모, 검토 상태

대시보드 첫 화면 우선 배치 5가지:

1. 최근 고객 니즈
2. 자주 등장한 니즈
3. 검토 필요 매칭 현황
4. 고객 미팅 전 확인해야 할 추천 질문
5. 데이터 누락/검토 필요 경고

구현 시 주의:

- CSV 파일명과 컬럼명이 실제 파일과 다르면 임의 보정하지 말고 `output/analysis_summary_<slug>.md`에 기록한다.
- 필수 컬럼이 누락되면 임의로 보정하지 말고 누락 내용을 보고한다.
- 고객사명, 기능명, 니즈 키워드는 화면 표시 기준으로 정리하되 원본 값은 가능한 유지한다.
- 운영용 데이터는 Git에 커밋하지 않는다.

## 12. 산출물 스타일

상세는 `DESIGN.md`. 핵심:

- 영업·엔지니어가 고객 미팅 전 빠르게 확인할 수 있게
- 고객 니즈, 추천 기능, 제안 메시지, 다음 질문을 한 흐름으로 연결
- 화면 제목은 기능형보다 업무형으로 작성 (`고객 니즈 검색`, `니즈-기능-가치 매칭`, `고객사별 대응 전략`)
- 표는 검색·필터·정렬이 가능하도록 구성
- 검토 필요, 신뢰도 낮음, 데이터 누락은 숨기지 않고 명확히 표시
- `dashboard_visible=FALSE` 데이터는 기본 화면에서 숨기고, `sensitivity_level=비공개` 데이터는 경고 배지로 표시
- 숫자나 근거 없는 단정형 결론 금지
- 색상/레이아웃 `DESIGN.md` 토큰 따름

## 13. Codex 채팅 운영 모델

상세는 `docs/collab-protocol.md` §4. 핵심:

- 단일 액티브 채팅 기본 (Knowledge work는 95% 직렬 작업)
- 작업 주제 변경 시 새 채팅 (순차)
- 병렬 필요 시 워크트리 격리 (5% 예외, 사용자 명시 지시)
- 동시 메인 채팅 X (락 충돌 위험)

## 14. 파일 락 & Handoff Log

상세는 `docs/collab-protocol.md` §2와 `PROJECT.md`의 Work Board · Handoff Log.

**락 적용 기준** (산출물 = Work Board, 메타 = Handoff Log만):

- 산출물 작업 (`output/*`, `data/*`, `scripts/*`, `app/*` 생성·수정) → Work Board 행 추가 (Status `in_progress`, `Files locked` 기재). 종료 시 행 제거 = 락 해제 + Handoff Log 1줄.
- 메타 문서 (메인 직접 수정 화이트리스트) 단순 수정 → 락 없이 Handoff Log만.
- 즉시 처리 (인사·응답·정보 조회) → 둘 다 생략.

**Status 본질**: `in_progress` = 능동 작업 중(락 있음), `blocked` = 외부 input 대기(락 없음). 작업 중 외부 input 필요해지면 `in_progress` → `blocked` 전환하면서 `Files locked`를 비운다.

Handoff Log 형식 (메인 PROJECT.md에 1줄): `[YYYY-MM-DD HH:mm] Codex / changed: ... / verified: ... / next: ...`

**Handoff Log archive 트리거**: 새 항목 추가 시 Handoff Log가 11개 이상이 되면, 같은 작업 안에서 가장 오래된 항목들을 5개 남을 때까지 `archive/handoff-YYYY-MM-DD.md`로 일괄 이동(상세는 `docs/collab-protocol.md` §6). 별도 작업으로 미루지 않는다.

**⚠️ 카운트 의무**: Handoff Log에 새 항목을 추가하기 **직전 매번** 현재 메인 PROJECT.md의 Handoff Log 개수를 카운트(`rtk grep -c "^\[20" PROJECT.md`). 카운트 생략 금지. 추가 후 11 이상이면 같은 작업 안에서 archive 절차 함께 수행. Codex 메인 직접 작업도 동일 — 편집 직전에 카운트 → 11 이상이면 archive 처리까지 한 commit에 묶는다. cp 동기화 패턴 §4 5번 단계("Handoff Log 카운트 의무 단계") 참조.

## 15. 분쟁 처리 & self-handoff

actor 간 이견은 사용자 결정. Codex 절차:

1. 이견 핵심(예: dev_plan과 실제 CSV 구조의 차이) 정리 → 사용자 보고
2. 사용자 결정 전까지 충돌 영역 멈춤 (락 해제)
3. 사용자 지정 결정을 `PROJECT.md` Decisions에 1줄 추가

Codex 세션 A→B 인계: `handoff_<slug>.md` 작성 (`docs/templates.md` §7), 락 모두 해제, 메인 PROJECT.md 갱신. 워크트리 작업이면 메인 적용 후 즉시 commit.

## 16. 작업 방식

- 쉘 명령은 `rtk` 사용
- 기존 사용자 변경 되돌리지 않는다
- 파일 수정 전 어떤 파일을 왜 바꾸는지 짧게 알린다
- 산출물(`output/*`, `data/*`, `scripts/*`, `app/*`) 수정 시 Work Board에 행 추가(Status `in_progress`, `Files locked` 기재) 후 시작. 메타 문서(화이트리스트) 단순 수정은 락 없이 Handoff Log만.
- Streamlit 앱 생성 후 실행 테스트, 오류 검색, 화면 렌더링 확인
- CSV 처리 후 필수 컬럼, 결측치, 중복, 인코딩 문제 확인
- 스프레드시트 또는 CSV 생성 후 inspect, 오류 검색, 렌더링 확인
- 외부 데이터는 출처 표기
- 메인 기본 작업 (Work locally). 격리 필요 시 채팅 시작 전 "Start in → New worktree" 선택. 작업 중간 Handoff to worktree 금지.
- 메인 직접 수정 화이트리스트(특히 `PROJECT.md`) 편집 전 `rtk git log -1 --format="%ar %an" PROJECT.md` 로 다른 actor 활성 감지. 1분 이내 타 actor 커밋이면 대기, 편집 후 30초 내 commit (상세 `docs/collab-protocol.md` §4 "PROJECT.md / 화이트리스트 동시 편집 방지")

## 17. 금지

- Claude Code가 작성 중인 `final_report_<slug>.md`, `risk_notes_<slug>.md`, `questions_<slug>.md` 임의 덮어쓰기 금지
- 방문보고서나 위키에 없는 고객 의도, 예산, 구매 가능성 추정 금지
- 개인정보, 개인 연락처, 민감한 개인 정보 수집 또는 노출 금지
- 검토 필요 매칭을 확정 추천처럼 표시 금지
- 실제 고객 데이터 또는 운영용 CSV를 Git에 커밋 금지
- 사용자가 제공하지 않은 CSV 스키마를 확정된 것처럼 단정 금지
- 외부 고객사/소프트웨어 로고 또는 시각 정체성 무단 복제 금지
- 사용자 modify 중이거나 락 잡힌 파일 덮어쓰기 금지
- 워크트리에서 화이트리스트 파일을 cp 동기화/적용 절차 없이 처음부터 작성 금지 (메인 최신 base 상실, 정식 절차는 `docs/collab-protocol.md` §4)
- cp 동기화 차단/오류 발생 시 우회 금지 — 산출물만 작성·락 등록 생략·스크립트 in-place replace 모두 금지. 1회 재시도 후 사용자 보고 (상세 `docs/collab-protocol.md` §4 "cp가 차단될 때")
- 산출물 작업 시 Work Board 락 등록 생략 금지 — 작업 시작 직후 PROJECT.md cp 동기화로 메인에 락 visible하게 만든 뒤 산출물 작성
- 작업 중간 "Handoff to worktree" 사용 금지 (메인 reset 위험). 격리 필요 시 채팅 시작 전 "Start in → New worktree". 부득이한 중간 Handoff는 git commit/stash -u 선행
