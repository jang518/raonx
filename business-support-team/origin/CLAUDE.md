# CLAUDE.md — Claude Code 작업 지침

## 1. 역할

오브온(OBON) 성장 대시보드 프로젝트의 **계획, 전략 판단, 최종 결과물 문서 작성** 담당이다.

핵심 임무:
- 복잡한 개발/분석 작업 전 1차 계획 설계 → Codex에 인계
- 대표가 5분 안에 의사결정할 수 있는 최종 결과물 문서 작성
- 데이터 해석의 과잉/단정/추정을 미리 잡아내기
- 대표가 봐야 할 질문과 분석 프레임 설계

Codex가 구현/검증, Claude Code가 계획/해석/문서화. 코드/데이터/대시보드 파일은 직접 만들거나 덮어쓰지 않는다.

## 2. 먼저 읽을 문서 (순서 고정)

1. `PROJECT.md` — 현황판 (Work Board / Decisions / Handoff Log)
2. `docs/collab-protocol.md` — 협업 메타 규칙 (역할 / 갱신 방식 / 네이밍 / 환경 / archive)
3. `business-context.md` — 사업 맥락, 데이터 해석 기준, 금지 사항
4. `DESIGN.md` — 시각 기준, 산출물 스타일
5. `AGENTS.md` — Codex 영역 (충돌 회피)
6. `docs/templates.md` — 협업 문서 표준 구조 8종
7. Codex 산출물 + Handoff Log

## 3. Claude Code가 만드는 결과물

| 역할 | 결과물 문서 | 트리거 |
|---|---|---|
| 1차 계획 수립 | `output/dev_plan_<slug>.md` | 복잡한 작업 전 |
| 대표 질문 / 분석 프레임 | `output/questions_<slug>.md` | 분석 방향 정할 때 |
| 데이터/전략 리스크 점검 | `output/risk_notes_<slug>.md` | 리스크 리뷰 / 결정 직전 |
| 최종 전략보고서 | `output/final_report_<slug>.md` | Codex 분석 후 대표 보고 |
| 세션 간 인계 | `output/handoff_<slug>.md` | Claude Code 세션 A→B |

각 문서의 표준 구조는 `docs/templates.md` §1~5 참조.

## 4. Codex에게 넘기는 일

상세는 `AGENTS.md` 3절. 주요 산출물:
- `data/monthly_ops.xlsx` (sales/survey/instagram/Data Notes 시트)
- `output/execution_plan_<slug>.md`, `output/analysis_summary_<slug>.md`
- `output/dashboard.html`, `output/promo_image_<slug>_<NN>.png`
- `output/handoff_<slug>.md` (Codex 작성분)

수정 요청은 `dev_plan_<slug>.md` 또는 `handoff_<slug>.md`에 리뷰 코멘트로.

## 5. 1차 계획 작성 가이드

복잡한 작업은 시작 전 `output/dev_plan_<slug>.md` 작성. 구조는 `docs/templates.md` §1.

`dev_plan_<slug>.md` 생략 가능:
- 단순 문서 수정, 작은 샘플 데이터 보정, 긴급 수정
- 사용자가 즉시 처리 지시한 명확한 작업
- 한 actor가 시작부터 끝까지 처리 가능한 작업

`dev_plan` 생략과 Work Board 락은 별개다. 산출물(`output/*`, `data/*`)을 만지면 `dev_plan`이 생략돼도 Work Board 행은 등록한다 (§11 참조).

Codex가 이 계획을 받아 `execution_plan_<slug>.md`로 보완 후 구현.

## 6. Codex 산출물 받기 체크리스트

Codex가 `analysis_summary`, `dashboard.html`, `execution_plan`, `handoff`를 넘겼을 때 확인:

```
[ ] handoff에 어떤 파일이 어떻게 바뀌었는지 명시
[ ] Codex verified 항목이 실제 검증되었는가 (필요 시 재확인)
[ ] 락 잡았던 파일 모두 해제됨 (PROJECT.md Work Board 행 제거)
[ ] analysis_summary가 business-context.md §6 의사결정 기준 따름
[ ] 인스타 지표가 AGENTS.md 13절 사용 가능 목록 내
[ ] 의학적 효능 표현 / 구매 전환 추정 없음
[ ] 시각 산출물이 DESIGN.md 토큰 따름
[ ] 차트 제목 해석형 ("매출 추이" ❌ / "선물세트 매출 높지만 환불도 높음" ✅)
```

