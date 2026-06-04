# PROJECT.md

knowledge_for_marketing_dashboard 프로젝트의 현황판. Codex와 Claude Code가 협업할 때 보는 단일 진실 원천이다.

Goal: 고객 방문보고서에서 파악한 고객 니즈와 소프트웨어 기능을 연결해, 영업·엔지니어가 고객 미팅 전 고객 니즈 / 추천 기능 / 제안 메시지 / 다음 미팅 질문을 빠르게 확인할 수 있는 Streamlit 기반 사내 웹 대시보드를 구축한다.

메타 규칙: docs/collab-protocol.md — 역할, Work Board/락/Decisions/Handoff Log 갱신 방식, 파일 네이밍, 협업 환경 정책, archive 규칙.

---

## Work Board

활성 작업('in_progress', 'blocked', 'todo')만 표시. 'in_progress'가 파일 락(능동 작업 중), 'blocked'는 외부 input 대기(락 없음). 산출물('app/*', 'data/*', 'scripts/*', 'output/*') 작업은 짧아도 행을 만들고, 메타 문서(화이트리스트) 단순 수정은 Handoff Log만. 상세 룰은 'docs/collab-protocol.md' §2.

| ID | Status | Owner | Task | Files locked | Slug | Last update |
|---|---|---|---|---|---|---|

(활성 작업 없음)


## Decisions

* 사용자 요청이 기본 협업 모델보다 우선한다.
* actor 간 이견은 사용자가 직접 우선순위와 담당자를 지정해서 해결한다.
* 복잡한 작업은 Claude Code 계획 → Codex 보완·구현 → Claude Code 최종 문서화 흐름.
* 협업 문서는 이어받기 필요한 작업에서만 만든다.
* 모든 협업/결과물 문서는 작업별 slug를 붙여 구분한다.
* 파일 동시 수정은 Work Board `in_progress` 상태와 `Files locked` 컬럼으로 락을 잡아 방지한다.
* 협업 모델은 옵션 B (PROJECT.md 중앙 락, 메인 SSOT). actor 4명+ 또는 동시 작업 빈번 시 재검토.
* Codex 기본은 메인 직접 작업, 큰 작업/사용자 명시 지시 시 채팅 시작 전 "Start in → New worktree" 선택.
* Claude Code 기본은 백그라운드 세션 시 워크트리 자동, 라이브 세션은 메인 직접.
* PROJECT.md / 메타 문서 / git 설정 파일은 양쪽 모두 메인에서 직접 수정한다.
* Codex 워크트리는 채팅 시작 전 "Start in → New worktree"로만 시작. 작업 중간 "Handoff to worktree" 금지 (메인 reset 위험).
* Knowledge work는 95% 단일 순차 작업이며, 병렬은 사용자 명시 지시 기반 5% 예외.
* 원본 자료(`../raw/*`)와 위키 원본(`../wiki/*`)은 읽기 전용으로 취급하며, dashboard 작업에서 직접 수정하지 않는다.
* export CSV는 `../export/*`에서 생성된 결과를 기준으로 하며, dashboard 프로젝트에서는 원본 export 생성 로직을 임의로 변경하지 않는다.
* dashboard 개발 중 사용하는 데이터는 `data/*`에 복사한 개발용 CSV 또는 샘플 CSV를 사용한다.
* 실제 고객명, 고객 니즈, 방문보고서 기반 내용은 사내 활용 목적으로만 사용하며, 외부 공개 산출물에 포함하지 않는다.
* 데이터에 없는 고객 의도, 구매 가능성, 경쟁사 검토 여부, 예산 규모는 추정하지 않는다.
* 대시보드는 Streamlit 기반으로 제작한다.
* 데이터 처리는 pandas를 기본으로 한다.
* 운영 배포는 서버 PC의 Docker 환경을 전제로 한다.
* Python 버전은 3.13을 기준으로 한다.
* 대시보드 화면은 5개로 구성한다: Home, 고객 니즈 검색, 니즈-기능-가치 매칭, 고객사별 대응 전략, 소프트웨어별 가치 맵.
* Home 화면은 최근 고객 니즈, 자주 등장한 니즈, 검토 필요 매칭 현황을 보여준다.
* 고객 니즈 검색 화면은 고객사·산업군·키워드 기준 필터링을 제공한다.
* 니즈-기능-가치 매칭 화면은 고객 니즈에 연결된 추천 기능과 제안 메시지를 보여준다.
* 고객사별 대응 전략 화면은 추천 접근 방향과 다음 미팅 추천 질문을 보여준다.
* 소프트웨어별 가치 맵 화면은 기능 목록과 연결 가능한 고객 니즈를 보여준다.
* 대시보드의 주요 사용자는 영업·엔지니어이며, 목적은 고객 미팅 전 준비 시간을 줄이는 것이다.
* 위키 관리자는 방문보고서 입수 → Claude로 위키 갱신 → export 재생성 → 서버 반영 흐름을 따른다.
* 서버 PC 운영 폴더는 `marketing_llmwiki/` 구조를 따른다.
* 서버 PC의 `incoming/`은 내 컴퓨터에서 전달받는 임시 데이터, `data/`는 Docker 대시보드가 실제로 읽는 데이터로 구분한다.
* 서버 반영 전 기존 운영 데이터는 `backup/`에 백업하는 것을 원칙으로 한다.
* 대시보드 산출물과 테스트 결과는 `output/`에 저장한다.
* 실행 코드와 보조 스크립트는 `app/` 또는 `scripts/`에 저장한다.
* 임시 파일, 로그, 실제 고객 CSV는 원칙적으로 Git에 커밋하지 않는다.
* 최종 판단이 필요한 내용, 데이터 품질 이슈, 매칭 신뢰도 이슈는 Claude Code가 검토 문서로 정리한다.
* Codex는 앱 구조 생성, 코드 작성, CSV 로딩, Streamlit 화면 구현, Docker 관련 파일 생성을 담당한다.
* Claude Code는 화면 구성 검토, 데이터 해석 리스크 점검, 문서화, 사용자 관점의 개선사항 정리를 담당한다.
* PROJECT.md는 현황판(Work Board + Decisions + Handoff Log) 3섹션으로만 운영, 메타 규칙은 `docs/collab-protocol.md`로 분리.

## Handoff Log

작업 종료 시 `[YYYY-MM-DD HH:mm] actor / changed: ... / verified: ... / next: ...` 형식으로 1줄 기록한다. 최근 10개를 유지하고 초과분은 `archive/`로 이동한다(상세 `docs/collab-protocol.md` §6).

(기록 없음)

