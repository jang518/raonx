# business-context.md — knowledge_for_marketing 대시보드 사업 맥락

> 목적: Claude와 Codex가 고객 방문보고서 기반 지식 위키, export CSV, Streamlit 대시보드를 해석할 때 같은 사업 기준을 보게 하기 위한 문서입니다.

## 1. 프로젝트 개요

`knowledge_for_marketing`은 고객 방문보고서와 소프트웨어 기능 업데이트 자료를 정리해, 영업·기술지원 엔지니어가 고객 미팅 전에 필요한 정보를 빠르게 확인할 수 있게 만드는 사내 지식 활용 프로젝트입니다.

이 프로젝트의 핵심 메시지는 “자료를 많이 모으는 것”보다 “고객 니즈에 맞는 소프트웨어 기능과 제안 메시지를 빠르게 연결하는 것”입니다. 특히 고객 미팅 전 준비, 고객사별 대응 전략 확인, 추천 기능 탐색, 다음 미팅 질문 도출 같은 사용 장면을 중요하게 봅니다.

대시보드는 상위 `export/` 폴더에서 생성된 CSV 4개를 읽어 고객 니즈, 고객사별 대응 전략, 니즈-기능-가치 매칭, 소프트웨어별 가치 맵을 조회할 수 있게 합니다. 개발은 사용자 PC의 `knowledge_for_marketing/dashboard/`에서 진행하고, 운영은 Windows 11 서버 PC에서 Docker Compose 기반으로 실행합니다.

대시보드는 사내 네트워크에서만 사용하며, 접속은 내부 IP와 포트 형태를 기본으로 합니다. 로그인 기능은 필요합니다. 권한은 `admin`과 `viewer`로 구분하며, 데이터 수정은 `admin` 역할인 사용자만 수행하고 팀원은 최종 사용자로서 조회만 가능하게 합니다.

## 2. 정보 자산

| 정보 자산 | 역할 | 현재 기준 |
|---|---|---|
| 방문보고서 | 고객 니즈와 문의 내용을 확인하는 핵심 원천 자료 | 고객별 니즈, 관심 기능, 대응 이력의 1차 근거 |
| 기능 업데이트 자료 | 소프트웨어별 기능과 가치 메시지를 정리하는 자료 | Fegate for Ship, pSeven Desktop, pSeven Enterprise, Nastran, Patran, Apex 등 주요 소프트웨어 기능을 고객 니즈와 연결하는 근거 |
| 지식 위키 | 원천 자료를 Claude가 분석·정리한 마크다운 지식 베이스 | 고객별 니즈, 기능, 매칭, 대응 전략을 사람이 검토 가능한 형태로 유지 |
| export CSV | 웹 대시보드가 읽는 구조화 데이터 | `customer_needs.csv`, `customer_strategy.csv`, `need_value_matching.csv`, `software_features.csv` 4개 파일 |
| `dashboard_manifest.json` | 서버 PC로 전달되는 데이터 묶음의 기준 정보 | export 파일 목록, 생성 시점, 반영 상태 등을 관리하는 보조 파일 |
| Streamlit 대시보드 | 팀원이 고객 미팅 전 조회하는 사내 웹 화면 | 고객 니즈, 추천 기능, 제안 메시지, 다음 질문, 검토 상태를 빠르게 확인 |
| 서버 PC 운영 파일 | 팀 공유 대시보드를 실행하는 운영 환경 | 사용자 PC의 `export/` 데이터와 `dashboard/` 코드를 서버 PC로 `robocopy` 복사해 반영 |
| 검토 기록 | 담당자가 직접 보완하는 운영 메모 | `need_value_matching.csv`와 `customer_strategy.csv`의 `review_status`를 기준으로 검토 상태를 표시 |

## 3. 이번 프로젝트 목표

`knowledge_for_marketing/dashboard` 폴더에서 Streamlit 기반 사내 웹 대시보드를 개발·배포해, 영업·엔지니어가 고객 미팅 전에 고객 니즈와 추천 기능을 빠르게 확인할 수 있게 합니다.

최종 질문:

1. 특정 고객사는 과거에 어떤 니즈와 문의를 가지고 있었는가?
2. 해당 고객 니즈에 연결할 수 있는 소프트웨어 기능은 무엇인가?
3. 고객에게 어떤 가치 메시지와 제안 방향을 제시할 수 있는가?
4. 다음 미팅에서 확인해야 할 추천 질문은 무엇인가?
5. 어떤 니즈-기능 매칭은 신뢰도나 검토 상태상 주의가 필요한가?
6. 고객사별 대응 전략은 검토 완료 상태인가, 아니면 검토 필요 상태인가?
7. 사내 팀원이 고객 미팅 전에 가장 빠르게 확인해야 할 정보는 무엇인가?

