# PROJECT.md

`knowledge_for_marketing_dashboard` 프로젝트의 현황판이다. Codex와 Claude Code가 협업할 때 보는 단일 진실 원천이며, 현재 작업 상태·결정사항·인수인계 기록을 이 파일에서 관리한다.

Goal: 고객 방문보고서에서 파악한 고객 니즈와 소프트웨어 기능 업데이트 자료를 연결해, **마케팅 활용**과 **고객 미팅 준비**에 필요한 고객 니즈 / 추천 기능 / 제안 메시지 / 다음 확인 질문을 빠르게 확인할 수 있는 사내 Streamlit 웹 대시보드를 구축한다.

Project flow: `방문보고서·기능 업데이트 자료` → `Claude 분석·정리` → `wiki/ 지식 위키` → `export CSV 4개` → `로컬 대시보드 개발` → `robocopy로 서버 PC에 필요 파일만 전송` → `사내망 내부 팀 공유 웹 대시보드`.

메타 규칙: `docs/collab-protocol.md` — 역할, Work Board/락/Decisions/Handoff Log 갱신 방식, 파일 네이밍, 협업 환경 정책, archive 규칙.

* * *

## Work Board

활성 작업(`in_progress`, `blocked`, `todo`)만 표시. 산출물(`output/*`, `data/*`, `scripts/*`, `dashboard/*`) 작업은 짧아도 행을 만들고, 메타 문서(화이트리스트) 단순 수정은 Handoff Log만 기록한다. 상세 룰은 `docs/collab-protocol.md`를 따른다.

| ID | Status | Owner | Task | Files locked | Slug | Last update |
|---|---|---|---|---|---|---|
| WB-001 | todo | Claude Code | 6개 화면 기준 핵심 질문 정리: Home, 고객 니즈 검색, 니즈-기능-가치 매칭, 고객사별 대응 전략, 소프트웨어별 가치 맵, 마케팅 활용 | `output/questions_dashboard-foundation.md` | dashboard-foundation | 2026-06-06 |
| WB-002 | todo | Codex | 실제 export CSV 4개 구조 확인 및 customer_id 추가 기준 정리 | `docs/data-schema.md`, `output/analysis_summary_dashboard-foundation.md` | dashboard-foundation | 2026-06-06 |
| WB-003 | todo | Codex | Streamlit 앱 기본 구조 생성 및 6개 화면 라우팅 골격 작성 | `dashboard/*`, `README.md` | streamlit-app-foundation | 2026-06-06 |
| WB-004 | todo | Claude Code | Codex 산출물의 데이터 해석, 마케팅 과잉 표현, 내부 정보 노출 주의사항 검토 | `output/risk_notes_dashboard-foundation.md` | dashboard-foundation | 2026-06-06 |
| WB-005 | todo | Codex | 로컬 개발 파일 중 서버 웹 대시보드 운영에 필요한 파일만 robocopy로 복사하는 배포 흐름 설계 | `docs/deploy-flow.md`, `scripts/*` | server-deploy-flow | 2026-06-06 |
| WB-006 | todo | Codex | 사내망 내부 계정 로그인, admin/viewer 권한, admin override CSV 저장 방식의 1차 구현 계획 작성 | `output/execution_plan_permission-review.md` | permission-review | 2026-06-06 |
| WB-007 | todo | Codex | export 생성 규칙 업데이트 계획 작성: customer_id 추가, dashboard_visible TRUE/FALSE 유지 | `output/execution_plan_export-schema-update.md`, `docs/data-schema.md` | export-schema-update | 2026-06-06 |

## Decisions

