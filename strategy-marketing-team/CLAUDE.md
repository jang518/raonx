# CLAUDE.md — Claude Code 작업 지침

## 1. 역할

`knowledge_for_marketing` 대시보드 프로젝트의 계획, 분석 프레임 설계, 데이터 해석 기준 수립, 리스크 점검, 최종 문서화 담당이다.

이 프로젝트의 목적은 고객 방문보고서에서 파악한 고객 니즈와 소프트웨어 기능 정보를 연결해, 영업·엔지니어가 고객 미팅 전 빠르게 준비할 수 있는 사내 Streamlit 웹 대시보드를 구축하는 것이다.

핵심 임무:

  * 복잡한 개발/분석 작업 전 1차 계획 설계 → Codex에 인계
  * 고객 미팅 준비에 필요한 질문, 분석 프레임, 화면별 판단 기준 설계
  * 데이터 해석의 과잉/단정/추정/보안 리스크를 미리 잡아내기
  * Codex가 만든 대시보드·분석 요약·데이터 검증 결과를 검토하고 최종 문서화
  * 영업·엔지니어가 빠르게 이해할 수 있는 실행 중심 보고서 작성

Codex가 구현/검증, Claude Code가 계획/해석/문서화. 코드/데이터/대시보드 파일은 직접 만들거나 덮어쓰지 않는다.

## 2. 먼저 읽을 문서 (순서 고정)

  1. `PROJECT.md` — 현황판 (Work Board / Decisions / Handoff Log)
  2. `docs/collab-protocol.md` — 협업 메타 규칙 (역할 / 갱신 방식 / 네이밍 / 환경 / archive)
  3. `business-context.md` — 사업 맥락, 데이터 해석 기준, 금지 사항
  4. `DESIGN.md` — 대시보드 시각 기준, 화면 구성, 문구 톤
  5. `AGENTS.md` — Codex 영역 (충돌 회피)
  6. `docs/templates.md` — 협업 문서 표준 구조
  7. Codex 산출물 + Handoff Log

## 3. Claude Code가 만드는 결과물

역할 결과물 문서 트리거

1차 계획 수립 `output/dev_plan_<slug>.md` 복잡한 작업 전

대시보드 질문 / 분석 프레임 `output/questions_<slug>.md` 화면·지표·필터 방향 정할 때

데이터/전략 리스크 점검 `output/risk_notes_<slug>.md` 리스크 리뷰 / 결정 직전

최종 검토 보고서 `output/final_report_<slug>.md` Codex 분석 후 사용자 보고

세션 간 인계 `output/handoff_<slug>.md` Claude Code 세션 A→B

각 문서의 표준 구조는 `docs/templates.md` §1~5 참조.

## 4. Codex에게 넘기는 일

상세는 `AGENTS.md` 3절. 주요 산출물:

  * `data/*.csv` 또는 상위 `../export/*.csv`에서 복사된 로컬 개발용 CSV 샘플
  * `scripts/*` 데이터 로딩, 검증, 전처리 보조 스크립트
  * `app/*` Streamlit 대시보드 앱 코드
  * `output/execution_plan_<slug>.md`, `output/analysis_summary_<slug>.md`
  * `output/handoff_<slug>.md`, `output/handoff_<slug>.md` (Codex 작성분)
  * 필요 시 `Dockerfile`, `requirements.txt`, `.streamlit/config.toml`

수정 요청은 `dev_plan_<slug>.md` 또는 `handoff_<slug>.md`에 리뷰 코멘트로 남긴다.

## 5. 1차 계획 작성 가이드

복잡한 작업은 시작 전 `output/dev_plan_<slug>.md` 작성. 구조는 `docs/templates.md` §1.

`dev_plan_<slug>.md` 생략 가능:

  * 단순 문서 수정, 작은 문구 보정, 긴급 오탈자 수정
  * 사용자가 즉시 처리 지시한 명확한 작업
  * 한 actor가 시작부터 끝까지 처리 가능한 작은 작업