대시보드는 기본적으로 팀원이 조회하는 화면입니다. 데이터 수정, 검토 상태 변경, 운영 데이터 반영은 `admin` 역할인 사용자만 수행합니다. 팀원은 고객 미팅 전 필요한 정보를 조회하는 `viewer` 역할로 사용합니다.

## 4. 사용 데이터

파일 위치:

- 상위 `export/`: 위키에서 추출한 대시보드용 CSV 4개와 `dashboard_manifest.json`의 원본 위치
- 현재 `dashboard/data/`: 로컬 개발 및 Streamlit 실행에 사용하는 CSV 복사본 또는 샘플 데이터
- 서버 PC `marketing_llmwiki/incoming/`: 사용자 PC에서 `robocopy`로 전달받는 임시 데이터 위치
- 서버 PC `marketing_llmwiki/data/`: Docker 대시보드가 실제로 읽는 운영 데이터 위치
- 서버 PC `marketing_llmwiki/dashboard/`: Docker 대시보드 실행 코드 위치

CSV 파일:

| 파일명 | 역할 | 주요 내용 |
|---|---|---|
| `customer_needs.csv` | 고객 니즈 목록 | 고객사, 산업군, 니즈 키워드, 니즈 요약, 출처 파일, 보고서 일자, 민감도, 대시보드 노출 여부, 고객 등급, 작성자, 담당자 |
| `customer_strategy.csv` | 고객사별 대응 전략 | 고객사, 주요 니즈, 추천 소프트웨어, 접근 방향, 추천 질문, 제안 메시지, 민감도, 대시보드 노출 여부, 고객 등급, 방문 횟수, 검토 상태, 다음 액션, 담당자 |
| `need_value_matching.csv` | 니즈-기능-가치 매칭 | 고객 니즈와 소프트웨어 기능 연결, 가치 메시지, 최근 검토일, 대시보드 노출 여부, 검토 상태, 신뢰도 |
| `software_features.csv` | 소프트웨어 기능 목록 | 소프트웨어명, 기능명, 기능 요약, 가치 키워드, 관련 니즈 키워드, 출처 파일, 최근 업데이트일, 대시보드 노출 여부 |

확정 컬럼 기준:

| 파일명 | 컬럼 |
|---|---|
| `customer_needs.csv` | `need_id`, `customer_name`, `industry`, `need_keyword`, `need_summary`, `source_file`, `report_date`, `sensitivity_level`, `dashboard_visible`, `customer_grade`, `author`, `assignee` |
| `customer_strategy.csv` | `strategy_id`, `customer_name`, `main_needs`, `recommended_software`, `approach_details`, `suggested_questions`, `proposal_message`, `author`, `sensitivity_level`, `dashboard_visible`, `customer_grade`, `visit_count`, `review_status`, `next_action`, `assignee` |
| `need_value_matching.csv` | `match_id`, `need_id`, `feature_id`, `value_message`, `last_reviewed`, `dashboard_visible`, `review_status`, `confidence` |
| `software_features.csv` | `feature_id`, `software`, `feature_name`, `feature_summary`, `value_keywords`, `related_need_keywords`, `source_file`, `last_updated`, `dashboard_visible` |

검토 관련 컬럼:

| 파일명 | 컬럼 | 값 | 의미 |
|---|---|---|---|
| `need_value_matching.csv` | `review_status` | `검토 필요`, `검토 완료` | 니즈-기능 매칭의 직접 검토 상태 |
| `need_value_matching.csv` | `confidence` | `높음`, `중간`, `낮음` | 니즈-기능 매칭의 신뢰도 |
| `customer_strategy.csv` | `review_status` | `검토 필요`, `검토 완료` | 고객사별 대응 전략의 직접 검토 상태 |

주의:

- `review_status`와 `confidence`는 사용자의 직접 검토가 필요한 운영 핵심 항목입니다.
- 위 항목 외의 사용자 직접 기입 항목은 검토 상태와 별개의 일반 운영 입력값으로 봅니다.
- 실제 고객 데이터 CSV는 기본적으로 Git에 커밋하지 않습니다.
- `raw/`, `wiki/`, `export/`는 대시보드 개발 중 직접 수정하지 않습니다.
- 대시보드는 `dashboard/data/`에 배치된 CSV를 읽는 구조로 개발합니다.
- 업데이트 주기는 정기적이지 않으므로, 데이터 반영은 필요 시 수동 또는 반수동으로 수행합니다.
- `dashboard_visible`이 노출 제외를 의미하는 값이면 대시보드에 표시하지 않습니다.
- `sensitivity_level`은 `공개 가능`, `비공개` 두 값을 표시용으로 사용합니다. 이 값은 숨김 처리 기준이 아니라 사용자가 주의해서 보도록 표시하는 기준입니다.

