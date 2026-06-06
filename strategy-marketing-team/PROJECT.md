# PROJECT.md

`knowledge_for_marketing_dashboard` 프로젝트의 현황판이다. Codex와 Claude Code가 협업할 때 보는 단일 진실 원천이며, 현재 작업 상태·결정사항·인수인계 기록을 이 파일에서 관리한다.

Goal: 고객 방문보고서에서 파악한 고객 니즈와 소프트웨어 기능 업데이트 자료를 연결해, 영업·엔지니어가 고객 미팅 전 고객 니즈 / 추천 기능 / 제안 메시지 / 다음 미팅 질문을 빠르게 확인할 수 있는 사내 Streamlit 웹 대시보드를 구축한다.

Project flow: `방문보고서·기능 업데이트 자료` → `Claude 분석·정리` → `wiki/ 지식 위키` → `export CSV 4개` → `서버 PC 전송` → `팀 공유 웹 대시보드`.

메타 규칙: `docs/collab-protocol.md` — 역할, Work Board/락/Decisions/Handoff Log 갱신 방식, 파일 네이밍, 협업 환경 정책, archive 규칙.

* * *

## Work Board

활성 작업(`in_progress`, `blocked`, `todo`)만 표시. `in_progress`가 파일 락(능동 작업 중), `blocked`는 외부 input 대기(락 없음). 산출물(`output/*`, `data/*`, `scripts/*`, `app/*`, `dashboard/*`) 작업은 짧아도 행을 만들고, 메타 문서(화이트리스트) 단순 수정은 Handoff Log만 기록한다. 상세 룰은 `docs/collab-protocol.md` §2를 따른다.

| ID | Status | Owner | Task | Files locked | Slug | Last update |
|---|---|---|---|---|---|---|
| WB-001 | todo | Claude Code | 대시보드 목적과 5개 화면별 핵심 질문 정리 | `output/questions_dashboard-foundation.md` | dashboard-foundation | 2026-06-06 |
| WB-002 | todo | Codex | `export/` CSV 4개 구조 확인 및 대시보드 입력 데이터 스키마 초안 작성 | `docs/data-schema.md`, `output/analysis_summary_dashboard-foundation.md` | dashboard-foundation | 2026-06-06 |
| WB-003 | todo | Codex | Streamlit 앱 기본 구조 생성 및 5개 화면 라우팅 골격 작성 | `dashboard/*`, `README.md` | streamlit-app-foundation | 2026-06-06 |
| WB-004 | todo | Claude Code | Codex 산출물의 데이터 해석 리스크와 보안 주의사항 검토 | `output/risk_notes_dashboard-foundation.md` | dashboard-foundation | 2026-06-06 |
| WB-005 | todo | Codex | 서버 PC 반영 흐름 설계: `incoming/` → `data/` → `backup/` 구조와 운영 스크립트 초안 작성 | `docs/deploy-flow.md`, `scripts/*` | server-deploy-flow | 2026-06-06 |
| WB-006 | todo | Claude Code | 관리자/admin과 조회자/viewer 권한 기준, 데이터 수정 범위, 검토 상태 관리 기준 문서화 | `output/questions_permission-review.md` | permission-review | 2026-06-06 |

## Decisions

