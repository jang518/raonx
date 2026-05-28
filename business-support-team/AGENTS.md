# AGENTS.md — Codex 작업 지침

## 1. 역할

경영지원팀 CAE 매출 대시보드 프로젝트의 **실행 담당**이다.

핵심 임무:

* Excel 기반 CAE 매출/손익 데이터 정리, 정규화, 검증
* `팀별실적`, `목표`, 선택적 `실적G` 시트를 기반으로 대시보드 데이터 생성
* 사내 웹 대시보드 구현, 로그인 기능 구현, 렌더링/오류 검증
* Docker Compose 기반 Windows 11 운영 PC 배포 구성 검토 및 구현
* Claude Code의 1차 계획을 실행 가능하게 보완 후 구현
* 파일/렌더링/데이터/배포 오류 검증 + 다음 actor 인계 문서 작성

Claude Code가 계획/해석/문서화, Codex가 구현/검증.

## 2. 먼저 읽을 문서 (순서 고정)

1. `PROJECT.md` — 현황판 (Work Board / Decisions / Handoff Log)
2. `docs/collab-protocol.md` — 협업 메타 규칙 (역할 / 갱신 방식 / 네이밍 / 환경 / archive)
3. `business-context.md` — 회사/팀/데이터 맥락, CAE 매출 해석 기준
4. `DESIGN.md` — 대시보드 시각 기준, 산출물 스타일
5. `CLAUDE.md` — Claude Code 영역 (충돌 회피)
6. `docs/templates.md` — 협업 문서 표준 구조
7. Claude Code의 `dev_plan_<slug>.md` + Handoff Log
8. 필요 시 `config/dashboard.yml`, `config/data-mapping.yml`, `config/metrics.yml`

## 3. Codex가 만드는 결과물

| 역할           | 결과물 문서/파일                                                 | 트리거                       |
| ------------ | --------------------------------------------------------- | ------------------------- |
| 1차 계획 보완     | `output/execution_plan_<slug>.md`                         | `dev_plan_<slug>.md` 받은 후 |
| Excel 구조 분석  | `output/excel_profile_<slug>.md`                          | 원천 Excel 구조 확인 요청 시       |
| 데이터 정리/정규화   | `data/processed/*` 또는 DB/캐시 파일                            | 데이터 작업 요청 시               |
| 데이터 매핑 설정    | `config/data-mapping.yml`                                 | Excel 시트/컬럼/팀명 매핑 정의 시    |
| 지표 계산 설정     | `config/metrics.yml`                                      | KPI/목표/기여도 계산식 정의 시       |
| 화면 구성 설정     | `config/dashboard.yml`                                    | KPI/차트/테이블/필터 구성 정의 시     |
| 데이터 검증 리포트   | `output/data_quality_<slug>.md`                           | 누락/오류/이상치 확인 시            |
| 데이터 분석 요약    | `output/analysis_summary_<slug>.md`                       | 분석 결과 정리 시                |
| 웹 대시보드 구현    | `app/*`, `server/*`, `dashboard/*`                        | 대시보드 구현 요청 시              |
| 로그인/권한 기능    | `app/*`, `server/*`, `config/*`                           | 인증 기능 구현 요청 시             |
| Docker 배포 구성 | `deploy/compose.yaml`, `.env.example`, `deploy/README.md` | Windows 11 운영 PC 배포 요청 시  |
| 운영 문서        | `output/ops_guide_<slug>.md` 또는 `deploy/README.md`        | 실행/중지/재시작/백업 안내 필요 시      |
| 다음 actor 인계  | `output/handoff_<slug>.md`                                | 작업 종료 + 인계 필요 시           |

각 문서의 표준 구조는 `docs/templates.md` 참조. 오류 디버깅, 렌더링 확인, 데이터 검증, 배포 가능성 확인은 모든 산출물의 기본 책임.

## 4. Claude Code에게 남기는 일

상세는 `CLAUDE.md` 3절. 주요:

* `output/dev_plan_<slug>.md` (1차 계획)
* `output/questions_<slug>.md` (경영진/경영지원팀이 봐야 할 질문 설계)
* `output/risk_notes_<slug>.md` (데이터/보안/운영 리스크 점검)
* `output/final_report_<slug>.md` (경영진용 요약 보고서)
* `output/review_notes_<slug>.md` (Codex 구현물 검토 의견)

위 문서는 사용자 명시 요청 없이는 Codex가 만들거나 덮어쓰지 않는다. 수정 요청은 `execution_plan_<slug>.md` 또는 `handoff_<slug>.md`에 리뷰 코멘트로 남긴다.

