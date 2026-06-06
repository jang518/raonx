# DESIGN.md — 라온엑스 고객 니즈-기능 매칭 대시보드 디자인 기준

> 목적: Codex가 `knowledge_for_marketing_dashboard`의 Streamlit 대시보드, 조회 화면, 상태 표시, 권한 안내, 향후 PDF/인쇄용 출력물을 만들 때 참고할 디자인 기준입니다. 이 문서는 단순히 예쁜 화면을 만들기 위한 문서가 아니라, 마케팅 자료 기획과 고객 미팅 준비에 필요한 고객 니즈, 추천 소프트웨어 기능, 제안 메시지, 다음 확인 질문을 빠르게 이해하고 바로 활용할 수 있도록 만드는 기준 문서입니다.

---

## 1. 디자인 목표

이 대시보드는 라온엑스 내부에서 고객 방문보고서와 소프트웨어 기능 정보를 연결해 활용하기 위한 **사내망 내부 전용 지식 검색형 대시보드**입니다.

디자인 목표는 크게 두 가지입니다.

1. **마케팅 활용**
   - 고객 니즈와 소프트웨어 기능 정보를 바탕으로 세미나, 제안서, 고객 맞춤 기능 소개, 기술 콘텐츠 기획에 활용할 수 있게 한다.
   - 반복적으로 등장하는 니즈, 소프트웨어별 가치 포인트, 고객군별 관심 주제를 빠르게 파악할 수 있게 한다.
   - 단순 기능 나열이 아니라 “어떤 고객 문제에 어떤 가치 메시지를 연결할 수 있는가”가 보이게 한다.

2. **고객 미팅 준비**
   - 고객 미팅 전 3~5분 안에 핵심 정보를 파악한다.
   - 고객 니즈 → 추천 기능 → 제안 가치 → 다음 미팅 질문 흐름이 한눈에 보이게 한다.
   - 낮은 confidence, 데이터 누락, 확인 필요 항목을 숨기지 않고 명확히 표시한다.

---

## 2. 데이터 기반 디자인 원칙

대시보드 화면은 v5 export CSV 5개가 서로 연결되어 작동한다는 전제를 따른다.

```text
customer_master.customer_id → customer_needs.customer_id
customer_master.customer_id → customer_strategy.customer_id
customer_needs.need_id → need_value_matching.need_id
software_features.feature_id → need_value_matching.feature_id
```

| 원칙 | 설명 |
|---|---|
| customer_master 중심 | 고객 기본 정보는 `customer_master.csv`를 기준으로 표시한다. |
| customer_id 우선 | 고객사 단위 검색·필터·상세 화면은 `customer_id` 기준으로 연결한다. |
| customer_name 표시용 | `customer_needs.csv`, `customer_strategy.csv`의 `customer_name`은 표시용으로만 사용한다. |
| 매칭 흐름 준수 | 니즈-기능 화면은 `needs → matching → features` 관계를 따른다. 고객 정보가 필요하면 `matching → needs → master` 순서로 연결한다. |
| 기능 중심 화면 | 소프트웨어별 가치 맵은 `features → matching → needs → master` 순서로 고객 니즈를 보여준다. |
| confidence 중심 | 니즈-기능 매칭의 신뢰도는 `confidence`를 기준으로 표현한다. |
| 민감도 미사용 | 내부 직원용 대시보드이므로 `sensitivity_level`은 표시/필터/배지 기준으로 사용하지 않는다. |
| 노출 기준 준수 | `dashboard_visible=FALSE`는 기본 화면에서 숨긴다. |
| 단정 금지 | 구매 가능성, 예산, 도입 의지, 고객 내부 의사결정 상황을 추정하지 않는다. |
| 권한 분리 | admin은 수정 가능, viewer는 조회 전용으로 명확히 구분한다. |

---

## 3. 브랜드와 레이아웃 기준

- 라온엑스 로고는 실제 제공된 PNG 파일을 우선 사용한다.
- 로고 파일이 없으면 외부 URL에서 임의로 가져오지 않고 텍스트 로고 또는 임시 placeholder로 처리한다.
- 기본 화면 폭은 1280px 이상 데스크톱 브라우저를 기준으로 한다.
- 첫 화면은 `Home`을 가장 우선한다.
- 첫 화면 상단에는 라온엑스 로고, 서비스명, 로그인 사용자/권한, 마지막 데이터 업데이트 시간을 표시한다.
- 검색창은 사용자가 가장 먼저 보게 상단에 배치한다.
- 고객사명과 담당자는 표시하되, 불필요하게 큰 시각 강조를 하지 않는다.
- admin 수정 UI는 viewer에게 보이지 않게 한다.