## 5. 고객 데이터 해석 규칙

방문보고서와 위키, export CSV에 명시된 정보만 사용합니다.

사용 가능:

- 고객사명
- 고객 담당자 또는 고객 관련 표시 정보
- 사내 담당자 또는 `assignee`
- 산업군
- 고객 등급
- 방문보고서에서 확인된 고객 니즈
- 고객이 언급한 문제, 요구, 관심 기능
- 위키에 정리된 소프트웨어 기능
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
- `raw/`, `wiki/`, `export/` 원본을 대시보드 앱에서 직접 수정

민감도와 대시보드 노출 관련 기준:

- `dashboard_visible`은 대시보드 표시 여부를 판단하는 핵심 컬럼입니다.
- `sensitivity_level`은 `공개 가능`, `비공개` 두 값을 화면에 표시합니다.
- `sensitivity_level = 비공개`인 항목은 사용자가 민감 정보를 인지할 수 있도록 표시하되, 실제 숨김 여부는 `dashboard_visible` 기준을 우선합니다.
- 민감도 판단이 불명확하면 “검토 필요” 또는 “비공개”에 준하는 주의 표시를 우선합니다.

## 6. 의사결정 기준

### 우선 표시할 고객 니즈

다음 조건을 함께 봅니다.

- Home 화면에서 최근 고객 니즈와 검토 필요 항목을 우선 보여줄 수 있다.
- 최근 방문보고서 또는 위키에서 반복적으로 등장한다.
- 고객 미팅 전 영업·엔지니어가 바로 확인할 필요가 있다.
- 소프트웨어 기능 또는 가치 메시지와 연결 가능하다.
- 고객사명, 산업군, 담당자, 출처 파일이 확인 가능하다.
- `dashboard_visible` 기준으로 노출 가능 상태다.

### 우선 추천할 기능/메시지

다음 조건이면 우선 추천 후보로 봅니다.

- 고객 니즈와 기능의 연결 근거가 명확하다.
- `need_value_matching.csv`의 `review_status`가 `검토 완료` 상태다.
- `confidence`가 `높음` 또는 최소 `중간` 이상이다.
- 고객에게 제안할 `value_message`가 구체적이다.
- 고객사별 대응 전략의 `proposal_message`, `suggested_questions`, `next_action`과 연결된다.
- Fegate for Ship, pSeven Desktop, pSeven Enterprise, Nastran, Patran, Apex 등 현재 관리 중인 소프트웨어와 연결된다.

향후 소프트웨어 항목은 추가될 수 있으므로, 코드와 화면은 특정 소프트웨어명을 하드코딩하지 않고 CSV의 `software` 값을 기준으로 동적으로 처리하는 것을 원칙으로 합니다.

### 검토 필요 매칭

다음 조건이면 검토 필요로 표시합니다.

- `need_value_matching.csv`의 `review_status`가 `검토 필요`이다.
- `customer_strategy.csv`의 `review_status`가 `검토 필요`이다.
- `confidence`가 `낮음`이다.
- 출처 문서 또는 위키 근거가 불명확하다.
- 고객 니즈와 기능의 연결이 일반론에 가깝다.
- 담당자의 추가 확인이 필요한 상태다.

### 권한과 운영 기준

- `admin` 역할은 데이터 수정, 검토 상태 관리, 서버 반영, 대시보드 코드 변경을 수행합니다.
- 팀원은 `viewer` 역할의 최종 사용자로 대시보드 조회만 가능합니다.
- 1차 개발에서는 팀원 코멘트 작성 기능을 포함하지 않습니다.
- 대시보드 로그인 기능은 필요하며, 사내 네트워크 접속을 전제로 합니다.
- 사용자 PC에서 개발하고, Windows 11 서버 PC에서는 Docker Compose로 운영합니다.
- 서버 접속은 내부 IP와 포트 형태를 기본으로 합니다.

### 화면 개발 우선순위

1차 개발 우선순위는 다음과 같습니다.

1. **Home** — 최근 고객 니즈, 자주 등장한 니즈, 검토 필요 매칭 현황
2. **고객 니즈 검색** — 고객사·산업군·키워드 중심 검색
3. **니즈-기능-가치 매칭** — 고객 니즈에 연결된 추천 기능과 제안 메시지
4. **고객사별 대응 전략** — 추천 접근 방향, 다음 미팅 추천 질문
5. **소프트웨어별 가치 맵** — 기능 목록과 연결 가능한 고객 니즈

검색/필터는 1차 개발에서 일반적인 검색창 중심으로 구현하고, 세부 필터와 UI 개선은 사용 후 피드백을 반영합니다.

### CSV 관계 정의

4개 CSV의 관계는 다음 기준으로 해석합니다.