## 5. 개발 작업 시작 순서

기본 협업 모델 (사용자 지시 우선):

1. Claude Code가 `output/dev_plan_<slug>.md` 작성
2. Codex가 `dev_plan_<slug>.md` 읽고 `execution_plan_<slug>.md`로 보완
3. PROJECT.md Work Board에 행 추가

   * Status `in_progress`
   * `Files locked`에 수정할 파일 기재
   * 산출물(`output/*`, `data/*`, `scripts/*`, `app/*`, `server/*`, `dashboard/*`, `config/*`, `deploy/*`) 작업 시 필수
4. 구현 시작
5. 구현 후 검증

   * Excel 파싱 검증
   * 데이터 집계 검증
   * 설정 파일 검증
   * 웹 렌더링 검증
   * 로그인/권한 검증
   * Docker Compose 실행 가능성 검증
6. 다음 actor 인계 필요 시 `output/handoff_<slug>.md` 작성

생략 기준 (별개 항목):

* `dev_plan/execution_plan` 생략: 단순 문서 수정, 샘플 데이터 보정, 긴급 수정, 단일 파일 수정
* **Work Board 락 생략**: 메타 문서(메인 직접 수정 화이트리스트) 단순 수정만. 산출물은 짧아도 행을 잡았다 뺀다.

## 6. Claude Code 산출물 받기 체크리스트

`dev_plan_<slug>.md`나 `risk_notes_<slug>.md`를 받으면 구현 전 확인:

```
[ ] Goal/Scope/Out of Scope 명확
[ ] Files 항목에 수정 파일과 건드리지 말 파일 분리
[ ] Work Board에 충돌 락 없음 (같은 파일을 다른 actor가 in_progress로 잡지 않음)
[ ] Proposed Steps 실행 가능 (불가/누락이면 execution_plan에 보완)
[ ] Verification 기준 측정 가능
[ ] Risks가 데이터 한계, 사내 보안, 운영 PC 환경과 일치
[ ] Handoff Needed 명시
[ ] 사용자가 지시한 도구/순서 없음 (있으면 사용자 우선)
[ ] 실제 Excel 원천 파일을 덮어쓰지 않는 흐름인지 확인
[ ] 로그인/권한/민감정보 처리 범위가 명확
[ ] Docker 배포가 확정인지, 검토 후보인지 구분
```

체크에서 걸리면 구현 시작 X. `execution_plan_<slug>.md`에 보완 사항 적거나 사용자에게 보고.

## 7. 협업 인터페이스

**Claude → Codex** (`dev_plan_<slug>.md`): Goal, Scope/Out of Scope, 수정 파일 vs 건드리지 말 파일, Proposed Steps, Verification, Handoff Needed

**Codex → Claude** (`handoff_<slug>.md`): What Changed, Verified(명령/결과 포함), Lock State 해제, Risks Carried Over, Open Items

**Codex → Codex** (`handoff_<slug>.md`): 어떤 명령으로 어디까지 확인했는지(Verified) 자세히 기록.

## 8. 워크트리 사용 핵심

상세는 `docs/collab-protocol.md` §4. 핵심:

* **Codex 기본은 메인 직접 작업 (Work locally)** — UX 마찰 최소화, 즉시 가시성
* **큰 작업 / 사용자 명시 지시 시 채팅 시작 전 "Start in → New worktree" 선택** — 메인 복사본 생성, 메인 안전
* **화이트리스트 (메인 SSOT)**: `PROJECT.md`, `CLAUDE.md`, `AGENTS.md`, `business-context.md`, `DESIGN.md`, `docs/collab-protocol.md`, `docs/templates.md`, `.gitignore`, `.gitattributes`
* Codex 메인 작업(Work locally)은 메인에서 직접 수정
* "Start in → New worktree" 사용 시 워크트리가 곧 작업 base이므로 초기 cp 동기화 불필요
* 종료 시 **cp 적용 직전 메인 최신 재확인** → cp로 메인 적용 → 30초 내 commit
* **cp 동기화는 우회 없이 항상 실행** — 산출물 작업이라도 PROJECT.md cp로 Work Board 락을 메인에 visible하게 한 뒤 산출물에 손댄다
* cp 차단 시 1회 재시도 후에도 실패하면 사용자 보고
* 스크립트 in-place replace, 락 등록 생략, "산출물만 먼저 만들기" 우회 금지
* 워크트리 격리 영역: `data/`, `scripts/`, `output/`, `archive/`, `app/`, `server/`, `dashboard/`, `config/`, `deploy/`
* **작업 중간 "Handoff to worktree" 금지** — 메인을 `git reset --hard`로 정리하여 미커밋 변경/untracked 모두 손실 가능. 부득이한 경우에만 `git commit` 또는 `git stash -u` 선행
* 종료: `git merge codex/<slug>` 또는 cp → PROJECT.md Work Board 락 해제 → `git worktree remove` → 메인에서 즉시 commit

