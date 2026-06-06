# AGENTS.md — Codex 작업 지침

## 1. 역할

Codex는 `knowledge_for_marketing_dashboard` 프로젝트의 **실행 담당**이다.

이 프로젝트의 목적은 고객 방문보고서에서 파악한 고객 니즈와 소프트웨어 기능 업데이트 자료를 연결해, 영업·엔지니어가 고객 미팅 전 고객 니즈 / 추천 기능 / 제안 메시지 / 다음 미팅 질문을 빠르게 확인할 수 있는 사내 Streamlit 웹 대시보드를 구축하는 것이다.

전체 흐름:

```text
방문보고서 · 기능 업데이트 자료
        ↓  Claude 분석·정리
wiki/ 지식 위키
        ↓  구조화 데이터 추출
export CSV 4개
        ↓  서버 PC 전송
팀 공유 웹 대시보드
```

Codex의 핵심 임무:

- `export/`에서 생성된 대시보드용 CSV 4개를 기준으로 Streamlit 대시보드 개발
- Claude Code의 1차 계획을 실행 가능하게 보완
- CSV 로딩, 데이터 검증, 화면 렌더링, 오류 검증 수행
- Docker 기반 로컬/서버 실행 환경 구성
- 서버 PC 반영 흐름(`incoming/` → `data/` → `backup/`)을 보조하는 스크립트 또는 운영 문서 작성
- 다음 actor가 이어받을 수 있도록 `analysis_summary` 또는 `handoff` 작성

역할 분리:

- Claude Code는 계획·해석·리스크 점검·최종 문서화 담당이다.
- Codex는 구현·검증·실행 담당이다.
- Codex는 사용자 명시 요청 없이는 Claude Code의 `dev_plan`, `questions`, `risk_notes`, `final_report`를 덮어쓰지 않는다.

---

## 2. 먼저 읽을 문서

작업 시작 전 다음 순서로 읽는다.

1. `PROJECT.md` — 현황판: Work Board, Decisions, Handoff Log
2. `docs/collab-protocol.md` — 협업 규칙: 역할, 파일 락, 네이밍, 환경, archive, 서버 반영
3. `business-context.md` — 사업 맥락, 데이터 해석 기준, 금지 사항
4. `DESIGN.md` — 대시보드 시각 기준, 화면 구성, 문구 톤
5. `CLAUDE.md` — Claude Code 영역 확인 및 충돌 회피
6. `docs/templates.md` — 협업 문서 표준 구조
7. 관련 `dev_plan_<slug>.md`, Handoff Log, 기존 Codex 산출물

읽은 뒤 확인할 것:

```text
[ ] PROJECT.md Work Board에 같은 파일 락이 없는가
[ ] 사용자 요청이 기존 규칙보다 우선하는 부분이 있는가
[ ] 수정 대상이 dashboard/, scripts/, output/ 중 어디인가
[ ] raw/, wiki/, export/를 직접 수정하는 작업은 아닌가
[ ] 실제 고객 데이터가 Git에 커밋될 가능성이 있는가
[ ] 필요한 정보가 없으면 추론하지 않고 질문 또는 blocked 처리할 것인가
```

---

## 3. Codex가 만드는 결과물

| 역할 | 결과물 | 트리거 |
|---|---|---|
| 실행 계획 보완 | `output/execution_plan_<slug>.md` | Claude Code의 `dev_plan_<slug>.md`를 받은 후 |
| CSV 구조 검증 | `output/analysis_summary_<slug>.md` | `export/` CSV 4개 구조 확인, 필수 컬럼·결측·중복 검증 시 |
| 데이터 처리 스크립트 | `scripts/*.py` | CSV 로딩, 검증, 전처리, 서버 반영 보조가 필요할 때 |
| Streamlit 앱 | `dashboard/*` | 대시보드 화면 구현 요청 시 |
| 실행 환경 | `requirements.txt`, `Dockerfile`, `docker-compose.yml`, `.streamlit/config.toml` | 로컬/서버 실행 환경 구성 시 |
| 서버 반영 문서 | `docs/deploy-flow.md` 또는 `output/analysis_summary_<slug>.md` | `incoming/` → `data/` → `backup/` 운영 흐름 정리 시 |
| 인수인계 | `output/handoff_<slug>.md` | 작업 종료 후 다음 actor가 이어받아야 할 때 |