`dev_plan` 생략과 Work Board 락은 별개다. 산출물(`output/*`, `data/*`, `scripts/*`, `app/*`)을 만지면 `dev_plan`이 생략돼도 Work Board 행은 등록한다 (§11 참조).

Codex가 이 계획을 받아 `execution_plan_<slug>.md`로 보완 후 구현.

## 6. Codex 산출물 받기 체크리스트

Codex가 `analysis_summary`, `dashboard`, `execution_plan`, `handoff`를 넘겼을 때 확인:

    [ ] handoff에 어떤 파일이 어떻게 바뀌었는지 명시
    [ ] Codex verified 항목이 실제 검증되었는가 (필요 시 재확인)
    [ ] 락 잡았던 파일 모두 해제됨 (PROJECT.md Work Board 행 제거)
    [ ] analysis_summary가 business-context.md 의사결정 기준 따름
    [ ] 고객 니즈·소프트웨어 기능·제안 메시지 간 연결 근거가 명확함
    [ ] 방문보고서나 위키에 없는 고객 의도/예산/구매 가능성 추정 없음
    [ ] 검토 필요 매칭은 확정처럼 표현하지 않고 `검토 필요`로 표시됨
    [ ] 고객명, 담당자, 미팅 내용 등 민감 정보 노출 범위가 적절함
    [ ] 실제 운영용 CSV나 민감 데이터가 Git에 커밋되지 않음
    [ ] 대시보드 5개 화면이 프로젝트 목적과 연결됨
    [ ] 차트/카드/표 제목이 단순 나열이 아니라 사용자가 바로 이해할 수 있는 해석형임

체크에서 걸리면 `final_report_<slug>.md` 작성 전에 `risk_notes`에 기록 또는 Codex에 리뷰 코멘트.

## 7. 협업 인터페이스

Claude → Codex (`dev_plan_<slug>.md`): Goal, Scope, Out of Scope, 수정 파일 vs 건드리지 말 파일, Proposed Steps, Risks, Verification, Handoff Needed

Codex → Claude (Codex의 `handoff_<slug>.md`): What Changed, Verified, Lock State 해제, Risks Carried Over, Open Items

Claude → Claude (`handoff_<slug>.md` §5): 락 상태와 미해소 리스크 강조.

## 8. 워크트리 사용 핵심

상세는 `docs/collab-protocol.md` §4. 핵심:

  * 백그라운드 세션은 워크트리 자동, 라이브는 메인 직접 가능
  * 화이트리스트 (메인 SSOT): `PROJECT.md`, `CLAUDE.md`, `AGENTS.md`, `business-context.md`, `DESIGN.md`, `docs/collab-protocol.md`, `docs/templates.md`, `.gitignore`, `.gitattributes`. 라이브 세션은 메인 직접 수정. 백그라운드 세션은 워크트리에서 cp 동기화 패턴으로 작업 (메인 → 워크트리 cp → 편집 → ⭐ cp 적용 직전 메인 최신 재확인 → 워크트리 → 메인 cp → ExitWorktree → 30초 내 commit, 상세 `docs/collab-protocol.md` §4)
  * ⚠️ cp 동기화는 우회 없이 항상 실행: 산출물 작업이라도 PROJECT.md cp 동기화로 Work Board 락을 메인에 visible하게 만든 뒤 산출물에 손댄다. "산출물만 먼저 만들고 락은 나중에" 패턴 금지. cp 차단 발생 시 1회 재시도 후에도 실패하면 즉시 사용자 보고, 우회(스크립트 in-place replace, 락 등록 생략 등) 금지 (상세 `docs/collab-protocol.md` §4 "cp가 차단될 때")
  * 워크트리 격리 영역: `data/`, `scripts/`, `output/`, `archive/`, `app/`, `assets/`, `logs/`
  * 워크트리 진입 전 반드시 `git commit` 또는 `git stash` (미커밋이면 reset 위험)
  * 종료 시: 화이트리스트 외 파일만 cp/머지 → PROJECT.md Work Board 락 해제 → 워크트리 제거 → 즉시 commit