## 9. 데이터 판단 원칙

이 프로젝트는 경영지원팀이 Excel로 관리하는 CAE 소프트웨어 판매 / 유지보수 / 용역 / 컨설팅 관련 매출·손익 데이터를 사내 웹 대시보드로 조회하기 위한 프로젝트다.

핵심 판단 기준:

* 대시보드 사용자는 경영진과 경영지원팀이다.
* 숫자 해석은 경영 의사결정에 필요한 수준으로 단순하고 명확해야 한다.
* 원천 Excel에 없는 계약 가능성, 예상 매출, 영업 확률, 미확정 수주, 고객사별 전망은 추정하지 않는다.
* 매출 인식 기준, 부가세 포함 여부, 유지보수 기간 배분, 다년 계약 처리 방식은 사용자 확인 전까지 임의로 확정하지 않는다.
* 금액 집계는 원천 Excel에 있는 값과 명시된 계산 규칙만 사용한다.
* 데이터 검증 결과는 숨기지 않는다.
* 누락 컬럼, 빈 금액, 날짜/월 형식 오류, 중복 팀명, 음수 매출, 비정상적으로 큰 금액, Excel 수식 오류는 별도 경고로 표시한다.

추천 문장이나 대시보드 해석은 반드시 근거 지표 + 다음 확인 항목을 함께 제시한다.

## 10. 민감정보 및 사내 데이터 제한

**사용 가능 데이터**:

* 사용자가 제공한 원천 Excel 파일
* 사내 사용을 전제로 한 팀별 실적/목표 데이터
* 사용자가 명시적으로 허용한 설정 정보
* 익명화된 샘플 데이터

**금지**:

* 실제 고객사명, 계약 금액, 유지보수 금액, 담당자 정보가 포함된 데이터를 외부 공개 저장소에 업로드
* 실제 원천 Excel을 공개 GitHub에 커밋
* `.env`, 비밀번호, 세션 키, 사내 IP/포트, 로그인 계정 정보를 커밋
* 사용자 승인 없이 외부 인터넷 공개 배포
* 원천 Excel 파일 직접 덮어쓰기
* 데이터에 없는 매출 전망, 계약 가능성, 영업 확률 추정
* 고객사별 민감 정보를 마스킹 없이 샘플 데이터로 사용

필요 시 실제 데이터와 샘플 데이터를 분리한다. 샘플 데이터는 고객사명, 금액, 담당자, 계약 식별자를 익명화하거나 가공한다.

## 11. 스프레드시트 기준

현재 Excel 기준 핵심 원천 시트:

* `팀별실적`

  * 가장 핵심 데이터 원본
  * 팀별 월별 실적과 손익 항목을 가져옴
* `목표`

  * 목표값 비교용
  * 목표 달성률, 목표 대비 진행률, 팀별 기여도 계산에 사용
* `실적G`

  * 선택적 FCST 시트
  * 현재 파일에 없으면 무시
  * 있으면 실적이 없는 구간만 `매출이익` / `경비` 보완에 사용
* `total`, `실적(23~26)`, `경영현황비교`

  * 현재 메인 화면의 핵심 계산 원천은 아님
  * 참고용 또는 향후 확장 후보
* 팀별 개별 시트

  * `SDT`, `TST1`, `TST2`, `MKT`, `EST1`, `EST2`, `EST3` 등
  * 상세 검증 또는 향후 drill-down 후보

`팀별실적` 시트 기준 우선 사용 항목:

* `매출`
* `매입`
* `매출이익`
* `영업이익`
* `경상이익`
* `직접 비용`
* `간접 비용`
* `경비`

  * 있으면 그대로 사용
  * 없으면 `직접 비용 + 간접 비용`으로 계산
* 월 컬럼

  * `1월 ~ 12월`
* 기간 집계용 구조

  * 월별
  * 분기별 누적
  * 반기별 누적
  * 연간 누적

`목표` 시트 기준 계산:

* 팀별 목표값
* 연간 목표 달성률
* 목표 대비 매출이익 진행률
* 전체 진행률 중 팀별 기여도

현재 구조상 월 목표는 없고, 분기/반기/연간 목표 중심이다. 월별 조회 시 목표 관련 일부 지표는 현재 분기 목표 기준 fallback으로 계산한다.

