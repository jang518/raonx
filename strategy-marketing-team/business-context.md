# business-context.md — knowledge_for_marketing_dashboard 사업 맥락

> 목적: Claude Code와 Codex가 고객 방문보고서 기반 지식 위키, export CSV, Streamlit 대시보드를 해석할 때 같은 사업 기준과 데이터 해석 원칙을 보게 하기 위한 문서입니다.

---

## 1. 프로젝트 개요

`knowledge_for_marketing_dashboard`는 고객 방문보고서에서 파악한 고객 니즈와 소프트웨어 기능 업데이트 자료를 연결해, 영업·엔지니어가 고객 미팅 전에 필요한 정보를 빠르게 확인할 수 있게 만드는 사내 지식 활용 프로젝트입니다.

핵심 메시지는 “자료를 많이 모으는 것”이 아니라, **고객 니즈에 맞는 소프트웨어 기능, 제안 메시지, 다음 미팅 질문을 빠르게 연결하는 것**입니다.

기본 흐름:

```text
방문보고서 · 기능 업데이트 자료
        ↓  Claude 분석·정리
wiki/ 지식 위키
        ↓  구조화 데이터 추출
export CSV 4개
        ↓  서버 PC 전송
팀 공유 웹 대시보드
```

이 대시보드는 사내 팀원이 고객 미팅 전 다음 질문에 빠르게 답하기 위한 도구입니다.

1. 이 고객사는 과거에 어떤 니즈와 문의를 가지고 있었는가?
2. 해당 고객 니즈에 연결할 수 있는 소프트웨어 기능은 무엇인가?
3. 고객에게 어떤 가치 메시지와 제안 방향을 제시할 수 있는가?
4. 다음 미팅에서 확인해야 할 추천 질문은 무엇인가?
5. 어떤 니즈-기능 매칭은 신뢰도나 검토 상태상 주의가 필요한가?
6. 고객사별 대응 전략은 검토 완료 상태인가, 검토 필요 상태인가?
7. 영업·엔지니어가 고객 미팅 전에 가장 먼저 확인해야 할 정보는 무엇인가?

---

## 2. 사용자와 활용 방식

| 사용자 | 역할 | 활용 방식 |
|---|---|---|
| 영업·엔지니어 | viewer | 고객 미팅 전 고객 니즈, 추천 기능, 제안 메시지, 다음 질문 조회 |
| 담당자 | admin 또는 운영 담당 | 매칭 신뢰도, 검토 상태, 담당자 입력값 관리 |
| 위키 관리자 | admin | 방문보고서 입수 → Claude로 위키 갱신 → export 재생성 → 서버 반영 |
| Claude Code | 계획·해석·리스크 담당 | 질문 설계, 리스크 점검, 최종 문서화 |
| Codex | 구현·검증 담당 | Streamlit 구현, CSV 검증, Docker/서버 반영 보조 |

권한 기준:

- `admin`: 사용자. 데이터 수정, 검토 상태 관리, 매칭 신뢰도 관리, 운영 반영 수행.
- `viewer`: 팀원. 대시보드 조회만 수행.
- 1차 개발에는 viewer 코멘트 기능을 포함하지 않습니다.
- admin 수정 기능은 저장 방식이 확정된 이후 별도 작업으로 진행합니다.
- 로그인 방식, viewer 접근 방식, admin 수정값 저장 위치는 확정되지 않았으면 임의 구현하지 않습니다.

---

## 3. 정보 자산

| 정보 자산 | 역할 | 현재 기준 |
|---|---|---|
| 방문보고서 | 고객 니즈와 문의 내용을 확인하는 핵심 원천 자료 | 고객별 니즈, 관심 기능, 대응 이력의 1차 근거 |
| 기능 업데이트 자료 | 소프트웨어별 기능과 가치 메시지를 정리하는 자료 | 소프트웨어 기능을 고객 니즈와 연결하는 근거 |
| `wiki/` 지식 위키 | 원천 자료를 Claude가 분석·정리한 마크다운 지식 베이스 | 고객별 니즈, 기능, 매칭, 대응 전략을 사람이 검토 가능한 형태로 유지 |
| `export/` CSV | 웹 대시보드가 읽는 구조화 데이터 | 대시보드 입력 데이터 4개 CSV |
| `dashboard_manifest.json` | 데이터 묶음의 기준 정보 | export 파일 목록, 생성 시점, 반영 상태 등을 관리하는 보조 파일 |
| Streamlit 대시보드 | 팀원이 고객 미팅 전 조회하는 사내 웹 화면 | 고객 니즈, 추천 기능, 제안 메시지, 다음 질문, 검토 상태 확인 |
| 서버 PC 운영 파일 | 팀 공유 대시보드를 실행하는 운영 환경 | Docker 기반 운영, `incoming/` → `data/` 반영 |
| 검토 기록 | 담당자가 직접 보완하는 운영 메모 | `review_status`, `confidence`, `assignee`, `next_action` 등을 기준으로 관리 |