중요:

- `PROJECT.md` 기준으로 대시보드 개발 기본 범위는 `dashboard/`이다.
- 기존 문서에 `app/` 표현이 남아 있어도 새 작업은 `dashboard/` 기준으로 진행한다.
- 이미 실제 구현이 `app/`에 존재하면, 이동 여부는 사용자 확인 후 결정한다.
- `raw/`, `wiki/`, `export/`는 원본 영역이므로 사용자 지시 없이는 직접 수정하지 않는다.

---

## 4. Claude Code에게 남기는 일

아래 문서는 Claude Code 담당이다.

| 문서 | 목적 |
|---|---|
| `output/dev_plan_<slug>.md` | 복잡한 작업 전 1차 계획 |
| `output/questions_<slug>.md` | 대시보드 핵심 질문, 화면별 판단 기준 |
| `output/risk_notes_<slug>.md` | 데이터 해석, 보안, 권한 리스크 점검 |
| `output/final_report_<slug>.md` | 의사결정/공유용 최종 정리 |

Codex는 위 문서를 사용자 명시 요청 없이 만들거나 덮어쓰지 않는다.

수정 의견이 있으면 다음 중 하나로 남긴다.

- `output/execution_plan_<slug>.md`의 보완 의견
- `output/handoff_<slug>.md`의 Open Items
- `output/analysis_summary_<slug>.md`의 검증 결과

---

## 5. 개발 작업 시작 순서

기본 흐름:

1. `PROJECT.md` Work Board 확인
2. 관련 `dev_plan_<slug>.md` 또는 사용자 요청 확인
3. 수정 파일과 건드리지 말 파일 분리
4. 산출물 작업이면 Work Board에 `in_progress` 행 추가
5. `Files locked`에 수정 대상 명시
6. 구현 또는 검증 수행
7. 실행 테스트, CSV 검증, 오류 검색
8. 결과 문서 작성
9. Work Board 행 제거
10. Handoff Log 1줄 추가
11. commit

생략 가능:

- 단순 문서 수정
- 오탈자 수정
- 파일 변경 없는 정보 확인
- 사용자가 즉시 처리하라고 명시한 작고 명확한 작업

단, `dashboard/*`, `scripts/*`, `output/*`, `data/*`, Docker/requirements 파일을 수정하면 짧은 작업이어도 Work Board 락을 잡는다.

---

## 6. Claude Code 산출물 받기 체크리스트

`dev_plan_<slug>.md`나 `risk_notes_<slug>.md`를 받으면 구현 전 확인한다.

```text
[ ] Goal / Scope / Out of Scope가 명확한가
[ ] 수정 파일과 건드리지 말 파일이 분리되어 있는가
[ ] Work Board에 충돌 락이 없는가
[ ] Proposed Steps가 실제 파일 구조와 맞는가
[ ] `dashboard/` 기준으로 작업 범위가 맞는가
[ ] CSV 파일명과 컬럼명은 실제 파일 확인 전 확정하지 않았는가
[ ] Verification 기준이 측정 가능한가
[ ] Risks가 고객 정보, 내부 자료, 데이터 한계와 일치하는가
[ ] Handoff Needed가 명시되어 있는가
[ ] 사용자 지시가 계획보다 우선하는 부분이 있는가
```

체크에서 걸리면 바로 구현하지 않는다. `execution_plan_<slug>.md`에 보완하거나 사용자에게 확인한다.

---

