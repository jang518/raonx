# business-context.md — knowledge_for_marketing_dashboard 사업 맥락

> 목적: Claude Code와 Codex가 고객 방문보고서 기반 지식 위키, export CSV, Streamlit 대시보드를 해석할 때 같은 사업 기준과 데이터 해석 원칙을 보게 하기 위한 문서입니다.

---

## 1. 프로젝트 개요

`knowledge_for_marketing_dashboard`는 고객 방문보고서에서 파악한 고객 니즈와 소프트웨어 기능 업데이트 자료를 연결해, **마케팅 활용**과 **고객 미팅 준비**에 필요한 정보를 빠르게 확인할 수 있게 만드는 사내 지식 활용 프로젝트입니다.

핵심 메시지는 “자료를 많이 모으는 것”이 아니라, **고객 니즈에 맞는 소프트웨어 기능, 제안 메시지, 마케팅 활용 후보, 다음 확인 질문을 빠르게 연결하는 것**입니다.

기본 흐름:

```text
방문보고서 · 기능 업데이트 자료
        ↓  Claude 분석·정리
wiki/ 지식 위키
        ↓  구조화 데이터 추출
export CSV 4개
        ↓  로컬 대시보드 개발
robocopy로 서버 PC에 필요한 파일만 전송
        ↓
팀 공유 웹 대시보드
```

이 대시보드는 사내 팀원이 다음 질문에 빠르게 답하기 위한 도구입니다.

1. 반복적으로 등장하는 고객 니즈는 무엇이며, 어떤 마케팅 콘텐츠나 세미나 주제로 활용할 수 있는가?
2. 이 고객사는 과거에 어떤 니즈와 문의를 가지고 있었는가?
3. 해당 고객 니즈에 연결할 수 있는 소프트웨어 기능은 무엇인가?
4. 고객에게 어떤 가치 메시지와 제안 방향을 제시할 수 있는가?
5. 다음 미팅에서 확인해야 할 추천 질문은 무엇인가?
6. 어떤 니즈-기능 매칭은 신뢰도나 검토 상태상 주의가 필요한가?
7. 영업·엔지니어가 고객 미팅 전에 가장 먼저 확인해야 할 정보는 무엇인가?

---

## 2. 사용자와 활용 방식

| 사용자 | 역할 | 활용 방식 |
|---|---|---|
| admin 계정 | admin | 조회, 데이터 수정, 검토 상태 관리, 매칭 신뢰도 관리, 운영 반영 |
| 일반 사용자 계정 | viewer | 대시보드 조회 전용 |
| 영업·엔지니어 | viewer | 고객 미팅 전 고객 니즈, 추천 기능, 제안 메시지, 다음 질문 조회 |
| 마케팅/전략 담당 | viewer 또는 admin | 반복 니즈, 가치 메시지, 세미나/제안서/기술 콘텐츠 후보 확인 |
| 위키 관리자 | admin | 방문보고서 입수 → Claude로 위키 갱신 → export 재생성 → 서버 반영 |
| Claude Code | 계획·해석·리스크 담당 | 질문 설계, 리스크 점검, 최종 문서화 |
| Codex | 구현·검증 담당 | Streamlit 구현, CSV 검증, Docker/서버 반영 보조 |

권한 기준:

- 모든 사용자는 계정별로 로그인합니다.
- 접속은 비밀번호 입력 후 가능합니다.
- `admin` 계정은 조회와 수정이 가능합니다.
- `admin` 이외 계정은 `viewer`로 조회만 가능합니다.
- 비밀번호는 코드에 하드코딩하지 않고 환경변수 또는 Streamlit secrets 등 외부 설정으로 관리합니다.
- 1차 구현에서 인증은 사내망 내부 운영 보조 수준으로 다룹니다. 외부 공개 수준의 강한 인증/권한 관리는 별도 보안 검토 전까지 범위에서 제외합니다.
- admin 수정값은 원본 CSV를 직접 수정하지 않고 별도 override CSV에 저장합니다.
- viewer 코멘트 기능은 1차 개발에 포함하지 않습니다.

---

## 3. 정보 자산