* 사용자 요청이 기본 협업 모델보다 우선한다.
* actor 간 이견은 사용자가 직접 우선순위와 담당자를 지정해서 해결한다.
* 복잡한 작업은 Claude Code 계획 → Codex 보완·구현 → Claude Code 최종 문서화 흐름을 기본으로 한다.
* 협업 문서는 이어받기 필요한 작업에서만 만든다.
* 모든 협업/결과물 문서는 작업별 slug를 붙여 구분한다.
* 파일 동시 수정은 Work Board `in_progress` 상태와 `Files locked` 컬럼으로 락을 잡아 방지한다.
* Codex 기본은 main 직접 작업이며, 큰 작업 또는 사용자 명시 지시 시 채팅 시작 전 `Start in → New worktree`를 선택한다.
* 실제 Streamlit 구현처럼 변경 파일이 많은 작업은 작업 규모별로 판단하되, 큰 작업은 worktree 사용을 권장한다.
* 원본 자료(`raw/`)와 지식 위키(`wiki/`)는 사용자 컴퓨터에서 관리하는 원본 지식 베이스로 간주하며, 대시보드 작업 중 직접 수정하지 않는다.
* 대시보드는 `export/`에서 생성된 CSV 4개를 읽는 구조로 설계한다. 실제 고객 데이터는 Git 커밋 대상에서 제외한다.
* 실제 export 파일은 `customer_needs.csv`, `software_features.csv`, `need_value_matching.csv`, `customer_strategy.csv` 4개이며 총 44개 컬럼이다.
* 현재 실제 export CSV에는 `customer_id`가 없다. 고객사 식별 안정성을 위해 export 생성 시 `customer_id` 컬럼을 추가하도록 업데이트한다.
* `review_status`는 1차 범위에서 제외한다. 현재 존재하는 `confidence`를 니즈-기능 매칭 신뢰도 표시 기준으로 사용한다.
* 내부 직원용 사내망 대시보드이므로 `sensitivity_level`은 대시보드 표시/필터/배지 기준으로 사용하지 않는다. 기존 CSV에 컬럼이 남아 있어도 화면 기능의 기준으로 삼지 않는다.
* `dashboard_visible` 값은 `TRUE/FALSE`로 유지한다. `FALSE` 데이터는 기본 화면에서 숨긴다.
* 대시보드 개발은 사용자 컴퓨터의 `dashboard/` 폴더 내부를 기본 작업 범위로 한다. 상위 폴더의 `raw/`, `wiki/`, `export/`, `scripts/`, `logs/`는 사용자 지시 없이는 수정하지 않는다.
* 대시보드는 Streamlit + pandas + Docker + Python 3.13 기준으로 개발한다.
* 대시보드 화면은 Home, 고객 니즈 검색, 니즈-기능-가치 매칭, 고객사별 대응 전략, 소프트웨어별 가치 맵, 마케팅 활용 6개를 기본 범위로 한다.
* 마케팅 활용 화면은 별도 화면으로 추가한다. 반복 니즈, 산업군/고객군별 관심 주제, 소프트웨어별 가치 메시지 후보, 세미나/제안서/기술 콘텐츠 후보를 확인하는 화면으로 설계한다.
* 고객 니즈와 기능 매칭은 데이터에 기록된 근거를 우선하며, 데이터에 없는 고객 의도·구매 가능성·확정 수요는 추정하지 않는다.
* 마케팅 활용 후보는 확정 캠페인 또는 확정 고객 수요처럼 표현하지 않는다. 후보, 검토 필요, 근거 확인 필요 표현을 사용한다.
* 대시보드는 사내망 내부 접속 전용으로 운영한다. 외부 인터넷 접속은 진행하지 않는다.
* 권한은 계정별 로그인 기반으로 구분한다. `admin` 계정은 조회와 수정이 가능하고, 그 외 계정은 `viewer`로 조회만 가능하다.
* 접속은 계정과 비밀번호 입력 후 가능하게 한다. 인증 정보는 코드에 하드코딩하지 않고, 환경변수 또는 Streamlit secrets 등 외부 설정으로 관리한다.
* admin 수정값은 원본 CSV를 직접 수정하지 않고 별도 override CSV에 저장한다.
* override CSV는 `data/overrides/` 또는 서버 운영 기준의 `data/overrides/` 하위에 저장하는 것을 기본 후보로 하며, 파일명과 컬럼은 Codex가 실행 계획에서 확정 제안한다.
* admin 수정 이력은 최소한 수정자, 수정 시각, 대상 ID, 변경 전/후 값을 남기는 방향으로 설계한다.
* 원본 파일과 대시보드 개발은 사용자 컴퓨터에서 진행한다.
* 서버 PC에는 robocopy를 이용해 웹 기반 대시보드 구현에 필요한 파일만 복사한다. 원본 `raw/`, `wiki/`, 전체 개발 로그는 서버 전송 대상에서 제외한다.
* 서버 PC 운영 구조는 `marketing_llmwiki/` 기준으로 하며, `incoming/` → 검증 → `backup/` → `data/` 반영 흐름을 전제로 한다.
* CSV가 바뀔 때는 대시보드 데이터 새로고침 기능을 우선 사용하고, 코드 변경이나 장애 대응 시 Docker 컨테이너 재시작도 사용한다.
* 사용자 컴퓨터의 기본 폴더 구조는 `raw/`, `wiki/`, `export/`, `dashboard/`, `scripts/`, `logs/`로 본다.
* 서버 PC의 기본 폴더 구조는 `dashboard/`, `incoming/`, `data/`, `backup/`, `logs/`로 본다.
* Codex의 CSV 구조 확인·검증 결과는 `output/analysis_summary_<slug>.md`에 포함한다.
* PROJECT.md는 현황판(Work Board + Decisions + Handoff Log) 중심으로 운영하고, 상세 메타 규칙은 `docs/collab-protocol.md`로 분리한다.