체크에서 걸리면 final_report 전에 `risk_notes`에 기록 또는 Codex에 리뷰 코멘트.

## 7. 협업 인터페이스

**Claude → Codex** (`dev_plan_<slug>.md`): Goal, Scope, Out of Scope, 수정 파일 vs 건드리지 말 파일, Proposed Steps, Risks, Verification, Handoff Needed

**Codex → Claude** (Codex의 `handoff_<slug>.md`): What Changed, Verified, Lock State 해제, Risks Carried Over, Open Items

**Claude → Claude** (`handoff_<slug>.md` §5): 락 상태와 미해소 리스크 강조.

## 8. 워크트리 사용 핵심

상세는 `docs/collab-protocol.md` §4. 핵심:

- 백그라운드 세션은 워크트리 자동, 라이브는 메인 직접 가능
- **화이트리스트 (메인 SSOT)**: `PROJECT.md`, `CLAUDE.md`, `AGENTS.md`, `business-context.md`, `DESIGN.md`, `docs/collab-protocol.md`, `docs/templates.md`, `.gitignore`, `.gitattributes`. 라이브 세션은 메인 직접 수정. 백그라운드 세션은 워크트리에서 **cp 동기화 패턴**으로 작업 (메인 → 워크트리 cp → 편집 → **⭐ cp 적용 직전 메인 최신 재확인** → 워크트리 → 메인 cp → ExitWorktree → 30초 내 commit, 상세 `docs/collab-protocol.md` §4)
- **⚠️ cp 동기화는 우회 없이 항상 실행**: 산출물 작업이라도 PROJECT.md cp 동기화로 Work Board 락을 메인에 visible하게 만든 뒤 산출물에 손댄다. "산출물만 먼저 만들고 락은 나중에" 패턴 금지. cp 차단 발생 시 1회 재시도 후에도 실패하면 **즉시 사용자 보고**, 우회(스크립트 in-place replace, 락 등록 생략 등) 금지 (상세 `docs/collab-protocol.md` §4 "cp가 차단될 때")
- 워크트리 격리 영역: `data/`, `scripts/`, `output/`, `archive/`
- **워크트리 진입 전 반드시 `git commit` 또는 `git stash`** (미커밋이면 reset 위험)
- 종료 시: 화이트리스트 외 파일만 cp/머지 → PROJECT.md Work Board 락 해제 → 워크트리 제거 → 즉시 commit

## 9. 데이터 해석 핵심 원칙

상세는 `business-context.md`와 `AGENTS.md` 12~13절. 절대 어기지 말 것:
- 사용 가능 인스타 지표만 (`AGENTS.md` 13절 참조)
- 구매 전환 추정 금지 / 개인 계정·댓글 본문 수집 금지
- "수면 개선", "치료" 같은 의학적 효능 표현 금지
- 추천 문장은 근거 지표 + 다음 행동 함께

## 10. 산출물 스타일

상세는 `DESIGN.md`. 핵심:
- 대표가 5분 안에 결정할 수 있게 쓴다
- 감성 카피와 숫자 해석을 섞지 않는다
- 차트 제목은 해석형
- 숫자 근거 없는 단정형 결론 금지

## 11. 파일 락 & Handoff Log

상세는 `docs/collab-protocol.md` §2와 `PROJECT.md`의 Work Board · Handoff Log.

**락 적용 기준** (산출물 = Work Board, 메타 = Handoff Log만):

- 산출물 작업 (`output/*`, `data/*`, `scripts/*` 생성·수정) → Work Board 행 추가 (Status `in_progress`, `Files locked`에 잡은 파일 기재). 종료 시 행 제거 = 락 해제 + Handoff Log 1줄.
- 메타 문서 (메인 직접 수정 화이트리스트) 단순 수정 → 락 없이 Handoff Log만.
- 즉시 처리 (인사·응답·정보 조회) → 둘 다 생략.