1. `need_value_matching.csv` ↔ `customer_needs.csv`  
   - 연결 컬럼: `need_value_matching.need_id` → `customer_needs.need_id`  
   - 의미: 고객 니즈 1건에 여러 기능이 매칭될 수 있음  
   - 예: `N001`에 `F001`, `F003` 매칭 가능

2. `need_value_matching.csv` ↔ `software_features.csv`  
   - 연결 컬럼: `need_value_matching.feature_id` → `software_features.feature_id`  
   - 의미: 기능 1개가 여러 고객 니즈에 매칭될 수 있음  
   - 예: `F012` 기능이 여러 고객 니즈와 매칭 가능

3. `customer_strategy.csv` ↔ `customer_needs.csv`  
   - 연결 컬럼: `customer_strategy.customer_name` → `customer_needs.customer_name`  
   - 의미: 고객사의 여러 니즈를 종합해 전략 1건으로 요약  
   - 주의: PK/FK가 아닌 고객사명 문자열 연결이므로 완전 일치가 필요함

4. `software_features.csv` ↔ `customer_needs.csv`  
   - 연결 컬럼: `software_features.related_need_keywords` ↔ `customer_needs.need_keyword`  
   - 의미: 동일한 1개 키워드 어휘를 공유하는 직접 JOIN이 아닌 어휘 기반 연결  
   - 용도: “이 기능은 어떤 니즈 유형과 관련 있나” 조회 시 사용

## 7. 산출물

이번 프로젝트의 목표 산출물은 권장 개발 순서에 따라 아래와 같이 진행합니다.

### 1차 — 로컬 Streamlit 실행

- `app/`: Streamlit 대시보드 앱 코드
- `app/main.py` 또는 `streamlit_app.py`: 앱 진입점
- `app/pages/`: 5개 화면 구성 파일
- `scripts/validate_csv.py`: 4개 CSV 존재 여부, 컬럼명, 관계 무결성 검증
- `scripts/load_data.py`: CSV 로딩 및 공통 전처리 로직
- `requirements.txt`: Python 패키지 목록
- `README.md`: 로컬 실행 방법과 프로젝트 설명
- `output/dashboard_review.md`: 화면 구성 및 개선 검토 기록

### 2차 — 서버 PC Docker Compose 운영

- `Dockerfile`: Streamlit 앱 컨테이너 설정
- `docker-compose.yml`: Windows 11 서버 PC 운영용 Compose 설정
- `.streamlit/config.toml`: Streamlit 실행 설정
- `docs/server-runbook.md`: 서버 PC 실행, 중지, 재시작 방법

### 3차 — robocopy 기반 데이터/코드 반영

사용자 PC에서 서버 PC로 보낼 파일:

데이터 파일 — 상위 `export/` → 서버 PC `incoming/`

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

- 데이터 파일은 위키 갱신 때마다 반복 전송합니다.
- 대시보드 코드 파일은 최초 1회와 기능 변경 시에만 전송합니다.
- 서버 PC에서는 `incoming/`으로 받은 데이터를 검증 후 `data/`로 반영하는 흐름을 우선합니다.
- 운영 데이터 반영 전 기존 `data/` 백업을 고려합니다.

### 4차 — 로그인 및 권한

- 로그인 기능
- `admin` / `viewer` 역할 구분
- `admin`: 데이터 수정, 검토 상태 관리, 서버 반영, 설정 관리
- `viewer`: 조회 전용

### 5차 — 후속 개선

- 세부 필터 고도화
- 팀원 피드백 기반 화면 개선
- 검토 상태 관리 UI 개선
- 필요 시 코멘트 기능 별도 검토

대시보드 화면 목표:

1. **Home** — 최근 고객 니즈, 자주 등장한 니즈, 검토 필요 매칭 현황
2. **고객 니즈 검색** — 고객사·산업군·키워드로 필터링
3. **니즈-기능-가치 매칭** — 고객 니즈에 연결된 추천 기능과 제안 메시지
4. **고객사별 대응 전략** — 추천 접근 방향, 다음 미팅 추천 질문
5. **소프트웨어별 가치 맵** — 기능 목록과 연결 가능한 고객 니즈

## 8. 톤과 문장 기준

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

## 9. 최종 결론 형식

최종 보고서는 아래 순서로 작성합니다.

1. 한 줄 결론
2. 왜 그런가
3. 대시보드에서 가장 먼저 보여줘야 할 정보
4. 우선 구현할 화면
5. 사용 CSV와 핵심 컬럼
6. 4개 CSV의 관계와 연결 기준
7. 검토가 필요한 데이터/매칭
8. 권한, 로그인, 조회 전용 운영 기준
9. 서버 PC 반영 방식
10. Docker Compose 운영 기준
11. 리스크 3개
12. 바로 실행할 액션 5개