색상은 내부 업무 시스템처럼 절제된 회색·청록 계열을 기본으로 한다.

| 토큰 | HEX | 용도 |
|---|---|---|
| `canvas` | `#F6F8FA` | 기본 배경 |
| `surface` | `#FFFFFF` | 카드, 표, 패널 |
| `ink` | `#1F2426` | 제목, 핵심 텍스트 |
| `body` | `#3F4749` | 본문 텍스트 |
| `muted` | `#6E7779` | 보조 설명, 날짜, 출처 |
| `hairline` | `#DDE3E5` | 카드 경계, 표 구분선 |
| `raonx-cyan` | `#36BFC7` | 주요 강조, 선택 상태, 핵심 액션 |
| `raonx-cyan-soft` | `#E6F8FA` | 선택된 필터, 정보 배지, 추천 기능 강조 |

상태 색:

| 상태 | 색상 | 용도 |
|---|---|---|
| confidence 높음 | `#20A67A` | 신뢰도 높음 |
| confidence 중간 / 확인 필요 | `#D97706` | 중간 confidence, 확인 필요 |
| confidence 낮음 / 오류 | `#DC2626` | 낮은 confidence, CSV 오류, 로그인 실패 |
| 정보/참고 | `#64748B` | 참고, 보조 정보 |
| 데이터 없음 | `#CBD5D9` | 결측, 데이터 없음 |

---

## 4. 화면별 디자인 기준

| 화면 | 목적 | 주요 데이터 연결 | 표시 기준 |
|---|---|---|---|
| Home | 전체 현황과 우선 확인 항목 | 5개 CSV 전체 요약 | 최근 니즈, 낮은 confidence, 마케팅 후보, 데이터 품질 경고 |
| 고객 니즈 검색 | 고객사·부서·산업군·키워드 검색 | master + needs | 고객 기본 정보는 master 기준, 니즈 상세는 needs 기준 |
| 니즈-기능-가치 매칭 | 고객 니즈에 연결된 기능과 가치 메시지 확인 | needs + matching + features + master | confidence 배지 필수, 낮은 confidence는 확인 필요로 표현 |
| 고객사별 대응 전략 | 고객별 접근 방향과 다음 질문 확인 | master + strategy + needs | 고객 기본 정보는 master, 전략 문구는 strategy 기준 |
| 소프트웨어별 가치 맵 | 기능별 연결 니즈 확인 | features + matching + needs + master | 소프트웨어명 하드코딩 금지, feature_id 기준 연결 |
| 마케팅 활용 | 반복 니즈와 콘텐츠 후보 확인 | needs + matching + features + master | 확정 캠페인처럼 표현 금지, 후보/검토 표현 사용 |

### Home

권장 순서:

1. 라온엑스 로고 + 대시보드 제목 + 로그인 사용자/권한 + 마지막 업데이트 시간
2. 전역 검색창
3. 핵심 요약 카드: 고객 수, 니즈 수, 매칭 수, 낮은 confidence 수
4. 최근 고객 니즈
5. 자주 등장한 니즈 키워드
6. 낮은 confidence 또는 확인 필요 매칭 현황
7. 마케팅 활용 후보 요약
8. 데이터 품질 경고: 누락 ID, 관계 불일치, 비정상 dashboard_visible 등

### 고객 니즈 검색

- 고객사명, customer_id, 부서, 산업군, 니즈 키워드, 니즈 요약을 함께 검색할 수 있게 한다.
- 고객 기본 정보는 `customer_master.csv`에서 가져온다.
- 니즈 정보는 `customer_needs.csv`에서 가져온다.
- `dashboard_visible=FALSE` 항목은 기본 결과에서 숨긴다.

### 니즈-기능-가치 매칭

- 고객 니즈 → 추천 소프트웨어 → 기능 → 가치 메시지 순서로 읽히게 한다.
- `confidence`를 반드시 배지로 표시한다.
- 낮은 confidence 항목은 확정 추천처럼 보이지 않게 한다.
- 고객 기본 정보가 필요하면 `matching → needs → master` 순서로 연결한다.

### 고객사별 대응 전략

- customer_id 기준으로 고객사를 선택한다.
- 고객 기본 정보는 `customer_master.csv`에서 표시한다.
- `main_needs`, `recommended_software`, `approach_details`, `suggested_questions`, `proposal_message`는 `customer_strategy.csv` 기준으로 표시한다.
- 대응 전략은 단정형 지시가 아니라 미팅 준비용 제안으로 보이게 한다.

### 소프트웨어별 가치 맵

