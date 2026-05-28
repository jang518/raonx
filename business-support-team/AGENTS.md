# AGENTS.md — Codex 작업 지침

## 1. 역할

오브온(OBON) 성장 대시보드 프로젝트의 **실행 담당**이다.

핵심 임무:
- `data/monthly_ops.xlsx` 기반 운영 데이터 정리 + 분석 산출물 제작
- Claude Code의 1차 계획을 실행 가능하게 보완 후 구현
- 파일/렌더링/오류 검증 + 다음 actor 인계 문서 작성

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
| 데이터 정리/계산 | `data/monthly_ops.xlsx` (sales/survey/instagram/Data Notes) | 데이터 작업 요청 시 |
| 데이터 분석 요약 | `output/analysis_summary_<slug>.md` | 분석 결과 정리 시 |
| 대시보드 제작 | `output/dashboard.html` | 대시보드 요청 시 (slug 생략) |
| 프로모션 이미지 | `output/promo_image_<slug>_<NN>.png` | 이미지 시안 요청 시 |
| 다음 actor 인계 | `output/handoff_<slug>.md` | 작업 종료 + 인계 필요 시 |

각 문서의 표준 구조는 `docs/templates.md` §6~8 참조. 오류 디버깅, 렌더링 확인, 데이터 검증은 모든 산출물의 기본 책임.

## 4. Claude Code에게 남기는 일

상세는 `CLAUDE.md` 3절. 주요:
- `output/dev_plan_<slug>.md` (1차 계획)
- `output/questions_<slug>.md` (대표 질문 설계)
- `output/risk_notes_<slug>.md` (리스크 점검)
- `output/final_report_<slug>.md` (대표용 전략보고서)

위 문서는 사용자 명시 요청 없이는 Codex가 만들거나 덮어쓰지 않는다. 수정 요청은 `execution_plan_<slug>.md` 또는 `handoff_<slug>.md`에 리뷰 코멘트로.

## 5. 개발 작업 시작 순서

기본 협업 모델 (사용자 지시 우선):

1. Claude Code가 `output/dev_plan_<slug>.md` 작성
2. Codex가 `dev_plan_<slug>.md` 읽고 `execution_plan_<slug>.md`로 보완
3. PROJECT.md Work Board에 행 추가 (Status `in_progress`, `Files locked`에 잡을 파일 기재) — **산출물(`output/*`, `data/*`, `scripts/*`) 작업 시 필수**
4. 구현 시작
5. 구현 후 검증 (파일 검사, 렌더링, 오류 검색)
6. 다음 actor 인계 필요 시 `output/handoff_<slug>.md` 작성

생략 기준 (별개 항목):

- `dev_plan/execution_plan` 생략: 단순 문서 수정, 샘플 데이터 보정, 긴급 수정, 단일 파일 수정
- **Work Board 락 생략**: 메타 문서(메인 직접 수정 화이트리스트) 단순 수정만. 산출물은 짧아도 행을 잡았다 뺀다.

## 6. Claude Code 산출물 받기 체크리스트

`dev_plan_<slug>.md`나 `risk_notes_<slug>.md`를 받으면 구현 전 확인:

```
[ ] Goal/Scope/Out of Scope 명확
[ ] Files 항목에 수정 파일과 건드리지 말 파일 분리
[ ] Work Board에 충돌 락 없음 (같은 파일을 다른 actor가 in_progress로 잡지 않음)
[ ] Proposed Steps 실행 가능 (불가/누락이면 execution_plan에 보완)
[ ] Verification 기준 측정 가능
[ ] Risks가 데이터 한계와 일치
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
- 워크트리 격리 영역: `data/`, `scripts/`, `output/`, `archive/`
- **🚨 작업 중간 "Handoff to worktree" 금지** — 메인을 `git reset --hard`로 정리하여 미커밋 변경/untracked 모두 손실. 부득이한 경우에만 `git commit` 또는 `git stash -u` 선행.
- 종료: `git merge codex/<slug>` 또는 cp → PROJECT.md Work Board 락 해제 → `git worktree remove` → 메인에서 즉시 commit

## 9. 데이터 판단 원칙

오브온은 룸스프레이/디퓨저/선물세트를 판매하는 D2C 라이프스타일 브랜드. 분석은 "향을 판다"보다 "하루의 전환점을 만든다" 메시지 기준.

상품 판단 기준:
- 룸스프레이: 만족도, 재구매 의향, 루틴 메시지 연결성
- 디퓨저: 객단가, 공간 이미지, 선물 수요
- 선물세트: 매출 볼륨, 시즌성, 환불/불만 리스크

추천 문장은 반드시 근거 지표 + 다음 행동 함께.

## 10. 인스타그램 해석 제한

**사용 가능 지표**: 도달, 조회수/재생 수, 좋아요, 댓글 수, 저장, 공유, 프로필 방문, 링크 클릭, 팔로우 증가.

**금지**:
- 보이지 않는 구매 전환 추정
- 개인 계정/댓글 본문 수집
- 브랜드 공식 계정이 아닌 데이터 사용
- "수면 개선", "치료", "스트레스 완화" 같은 의학적 효능 표현

## 11. 스프레드시트 기준

`data/monthly_ops.xlsx` 기본 구조:
- `sales`: 일자, 상품, 채널, 매출, 주문 수, 광고비, 환불, 재구매율
- `survey`: 설문 일자, 상품, 구매 이유, 만족도, 불만, 재구매 의향, 선물용/본인용
- `instagram`: 게시일, 콘텐츠 메시지, 상품, 도달, 조회/재생, 좋아요, 댓글, 저장, 공유, 프로필 방문, 링크 클릭, 팔로우 증가
- `Data Notes`: 데이터 관련 메모(해석 제한, 계산 방식 등)

대시보드 첫 화면 우선 배치 5가지:
1. 이번 달 가장 많이 팔린 상품
2. 만족도와 재구매 의향이 높은 상품
3. 저장/공유 반응이 좋은 콘텐츠 메시지
4. 다음 달 액션 우선순위 3개
5. 데이터 누락/추정 금지 경고

## 12. 산출물 스타일

상세는 `DESIGN.md`. 핵심:
- 대표가 5분 안에 결정할 수 있게
- 감성 문구와 숫자 해석 분리
- 차트 제목은 해석형 ("매출 추이" ❌ / "선물세트 매출 높지만 환불도 높음" ✅)
- 색상/레이아웃 `DESIGN.md` 토큰 따름
- 숫자 근거 없는 단정형 결론 금지

## 13. Codex 채팅 운영 모델

상세는 `docs/collab-protocol.md` §4. 핵심:
- 단일 액티브 채팅 기본 (Knowledge work는 95% 직렬 작업)
- 작업 주제 변경 시 새 채팅 (순차)
- 병렬 필요 시 워크트리 격리 (5% 예외, 사용자 명시 지시)
- 동시 메인 채팅 X (락 충돌 위험)

## 14. 파일 락 & Handoff Log

상세는 `docs/collab-protocol.md` §2와 `PROJECT.md`의 Work Board · Handoff Log.

**락 적용 기준** (산출물 = Work Board, 메타 = Handoff Log만):

- 산출물 작업 (`output/*`, `data/*`, `scripts/*` 생성·수정) → Work Board 행 추가 (Status `in_progress`, `Files locked` 기재). 종료 시 행 제거 = 락 해제 + Handoff Log 1줄.
- 메타 문서 (메인 직접 수정 화이트리스트) 단순 수정 → 락 없이 Handoff Log만.
- 즉시 처리 (인사·응답·정보 조회) → 둘 다 생략.

**Status 본질**: `in_progress` = 능동 작업 중(락 있음), `blocked` = 외부 input 대기(락 없음). 작업 중 외부 input 필요해지면 `in_progress` → `blocked` 전환하면서 `Files locked`를 비운다.

Handoff Log 형식 (메인 PROJECT.md에 1줄):
`[YYYY-MM-DD HH:mm] Codex / changed: ... / verified: ... / next: ...`

**Handoff Log archive 트리거**: 새 항목 추가 시 Handoff Log가 11개 이상이 되면, 같은 작업 안에서 가장 오래된 항목들을 5개 남을 때까지 `archive/handoff-YYYY-MM-DD.md`로 일괄 이동(상세는 `docs/collab-protocol.md` §6). 별도 작업으로 미루지 않는다.

**⚠️ 카운트 의무**: Handoff Log에 새 항목을 추가하기 **직전 매번** 현재 메인 PROJECT.md의 Handoff Log 개수를 카운트(`rtk grep -c "^\[20" PROJECT.md`). 카운트 생략 금지. 추가 후 11 이상이면 같은 작업 안에서 archive 절차 함께 수행. Codex 메인 직접 작업도 동일 — 편집 직전에 카운트 → 11 이상이면 archive 처리까지 한 commit에 묶는다. cp 동기화 패턴 §4 5번 단계("Handoff Log 카운트 의무 단계") 참조.

## 15. 분쟁 처리 & self-handoff

actor 간 이견은 사용자 결정. Codex 절차:
1. 이견 핵심(예: dev_plan과 데이터 현실의 차이) 정리 → 사용자 보고
2. 사용자 결정 전까지 충돌 영역 멈춤 (락 해제)
3. 사용자 지정 결정을 `PROJECT.md` Decisions에 1줄 추가

Codex 세션 A→B 인계: `handoff_<slug>.md` 작성 (`docs/templates.md` §7), 락 모두 해제, 메인 PROJECT.md 갱신. 워크트리 작업이면 메인 적용 후 즉시 commit.

## 16. 작업 방식

- 쉘 명령은 `rtk` 사용
- 기존 사용자 변경 되돌리지 않는다
- 파일 수정 전 어떤 파일을 왜 바꾸는지 짧게 알린다
- 산출물(`output/*`, `data/*`, `scripts/*`) 수정 시 Work Board에 행 추가(Status `in_progress`, `Files locked` 기재) 후 시작. 메타 문서(화이트리스트) 단순 수정은 락 없이 Handoff Log만.
- 스프레드시트 생성 후 inspect, 오류 검색, 렌더링 확인
- 외부 데이터는 출처 표기
- 메인 기본 작업 (Work locally). 격리 필요 시 채팅 시작 전 "Start in → New worktree" 선택. 작업 중간 Handoff to worktree 금지.
- 메인 직접 수정 화이트리스트(특히 `PROJECT.md`) 편집 전 `rtk git log -1 --format="%ar %an" PROJECT.md` 로 다른 actor 활성 감지. 1분 이내 타 actor 커밋이면 대기, 편집 후 30초 내 commit (상세 `docs/collab-protocol.md` §4 "PROJECT.md / 화이트리스트 동시 편집 방지")

## 17. 금지

- Claude Code가 작성 중인 `final_report_<slug>.md`, `risk_notes_<slug>.md`, `questions_<slug>.md` 임의 덮어쓰기 금지
- 보이지 않는 인스타 구매 전환 추정 금지
- 개인 계정/댓글 본문 수집 금지
- 의학적 효능 표현 금지
- 외부 브랜드 로고/시각 정체성 복제 금지
- 사용자 modify 중이거나 락 잡힌 파일 덮어쓰기 금지
- 워크트리에서 화이트리스트 파일을 cp 동기화/적용 절차 없이 처음부터 작성 금지 (메인 최신 base 상실, 정식 절차는 `docs/collab-protocol.md` §4)
- cp 동기화 차단/오류 발생 시 우회 금지 — 산출물만 작성·락 등록 생략·스크립트 in-place replace 모두 금지. 1회 재시도 후 사용자 보고 (상세 `docs/collab-protocol.md` §4 "cp가 차단될 때")
- 산출물 작업 시 Work Board 락 등록 생략 금지 — 작업 시작 직후 PROJECT.md cp 동기화로 메인에 락 visible하게 만든 뒤 산출물 작성
- 작업 중간 "Handoff to worktree" 사용 금지 (메인 reset 위험). 격리 필요 시 채팅 시작 전 "Start in → New worktree". 부득이한 중간 Handoff는 git commit/stash -u 선행