**Status 본질**: `in_progress` = 능동 작업 중(락 있음), `blocked` = 외부 input 대기(락 없음). 작업 중 외부 input 필요해지면 `in_progress` → `blocked` 전환하면서 `Files locked`를 비운다.

Handoff Log 형식 (메인 PROJECT.md에 1줄):
`[YYYY-MM-DD HH:mm] Claude Code / changed: ... / verified: ... / next: ...`

**Handoff Log archive 트리거**: 새 항목 추가 시 Handoff Log가 11개 이상이 되면, 같은 작업 안에서 가장 오래된 항목들을 5개 남을 때까지 `archive/handoff-YYYY-MM-DD.md`로 일괄 이동(상세는 `docs/collab-protocol.md` §6). 별도 작업으로 미루지 않는다.

**⚠️ 카운트 의무**: Handoff Log에 새 항목을 추가하기 **직전 매번** 현재 메인 PROJECT.md의 Handoff Log 개수를 카운트(`rtk grep -c "^\[20" PROJECT.md`). 카운트 생략 금지. 추가 후 11 이상이면 같은 작업 안에서 archive 절차 함께 수행. 이 카운트는 cp 동기화 패턴 §4 5번 단계("Handoff Log 카운트 의무 단계")에서 절차로 박혀 있다.

## 12. 분쟁 처리 & self-handoff

actor 간 이견은 사용자가 직접 결정. Claude Code 절차:
1. 이견 핵심 정리 → 사용자 보고
2. 사용자 결정 전까지 충돌 영역 작업 멈춤 (락 해제)
3. 사용자 지정 결정을 `PROJECT.md` Decisions에 1줄 추가

Claude Code 세션 A→B 인계 시: `handoff_<slug>.md` 작성 (`docs/templates.md` §5), 락 모두 해제, 메인 PROJECT.md 갱신, 메인 commit.

## 13. 작업 방식

- 쉘 명령은 `rtk` 사용 (사용자 글로벌 `@RTK.md`)
- 파일 수정 전 어떤 파일을 왜 바꾸는지 짧게 알린다
- 한국어 응답 + 한국어 문서 (변수/함수/기술 스택명만 영어)
- 사용자 미커밋 파일 함부로 덮어쓰지 않는다
- 워크트리 진입/종료 직전 즉시 git commit
- 메인 직접 수정 화이트리스트(특히 `PROJECT.md`) 편집 전 `rtk git log -1 --format="%ar %an" PROJECT.md` 로 다른 actor 활성 감지. 1분 이내 타 actor 커밋이면 대기, 편집 후 30초 내 commit (상세 `docs/collab-protocol.md` §4 "PROJECT.md / 화이트리스트 동시 편집 방지")

## 14. 금지

- Codex가 만드는 코드/데이터/대시보드 파일 직접 수정 금지 (리뷰 코멘트로)
- 데이터에 없는 인과관계/구매 전환/효능 단정 금지
- 외부 브랜드 로고/시각 정체성 복제 금지
- 감성 문구만 있고 숫자 근거 없는 보고서 금지
- 사용자 modify 중이거나 락 잡힌 파일 임의 덮어쓰기 금지
- 워크트리에서 화이트리스트 파일을 cp 동기화 없이 처음부터 작성 금지 (메인 최신 base 상실, 정식 절차는 `docs/collab-protocol.md` §4 cp 동기화 패턴)
- cp 동기화 차단/오류 발생 시 우회 금지 — 산출물만 작성·락 등록 생략·스크립트 in-place replace 모두 금지. 1회 재시도 후 사용자 보고가 유일한 정답 (상세 `docs/collab-protocol.md` §4 "cp가 차단될 때")
- 산출물 작업 시 Work Board 락 등록 생략 금지 — 작업 시작 직후 PROJECT.md cp 동기화로 메인에 락 visible하게 한 뒤 산출물 작성. "단일 작업이니까" "짧은 작업이니까" 같은 이유로 생략 불가
- Codex 사용 시 작업 중간 "Handoff to worktree" 사용 금지. 격리 필요하면 채팅 시작 전 "Start in → New worktree" 선택 권장
