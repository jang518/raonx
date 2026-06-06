# PROJECT.md

`knowledge_for_marketing_dashboard` 프로젝트의 현황판이다. Codex와 Claude Code가 협업할 때 보는 단일 진실 원천이며, 현재 작업 상태·결정사항·인수인계 기록을 이 파일에서 관리한다.

Goal: 고객 방문보고서에서 파악한 고객 니즈와 소프트웨어 기능 업데이트 자료를 연결해, **마케팅 활용**과 **고객 미팅 준비**에 필요한 고객 니즈 / 추천 기능 / 제안 메시지 / 다음 확인 질문을 빠르게 확인할 수 있는 사내 Streamlit 웹 대시보드를 구축한다.

Project flow: `방문보고서·기능 업데이트 자료` → `Claude 분석·정리` → `wiki/ 지식 위키` → `export CSV 5개` → `로컬 대시보드 개발` → `robocopy로 서버 PC에 필요 파일만 전송` → `사내망 내부 팀 공유 웹 대시보드`.

메타 규칙: `docs/collab-protocol.md` — 역할, Work Board/락/Decisions/Handoff Log 갱신 방식, 파일 네이밍, 협업 환경 정책, archive 규칙.

---

## Work Board

활성 작업(`in_progress`, `blocked`, `todo`)만 표시한다. 산출물(`output/*`, `data/*`, `scripts/*`, `dashboard/*`) 작업은 짧아도 행을 만들고, 메타 문서 단순 수정은 Handoff Log만 기록한다. 상세 룰은 `docs/collab-protocol.md`를 따른다.

| ID | Status | Owner | Task | Files locked | Slug | Last update |
|---|---|---|---|---|---|---|
| WB-001 | todo | Claude Code | v5 데이터 관계 기준 6개 화면 핵심 질문 정리 | `output/questions_dashboard-foundation.md` | dashboard-foundation | 2026-06-06 |
| WB-002 | todo | Codex | export CSV 5개 구조와 관계 무결성 검증 문서 작성 | `docs/data-schema.md`, `output/analysis_summary_dashboard-foundation.md` | dashboard-foundation | 2026-06-06 |
| WB-003 | todo | Codex | Streamlit 앱 기본 구조 생성 및 6개 화면 라우팅 골격 작성 | `dashboard/*`, `README.md` | streamlit-app-foundation | 2026-06-06 |
| WB-004 | todo | Claude Code | Codex 산출물의 데이터 해석, 마케팅 과잉 표현, 내부 정보 노출 주의사항 검토 | `output/risk_notes_dashboard-foundation.md` | dashboard-foundation | 2026-06-06 |
| WB-005 | todo | Codex | robocopy 기반 서버 반영 흐름 설계: export CSV 5개와 대시보드 운영 파일만 전송 | `docs/deploy-flow.md`, `scripts/*` | server-deploy-flow | 2026-06-06 |
| WB-006 | todo | Codex | 사내망 내부 계정 로그인, admin/viewer 권한, admin override CSV 저장 방식의 1차 구현 계획 작성 | `output/execution_plan_permission-review.md` | permission-review | 2026-06-06 |

## Decisions