* 사용자 요청이 기본 협업 모델보다 우선한다.
* actor 간 이견은 사용자가 직접 우선순위와 담당자를 지정해서 해결한다.
* 복잡한 작업은 Claude Code 계획 → Codex 보완·구현 → Claude Code 최종 문서화 흐름을 기본으로 한다.
* 협업 문서는 이어받기 필요한 작업에서만 만든다.
* 모든 협업/결과물 문서는 작업별 slug를 붙여 구분한다.
* 파일 동시 수정은 Work Board `in_progress` 상태와 `Files locked` 컬럼으로 락을 잡아 방지한다.
* 협업 모델은 옵션 B (PROJECT.md 중앙 락, 메인 SSOT)를 사용한다. actor 4명 이상 또는 동시 작업 빈번 시 재검토한다.
* Codex 기본은 메인 직접 작업이며, 큰 작업 또는 사용자 명시 지시 시 채팅 시작 전 `Start in → New worktree`를 선택한다.
* Claude Code 기본은 백그라운드 세션 시 워크트리 자동, 라이브 세션은 메인 직접 작업으로 본다.
* `PROJECT.md` / 메타 문서 / git 설정 파일은 양쪽 모두 메인에서 직접 수정한다.
* Codex 워크트리는 채팅 시작 전 `Start in → New worktree`로만 시작한다. 작업 중간 `Handoff to worktree`는 메인 reset 위험이 있으므로 금지한다.
* Knowledge work는 95% 단일 순차 작업이며, 병렬은 사용자 명시 지시 기반 5% 예외로 본다.
* 원본 자료(`raw/`)와 지식 위키(`wiki/`)는 사용자 컴퓨터에서 관리하는 원본 지식 베이스로 간주하며, 대시보드 작업 중 직접 수정하지 않는다.
* 현재 지식 위키는 `wiki/` 기준 약 170개 페이지로 정리된 상태이며, 대시보드 개발자는 이를 직접 수정하지 않고 `export/` 산출물을 입력 데이터로 사용한다.
* 현재 `export/` CSV는 고객 니즈 약 214건, 니즈-기능 매칭 약 246건을 포함한 대시보드용 구조화 데이터로 본다.
* 대시보드는 `export/`에서 생성된 CSV 4개를 읽는 구조로 설계한다. 실제 고객 데이터는 민감할 수 있으므로 Git 커밋 대상에서 제외한다.
* 대시보드 개발은 `dashboard/` 폴더 내부를 기본 작업 범위로 한다. 상위 폴더의 `raw/`, `wiki/`, `export/`, `scripts/`, `logs/`는 사용자 지시 없이는 수정하지 않는다.
* 대시보드는 Streamlit + pandas + Docker + Python 3.13 기준으로 개발한다.
* 대시보드 화면은 Home, 고객 니즈 검색, 니즈-기능-가치 매칭, 고객사별 대응 전략, 소프트웨어별 가치 맵 5개를 기본 범위로 한다.
* Home 화면은 최근 고객 니즈, 자주 등장한 니즈, 검토 필요 매칭 현황을 빠르게 확인하는 시작 화면으로 설계한다.
* 고객 니즈 검색 화면은 고객사·산업군·키워드 필터를 기본으로 한다.
* 니즈-기능-가치 매칭 화면은 고객 니즈에 연결된 추천 기능, 제안 메시지, 매칭 근거를 확인하는 화면으로 설계한다.
* 고객사별 대응 전략 화면은 추천 접근 방향과 다음 미팅 추천 질문을 확인하는 화면으로 설계한다.
* 소프트웨어별 가치 맵 화면은 소프트웨어 기능 목록과 연결 가능한 고객 니즈를 확인하는 화면으로 설계한다.
* 고객 니즈와 기능 매칭은 데이터에 기록된 근거를 우선하며, 데이터에 없는 고객 의도·구매 가능성·확정 수요는 추정하지 않는다.
* 고객사·방문보고서·담당자 정보는 사내 업무용 정보로 취급하고, 외부 공개 또는 불필요한 복제를 금지한다.
* 서버 PC 운영 구조는 `marketing_llmwiki/` 기준으로 하며, `incoming/` → `data/` 반영과 `backup/` 보관 흐름을 전제로 한다.
* 사용자 컴퓨터의 `knowledge_for_marketing/`은 개발·관리 영역이며, 서버 PC의 `marketing_llmwiki/`는 운영 영역으로 분리한다.
* 사용자 컴퓨터의 기본 폴더 구조는 `raw/`, `wiki/`, `export/`, `dashboard/`, `scripts/`, `logs/`로 본다.
* 서버 PC의 기본 폴더 구조는 `dashboard/`, `incoming/`, `data/`, `backup/`, `logs/`로 본다.
* 서버 PC 전송은 `incoming/`에 임시 반영한 뒤 검증 후 `data/`로 교체하고, 기존 `data/`는 `backup/`에 보관하는 흐름을 기본으로 한다.
* 대시보드 최종 운영은 서버 PC의 Docker 환경을 목표로 하지만, 초기 개발은 로컬 Streamlit 실행을 우선한다.
* `dashboard_visible=FALSE` 데이터는 기본 화면에서 숨긴다. `sensitivity_level=비공개` 데이터는 숨김 기준이 아니라 경고 배지 표시 기준으로 사용한다.
* 권한은 `admin`과 `viewer`로 구분한다. admin은 사용자이며 데이터 수정·검토 상태·신뢰도 관리·운영 반영을 수행하고, viewer인 팀원은 조회만 가능하다.
* 1차 개발에는 viewer 코멘트 기능을 포함하지 않는다. admin 수정 기능은 저장 방식 확정 후 별도 작업으로 진행한다.
* 담당자는 매칭 신뢰도, 검토 상태, 담당자 직접 입력 정보를 관리할 수 있어야 한다. 단, 1차 개발에서는 저장 방식과 권한 방식이 확정된 이후 구현한다.
* 최종 보고서는 `output/final_report_<slug>.md`를 원본으로 작성하고, 필요 시 PDF로 변환한다.
* Codex의 CSV 구조 확인·검증 결과는 `output/analysis_summary_<slug>.md`에 포함한다.
* PROJECT.md는 현황판(Work Board + Decisions + Handoff Log) 중심으로 운영하고, 상세 메타 규칙은 `docs/collab-protocol.md`로 분리한다.

## Handoff Log

작업 종료 시 `[YYYY-MM-DD HH:mm] actor / changed: ... / verified: ... / next: ...` 형식으로 1줄 기록한다. 최근 10개를 유지하고 초과분은 `archive/`로 이동한다(상세 `docs/collab-protocol.md` §6).

* [2026-06-06 00:00] ChatGPT / changed: 첨부된 `project-overview(1).md`의 목적·흐름·진행 상태·로컬/서버 폴더 구조를 반영해 PROJECT.md 업데이트 / verified: GitHub의 기존 PROJECT.md 구조와 사용자 제공 프로젝트 개요를 대조 / next: Claude Code 또는 Codex가 WB-001부터 순차 진행