| 정보 자산 | 역할 | 현재 기준 |
|---|---|---|
| 방문보고서 | 고객 니즈와 문의 내용을 확인하는 핵심 원천 자료 | 고객별 니즈, 관심 기능, 대응 이력의 1차 근거 |
| 기능 업데이트 자료 | 소프트웨어별 기능과 가치 메시지를 정리하는 자료 | 소프트웨어 기능을 고객 니즈와 연결하는 근거 |
| `wiki/` 지식 위키 | 원천 자료를 Claude가 분석·정리한 마크다운 지식 베이스 | 고객별 니즈, 기능, 매칭, 대응 전략을 사람이 검토 가능한 형태로 유지 |
| `export/` CSV | 웹 대시보드가 읽는 구조화 데이터 | 대시보드 입력 데이터 4개 CSV |
| `dashboard_manifest.json` | 데이터 묶음의 기준 정보 | export 파일 목록, 생성 시점, 반영 상태 등을 관리하는 보조 파일 |
| Streamlit 대시보드 | 팀원이 조회하는 사내 웹 화면 | 마케팅 활용, 고객 미팅 준비, 검토 상태 확인 |
| override CSV | admin 수정값 저장 영역 | 원본 CSV를 직접 수정하지 않고 검토 상태, 신뢰도, 담당자 메모 등을 별도 관리 |
| 서버 PC 운영 파일 | 팀 공유 대시보드를 실행하는 운영 환경 | Docker 기반 운영, `incoming/` → `data/` 반영 |

주의:

- 실제 고객 데이터 CSV는 기본적으로 Git에 커밋하지 않습니다.
- `raw/`, `wiki/`, `export/`는 원본 영역으로 보며 대시보드 개발 중 직접 수정하지 않습니다.
- 대시보드 개발 기본 범위는 `dashboard/`입니다.
- 기존 문서나 코드에 `app/` 표현이 남아 있더라도 새 작업은 `dashboard/` 기준으로 해석합니다.

---

## 4. 로컬/서버 운영 구조

### 4.1 사용자 컴퓨터: 원본·개발·관리 영역

```text
knowledge_for_marketing/
├── raw/          원본 자료 (읽기 전용)
├── wiki/         정리 지식 페이지
├── export/       대시보드용 CSV 4개
├── dashboard/    대시보드 개발 파일
├── scripts/      export 재생성 또는 보조 스크립트
└── logs/         작업 로그
```

규칙:

- 원본 파일은 사용자 컴퓨터에 있습니다.
- 대시보드 개발도 사용자 컴퓨터에서 진행합니다.
- `raw/`: 방문보고서와 기능 업데이트 자료의 원본. 직접 수정 금지.
- `wiki/`: Claude가 정리한 지식 베이스. 대시보드 개발자가 직접 수정하지 않음.
- `export/`: 위키에서 생성된 CSV. 대시보드 개발 중 임의 보정 금지.
- `dashboard/`: Streamlit 대시보드 개발 기본 폴더.
- `scripts/`: 검증, export 재생성, 서버 반영 보조 스크립트.
- `logs/`: 작업 기록. 고객 정보가 포함될 수 있으므로 외부 공유 금지.

### 4.2 서버 PC: 운영 영역

```text
marketing_llmwiki/
├── dashboard/    Docker 대시보드 실행 파일
├── incoming/     사용자 컴퓨터에서 전달받는 임시 데이터
├── data/         Docker 대시보드가 실제로 읽는 데이터
├── backup/       기존 data 백업
└── logs/         반영 작업 로그
```

서버 반영 기본 흐름:

```text
1. 사용자 컴퓨터에서 export CSV 4개와 대시보드 운영 파일 준비
2. robocopy로 서버 PC의 incoming/ 또는 운영 반영용 폴더에 필요한 파일만 전송
3. incoming/ 파일명·개수·헤더 검증
4. 기존 data/를 backup/으로 복사
5. incoming/ 검증본을 data/로 반영
6. Streamlit 데이터 새로고침 또는 Docker 컨테이너 재시작
7. logs/에 반영 결과 기록
```

기준:

- 서버 PC 전송 방식은 **robocopy**를 사용합니다.
- 서버 PC에는 웹 기반 대시보드 구현에 필요한 파일만 복사합니다.
- 원본 `raw/`, 전체 `wiki/`, 불필요한 개발 로그는 서버 전송 대상에서 제외합니다.
- 검증 실패 시 기존 `data/`를 교체하지 않습니다.
- 운영 로그에는 고객 정보 원문을 과도하게 남기지 않습니다.
- CSV 변경 시에는 데이터 새로고침 기능을 우선 사용하고, 코드 변경 또는 장애 대응 시 Docker 컨테이너 재시작도 사용합니다.

---

## 5. 사용 데이터

대시보드 입력 데이터는 `export/`에서 생성된 CSV 4개를 기준으로 합니다.

현재 실제 export CSV 파일:

```text
customer_needs.csv
software_features.csv
need_value_matching.csv
customer_strategy.csv
```