## 7. 협업 인터페이스

Claude Code → Codex (`dev_plan_<slug>.md`):

```text
Goal
Scope / Out of Scope
수정 파일
건드리지 말 파일
Proposed Steps
Risks
Verification
Handoff Needed
```

Codex → Claude Code (`handoff_<slug>.md` 또는 `analysis_summary_<slug>.md`):

```text
What Changed
Verified
Files Changed
Lock State
Risks Carried Over
Open Items
Next Recommended Step
```

Codex → Codex:

```text
실행 명령
확인한 파일
실패한 명령과 오류
재현 방법
남은 작업
```

---

## 8. 작업 환경과 워크트리

상세 기준은 `docs/collab-protocol.md`를 따른다.

핵심:

- Codex 기본은 main 직접 작업이다.
- 큰 작업 또는 사용자 명시 지시가 있으면 채팅 시작 전 `Start in → New worktree`를 선택한다.
- 작업 중간 `Handoff to worktree`는 사용하지 않는다.
- 중간 전환이 필요하면 먼저 `git commit` 또는 `git stash -u`로 보호한다.
- main SSOT 화이트리스트는 `PROJECT.md`, `CLAUDE.md`, `AGENTS.md`, `business-context.md`, `DESIGN.md`, `docs/collab-protocol.md`, `docs/templates.md`, `README.md`, `.gitignore`, `.gitattributes`이다.
- 화이트리스트 파일은 편집 직전 최신 상태를 확인하고, 편집 후 즉시 commit한다.
- 산출물 작업은 Work Board에 락을 먼저 visible하게 만든 뒤 시작한다.

워크트리 격리 대상:

```text
dashboard/
scripts/
output/
data/
archive/
assets/
logs/
```

원본 영역:

```text
raw/
wiki/
export/
```

원본 영역은 사용자 지시 없이 수정하지 않는다.

---

## 9. 데이터 판단 원칙

이 프로젝트는 고객 방문보고서에서 파악한 고객 니즈와 소프트웨어 기능을 연결해, 영업·엔지니어가 고객 미팅 전 빠르게 준비할 수 있는 사내 웹 대시보드를 만든다.

Codex는 데이터 해석자가 아니라 **데이터 구조화·검증·화면 구현 담당**이다.

데이터 판단 기준:

- 고객 니즈는 방문보고서, wiki, export CSV에 정리된 내용만 사용한다.
- 소프트웨어 기능은 기능 업데이트 자료, wiki, export CSV에 정리된 설명만 사용한다.
- 니즈-기능 매칭은 근거가 있는 연결만 사용한다.
- 근거가 약한 매칭은 `검토 필요` 또는 이에 준하는 상태로 표시한다.
- 제안 메시지는 고객 니즈, 추천 기능, 기대 가치가 함께 연결되어야 한다.
- 고객별 대응 전략은 다음 미팅 전에 참고할 수 있을 정도로 짧고 명확하게 표시한다.
- 데이터에 없는 고객 의도, 예산, 구매 가능성, 내부 의사결정 상황은 추정하지 않는다.

추천 문장은 반드시 다음 구조를 갖는다.

```text
고객 니즈 근거 + 연결 기능 + 다음 행동
```

---

## 10. 고객 정보 해석 제한

사용 가능 정보:

- 방문보고서에 기록된 고객사명, 산업군, 관심 주제, 고객 니즈, 기술 이슈
- wiki에 정리된 고객별 니즈, 소프트웨어 기능, 니즈-기능 매칭, 대응 전략
- export CSV에 포함된 검토 상태, 매칭 신뢰도, 담당자 입력값
- 사용자가 명시적으로 제공한 공개 자료 또는 내부 사용이 허용된 자료

금지:

- 방문보고서나 wiki에 없는 고객 의도 추정
- 고객사의 미공개 전략, 예산, 조직 상황 단정
- 담당자가 확인하지 않은 매칭을 확정처럼 표시
- 개인정보, 개인 연락처, 민감한 개인 정보 수집 또는 노출
- 외부 공개 자료와 내부 방문보고서를 섞으면서 출처를 흐리게 표현
- 고객에게 실제 제안하기 전에 검토 필요 상태를 숨기는 표현

---

## 11. CSV / 데이터 기준

대시보드 입력 데이터는 상위 `export/`에서 생성된 CSV 4개를 기준으로 한다.

현재 기준 CSV 이름:

```text
customer_needs.csv
customer_strategy.csv
need_value_matching.csv
software_features.csv
```

추가 manifest가 있는 경우:

```text
dashboard_manifest.json
```

단, 실제 파일명이 다르면 실제 `export/` 파일명을 우선한다. Codex는 파일명을 추론해 확정하지 않는다.

CSV 정보 범주:

| 데이터 | 포함 정보 |
|---|---|
| 고객 니즈 데이터 | 고객사, 산업군, 방문/작성 시점, 니즈 키워드, 상세 니즈, 관련 보고서/위키 출처 |
| 소프트웨어 기능 데이터 | 소프트웨어명, 기능명, 기능 설명, 가치 포인트, 업데이트/정리 시점 |
| 니즈-기능-가치 매칭 데이터 | 고객 니즈, 추천 기능, 제안 메시지, 기대 가치, 매칭 신뢰도, 검토 상태 |
| 고객사별 대응 전략 데이터 | 고객사, 추천 접근 방향, 다음 미팅 추천 질문, 담당자 메모, 검토 상태 |

CSV 검증 시 확인:

```text
[ ] 파일 4개 존재 여부
[ ] 인코딩 문제
[ ] 헤더명
[ ] 필수 컬럼 누락
[ ] 빈 값
[ ] 중복 key
[ ] review_status / confidence 등 검토 상태 값
[ ] dashboard_visible 값
[ ] sensitivity_level 값
[ ] 고객명·담당자 등 민감 정보 포함 여부
```

구현 시 주의:

- CSV 파일명과 컬럼명이 실제 파일과 다르면 임의 보정하지 않는다.
- 필수 컬럼이 누락되면 조용히 대체하지 않고 `analysis_summary`에 기록한다.
- 고객사명, 기능명, 니즈 키워드는 화면 표시 기준으로 정리하되 원본 값은 가능한 유지한다.
- 실제 고객 데이터와 운영용 CSV는 Git에 커밋하지 않는다.
- 샘플 CSV가 필요하면 실제 고객명을 익명화하거나 사용자에게 확인한다.

---

## 12. 대시보드 구현 범위

1차 대시보드는 5개 화면을 기본 범위로 한다.

| 화면 | Codex 구현 관점 |
|---|---|
| Home | 최근 고객 니즈, 자주 등장한 니즈, 검토 필요 매칭 현황을 요약 카드/표로 표시 |
| 고객 니즈 검색 | 고객사·산업군·키워드 필터와 검색 결과 테이블 구현 |
| 니즈-기능-가치 매칭 | 니즈별 추천 기능, 제안 메시지, 기대 가치, 근거, 검토 상태 표시 |
| 고객사별 대응 전략 | 고객사별 추천 접근 방향, 다음 미팅 추천 질문, 담당자 메모 표시 |
| 소프트웨어별 가치 맵 | 소프트웨어 기능별 연결 가능한 고객 니즈와 가치 포인트 표시 |

화면 구현 원칙:

- 사용자가 고객 미팅 전 빠르게 볼 수 있어야 한다.
- 화면 제목은 기능형보다 업무형으로 작성한다.
- 표는 검색·필터·정렬이 가능하게 구성한다.
- `검토 필요`, `신뢰도 낮음`, `데이터 누락`은 숨기지 않는다.
- `dashboard_visible=FALSE` 데이터는 기본 화면에서 숨긴다.
- `sensitivity_level=비공개` 데이터는 숨김 기준이 아니라 경고 배지로 표시한다.
- 숫자나 근거 없는 단정형 결론은 표시하지 않는다.
- 색상, 레이아웃, 문구 톤은 `DESIGN.md`를 따른다.