## Handoff Log

작업 종료 시 `[YYYY-MM-DD HH:mm] actor / changed: ... / verified: ... / next: ...` 형식으로 1줄 기록한다. 최근 10개를 유지하고 초과분은 `archive/`로 이동한다.

* [2026-06-06 00:00] ChatGPT / changed: `DESIGN.md`의 디자인 목표를 마케팅 활용과 고객 미팅 준비 두 축으로 수정 / verified: 디자인 목표, 설계 원칙, Home 표시 항목, 마케팅 활용 관점, PDF·출력 기준에 마케팅 목적 반영 확인 / next: docs/templates.md 정합성 점검 권장
* [2026-06-06 00:00] ChatGPT / changed: `docs/templates.md`를 마케팅 활용과 고객 미팅 준비를 모두 반영한 협업 문서 표준 구조로 재정리 / verified: dev_plan, questions, risk_notes, final_report, handoff, execution_plan, analysis_summary, data_schema, deploy_flow 템플릿에 dashboard/ 기준과 데이터 보호 기준 반영 확인 / next: README.md 또는 실제 WB-001 작업 착수 권장
* [2026-06-06 00:00] ChatGPT / changed: 사용자 12개 결정을 PROJECT.md에 공식 반영하고 마케팅 별도 화면, 계정별 로그인, override CSV, customer_id, sensitivity_level 3단계, robocopy 서버 전송 기준 추가 / verified: 첨부 CSV 컬럼 목록과 사용자 결정사항 대조 / next: business-context.md, collab-protocol.md, AGENTS.md, CLAUDE.md, DESIGN.md, templates.md에 동일 결정 반영
* [2026-06-06 00:00] ChatGPT / changed: `AGENTS.md`, `CLAUDE.md`, `DESIGN.md`에 최종 결정사항 반영 / verified: Codex 실행 기준, Claude 검토 기준, 디자인 화면 기준에서 6개 화면·실제 CSV·목표 export 스키마·권한·override·robocopy 반영 확인 / next: docs/templates.md는 안전 검사로 업데이트 실패, 이후 더 작은 단위로 재시도 권장
* [2026-06-06 00:00] ChatGPT / changed: 내부 사내망 전용 운영, 민감도 미사용, review_status 1차 제외, confidence 중심 매칭 표시 기준을 PROJECT.md에 반영 / verified: 사용자 최신 결정 3건과 기존 Decisions 대조 / next: business-context.md, collab-protocol.md, AGENTS.md, CLAUDE.md, DESIGN.md, templates.md 동일 기준 반영