추가 manifest가 있는 경우:

```text
dashboard_manifest.json
```

### 5.1 현재 실제 CSV 컬럼

| 파일명 | 현재 컬럼 |
|---|---|
| `customer_needs.csv` | `need_id`, `customer_name`, `industry`, `need_date`, `need_keyword`, `need_text`, `need_analysis`, `need_final`, `author`, `assignee`, `progress_note`, `sensitivity_level`, `dashboard_visible`, `customer_grade`, `visit_count` |
| `software_features.csv` | `feature_id`, `software`, `feature_name`, `feature_summary`, `value_keywords`, `related_need_keywords`, `source_wiki`, `last_updated`, `dashboard_visible` |
| `need_value_matching.csv` | `match_id`, `need_id`, `feature_id`, `value_message`, `need_date`, `dashboard_visible`, `confidence` |
| `customer_strategy.csv` | `strategy_id`, `customer_name`, `main_needs`, `recommended_software`, `approach_details`, `suggested_questions`, `proposal_message`, `author`, `assignee`, `sensitivity_level`, `dashboard_visible`, `customer_grade`, `visit_count` |

현재 실제 CSV 기준 주의사항:

- 현재 CSV에는 `customer_id`가 없습니다.
- 현재 CSV에는 `review_status`가 없습니다.
- 현재 `sensitivity_level` 값은 `민감/내부` 체계입니다.
- 현재 `dashboard_visible` 값은 `TRUE/FALSE` 체계입니다.

### 5.2 목표 export 생성 규칙

향후 export 생성 시 다음 기준을 반영합니다.

| 항목 | 목표 기준 |
|---|---|
| 고객 식별 | `customer_id` 컬럼 생성. 고객사명 문자열 JOIN 대신 `customer_id` 기준 연결 우선 |
| 민감도 | `sensitivity_level`을 `일반`, `주의`, `비공개` 3단계로 생성 |
| 기존 민감도 값 | 기본 제안: `내부 → 일반`, `민감 → 주의`, `비공개`는 원본 또는 사용자가 명시한 경우에만 부여 |
| 대시보드 표시 | `dashboard_visible`은 `TRUE/FALSE`로 유지 |
| admin 수정값 | 원본 CSV가 아니라 override CSV에 저장 |
| 검토 상태 | 필요 시 `review_status`는 override CSV 또는 향후 export 컬럼으로 관리 |

### 5.3 검토 및 수정 관련 데이터

| 데이터 | 저장 위치 | 비고 |
|---|---|---|
| `confidence` | 현재 `need_value_matching.csv`에 존재 | 낮음/중간/높음 등 실제 값 기준으로 표시 |
| `review_status` | 현재 CSV에는 없음 | admin override CSV에서 관리하거나 export 생성 규칙 업데이트 필요 |
| 담당자 메모 | 현재 `customer_needs.csv.progress_note` 존재 | admin 수정은 override CSV로 관리 |
| `sensitivity_level` | 현재 `customer_needs.csv`, `customer_strategy.csv`에 존재 | 목표 값은 `일반/주의/비공개` |
| `dashboard_visible` | 모든 CSV에 존재 | `TRUE/FALSE` 기준 |

---

## 6. CSV 관계 정의

1. `need_value_matching.csv` ↔ `customer_needs.csv`
   - 현재 연결 컬럼: `need_value_matching.need_id` → `customer_needs.need_id`
   - 의미: 고객 니즈 1건에 여러 기능이 매칭될 수 있음

2. `need_value_matching.csv` ↔ `software_features.csv`
   - 현재 연결 컬럼: `need_value_matching.feature_id` → `software_features.feature_id`
   - 의미: 기능 1개가 여러 고객 니즈에 매칭될 수 있음

3. `customer_strategy.csv` ↔ `customer_needs.csv`
   - 현재 연결 컬럼: `customer_strategy.customer_name` → `customer_needs.customer_name`
   - 주의: PK/FK가 아닌 고객사명 문자열 연결이므로 표기 차이에 취약함
   - 목표: export 생성 시 `customer_id`를 추가해 `customer_id` 기준 연결로 전환

4. `software_features.csv` ↔ `customer_needs.csv`
   - 현재 연결 컬럼: `software_features.related_need_keywords` ↔ `customer_needs.need_keyword`
   - 의미: 직접 JOIN이 아니라 어휘 기반 참고 연결

규칙:

- 관계가 불명확하면 임의 JOIN하지 않습니다.
- 고객사명 문자열 연결은 표기 차이로 오류가 날 수 있으므로 검증 결과에 기록합니다.
- 키워드 기반 연결은 직접 근거가 아니라 참고 연결로 표현합니다.
- `customer_id`가 생성된 이후에는 고객사 단위 연결에 `customer_id`를 우선 사용합니다.

---

## 7. 고객 데이터 해석 규칙

방문보고서, wiki, export CSV에 명시된 정보만 사용합니다.

사용 가능:

- 고객사명, 고객 ID, 산업군, 고객 등급
- 방문보고서에서 확인된 고객 니즈
- 고객이 언급한 문제, 요구, 관심 기능
- wiki에 정리된 소프트웨어 기능
- 니즈-기능 매칭 정보
- 매칭 신뢰도
- 다음 액션, 다음 미팅 추천 질문
- 마케팅 활용 후보
- 출처 파일과 보고서 일자
- 민감도 표시값
- 대시보드 노출 여부

금지:

- 고객의 구매 가능성 추정
- 예산 규모 추정
- 도입 의사 단정
- 방문보고서에 없는 고객 의도 상상
- 검토되지 않은 매칭을 확정 추천처럼 표시
- `confidence`가 낮은 매칭을 주요 추천으로 단정 표시
- 마케팅 활용 후보를 확정 캠페인 또는 확정 수요처럼 표시
- 고객 담당자 정보를 필요 이상으로 상세하게 노출
- 개인 정보 또는 민감 정보를 불필요하게 대시보드에 노출
- 원천 자료에 없는 기능 효과를 과장해서 작성
- `viewer` 역할의 팀원이 데이터를 수정할 수 있는 것처럼 설계 또는 문서화
- `raw/`, `wiki/`, `export/` 원본을 대시보드 앱이나 작업자가 직접 수정

추천 문장 구조:

```text
고객 니즈 근거 + 연결 기능 + 제안 가치 + 다음 확인 질문
```

마케팅 활용 문장 구조:

```text
반복 니즈 근거 + 관련 소프트웨어/기능 + 가치 메시지 후보 + 활용 전 확인 사항
```

---

## 8. 의사결정 기준

### 8.1 우선 표시할 고객 니즈

- Home 화면에서 최근 고객 니즈와 검토 필요 항목을 우선 보여줄 수 있다.
- 최근 방문보고서 또는 wiki에서 반복적으로 등장한다.
- 마케팅 콘텐츠, 세미나, 제안서, 고객 맞춤 기능 소개에 활용 가능하다.
- 고객 미팅 전 영업·엔지니어가 바로 확인할 필요가 있다.
- 소프트웨어 기능 또는 가치 메시지와 연결 가능하다.
- `dashboard_visible=TRUE` 상태다.

### 8.2 우선 추천할 기능/메시지

- 고객 니즈와 기능의 연결 근거가 명확하다.
- `confidence`가 `높음` 또는 최소 `중간` 이상이다.
- 고객에게 제안할 `value_message`가 구체적이다.
- 고객사별 대응 전략의 `proposal_message`, `suggested_questions`와 연결된다.
- 현재 관리 중인 소프트웨어 기능과 연결된다.

주의:

- 소프트웨어 항목은 추가될 수 있으므로, 코드와 화면은 특정 소프트웨어명을 하드코딩하지 않고 CSV의 `software` 값을 기준으로 동적으로 처리합니다.
- 검토 완료 정보가 없는 현재 CSV에서는 `confidence`와 근거 데이터를 우선 표시하고, 검토 상태는 override CSV 또는 향후 export 개선 후 반영합니다.

### 8.3 마케팅 활용 후보

다음 조건이면 마케팅 활용 후보로 볼 수 있습니다.

- 여러 고객 또는 여러 보고서에서 반복적으로 등장하는 니즈다.
- 특정 소프트웨어 기능 또는 가치 키워드와 연결된다.
- 세미나, 제안서, 고객 맞춤 기능 소개, 기술 콘텐츠 주제로 활용 가능하다.
- 단, 확정 캠페인 또는 확정 고객 수요처럼 표현하지 않는다.

---

## 9. 대시보드 화면 범위

1차 개발 화면은 다음 6개입니다.