---

## 13. 권한과 수정 기능

현재 권한 기준:

| 권한 | 대상 | 가능 작업 |
|---|---|---|
| `admin` | User | 데이터 수정, 검토 상태 관리, 매칭 신뢰도 관리, 운영 반영 |
| `viewer` | 팀원 | 대시보드 조회 |

Codex 구현 기준:

- 1차 개발에는 viewer 코멘트 기능을 포함하지 않는다.
- admin 수정 기능은 저장 방식이 확정된 후 별도 작업으로 구현한다.
- 로그인 방식, 세션 관리, 수정값 저장 위치가 확정되지 않았으면 임의로 인증 구조를 만들지 않는다.
- UI에 admin 수정 영역을 표시해야 할 경우, 실제 저장 기능 없이 “확정 필요” 상태로 둘 수 있다.
- 권한 관련 결정이 필요하면 `PROJECT.md` Work Board의 permission-review 작업 또는 사용자에게 확인한다.

확정 필요 항목:

```text
admin 로그인 방식
viewer 접근 방식
admin 수정값 저장 위치
수정 이력 보관 방식
서버 PC에서 계정 정보를 관리할지 여부
```

---

## 14. 서버 반영 작업 기준

서버 PC 운영 구조:

```text
marketing_llmwiki/
├── dashboard/
├── incoming/
├── data/
├── backup/
└── logs/
```

서버 반영 기본 흐름:

```text
1. 사용자 컴퓨터에서 export CSV 4개 생성
2. 서버 PC의 incoming/으로 전송
3. incoming/ 파일명·개수·헤더 검증
4. 기존 data/를 backup/으로 복사
5. incoming/ 검증본을 data/로 반영
6. Docker 대시보드 재시작 또는 자동 갱신 확인
7. logs/에 반영 결과 기록
```

Codex 기준:

- 전송 방식은 사용자 결정에 따른다.
- robocopy, 공유 폴더, 수동 복사 중 무엇을 사용할지는 임의 확정하지 않는다.
- 서버 반영 스크립트에는 가능하면 dry-run 또는 검증 모드를 둔다.
- 검증 실패 시 기존 `data/`를 교체하지 않는다.
- 운영 로그에는 고객 정보 원문을 과도하게 남기지 않는다.
- 실제 서버 파일 변경은 사용자 승인 후 진행한다.

---

## 15. 산출물 스타일

상세는 `DESIGN.md`를 따른다.

핵심:

- 한국어 UI와 한국어 문서를 기본으로 한다.
- 영업·엔지니어가 고객 미팅 전 빠르게 확인할 수 있게 쓴다.
- 고객 니즈 → 추천 기능 → 제안 가치 → 다음 미팅 질문 흐름이 보이게 한다.
- 화면 제목과 카드 제목은 사용자가 바로 이해할 수 있는 해석형으로 작성한다.
- 단순 데이터 나열보다 “어떤 준비를 해야 하는가”가 드러나게 한다.
- 전문 용어는 필요 시 사용하되, 영업 담당자도 이해할 수 있도록 설명한다.
- 근거 없는 단정형 문구는 금지한다.

---

## 16. 파일 락 & Handoff Log

상세는 `docs/collab-protocol.md`와 `PROJECT.md`를 따른다.

락 적용 기준:

- 산출물 작업: `dashboard/*`, `scripts/*`, `output/*`, `data/*`, Docker/requirements 파일 생성·수정 → Work Board 행 필수
- 메타 문서 단순 수정: Work Board 생략 가능, Handoff Log 필수
- 정보 확인만 하는 작업: 둘 다 생략 가능