- 소프트웨어명 기준으로 기능 목록을 보여준다.
- 기능명, 기능 요약, 가치 키워드, 관련 니즈를 표시한다.
- 고객 니즈는 `features → matching → needs → master` 순서로 연결한다.
- `related_need_keywords`는 직접 연결이 아니라 느슨한 키워드 연결임을 작게 표시한다.

### 마케팅 활용

표시 후보:

- 반복적으로 등장하는 고객 니즈 키워드
- 산업군 또는 고객군별 관심 주제
- 소프트웨어별 가치 메시지 후보
- 세미나 또는 기술 콘텐츠 주제로 활용 가능한 니즈
- 제안서나 고객 맞춤 기능 소개에 활용 가능한 기능/가치 연결

주의:

- 마케팅 활용 후보는 확정 캠페인이나 확정 고객 수요처럼 표현하지 않는다.
- 고객 내부 의사결정, 예산, 구매 가능성을 추정하지 않는다.
- 낮은 confidence 항목은 활용 전 담당자 확인이 필요하다고 표시한다.

---

## 5. 핵심 컴포넌트 기준

고객 카드:

1. customer_id
2. customer_name
3. department
4. industry
5. customer_grade
6. visit_count
7. 연결 니즈 수
8. 연결 전략 수

고객 니즈 카드:

1. customer_id / customer_name
2. department / industry
3. need_keyword
4. need_final 또는 need_analysis
5. need_date
6. assignee / progress_note
7. 연결 기능 개수

기능 매칭 카드:

1. need_keyword
2. software
3. feature_name
4. value_message
5. confidence 배지
6. 다음 확인 질문 또는 활용 전 확인 안내

고객사별 대응 전략 카드:

1. customer_id / customer_name
2. main_needs
3. recommended_software
4. approach_details
5. proposal_message
6. suggested_questions
7. admin override 메모가 있는 경우 별도 표시

마케팅 활용 카드:

1. 반복 니즈 키워드
2. 관련 산업군 또는 고객군
3. 연결 가능한 소프트웨어/기능
4. 가치 메시지 후보
5. 콘텐츠 또는 세미나 주제 후보
6. 근거 데이터 또는 출처
7. 활용 전 확인 사항

---

## 6. 문장 원칙

좋은 문장:

- “이 고객 니즈와 연결된 추천 기능을 확인하세요.”
- “다음 미팅에서 확인할 질문을 검토하세요.”
- “confidence가 낮은 매칭은 담당자 확인 후 제안에 활용하세요.”
- “반복적으로 등장하는 니즈를 마케팅 콘텐츠 후보로 검토하세요.”

피해야 할 문장:

- “이 고객은 구매 가능성이 높습니다.”
- “이 기능을 제안하면 수주될 것 같습니다.”
- “고객이 반드시 이 기능을 원합니다.”
- “예산이 충분할 것으로 보입니다.”
- “이 마케팅 캠페인은 확정입니다.”

---

## 7. 로그인/접근 안내 화면 기준

- 대시보드는 사내망 내부 전용이다.
- 외부 인터넷 접속은 범위에서 제외한다.
- 계정 입력, 비밀번호 입력, 로그인 버튼, 오류 메시지, 내부 시스템 안내 문구를 표시한다.
- `admin`: 조회, 데이터 수정, 매칭 신뢰도/메모 등 운영 정보 관리, 운영 반영 가능
- `viewer`: 조회 전용
- 인증 정보를 코드나 Git에 노출하지 않는다.

---

## 8. 금지 사항

- 라온엑스 로고 색상을 임의로 변경하지 않는다.
- 데이터 근거 없는 고객 의도, 구매 가능성, 예산, 도입 의지를 추정하지 않는다.
- `customer_master.csv`를 무시하고 customer_name 문자열만으로 고객 관계를 연결하지 않는다.
- `confidence`가 낮은 매칭을 확정 추천처럼 표시하지 않는다.
- 마케팅 활용 후보를 확정 캠페인 또는 확정 고객 수요처럼 표현하지 않는다.
- `dashboard_visible=FALSE` 항목을 기본 화면에 노출하지 않는다.
- `review_status` UI를 1차 범위에서 만들지 않는다.
- `sensitivity_level`을 표시/필터/배지 기준으로 사용하지 않는다.
- `need_value_matching.csv`나 `software_features.csv`에 `customer_id`가 있는 것처럼 화면을 설계하지 않는다.
- 실제 고객 정보를 예제 화면이나 테스트 이미지에 임의로 사용하지 않는다.
- admin 수정 UI를 viewer에게 노출하지 않는다.
- 인증 정보를 코드 예시 또는 화면 예시에 노출하지 않는다.
