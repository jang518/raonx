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
export CSV 5개
        ↓  로컬 대시보드 개발
robocopy로 서버 PC에 필요한 파일만 전송
        ↓
사내망 내부 팀 공유 웹 대시보드
```

이 대시보드는 **외부 인터넷 접속 없이 사내망 내부 전용**으로 운영합니다.

---

## 2. 사용자와 활용 방식

| 사용자 | 역할 | 활용 방식 |
|---|---|---|
| admin 계정 | admin | 조회, 데이터 수정, 매칭 신뢰도/메모 등 운영 정보 관리, 서버 반영 |
| 일반 사용자 계정 | viewer | 대시보드 조회 전용 |
| 영업·엔지니어 | viewer | 고객 미팅 전 고객 니즈, 추천 기능, 제안 메시지, 다음 질문 조회 |
| 마케팅/전략 담당 | viewer 또는 admin | 반복 니즈, 가치 메시지, 세미나/제안서/기술 콘텐츠 후보 확인 |
| 위키 관리자 | admin | 방문보고서 입수 → Claude로 위키 갱신 → export 재생성 → 서버 반영 |
| Claude Code | 계획·해석·리스크 담당 | 질문 설계, 리스크 점검, 최종 문서화 |
| Codex | 구현·검증 담당 | Streamlit 구현, CSV 검증, Docker/서버 반영 보조 |

권한 기준:

- 모든 사용자는 계정별로 로그인합니다.
- `admin` 계정은 조회와 수정이 가능합니다.
- `admin` 이외 계정은 `viewer`로 조회만 가능합니다.
- 인증 정보는 코드에 하드코딩하지 않고 환경변수 또는 Streamlit secrets 등 외부 설정으로 관리합니다.
- admin 수정값은 원본 CSV를 직접 수정하지 않고 별도 override CSV에 저장합니다.

---

## 3. 정보 자산과 역할

| 정보 자산 | 역할 | 현재 기준 |
|---|---|---|
| 방문보고서 | 고객 니즈와 문의 내용을 확인하는 핵심 원천 자료 | 고객별 니즈, 관심 기능, 대응 이력의 1차 근거 |
| 기능 업데이트 자료 | 소프트웨어별 기능과 가치 메시지를 정리하는 자료 | 소프트웨어 기능을 고객 니즈와 연결하는 근거 |
| `wiki/` 지식 위키 | 원천 자료를 Claude가 분석·정리한 마크다운 지식 베이스 | 고객별 니즈, 기능, 매칭, 대응 전략을 사람이 검토 가능한 형태로 유지 |
| `export/` CSV 5개 | 웹 대시보드가 읽는 구조화 데이터 | customer_master 중심의 관계형 데이터 묶음 |
| Streamlit 대시보드 | 팀원이 조회하는 사내 웹 화면 | 마케팅 활용, 고객 미팅 준비, confidence 확인 |
| override CSV | admin 수정값 저장 영역 | 원본 CSV를 직접 수정하지 않고 담당자 메모, 운영 메모 등 수정값 별도 관리 |
| 서버 PC 운영 파일 | 팀 공유 대시보드를 실행하는 운영 환경 | Docker 기반 운영, `incoming/` → `data/` 반영 |

주의:

- 실제 고객 데이터 CSV는 기본적으로 Git에 커밋하지 않습니다.
- override CSV 실데이터도 Git에 커밋하지 않습니다.
- `raw/`, `wiki/`, `export/`는 원본 영역으로 보며 대시보드 개발 중 직접 수정하지 않습니다.
- 대시보드 개발 기본 범위는 `dashboard/`입니다.

---

## 4. v5 export 데이터 구조

대시보드 입력 데이터는 `export/`에서 생성된 CSV 5개를 기준으로 합니다.

```text
customer_master.csv
customer_needs.csv
software_features.csv
need_value_matching.csv
customer_strategy.csv
```

### 4.1 파일별 역할

| 파일 | 역할 | 핵심 키 |
|---|---|---|
| `customer_master.csv` | 고객 정보의 단일 진실 공급원(SSOT). 고객 ID, 표준 고객사명, 부서, 산업군, 고객 등급, 방문 횟수 등을 관리 | `customer_id` |
| `customer_needs.csv` | 고객 니즈 데이터. 고객 기본 정보는 master를 참조하고, 니즈 본문·분석·담당자 입력값을 관리 | `need_id`, `customer_id` |
| `software_features.csv` | 소프트웨어 기능과 가치 키워드 데이터 | `feature_id` |
| `need_value_matching.csv` | 고객 니즈와 소프트웨어 기능의 가치 매칭 데이터 | `match_id`, `need_id`, `feature_id` |
| `customer_strategy.csv` | 고객사별 대응 전략, 추천 소프트웨어, 다음 미팅 질문, 제안 메시지 데이터 | `strategy_id`, `customer_id` |

### 4.2 파일 간 관계

```text
customer_master ──(customer_id)──► customer_needs
customer_master ──(customer_id)──► customer_strategy
customer_needs  ──(need_id)──────► need_value_matching
software_features ─(feature_id)──► need_value_matching
```

규칙:

- 고객사 기준 정보는 `customer_master.csv`를 단일 진실 공급원으로 봅니다.
- 고객사 단위 연결은 `customer_name` 문자열이 아니라 `customer_id`를 우선 사용합니다.
- `customer_needs.csv`와 `customer_strategy.csv`의 `customer_name`은 화면 표시용입니다.
- 고객 기본 정보(`department`, `industry`, `customer_grade`, `visit_count`)는 `customer_master.csv`에서 가져옵니다.
- `need_value_matching.csv`에는 `customer_id`를 중복 추가하지 않습니다.
- 고객 정보가 필요한 매칭 화면은 `need_value_matching.need_id` → `customer_needs.need_id` → `customer_needs.customer_id` → `customer_master.customer_id` 순서로 연결합니다.
- `software_features.csv`에는 `customer_id`를 추가하지 않습니다.
- `related_need_keywords`는 직접 FK가 아니라 참고 연결로 표현합니다.

### 4.3 현재 실제 CSV 컬럼

`customer_master.csv`:

```text
customer_id, customer_name, department, industry, customer_grade, visit_count, author, dashboard_visible
```

`customer_needs.csv`:

```text
need_id, customer_id, customer_name, need_date, need_keyword, need_text, need_analysis, need_final, author, assignee, progress_note, sensitivity_level, dashboard_visible
```

`software_features.csv`:

```text
feature_id, software, feature_name, feature_summary, value_keywords, related_need_keywords, source_wiki, last_updated, dashboard_visible
```

`need_value_matching.csv`:

```text
match_id, need_id, feature_id, value_message, need_date, dashboard_visible, confidence
```

`customer_strategy.csv`:

```text
strategy_id, customer_id, customer_name, main_needs, recommended_software, approach_details, suggested_questions, proposal_message, author, assignee, sensitivity_level, dashboard_visible
```

---

## 5. 데이터 표시 기준

- `dashboard_visible`은 모든 CSV에서 대시보드 기본 표시 여부를 판단하는 기준입니다.
- `dashboard_visible=FALSE` 데이터는 기본 화면에서 숨깁니다.
- `confidence`는 `need_value_matching.csv`에 존재하는 매칭 신뢰도 표시 기준으로 사용합니다.
- `review_status`는 1차 범위에서 제외합니다.
- `sensitivity_level`은 `customer_needs.csv`, `customer_strategy.csv`에 존재하지만, 내부 직원용 대시보드에서 표시/필터/배지 기준으로 사용하지 않습니다.
- `sensitivity_level` 값 변환은 진행하지 않습니다.

---

## 6. 대시보드 화면 범위

1차 개발 화면은 다음 6개입니다.

| 화면 | 목적 | 핵심 질문 | 주요 데이터 |
|---|---|---|---|
| Home | 전체 현황, 최근 니즈, 마케팅 후보 요약 | 지금 가장 먼저 봐야 할 고객 니즈와 활용 후보는 무엇인가? | 5개 CSV 전체 요약 |
| 고객 니즈 검색 | 고객사·부서·산업군·키워드 중심 검색 | 특정 고객 또는 산업군은 어떤 니즈를 가지고 있었는가? | master + needs |
| 니즈-기능-가치 매칭 | 고객 니즈에 연결된 추천 기능과 제안 메시지 확인 | 이 니즈에 어떤 기능과 가치 메시지를 연결할 수 있는가? | needs + matching + features + master |
| 고객사별 대응 전략 | 추천 접근 방향, 다음 미팅 추천 질문 확인 | 다음 미팅에서 어떤 접근과 질문을 준비해야 하는가? | master + strategy + needs |
| 소프트웨어별 가치 맵 | 기능 목록과 연결 가능한 고객 니즈 확인 | 이 소프트웨어 기능은 어떤 고객 니즈와 연결되는가? | features + matching + needs + master |
| 마케팅 활용 | 반복 니즈, 가치 메시지, 콘텐츠/세미나/제안서 후보 확인 | 어떤 고객 니즈를 마케팅 자료와 고객 맞춤 콘텐츠로 활용할 수 있는가? | needs + matching + features + master |

---

## 7. 고객 데이터 해석 규칙

사용 가능:

- `customer_master.csv`의 고객 ID, 표준 고객사명, 부서, 산업군, 고객 등급, 방문 횟수
- 방문보고서에서 확인된 고객 니즈
- wiki에 정리된 소프트웨어 기능
- 니즈-기능 매칭 정보와 `confidence`
- 다음 미팅 추천 질문
- 마케팅 활용 후보
- 대시보드 노출 여부

금지:

- 고객의 구매 가능성 추정
- 예산 규모 추정
- 도입 의사 단정
- 방문보고서에 없는 고객 의도 상상
- `customer_name` 문자열만으로 고객 관계를 단정하고 `customer_id` 검증을 생략하는 것
- `confidence`가 낮은 매칭을 주요 추천으로 단정 표시
- 마케팅 활용 후보를 확정 캠페인 또는 확정 수요처럼 표시
- 고객 담당자 정보를 필요 이상으로 상세하게 노출
- 원천 자료에 없는 기능 효과를 과장해서 작성
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

## 8. 로컬/서버 운영 구조

사용자 컴퓨터:

```text
knowledge_for_marketing/
├── raw/
├── wiki/
├── export/
├── dashboard/
├── scripts/
└── logs/
```

서버 PC:

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
1. 사용자 컴퓨터에서 export CSV 5개와 대시보드 운영 파일 준비
2. robocopy로 서버 PC의 incoming/ 또는 운영 반영용 폴더에 필요한 파일만 전송
3. incoming/ 파일명·개수·헤더·관계 검증
4. 기존 data/를 backup/으로 복사
5. incoming/ 검증본을 data/로 반영
6. Streamlit 데이터 새로고침 또는 Docker 컨테이너 재시작
7. logs/에 반영 결과 기록
```

---

## 9. 절대 금지

- 실제 고객 데이터 CSV를 Git에 커밋
- override CSV 실데이터를 Git에 커밋
- 인증 정보를 코드나 Git에 커밋
- `raw/`, `wiki/`, `export/` 원본 영역 임의 수정
- `customer_master.csv`를 무시하고 `customer_name` 문자열만으로 고객 관계를 연결
- `need_value_matching.csv`나 `software_features.csv`에 `customer_id`를 중복 추가
- `review_status`를 1차 기능으로 구현
- `sensitivity_level`을 표시/필터/배지 기준으로 사용
- 외부 인터넷 접속 지원을 전제로 설계