| 화면 | 목적 | 핵심 질문 |
|---|---|---|
| Home | 전체 현황, 최근 니즈, 검토 필요, 마케팅 후보 요약 | 지금 가장 먼저 봐야 할 고객 니즈와 활용 후보는 무엇인가? |
| 고객 니즈 검색 | 고객사·산업군·키워드 중심 검색 | 특정 고객 또는 산업군은 어떤 니즈를 가지고 있었는가? |
| 니즈-기능-가치 매칭 | 고객 니즈에 연결된 추천 기능과 제안 메시지 확인 | 이 니즈에 어떤 기능과 가치 메시지를 연결할 수 있는가? |
| 고객사별 대응 전략 | 추천 접근 방향, 다음 미팅 추천 질문 확인 | 다음 미팅에서 어떤 접근과 질문을 준비해야 하는가? |
| 소프트웨어별 가치 맵 | 기능 목록과 연결 가능한 고객 니즈 확인 | 이 소프트웨어 기능은 어떤 고객 니즈와 연결되는가? |
| 마케팅 활용 | 반복 니즈, 가치 메시지, 콘텐츠/세미나/제안서 후보 확인 | 어떤 고객 니즈를 마케팅 자료와 고객 맞춤 콘텐츠로 활용할 수 있는가? |

---

## 10. 산출물과 개발 단계

### 10.1 1차 — 로컬 Streamlit 조회 + 계정별 접근 + admin override 계획

- `dashboard/`: Streamlit 대시보드 앱 코드
- `dashboard/app.py` 또는 `dashboard/streamlit_app.py`: 앱 진입점
- `dashboard/pages/`: 6개 화면 구성 파일
- `dashboard/components/`: 공통 UI 컴포넌트
- `dashboard/services/`: CSV 로딩, 권한, 상태 처리 서비스 로직
- `scripts/validate_csv.py`: 4개 CSV 존재 여부, 컬럼명, 관계 무결성 검증
- `scripts/load_data.py`: CSV 로딩 및 공통 전처리 로직
- `requirements.txt`: Python 패키지 목록
- `README.md`: 로컬 실행 방법과 프로젝트 설명
- `output/analysis_summary_<slug>.md`: CSV 구조 및 검증 결과
- `output/handoff_<slug>.md`: 구현 후 인수인계

### 10.2 2차 — 서버 PC Docker 운영

- `Dockerfile`: Streamlit 앱 컨테이너 설정
- `docker-compose.yml`: 서버 PC 운영용 Compose 설정
- `.streamlit/config.toml`: Streamlit 실행 설정
- `docs/deploy-flow.md` 또는 `docs/server-runbook.md`: robocopy 기반 파일 전송, 실행, 중지, 재시작, 데이터 반영 방법

### 10.3 3차 — export 생성 규칙 개선

- `customer_id` 생성
- `sensitivity_level`을 `일반/주의/비공개`로 생성
- `dashboard_visible`을 `TRUE/FALSE`로 유지
- 필요 시 `review_status`를 export 또는 override CSV에서 관리

---

## 11. 톤과 문장 기준

좋은 문장:

- “이 니즈는 여러 고객군에서 반복적으로 등장하므로 마케팅 콘텐츠 후보로 검토할 수 있습니다.”
- “해당 니즈는 pSeven 기반 프로세스 자동화 기능과 연결할 수 있습니다.”
- “이 매칭은 confidence가 낮으므로 담당자 확인 후 제안에 활용하세요.”
- “다음 미팅에서는 현재 반복 작업의 병목 구간을 확인하는 질문이 적합합니다.”

피해야 할 문장:

- “이 고객사는 반드시 이 솔루션을 도입할 것입니다.”
- “구매 가능성이 높습니다.”
- “예산이 충분할 것으로 보입니다.”
- “이 마케팅 캠페인은 확정입니다.”
- “근거는 없지만 이 기능을 추천합니다.”
- “비공개 항목이지만 일반 화면에 그대로 노출해도 됩니다.”

---

## 12. 절대 금지

- 데이터에 없는 고객 의도, 구매 가능성, 예산, 도입 의지, 내부 의사결정 상황 단정
- 방문보고서에 없는 내용을 고객 요구사항처럼 표현
- 마케팅 활용 후보를 확정 캠페인 또는 확정 고객 수요처럼 표현
- 검토 필요 또는 confidence 낮음 매칭을 확정 추천처럼 표시
- 고객사, 담당자, 미팅 정보 등 민감 정보를 불필요하게 노출
- 실제 고객 데이터 CSV를 Git에 커밋
- 사용자가 제공하지 않은 CSV 스키마를 확정된 것처럼 단정
- `raw/`, `wiki/`, `export/` 원본 영역 임의 수정
- 서버 PC 운영 데이터나 Docker 운영 파일을 사용자 승인 없이 수정·배포
- `viewer`가 데이터를 수정할 수 있는 것처럼 설계 또는 문서화
- 숫자 근거 없는 단정형 보고서 작성