주의:

- 실제 고객 데이터 CSV는 기본적으로 Git에 커밋하지 않습니다.
- `raw/`, `wiki/`, `export/`는 원본 영역으로 보며 대시보드 개발 중 직접 수정하지 않습니다.
- 대시보드 개발 기본 범위는 `dashboard/`입니다.
- 기존 문서나 코드에 `app/` 표현이 남아 있더라도 새 작업은 `dashboard/` 기준으로 해석합니다.

---

## 4. 로컬/서버 운영 구조

### 4.1 사용자 컴퓨터: 개발·관리 영역

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
1. 사용자 컴퓨터에서 export CSV 4개 생성
2. 서버 PC의 incoming/으로 전송
3. incoming/ 파일명·개수·헤더 검증
4. 기존 data/를 backup/으로 복사
5. incoming/ 검증본을 data/로 반영
6. Docker 대시보드 재시작 또는 자동 갱신 확인
7. logs/에 반영 결과 기록
```

기준:

- 전송 방식은 사용자 결정에 따릅니다.
- robocopy, 공유 폴더, 수동 복사 중 무엇을 사용할지는 이 문서에서 임의 확정하지 않습니다.
- 검증 실패 시 기존 `data/`를 교체하지 않습니다.
- 운영 로그에는 고객 정보 원문을 과도하게 남기지 않습니다.
- 서버 운영 파일 변경 또는 배포는 사용자 승인 후 진행합니다.

---

## 5. 사용 데이터

대시보드 입력 데이터는 `export/`에서 생성된 CSV 4개를 기준으로 합니다.

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

단, 실제 파일명이 다르면 실제 `export/` 파일명을 우선합니다. 파일명과 컬럼명은 실제 파일 확인 전 확정하지 않습니다.

### 5.1 CSV 역할

| 파일명 | 역할 | 주요 내용 |
|---|---|---|
| `customer_needs.csv` | 고객 니즈 목록 | 고객사, 산업군, 니즈 키워드, 니즈 요약, 출처 파일, 보고서 일자, 민감도, 대시보드 노출 여부, 고객 등급, 작성자, 담당자 |
| `customer_strategy.csv` | 고객사별 대응 전략 | 고객사, 주요 니즈, 추천 소프트웨어, 접근 방향, 추천 질문, 제안 메시지, 민감도, 대시보드 노출 여부, 고객 등급, 방문 횟수, 검토 상태, 다음 액션, 담당자 |
| `need_value_matching.csv` | 니즈-기능-가치 매칭 | 고객 니즈와 소프트웨어 기능 연결, 가치 메시지, 최근 검토일, 대시보드 노출 여부, 검토 상태, 신뢰도 |
| `software_features.csv` | 소프트웨어 기능 목록 | 소프트웨어명, 기능명, 기능 요약, 가치 키워드, 관련 니즈 키워드, 출처 파일, 최근 업데이트일, 대시보드 노출 여부 |

### 5.2 현재 기준 컬럼

| 파일명 | 컬럼 |
|---|---|
| `customer_needs.csv` | `need_id`, `customer_name`, `industry`, `need_keyword`, `need_summary`, `source_file`, `report_date`, `sensitivity_level`, `dashboard_visible`, `customer_grade`, `author`, `assignee` |
| `customer_strategy.csv` | `strategy_id`, `customer_name`, `main_needs`, `recommended_software`, `approach_details`, `suggested_questions`, `proposal_message`, `author`, `sensitivity_level`, `dashboard_visible`, `customer_grade`, `visit_count`, `review_status`, `next_action`, `assignee` |
| `need_value_matching.csv` | `match_id`, `need_id`, `feature_id`, `value_message`, `last_reviewed`, `dashboard_visible`, `review_status`, `confidence` |
| `software_features.csv` | `feature_id`, `software`, `feature_name`, `feature_summary`, `value_keywords`, `related_need_keywords`, `source_file`, `last_updated`, `dashboard_visible` |

이 컬럼 기준은 현재 문서 기준입니다. 실제 CSV와 다르면 실제 CSV를 우선하고, 차이는 `analysis_summary`에 기록합니다.

### 5.3 검토 관련 컬럼

| 파일명 | 컬럼 | 값 | 의미 |
|---|---|---|---|
| `need_value_matching.csv` | `review_status` | `검토 필요`, `검토 완료` | 니즈-기능 매칭의 직접 검토 상태 |
| `need_value_matching.csv` | `confidence` | `높음`, `중간`, `낮음` | 니즈-기능 매칭의 신뢰도 |
| `customer_strategy.csv` | `review_status` | `검토 필요`, `검토 완료` | 고객사별 대응 전략의 직접 검토 상태 |
| 전체 또는 일부 CSV | `dashboard_visible` | 실제 CSV 값 기준 | 대시보드 표시 여부 |
| 전체 또는 일부 CSV | `sensitivity_level` | `공개 가능`, `비공개` 등 실제 CSV 값 기준 | 민감도 경고 표시 기준 |

주의:

- `review_status`와 `confidence`는 사용자의 직접 검토가 필요한 운영 핵심 항목입니다.
- `dashboard_visible`이 노출 제외를 의미하면 대시보드 기본 화면에 표시하지 않습니다.
- `sensitivity_level`은 숨김 처리 기준이 아니라 사용자가 주의해서 보도록 표시하는 기준입니다.
- 민감도 판단이 불명확하면 `검토 필요` 또는 `비공개`에 준하는 주의 표시를 우선합니다.

---

## 6. CSV 관계 정의

4개 CSV의 관계는 다음 기준으로 해석합니다.

1. `need_value_matching.csv` ↔ `customer_needs.csv`
   - 연결 컬럼: `need_value_matching.need_id` → `customer_needs.need_id`
   - 의미: 고객 니즈 1건에 여러 기능이 매칭될 수 있음

2. `need_value_matching.csv` ↔ `software_features.csv`
   - 연결 컬럼: `need_value_matching.feature_id` → `software_features.feature_id`
   - 의미: 기능 1개가 여러 고객 니즈에 매칭될 수 있음

3. `customer_strategy.csv` ↔ `customer_needs.csv`
   - 연결 컬럼: `customer_strategy.customer_name` → `customer_needs.customer_name`
   - 의미: 고객사의 여러 니즈를 종합해 전략 1건으로 요약
   - 주의: PK/FK가 아닌 고객사명 문자열 연결이므로 완전 일치 여부를 검증해야 함

4. `software_features.csv` ↔ `customer_needs.csv`
   - 연결 컬럼: `software_features.related_need_keywords` ↔ `customer_needs.need_keyword`
   - 의미: 직접 JOIN이 아니라 어휘 기반 연결
   - 용도: “이 기능은 어떤 니즈 유형과 관련 있나” 조회 시 사용

규칙:

- 관계가 불명확하면 임의 JOIN하지 않습니다.
- 고객사명 문자열 연결은 표기 차이로 오류가 날 수 있으므로 검증 결과에 기록합니다.
- 키워드 기반 연결은 직접 근거가 아니라 참고 연결로 표현합니다.

---

## 7. 고객 데이터 해석 규칙

방문보고서, wiki, export CSV에 명시된 정보만 사용합니다.

사용 가능:

- 고객사명
- 고객 담당자 또는 고객 관련 표시 정보
- 사내 담당자 또는 `assignee`
- 산업군
- 고객 등급
- 방문보고서에서 확인된 고객 니즈
- 고객이 언급한 문제, 요구, 관심 기능
- wiki에 정리된 소프트웨어 기능
- 니즈-기능 매칭 정보
- 매칭 신뢰도
- 검토 상태
- 다음 액션
- 다음 미팅 추천 질문
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
- 고객 담당자 정보를 필요 이상으로 상세하게 노출
- 개인 정보 또는 민감 정보를 불필요하게 대시보드에 노출
- 원천 자료에 없는 기능 효과를 과장해서 작성
- `viewer` 역할의 팀원이 데이터를 수정할 수 있는 것처럼 설계 또는 문서화
- `raw/`, `wiki/`, `export/` 원본을 대시보드 앱이나 작업자가 직접 수정

추천 문장 구조:

```text
고객 니즈 근거 + 연결 기능 + 제안 가치 + 다음 확인 질문
```

예시:

```text
이 고객사는 반복 해석 업무 감소에 대한 니즈가 확인되었습니다. 해당 니즈는 pSeven 기반 프로세스 자동화 기능과 연결할 수 있습니다. 다음 미팅에서는 현재 반복 작업의 병목 구간과 자동화 대상 범위를 확인하는 것이 좋습니다.
```

---

## 8. 의사결정 기준

### 8.1 우선 표시할 고객 니즈

다음 조건을 함께 봅니다.

- Home 화면에서 최근 고객 니즈와 검토 필요 항목을 우선 보여줄 수 있다.
- 최근 방문보고서 또는 wiki에서 반복적으로 등장한다.
- 고객 미팅 전 영업·엔지니어가 바로 확인할 필요가 있다.
- 소프트웨어 기능 또는 가치 메시지와 연결 가능하다.
- 고객사명, 산업군, 담당자, 출처 파일이 확인 가능하다.
- `dashboard_visible` 기준으로 노출 가능 상태다.

### 8.2 우선 추천할 기능/메시지

다음 조건이면 우선 추천 후보로 봅니다.

- 고객 니즈와 기능의 연결 근거가 명확하다.
- `need_value_matching.csv`의 `review_status`가 `검토 완료` 상태다.
- `confidence`가 `높음` 또는 최소 `중간` 이상이다.
- 고객에게 제안할 `value_message`가 구체적이다.
- 고객사별 대응 전략의 `proposal_message`, `suggested_questions`, `next_action`과 연결된다.
- 현재 관리 중인 소프트웨어 기능과 연결된다.

주의:

- 소프트웨어 항목은 추가될 수 있으므로, 코드와 화면은 특정 소프트웨어명을 하드코딩하지 않고 CSV의 `software` 값을 기준으로 동적으로 처리합니다.
- 검토 완료가 아닌 항목은 추천 후보가 될 수는 있지만, 확정 추천처럼 표시하지 않습니다.

### 8.3 검토 필요 매칭

다음 조건이면 검토 필요로 표시합니다.

- `need_value_matching.csv`의 `review_status`가 `검토 필요`이다.
- `customer_strategy.csv`의 `review_status`가 `검토 필요`이다.
- `confidence`가 `낮음`이다.
- 출처 문서 또는 wiki 근거가 불명확하다.
- 고객 니즈와 기능의 연결이 일반론에 가깝다.
- 담당자의 추가 확인이 필요한 상태다.

---

## 9. 대시보드 화면 범위

1차 개발 화면은 다음 5개입니다.

| 화면 | 목적 | 핵심 질문 |
|---|---|---|
| Home | 최근 고객 니즈, 자주 등장한 니즈, 검토 필요 매칭 현황 확인 | 지금 가장 먼저 봐야 할 고객 니즈와 검토 필요 항목은 무엇인가? |
| 고객 니즈 검색 | 고객사·산업군·키워드 중심 검색 | 특정 고객 또는 산업군은 어떤 니즈를 가지고 있었는가? |
| 니즈-기능-가치 매칭 | 고객 니즈에 연결된 추천 기능과 제안 메시지 확인 | 이 니즈에 어떤 기능과 가치 메시지를 연결할 수 있는가? |
| 고객사별 대응 전략 | 추천 접근 방향, 다음 미팅 추천 질문 확인 | 다음 미팅에서 어떤 접근과 질문을 준비해야 하는가? |
| 소프트웨어별 가치 맵 | 기능 목록과 연결 가능한 고객 니즈 확인 | 이 소프트웨어 기능은 어떤 고객 니즈와 연결되는가? |

화면 기준:

- 검색/필터는 1차 개발에서 일반적인 검색창 중심으로 구현합니다.
- 세부 필터와 UI 개선은 사용 후 피드백을 반영합니다.
- `검토 필요`, `신뢰도 낮음`, `데이터 누락`은 숨기지 않습니다.
- 화면 제목은 기능형보다 업무형으로 작성합니다.
- 표는 검색·필터·정렬이 가능하도록 구성합니다.

---

## 10. 산출물과 개발 단계

### 10.1 1차 — 로컬 Streamlit 조회 화면 실행

- `dashboard/`: Streamlit 대시보드 앱 코드
- `dashboard/app.py` 또는 `dashboard/streamlit_app.py`: 앱 진입점
- `dashboard/pages/`: 5개 화면 구성 파일
- `dashboard/components/`: 공통 UI 컴포넌트
- `dashboard/services/`: CSV 로딩, 상태 처리, 권한 관련 서비스 로직
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
- `docs/deploy-flow.md` 또는 `docs/server-runbook.md`: 서버 PC 실행, 중지, 재시작, 데이터 반영 방법

### 10.3 3차 — 데이터/코드 반영 흐름

사용자 PC에서 서버 PC로 보낼 파일:

데이터 파일 — `export/` → 서버 PC `incoming/`

```text
export/
├── customer_needs.csv
├── software_features.csv
├── need_value_matching.csv
├── customer_strategy.csv
└── dashboard_manifest.json
```

대시보드 코드 파일 — `dashboard/` → 서버 PC `dashboard/`

```text
dashboard/
```

운영 기준:

- 데이터 파일은 위키 갱신 때마다 반복 전송될 수 있습니다.
- 대시보드 코드 파일은 최초 1회와 기능 변경 시에만 전송합니다.
- 서버 PC에서는 `incoming/`으로 받은 데이터를 검증 후 `data/`로 반영합니다.
- 운영 데이터 반영 전 기존 `data/`를 `backup/`에 보관합니다.
- 전송 방식은 사용자 결정에 따릅니다.

### 10.4 4차 — 로그인 및 권한 완성

운영 공유 전 로그인과 viewer 조회 전용 권한은 적용 대상입니다.

- 로그인 기능
- `admin` / `viewer` 역할 구분
- `admin`: 데이터 수정, 검토 상태 관리, 매칭 신뢰도 관리, 운영 반영
- `viewer`: 조회 전용

단, 로그인 방식과 admin 수정값 저장 위치가 확정되지 않았으면 임의 구현하지 않습니다.

### 10.5 5차 — 후속 개선

- 세부 필터 고도화
- 팀원 피드백 기반 화면 개선
- 검토 상태 관리 UI 개선
- 필요 시 admin 수정 기능의 저장 방식 별도 검토

---

## 11. 톤과 문장 기준

영업·엔지니어가 고객 미팅 전에 빠르게 읽고 준비할 수 있게 씁니다.

좋은 문장:

- “이 고객사는 해석 자동화와 반복 업무 감소에 대한 니즈가 확인되었습니다.”
- “해당 니즈는 pSeven 기반 프로세스 자동화 기능과 연결할 수 있습니다.”
- “이 매칭은 검토 완료 상태이며, 신뢰도는 높음입니다.”
- “다음 미팅에서는 현재 반복 작업의 병목 구간을 확인하는 질문이 적합합니다.”
- “이 항목은 검토 필요 상태이므로 담당자 확인 후 제안에 활용하는 것이 좋습니다.”
- “고객사명과 담당자는 미팅 준비 목적에 한해 표시합니다.”

피해야 할 문장:

- “이 고객사는 반드시 이 솔루션을 도입할 것입니다.”
- “구매 가능성이 높습니다.”
- “예산이 충분할 것으로 보입니다.”
- “이 기능을 사용하면 모든 문제가 해결됩니다.”
- “근거는 없지만 이 기능을 추천합니다.”
- “검토 상태가 없지만 바로 제안해도 됩니다.”
- “팀원 누구나 데이터를 자유롭게 수정할 수 있습니다.”
- “비공개 항목이지만 일반 화면에 그대로 노출해도 됩니다.”

---

## 12. 최종 보고서 기준

최종 보고서는 아래 순서로 작성합니다.

1. 한 줄 결론
2. 왜 그런가
3. 대시보드에서 가장 먼저 보여줘야 할 정보
4. 우선 구현할 화면
5. 사용 CSV와 핵심 컬럼
6. 데이터 해석 리스크
7. 권한과 운영 기준
8. 서버 반영 방식
9. 다음 작업

보고서 작성 원칙:

- 근거와 한계를 함께 씁니다.
- 확정된 내용과 확인 필요한 내용을 분리합니다.
- 고객 니즈, 기능, 가치 메시지, 다음 질문의 연결 흐름을 유지합니다.
- 구현 세부는 Codex 산출물 또는 `analysis_summary`를 참고합니다.

---

## 13. 절대 금지

- 데이터에 없는 고객 의도, 구매 가능성, 예산, 도입 의지, 내부 의사결정 상황 단정
- 방문보고서에 없는 내용을 고객 요구사항처럼 표현
- 검토 필요 매칭을 확정 추천처럼 표시
- 고객사, 담당자, 미팅 정보 등 민감 정보를 불필요하게 노출
- 실제 고객 데이터 CSV를 Git에 커밋
- 사용자가 제공하지 않은 CSV 스키마를 확정된 것처럼 단정
- `raw/`, `wiki/`, `export/` 원본 영역 임의 수정
- 서버 PC 운영 데이터나 Docker 운영 파일을 사용자 승인 없이 수정·배포
- `viewer`가 데이터를 수정할 수 있는 것처럼 설계 또는 문서화
- 숫자 근거 없는 단정형 보고서 작성