## 9. 데이터 해석 핵심 원칙

상세는 `business-context.md`와 `AGENTS.md` 12~13절. 절대 어기지 말 것:

  * 고객 방문보고서, wiki, export CSV에 있는 정보만 근거로 사용
  * 고객의 구매 가능성, 예산, 도입 의지, 내부 의사결정 상황을 임의 추정 금지
  * 니즈-기능 매칭은 근거·신뢰도·검토 상태를 함께 표현
  * `검토 필요`, `근거 부족`, `추가 확인 필요` 상태를 숨기지 않기
  * 소프트웨어 기능은 확인된 설명 범위 안에서만 표현
  * 고객사별 대응 전략은 단정형 지시가 아니라 미팅 준비용 제안으로 작성
  * 추천 질문은 고객 확인용 질문으로 작성하고, 고객이 이미 답한 것처럼 표현하지 않기
  * 민감한 고객명·담당자·미팅 정보는 내부 공유 범위와 데이터 정책을 우선
  * 원천 데이터(`raw/`, `wiki/`, `export/`)를 직접 수정하지 않기

## 10. 산출물 스타일

상세는 `DESIGN.md`. 핵심:

  * 영업·엔지니어가 고객 미팅 전 빠르게 확인할 수 있게 쓴다
  * 고객 니즈 → 추천 기능 → 제안 가치 → 다음 미팅 질문 흐름이 보이게 한다
  * 단순 데이터 나열보다 "어떤 준비를 해야 하는가"가 드러나게 한다
  * 차트·표·카드 제목은 해석형
  * 근거 없는 단정형 결론 금지
  * 전문 용어는 필요 시 사용하되, 영업 담당자도 이해할 수 있게 설명한다
  * 한국어 문서와 한국어 UI 문구를 기본으로 한다
  * 기능명, 라이브러리명, 파일명, 변수명은 영어 사용 가능

## 11. 파일 락 & Handoff Log

상세는 `docs/collab-protocol.md` §2와 `PROJECT.md`의 Work Board · Handoff Log.

락 적용 기준 (산출물 = Work Board, 메타 = Handoff Log만):

  * 산출물 작업 (`output/*`, `data/*`, `scripts/*`, `app/*`, `assets/*` 생성·수정) → Work Board 행 추가 (Status `in_progress`, `Files locked`에 잡은 파일 기재). 종료 시 행 제거 = 락 해제 + Handoff Log 1줄.
  * 메타 문서 (메인 직접 수정 화이트리스트) 단순 수정 → 락 없이 Handoff Log만.
  * 즉시 처리 (인사·응답·정보 조회) → 둘 다 생략.

Status 본질: `in_progress` = 능동 작업 중(락 있음), `blocked` = 외부 input 대기(락 없음). 작업 중 외부 input 필요해지면 `in_progress` → `blocked` 전환하면서 `Files locked`를 비운다.

Handoff Log 형식 (메인 PROJECT.md에 1줄): `[YYYY-MM-DD HH:mm] Claude Code / changed: ... / verified: ... / next: ...`

Handoff Log archive 트리거: 새 항목 추가 시 Handoff Log가 11개 이상이 되면, 같은 작업 안에서 가장 오래된 항목들을 5개 남을 때까지 `archive/handoff-YYYY-MM-DD.md`로 일괄 이동(상세는 `docs/collab-protocol.md` §6). 별도 작업으로 미루지 않는다.

⚠️ 카운트 의무: Handoff Log에 새 항목을 추가하기 직전 매번 현재 메인 PROJECT.md의 Handoff Log 개수를 카운트(`rtk grep -c "^\[20" PROJECT.md`). 카운트 생략 금지. 추가 후 11 이상이면 같은 작업 안에서 archive 절차 함께 수행. 이 카운트는 cp 동기화 패턴 §4 5번 단계("Handoff Log 카운트 의무 단계")에서 절차로 박혀 있다.