* 사용자 요청이 기본 협업 모델보다 우선한다.
* 복잡한 작업은 Claude Code 계획 → Codex 보완·구현 → Claude Code 최종 문서화 흐름을 기본으로 한다.
* 협업 문서는 이어받기 필요한 작업에서만 만든다.
* 모든 협업/결과물 문서는 작업별 slug를 붙여 구분한다.
* 파일 동시 수정은 Work Board `in_progress` 상태와 `Files locked` 컬럼으로 락을 잡아 방지한다.
* Codex 기본은 main 직접 작업이며, 실제 Streamlit 구현처럼 변경 파일이 많은 작업은 worktree 사용을 권장한다.
* 원본 자료(`raw/`)와 지식 위키(`wiki/`)는 사용자 컴퓨터에서 관리하는 원본 지식 베이스로 간주하며, 대시보드 작업 중 직접 수정하지 않는다.
* 대시보드는 v5 기준 `export/` CSV 5개를 읽는 구조로 설계한다. 실제 고객 데이터는 Git 커밋 대상에서 제외한다.
* v5 export CSV는 `customer_master.csv`, `customer_needs.csv`, `software_features.csv`, `need_value_matching.csv`, `customer_strategy.csv` 5개다.
* `customer_master.csv`는 고객 정보의 단일 진실 공급원(SSOT)이다. 고객 ID, 표준 고객사명, 부서, 산업군, 고객 등급, 방문 횟수, 작성자, 표시 여부를 관리한다.
* 고객사 단위 연결은 `customer_name` 문자열이 아니라 `customer_id`를 우선 사용한다.
* `customer_needs.csv`와 `customer_strategy.csv`의 `customer_name`은 표시용이다. 고객 기본 정보는 `customer_master.csv`에서 참조한다.
* `need_value_matching.csv`에는 `customer_id`를 중복 추가하지 않는다. 고객 정보가 필요하면 `need_id`로 `customer_needs.csv`에 연결한 뒤 `customer_id`로 `customer_master.csv`를 참조한다.
* `software_features.csv`에는 `customer_id`를 추가하지 않는다.
* `review_status`는 1차 범위에서 제외한다. 현재 존재하는 `confidence`를 니즈-기능 매칭 신뢰도 표시 기준으로 사용한다.
* 내부 직원용 사내망 대시보드이므로 `sensitivity_level`은 대시보드 표시/필터/배지 기준으로 사용하지 않는다. 기존 CSV에 컬럼이 남아 있어도 화면 기능의 기준으로 삼지 않는다.
* `dashboard_visible` 값은 모든 CSV에서 기본 표시 여부 판단에 사용한다. `FALSE` 데이터는 기본 화면에서 숨긴다.
* 대시보드는 Streamlit + pandas + Docker + Python 3.13 기준으로 개발한다.
* 대시보드 화면은 Home, 고객 니즈 검색, 니즈-기능-가치 매칭, 고객사별 대응 전략, 소프트웨어별 가치 맵, 마케팅 활용 6개를 기본 범위로 한다.
* 마케팅 활용 후보는 확정 캠페인 또는 확정 고객 수요처럼 표현하지 않는다. 후보, 검토 필요, 근거 확인 필요 표현을 사용한다.
* 대시보드는 사내망 내부 접속 전용으로 운영한다. 외부 인터넷 접속은 진행하지 않는다.
* 권한은 계정별 로그인 기반으로 구분한다. `admin` 계정은 조회와 수정이 가능하고, 그 외 계정은 `viewer`로 조회만 가능하다.
* 인증 정보는 코드에 하드코딩하지 않고 환경변수 또는 Streamlit secrets 등 외부 설정으로 관리한다.
* admin 수정값은 원본 CSV를 직접 수정하지 않고 별도 override CSV에 저장한다.
* override CSV는 `data/overrides/` 또는 서버 운영 기준의 `data/overrides/` 하위에 저장하는 것을 기본 후보로 하며, 파일명과 컬럼은 Codex가 실행 계획에서 확정 제안한다.
* 원본 파일과 대시보드 개발은 사용자 컴퓨터에서 진행한다.
* 서버 PC에는 robocopy를 이용해 웹 기반 대시보드 구현에 필요한 파일만 복사한다. 원본 `raw/`, `wiki/`, 전체 개발 로그는 서버 전송 대상에서 제외한다.
* 서버 PC 운영 구조는 `marketing_llmwiki/` 기준으로 하며, `incoming/` → 검증 → `backup/` → `data/` 반영 흐름을 전제로 한다.
* CSV가 바뀔 때는 대시보드 데이터 새로고침 기능을 우선 사용하고, 코드 변경이나 장애 대응 시 Docker 컨테이너 재시작도 사용한다.
* Codex의 CSV 구조 확인·검증 결과는 `output/analysis_summary_<slug>.md`에 포함한다.
* PROJECT.md는 현황판(Work Board + Decisions + Handoff Log) 중심으로 운영하고, 상세 메타 규칙은 `docs/collab-protocol.md`로 분리한다.

## Handoff Log

작업 종료 시 `[YYYY-MM-DD HH:mm] actor / changed: ... / verified: ... / next: ...` 형식으로 1줄 기록한다. 최근 10개를 유지하고 초과분은 `archive/`로 이동한다.

* [2026-06-06 00:00] ChatGPT / changed: 프로젝트 목적을 마케팅 활용과 고객 미팅 준비 두 축으로 정리하고 6개 화면 기준을 확정 / verified: Home, 고객 니즈 검색, 니즈-기능-가치 매칭, 고객사별 대응 전략, 소프트웨어별 가치 맵, 마케팅 활용 범위 확인 / next: 화면별 핵심 질문 정리 착수
* [2026-06-06 00:00] ChatGPT / changed: 사내망 내부 전용 운영, 외부 인터넷 접속 제외, admin/viewer 계정 로그인, override CSV 저장 기준을 정리 / verified: 서버 PC에는 robocopy로 운영 필요 파일만 전송하고 원본 raw/wiki 및 실데이터는 Git 커밋 제외 기준 확인 / next: permission-review 실행 계획 작성
* [2026-06-06 00:00] ChatGPT / changed: `review_status`는 1차 범위에서 제외하고 `sensitivity_level`은 내부 직원용 대시보드의 표시/필터/배지 기준에서 제외 / verified: business-context.md, collab-protocol.md, AGENTS.md, CLAUDE.md, DESIGN.md, docs/templates.md와 정합성 확인 / next: Codex가 CSV와 목표 export 스키마 차이를 정리
* [2026-06-06 00:00] ChatGPT / changed: v5 export 구조 반영: `customer_master.csv`를 고객 정보 SSOT로 추가하고 export CSV 5개 관계를 customer_id/need_id/feature_id 기준으로 정리 / verified: customer_master→needs/strategy, needs→matching, features→matching 관계 확인 / next: 7개 메타 문서 전체를 v5 기준으로 동기화
* [2026-06-06 00:00] ChatGPT / changed: 7개 메타 문서를 v5 데이터 관계 기준으로 통합 업데이트 / verified: CSV 5개, customer_master SSOT, customer_id 우선 연결, confidence 중심, sensitivity 미사용, review_status 제외 기준 확인 / next: WB-002 또는 WB-003 착수 권장