FCST 반영 방식:

* `실적G` 시트가 있을 때만 반영
* 실적값이 있으면 실적 우선
* 실적값이 없으면 FCST로 보완
* 보완 대상:

  * `매출이익`
  * `경비`
  * 필요 시 `영업이익` 영향 반영
* `매출` 자체는 FCST 보완 대상이 아니다.

대시보드 첫 화면 우선 배치:

1. 총매출
2. 매출이익
3. 영업이익
4. 경비
5. 전월 대비 영업이익 증감률
6. 전월 대비 경비 증감률
7. 매출 추이
8. 영업이익 추이
9. 매출이익 추이
10. 팀별 매출 비교
11. 팀별 매출이익 비교
12. 팀별 영업이익 비교
13. 팀별 매출 기여도
14. 목표 대비 매출이익 진행률
15. 목표 대비 팀 기여도
16. 연간 목표 달성률
17. 팀별 상세 테이블

팀별 상세 테이블 우선 컬럼:

* `팀명`
* `매출`
* `매출이익`
* `영업이익`
* `경비`
* `목표대비매출이익기여도`
* `전체진행률기여도`
* `연간 목표달성률`
* `연간 목표대비부족액`

## 12. 산출물 스타일

상세는 `DESIGN.md`. 핵심:

* 경영진과 경영지원팀이 빠르게 판단할 수 있게
* 숫자, 목표 대비, 증감률, 경고를 명확히 분리
* 차트 제목은 가능하면 해석형으로 작성

  * 나쁜 예: `매출 추이`
  * 좋은 예: `3분기 누적 매출은 증가했지만 영업이익률은 둔화`
* 금액 단위, 기준 기간, 목표 기준을 화면에 명시
* 데이터 누락/FCST 반영 여부는 숨기지 않고 표시
* 색상/레이아웃은 `DESIGN.md` 토큰을 따른다
* 숫자 근거 없는 단정형 결론 금지
* 실제 고객사명이나 민감 정보가 화면에 노출되는 경우, 사용 권한과 표시 범위를 확인한다

## 13. Codex 채팅 운영 모델

상세는 `docs/collab-protocol.md` §4. 핵심:

* 단일 액티브 채팅 기본
* 작업 주제 변경 시 새 채팅 (순차)
* 병렬 필요 시 워크트리 격리 (예외, 사용자 명시 지시)
* 동시 메인 채팅 X (락 충돌 위험)
* 데이터 파싱, 대시보드 구현, 배포 구성처럼 범위가 큰 작업은 slug를 명확히 나누어 진행
* 예시 slug:

  * `excel-profile`
  * `data-normalization`
  * `dashboard-mvp`
  * `login-auth`
  * `docker-deploy`
  * `config-driven-dashboard`

## 14. 파일 락 & Handoff Log

상세는 `docs/collab-protocol.md` §2와 `PROJECT.md`의 Work Board · Handoff Log.

**락 적용 기준** (산출물 = Work Board, 메타 = Handoff Log만):

* 산출물 작업 (`output/*`, `data/*`, `scripts/*`, `app/*`, `server/*`, `dashboard/*`, `config/*`, `deploy/*` 생성·수정) → Work Board 행 추가 (Status `in_progress`, `Files locked` 기재). 종료 시 행 제거 = 락 해제 + Handoff Log 1줄.
* 메타 문서 (메인 직접 수정 화이트리스트) 단순 수정 → 락 없이 Handoff Log만.
* 즉시 처리 (인사·응답·정보 조회) → 둘 다 생략.

**Status 본질**: `in_progress` = 능동 작업 중(락 있음), `blocked` = 외부 input 대기(락 없음). 작업 중 외부 input 필요해지면 `in_progress` → `blocked` 전환하면서 `Files locked`를 비운다.

Handoff Log 형식 (메인 PROJECT.md에 1줄):

`[YYYY-MM-DD HH:mm] Codex / changed: ... / verified: ... / next: ...`

**Handoff Log archive 트리거**: 새 항목 추가 시 Handoff Log가 11개 이상이 되면, 같은 작업 안에서 가장 오래된 항목들을 5개 남을 때까지 `archive/handoff-YYYY-MM-DD.md`로 일괄 이동한다. 별도 작업으로 미루지 않는다.

**카운트 의무**: Handoff Log에 새 항목을 추가하기 직전 매번 현재 메인 PROJECT.md의 Handoff Log 개수를 카운트한다. 카운트 생략 금지. 추가 후 11 이상이면 같은 작업 안에서 archive 절차를 함께 수행한다.

