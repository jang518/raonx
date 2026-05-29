# PROJECT.md

오브온 프로젝트의 현황판. Codex와 Claude Code가 협업할 때 보는 단일 진실 원천이다.

**Goal**: 오브온 월간 운영 데이터로 대표가 다음 달 밀 상품 / 개선 메시지 / 인스타 콘텐츠 방향을 결정하게 한다.

**메타 규칙**: `docs/collab-protocol.md` — 역할, Work Board/락/Decisions/Handoff Log 갱신 방식, 파일 네이밍, 협업 환경 정책, archive 규칙.

---

## Work Board

활성 작업(`in_progress`, `blocked`, `todo`)만 표시. `in_progress`가 파일 락(능동 작업 중), `blocked`는 외부 input 대기(락 없음). 산출물(`output/*`, `data/*`, `scripts/*`) 작업은 짧아도 행을 만들고, 메타 문서(화이트리스트) 단순 수정은 Handoff Log만. 상세 룰은 `docs/collab-protocol.md` §2.

| ID | Status | Owner | Task | Files locked | Slug | Last update |
|---|---|---|---|---|---|---|

(활성 작업 없음)


## Decisions

- 사용자 요청이 기본 협업 모델보다 우선한다.
- actor 간 이견은 사용자가 직접 우선순위와 담당자를 지정해서 해결한다.
- 복잡한 작업은 Claude Code 계획 → Codex 보완·구현 → Claude Code 최종 문서화 흐름.
- 협업 문서는 이어받기 필요한 작업에서만 만든다.
- 모든 협업/결과물 문서는 작업별 slug를 붙여 구분한다.
- 파일 동시 수정은 Work Board `in_progress` 상태와 `Files locked` 컬럼으로 락을 잡아 방지한다.
- 협업 모델은 옵션 B (PROJECT.md 중앙 락, 메인 SSOT). actor 4명+ 또는 동시 작업 빈번 시 재검토.
- Codex 기본은 메인 직접 작업, 큰 작업/사용자 명시 지시 시 채팅 시작 전 "Start in → New worktree" 선택.
- Claude Code 기본은 백그라운드 세션 시 워크트리 자동, 라이브 세션은 메인 직접.
- PROJECT.md / 메타 문서 / git 설정 파일은 양쪽 모두 메인에서 직접 수정한다.
- Codex 워크트리는 채팅 시작 전 "Start in → New worktree"로만 시작. 작업 중간 "Handoff to worktree" 금지 (메인 reset 위험).
- Knowledge work는 95% 단일 순차 작업이며, 병렬은 사용자 명시 지시 기반 5% 예외.
- 인스타 지표는 공개 가능 지표만 사용. 구매 전환·개인 댓글 본문·의학적 효능 추정 금지.
- PROJECT.md는 현황판(Work Board + Decisions + Handoff Log) 3섹션으로만 운영, 메타 규칙은 `docs/collab-protocol.md`로 분리.
- 데이터는 `data/monthly_ops.xlsx`를 실제 운영 데이터로 가정하고 분석 진행. 샘플/실데이터 구분 라벨링은 하지 않는다.
- 대시보드는 `output/dashboard.html`로 제작 (Excel 시트 아님).

## Handoff Log

작업 종료 시 `[YYYY-MM-DD HH:mm] actor / changed: ... / verified: ... / next: ...` 형식으로 1줄 기록한다. 최근 10개를 유지하고 초과분은 `archive/`로 이동한다(상세 `docs/collab-protocol.md` §6).

_(기록 없음)_