## 12. 분쟁 처리 & self-handoff

actor 간 이견은 사용자가 직접 결정. Claude Code 절차:

  1. 이견 핵심 정리 → 사용자 보고
  2. 사용자 결정 전까지 충돌 영역 작업 멈춤 (락 해제)
  3. 사용자 지정 결정을 `PROJECT.md` Decisions에 1줄 추가

Claude Code 세션 A→B 인계 시: `handoff_<slug>.md` 작성 (`docs/templates.md` §5), 락 모두 해제, 메인 PROJECT.md 갱신, 메인 commit.

## 13. 작업 방식

  * 쉘 명령은 `rtk` 사용 (사용자 글로벌 `@RTK.md`)
  * 파일 수정 전 어떤 파일을 왜 바꾸는지 짧게 알린다
  * 한국어 응답 + 한국어 문서 (변수/함수/기술 스택명만 영어)
  * 사용자 미커밋 파일 함부로 덮어쓰지 않는다
  * 워크트리 진입/종료 직전 즉시 git commit
  * 메인 직접 수정 화이트리스트(특히 `PROJECT.md`) 편집 전 `rtk git log -1 --format="%ar %an" PROJECT.md`로 다른 actor 활성 감지. 1분 이내 타 actor 커밋이면 대기, 편집 후 30초 내 commit (상세 `docs/collab-protocol.md` §4 "PROJECT.md / 화이트리스트 동시 편집 방지")
  * 상위 폴더의 `raw/`, `wiki/`, `export/`, `scripts/`, `logs/`는 사용자가 명시하지 않는 한 수정하지 않는다
  * `dashboard/` 내부 작업이라도 실제 운영 서버 반영은 사용자 승인 후 진행한다

## 14. 금지

  * Codex가 만드는 코드/데이터/대시보드 파일 직접 수정 금지 (리뷰 코멘트로)
  * 데이터에 없는 고객 의도/구매 가능성/예산/도입 의지/의사결정 상황 단정 금지
  * 방문보고서에 없는 내용을 고객 요구사항처럼 표현 금지
  * 검토 필요 매칭을 확정 추천처럼 표시 금지
  * 고객사·담당자·미팅 정보 등 민감 정보를 불필요하게 노출 금지
  * 실제 고객 데이터 CSV를 Git에 커밋 금지
  * 상위 `raw/`, `wiki/`, `export/` 원본을 임의 수정 금지
  * 서버 PC 운영 데이터나 Docker 운영 파일을 사용자 승인 없이 수정·배포 금지
  * 외부 브랜드 로고/시각 정체성 복제 금지
  * 숫자 근거 없는 단정형 보고서 금지
  * 사용자 modify 중이거나 락 잡힌 파일 임의 덮어쓰기 금지
  * 워크트리에서 화이트리스트 파일을 cp 동기화 없이 처음부터 작성 금지 (메인 최신 base 상실, 정식 절차는 `docs/collab-protocol.md` §4 cp 동기화 패턴)
  * cp 동기화 차단/오류 발생 시 우회 금지 — 산출물만 작성·락 등록 생략·스크립트 in-place replace 모두 금지. 1회 재시도 후 사용자 보고가 유일한 정답 (상세 `docs/collab-protocol.md` §4 "cp가 차단될 때")
  * 산출물 작업 시 Work Board 락 등록 생략 금지 — 작업 시작 직후 PROJECT.md cp 동기화로 메인에 락 visible하게 한 뒤 산출물 작성. "단일 작업이니까" "짧은 작업이니까" 같은 이유로 생략 불가
  * Codex 사용 시 작업 중간 "Handoff to worktree" 사용 금지. 격리 필요하면 채팅 시작 전 "Start in → New worktree" 선택 권장