## 15. 분쟁 처리 & self-handoff

actor 간 이견은 사용자 결정. Codex 절차:

1. 이견 핵심 정리

   * 예: Claude 계획과 실제 Excel 구조가 다름
   * 예: `실적G` 시트가 없는데 FCST 계산이 계획에 포함됨
   * 예: Docker 배포가 회사 PC 환경과 맞지 않음
   * 예: 로그인 구현 범위가 불명확함
2. 사용자에게 보고
3. 사용자 결정 전까지 충돌 영역 멈춤
4. 멈출 때는 락 해제
5. 사용자 지정 결정을 `PROJECT.md` Decisions에 1줄 추가

Codex 세션 A→B 인계: `handoff_<slug>.md` 작성, 락 모두 해제, 메인 PROJECT.md 갱신. 워크트리 작업이면 메인 적용 후 즉시 commit.

## 16. 작업 방식

* 쉘 명령은 가능하면 `rtk` 사용
* 기존 사용자 변경 되돌리지 않는다
* 파일 수정 전 어떤 파일을 왜 바꾸는지 짧게 알린다
* 산출물(`output/*`, `data/*`, `scripts/*`, `app/*`, `server/*`, `dashboard/*`, `config/*`, `deploy/*`) 수정 시 Work Board에 행 추가(Status `in_progress`, `Files locked` 기재) 후 시작
* 메타 문서(화이트리스트) 단순 수정은 락 없이 Handoff Log만
* Excel 파일을 읽을 때는 원천 파일을 직접 수정하지 않는다
* 원천 Excel은 `data/raw/`에 두고, 정제 결과는 `data/processed/`, DB, 캐시로 분리한다
* 대시보드 항목 추가/삭제/변경은 먼저 설정 파일 변경으로 가능한지 검토한다
* KPI, 차트, 테이블 컬럼, 필터, 팀 그룹, 시트명/컬럼명 매핑은 코드 하드코딩보다 설정 파일을 우선한다
* 설정 파일 변경 후에는 설정 검증 스크립트 또는 앱 기동 테스트로 깨짐 여부를 확인한다
* 스프레드시트 처리 후 inspect, 오류 검색, 정제 데이터 샘플 확인
* 웹 대시보드 구현 후 렌더링 확인, 콘솔 오류 확인, 로그인 흐름 확인
* Docker 배포 구성 후 `docker compose config` 수준의 구성 검증을 수행한다
* 가능하면 `docker compose up` 실행 결과와 헬스체크 결과를 확인한다
* 외부 데이터는 출처 표기
* 메인 기본 작업 (Work locally)
* 격리 필요 시 채팅 시작 전 "Start in → New worktree" 선택
* 작업 중간 Handoff to worktree 금지
* 메인 직접 수정 화이트리스트(특히 `PROJECT.md`) 편집 전 다른 actor 활성 감지
* 1분 이내 타 actor 커밋이면 대기, 편집 후 30초 내 commit

## 17. 금지

* Claude Code가 작성 중인 `final_report_<slug>.md`, `risk_notes_<slug>.md`, `questions_<slug>.md`, `dev_plan_<slug>.md` 임의 덮어쓰기 금지
* 사용자 modify 중이거나 락 잡힌 파일 덮어쓰기 금지
* 원천 Excel 파일 직접 덮어쓰기 금지
* 실제 고객사명, 계약 금액, 담당자 정보가 포함된 파일을 공개 저장소에 커밋 금지
* `.env`, 비밀번호, 세션 키, 사내 IP/포트, 로그인 계정 정보 커밋 금지
* 사용자 승인 없는 외부 공개 배포 금지
* 데이터에 없는 계약 가능성, 예상 매출, 영업 확률, 미확정 수주 추정 금지
* 매출 인식 기준, 부가세 포함 여부, 유지보수 기간 배분, 다년 계약 처리 방식을 임의 확정 금지
* `실적G` 시트가 없는데 FCST가 있는 것처럼 계산 금지
* 실적값이 있는데 FCST로 덮어쓰기 금지
* `매출` 자체를 FCST로 보완 금지
* 월 목표가 없는데 월 목표가 있는 것처럼 표시 금지
* 설정 파일로 처리할 수 있는 KPI/차트/테이블 변경을 불필요하게 앱 코드에 하드코딩 금지
* 워크트리에서 화이트리스트 파일을 cp 동기화/적용 절차 없이 처음부터 작성 금지
* cp 동기화 차단/오류 발생 시 우회 금지
* 산출물 작업 시 Work Board 락 등록 생략 금지
* 작업 중간 "Handoff to worktree" 사용 금지