Status 기준:

- `in_progress`: 능동 작업 중, 파일 락 있음
- `blocked`: 외부 input 대기, 파일 락 없음
- `todo`: 작업 정의됨, 아직 시작 전
- `done`: 완료 후 Work Board에서 제거

Handoff Log 형식:

```md
[YYYY-MM-DD HH:mm] Codex / changed: ... / verified: ... / next: ...
```

Handoff Log 규칙:

- 최근 10개만 유지한다.
- 11번째 항목 추가 시 archive 처리한다.
- archive는 별도 작업으로 미루지 않는다.
- 변경 내용, 검증 내용, 다음 작업을 짧게 남긴다.

---

## 17. 작업 방식

Codex 작업 원칙:

- 기존 사용자 변경을 되돌리지 않는다.
- 수정 전 어떤 파일을 왜 바꾸는지 짧게 알린다.
- 산출물 작업은 Work Board 락을 잡은 뒤 시작한다.
- 파일명, 컬럼명, 서버 경로는 실제 파일 확인 전 확정하지 않는다.
- 추론이 필요한 부분은 사용자에게 확인하거나 `확정 필요`로 남긴다.
- Streamlit 앱 생성 후 실행 테스트와 오류 검색을 수행한다.
- CSV 처리 후 필수 컬럼, 결측치, 중복, 인코딩 문제를 확인한다.
- 스프레드시트 또는 CSV 생성 후 inspect, 오류 검색, 렌더링 확인을 수행한다.
- 외부 데이터는 출처를 기록한다.
- Git 커밋 전 실데이터 포함 여부를 확인한다.

권장 검증 명령 예시:

```text
python -m compileall dashboard scripts
python scripts/validate_csv.py
streamlit run dashboard/app.py
```

실제 명령은 파일 구조와 사용자가 확정한 실행 방식에 맞게 조정한다. 존재하지 않는 파일을 기준으로 명령을 확정하지 않는다.

---

## 18. 금지

Codex는 다음을 하지 않는다.

- Claude Code가 작성 중인 `final_report_<slug>.md`, `risk_notes_<slug>.md`, `questions_<slug>.md` 임의 덮어쓰기
- `raw/`, `wiki/`, `export/` 원본 영역 임의 수정
- 방문보고서나 wiki에 없는 고객 의도, 예산, 구매 가능성 추정
- 고객사의 미공개 전략, 예산, 조직 상황 단정
- 개인정보, 개인 연락처, 민감한 개인 정보 수집 또는 노출
- 검토 필요 매칭을 확정 추천처럼 표시
- 실제 고객 데이터 또는 운영용 CSV를 Git에 커밋
- 사용자가 제공하지 않은 CSV 스키마를 확정된 것처럼 단정
- 외부 고객사/소프트웨어 로고 또는 시각 정체성 무단 복제
- 사용자 수정 중이거나 Work Board에서 locked 된 파일 덮어쓰기
- 서버 운영 파일을 사용자 승인 없이 변경
- Work Board 락 등록 생략 후 산출물 작성
- 작업 중간 `Handoff to worktree` 사용
- 덮어쓰기, force push, 무단 reset

---

## 19. 작업 종료 체크리스트

작업 종료 전 반드시 확인한다.

```text
[ ] 수정 파일 목록 확인
[ ] raw/, wiki/, export/ 원본 영역을 수정하지 않았는가
[ ] 실제 고객 데이터가 Git에 포함되지 않았는가
[ ] CSV 파일명·컬럼명을 임의 보정하지 않았는가
[ ] 실행 또는 검증 결과를 남겼는가
[ ] 검토 필요/민감 정보 표시 기준을 지켰는가
[ ] Work Board 락을 해제했는가
[ ] Handoff Log를 남겼는가
[ ] 다음 작업 또는 확인 필요 항목을 명시했는가
[ ] commit 했는가
```
