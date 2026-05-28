# docs/templates.md — 협업 문서 표준 구조

경영현황 AI 대시보드 프로젝트에서 Claude Code와 Codex가 만드는 모든 협업/결과물 문서의 표준 구조다. 파일명은 `docs/collab-protocol.md` §3 `파일 네이밍 규칙`에 따라 slug를 붙인다.

모든 섹션을 반드시 채울 필요는 없다. 비워둘 때는 `해당 없음`이라고 표시한다.

## 인덱스

| 문서                           | Owner       | 트리거                           | 섹션 번호 |
| ---------------------------- | ----------- | ----------------------------- | ----- |
| `dev_plan_<slug>.md`         | Claude Code | 복잡한 개발/분석 작업 전 1차 계획          | 1     |
| `questions_<slug>.md`        | Claude Code | 경영진/경영지원팀 질문 및 분석 프레임 설계      | 2     |
| `risk_notes_<slug>.md`       | Claude Code | 데이터/보안/운영/해석 리스크 점검           | 3     |
| `final_report_<slug>.pdf`    | Claude Code | 경영회의용 최종 보고서                  | 4     |
| `handoff_<slug>.md` (Claude) | Claude Code | Claude Code 세션 인계             | 5     |
| `execution_plan_<slug>.md`   | Codex       | Claude Code 1차 계획 보완          | 6     |
| `handoff_<slug>.md` (Codex)  | Codex       | Codex 작업 인계                   | 7     |
| `analysis_summary_<slug>.md` | Codex       | 데이터 분석 요약                     | 8     |
| `data_quality_<slug>.md`     | Codex       | Excel/정제 데이터 품질 검증            | 9     |
| `dashboard_review_<slug>.md` | Claude Code | 대시보드 화면·지표·이해도 검토             | 10    |
| `config_review_<slug>.md`    | Claude Code | 설정 기반 구조 검토                   | 11    |
| `deploy_review_<slug>.md`    | Claude Code | Windows 11 + Docker 배포 리스크 검토 | 12    |
| `ops_guide_<slug>.md`        | Codex       | Docker 운영/재시작/백업 가이드 작성       | 13    |

---

## 1. dev_plan_<slug>.md (Claude Code)

```md
# dev_plan_<slug>.md

## Goal

이번 작업으로 무엇을 만들거나 바꿀지 한두 문장.

예:
- Excel 기반 경영현황 데이터를 웹 대시보드에서 조회할 수 있도록 MVP를 구현한다.
- `팀별실적`과 `목표` 시트를 기준으로 KPI/차트/테이블 산출 흐름을 만든다.

## Scope

포함할 일.

예:
- `팀별실적` 시트 파싱
- `목표` 시트 파싱
- KPI 계산 기준 정의
- 대시보드 첫 화면 구현
- 로그인 기능 구현
- Docker Compose 배포 파일 작성

## Out of Scope

이번 작업에서는 하지 않을 일. 과잉 구현 방지.

예:
- 원천 Excel 직접 수정
- 외부 공개 배포
- 고객사별 상세 분석
- FCST 정식 반영
- 사업 유형별 매출 분석
- 복잡한 권한 관리

## Files

### 수정 예상 파일
- `app/*`
- `server/*`
- `dashboard/*`
- `config/*`
- `deploy/*`
- `output/*`

### 건드리면 안 되는 파일
- 원천 Excel 파일
- 다른 actor가 Work Board에서 락 잡은 파일
- `.env`
- 실제 로그인 정보 또는 사내 IP가 포함된 파일

### 새로 생성할 파일
- `output/execution_plan_<slug>.md`
- `output/handoff_<slug>.md`
- 필요 시 `output/data_quality_<slug>.md`

## Proposed Steps

Codex가 실행할 순서. 단계별로 짧게.

1. `PROJECT.md`, `AGENTS.md`, `business-context.md`, `DESIGN.md`를 읽는다.
2. Work Board에 락을 등록한다.
3. Excel 구조를 확인한다.
4. 데이터 매핑/지표/화면 설정을 분리한다.
5. 구현한다.
6. 검증한다.
7. Handoff Log와 `handoff_<slug>.md`를 남긴다.

## Risks

- 원천 Excel 구조가 예상과 다를 수 있음
- 월 목표가 없는데 월 목표처럼 표시될 수 있음
- 기술지원팀이 단순 매출 기준으로 오해될 수 있음
- 민감 데이터가 커밋될 수 있음
- 로그인/권한 범위가 과도하게 복잡해질 수 있음
- Docker 배포가 Windows 11 PC 환경과 충돌할 수 있음

## Verification

Codex가 확인해야 할 기준.

- Excel 파일 읽기 성공
- 사용 시트 확인
- 누락 컬럼 확인
- KPI 계산 결과 샘플 확인
- 대시보드 렌더링 확인
- 로그인 동작 확인
- 설정 파일 검증
- Docker Compose 구성 검증
- `.env` / 원천 Excel / 민감 데이터 커밋 여부 확인

## Handoff Needed

- 작업 끝나고 누가 이어받는지
- 어떤 산출물을 누구에게 넘기는지
- Claude Code의 리뷰가 필요한지
- 최종 보고서 작성이 필요한지
```

## 2. questions_<slug>.md (Claude Code)

```md
# questions_<slug>.md

## Decision Context

이번에 답해야 할 의사결정 한 줄.

예:
경영진과 경영지원팀이 2026년 팀별 매출/이익/경비/목표 달성률을 웹 대시보드에서 빠르게 확인할 수 있어야 한다.

## Key Questions

경영진 또는 경영지원팀이 결정 전에 답해야 할 질문 3~10개.

각 질문은 아래 형식을 따른다.

### Q1. [질문]

- 답변에 필요한 데이터:
- 사용할 시트/컬럼:
- 분석 축:
- 연결되는 액션:
- 주의사항:

예:
### Q1. 목표 달성률이 낮은 팀은 어디인가?

- 답변에 필요한 데이터: `팀별실적`, `목표`
- 사용할 시트/컬럼: 팀명, 매출, 매출이익, 목표값
- 분석 축: 팀별, 분기별, 연간
- 연결되는 액션: 경영회의에서 원인 확인 대상 지정
- 주의사항: 팀별 목표 기준이 서로 다를 수 있음

## Analysis Frame

질문을 어떤 프레임으로 볼 것인지.

- 기간 비교: 월 / 분기 / 반기 / 연간
- 팀 비교: SDT, TST1, TST2, MKT, EST1, EST2, EST3, 경영지원팀
- 손익 비교: 매출, 매출이익, 영업이익, 경비
- 목표 비교: 목표 달성률, 목표대비부족액
- 변화 비교: 전월 대비, 전분기 대비

## Out of Scope Questions

이번 작업에서는 답하지 않을 질문. 다음 작업으로 미룸.

예:
- 고객사별 상세 매출 분석
- 사업 유형별 매출 분석
- FCST 기반 전망 분석
- 미확정 수주 예측
- 영업 확률 기반 예상 매출
```

## 3. risk_notes_<slug>.md (Claude Code)

```md
# risk_notes_<slug>.md

## Top Risks

가장 큰 리스크 3~7개.

각 리스크는 다음 구조를 따른다.

### Risk 1. [리스크 이름]

- 무엇이 문제인가:
- 어떤 데이터/가정에서 발생하는가:
- 발생하면 어떤 결과로 이어지는가:
- 어떻게 완화할 수 있는가:
- Codex 또는 User에게 넘길 확인 사항:

## Data Caveats

데이터 관련 주의사항.

- 원천 Excel 구조 한계
- 누락 컬럼
- 빈 값
- 수식 오류
- 음수 값
- 월 목표 없음
- `실적G` 미사용 또는 임시 데이터
- 팀별 목표 기준 차이
- 기술지원팀 단순 매출 비교 주의

## Interpretation Limits

이 데이터로 말할 수 있는 것 / 없는 것.

### 말할 수 있는 것
- 팀별 매출/손익 흐름
- 목표 대비 진행률
- 전월 대비 증감
- 경비 증가 여부
- 데이터 누락/이상치

### 말할 수 없는 것
- 미래 매출 전망
- 영업 확률
- 미확정 수주
- 고객사별 전망
- 월 목표가 없는 상태에서의 월 목표 달성률
- FCST 정식 반영 결과

## Security / Operation Risks

- 실제 Excel 파일 커밋 위험
- `.env` 커밋 위험
- 로그인 정보 노출 위험
- 사내 IP/포트 노출 위험
- Docker 배포 후 자동 재시작 미설정 위험
- 백업 미정 위험

## Open Questions for User

사용자 결정이 필요한 항목.

- 목표 fallback 방식:
- 권한 구분:
- PDF 출력 범위:
- Docker 운영 방식:
- 데이터 업데이트 주기:
```

## 4. final_report_<slug>.pdf (Claude Code)

경영진과 경영지원팀이 경영회의에서 사용할 수 있도록 **핵심 → 근거 → 확인 필요 사항 → 액션** 순서로 구성한다.

PDF 기준으로 작성한다. 상세 보고서를 기본으로 하며, 10~15페이지 범위를 권장한다. 단, 숫자와 표가 많아져도 각 페이지는 한 가지 메시지만 담는다.

### 페이지 구성

| #  | 페이지               | 핵심 콘텐츠                      |
| -- | ----------------- | --------------------------- |
| 1  | 표지                | 보고서 제목, 기간, 작성일, 데이터 출처     |
| 2  | Executive Summary | 한 줄 결론, 핵심 KPI, 주요 확인 사항    |
| 3  | KPI Snapshot      | 총매출, 매출이익, 영업이익, 경비, 목표 달성률 |
| 4  | 기간별 추이            | 월/분기/연간 흐름                  |
| 5  | 팀별 실적 비교          | 팀별 매출, 매출이익, 영업이익           |
| 6  | 목표 달성률            | 팀별 목표 대비 진행률과 부족액           |
| 7  | 경비와 이익성           | 경비 증가와 영업이익 변화              |
| 8  | 팀별 상세             | 주요 팀별 해석                    |
| 9  | 기술지원팀 주석          | 단순 매출 비교가 어려운 팀에 대한 해석      |
| 10 | 데이터 품질 경고         | 누락, 오류, 이상치, 목표 없음          |
| 11 | 리스크와 해석 한계        | 이 데이터로 말할 수 없는 것            |
| 12 | 경영진 확인 포인트        | 회의에서 결정할 항목                 |
| 13 | 경영지원팀 액션          | 후속 확인/운영 액션                 |
| 14 | Appendix          | 데이터 출처, 계산 기준, 설정 파일        |
| 15 | Appendix          | 참고 차트/표                     |

### 템플릿 본문

```md
# [보고서 제목 — 예: 2026년 경영현황 대시보드 보고서]

**기간**: YYYY-MM-DD ~ YYYY-MM-DD  
**데이터 출처**: `data/raw/[파일명].xlsx`  
**작성**: Claude Code  
**작성일**: YYYY-MM-DD  

---

## Executive Summary

### 한 줄 결론

[가장 강조하고 싶은 경영 판단 한 줄. 숫자 근거 1개 포함. 단정형 예측 금지]

### 핵심 KPI 요약

| 지표 | 값 | 비교 기준 | 해석 |
|---|---:|---|---|
| 총매출 | | 전월/전분기/목표 대비 | |
| 매출이익 | | | |
| 영업이익 | | | |
| 경비 | | | |
| 목표 달성률 | | | |

### 주요 확인 사항

1. [확인 사항]
2. [확인 사항]
3. [확인 사항]

---

## 1. KPI Snapshot

[차트 1. 핵심 KPI 카드]

### 한 문장 요약

[숫자 근거 포함]

---

## 2. 기간별 추이

[차트 2. 매출/매출이익/영업이익 추이]

### 해석

- 확인된 흐름:
- 주의할 구간:
- 다음 확인 항목:

---

## 3. 팀별 실적 비교

[차트 3. 팀별 매출 비교]  
[차트 4. 팀별 매출이익 비교]  
[차트 5. 팀별 영업이익 비교]

| 팀 | 매출 | 매출이익 | 영업이익 | 경비 | 해석 |
|---|---:|---:|---:|---:|---|
| SDT | | | | | |
| TST1 | | | | | |
| TST2 | | | | | |
| MKT | | | | | |
| EST1 | | | | | |
| EST2 | | | | | |
| EST3 | | | | | |

---

## 4. 목표 달성률

[차트 6. 팀별 목표 달성률]  
[차트 7. 목표대비부족액]

### 주의

- 월 목표가 없으면 월 목표 달성률처럼 표현하지 않는다.
- 월별 화면에서 목표가 필요하면 `분기 목표 기준 참고값`이라고 표시한다.

---

## 5. 경비와 이익성

[차트 8. 경비 추이]  
[차트 9. 영업이익과 경비 비교]

### 해석

- 경비 증가 여부:
- 영업이익 영향:
- 확인해야 할 항목:

---

## 6. 기술지원팀 해석 주석

기술지원팀은 단순 매출 기준만으로 평가하면 안 된다. 고객 유지, 기술 대응, 유지보수 안정성, 용역/컨설팅 지원 등 간접 기여가 있을 수 있다.

### 확인 포인트

- 직접 매출:
- 지원 기여:
- 유지보수와의 연결:
- 추가로 필요한 데이터:

---

## 7. 데이터 품질 경고

| 항목 | 상태 | 영향 | 조치 |
|---|---|---|---|
| 누락 컬럼 | | | |
| 빈 값 | | | |
| 음수 값 | | | |
| 수식 오류 | | | |
| 목표 없음 | | | |
| `실적G` 없음 | | | |

---

## 8. 리스크와 해석 한계

상세는 `risk_notes_<slug>.md` 참조.

| 리스크 | 영향도 | 대응 |
|---|---|---|
| | | |

---

## 9. 경영진 확인 포인트

1. [결정 필요 항목]
2. [결정 필요 항목]
3. [결정 필요 항목]

---

## 10. 경영지원팀 액션

| 우선순위 | 액션 | 담당 | 시점 | 확인 지표 |
|---|---|---|---|---|
| 1 | | | | |
| 2 | | | | |
| 3 | | | | |

---

## Appendix

### 데이터 출처

- 파일:
- 사용 시트:
- 사용 컬럼:
- 기간:
- 생성일:

### 계산 기준

- 매출:
- 매출이익:
- 영업이익:
- 경비:
- 목표 달성률:
- 목표대비부족액:

### 해석 한계

- 답할 수 있는 것:
- 답할 수 없는 것:
```

### 시각·서식 가이드

* 색상, 여백, 폰트는 `DESIGN.md` 토큰을 따른다.
* 차트 제목은 단순형, 부제는 해석형으로 작성한다.
* 표와 차트 placeholder는 `[차트 1. ...]`, `[표 1. ...]` 형식으로 명시한다.
* 페이지 break는 각 큰 섹션 앞에 `---`를 사용한다.
* PDF에는 기준 기간, 작성일, 데이터 출처를 반드시 표시한다.
* PDF 출력 전 렌더링과 페이지 잘림을 확인한다.

````

## 5. handoff_<slug>.md (Claude Code → 다음 actor)

```md
# handoff_<slug>.md

## Status

이 작업의 현재 상태. 어디까지 끝났고 어디서 멈췄는지.

## What Changed

바뀐 파일과 변경 요점.

## Verified

Claude Code가 확인한 사실관계/일관성.

예:
- `business-context.md`와 일치 확인
- `PROJECT.md` Decisions와 충돌 없음 확인
- 데이터 해석 금지 사항 반영 확인

## Open Items

다음 actor가 이어받을 일.

## Risks Carried Over

아직 해소되지 않은 리스크.

## Lock State

잡고 있던 파일 락의 현재 상태. 모두 해제했어야 한다.
````

## 6. execution_plan_<slug>.md (Codex)

```md
# execution_plan_<slug>.md

## Plan Source

참조한 `dev_plan_<slug>.md` 명시.

## Scope Diff

dev_plan에서 빠진 범위, 추가/축소가 필요한 항목.

예:
- Docker 검증은 현재 환경에서 불가해 `docker compose config`까지만 수행
- `실적G` 시트가 없어 FCST 반영 제외
- 월 목표가 없어 목표 관련 월별 지표는 `목표 없음`으로 표시

## File Plan

### 수정할 파일
- 파일:
- 변경 요점:

### 새로 만들 파일
- 파일:
- 목적:

### 락을 잡을 파일
- 파일:
- 이유:

### 건드리지 않을 파일
- 원천 Excel:
- `.env`:
- 다른 actor 락 파일:

## Implementation Steps

구현 순서. 단계별로 짧게.

## Verification Plan

각 단계 후 어떤 검사를 할지.

- Excel 파일 읽기
- 시트 존재 확인
- 컬럼 존재 확인
- 집계 결과 샘플 확인
- 대시보드 렌더링
- 로그인 동작
- 설정 파일 검증
- Docker Compose 검증
- PDF 출력 필요 시 렌더링 확인
- 민감정보 커밋 여부 확인

## Risks Identified

dev_plan에 없거나 보완된 리스크.

## Handoff Trigger

어떤 조건이면 다음 actor에게 넘기는지.
```

## 7. handoff_<slug>.md (Codex → 다음 actor)

```md
# handoff_<slug>.md

## Status

이 작업의 현재 상태. 어디까지 끝났고 어디서 멈췄는지.

## What Changed

바뀐 파일과 변경 요점. 파일/시트/컬럼 단위로 짧게.

## Verified

실제 수행한 검증.

### File Checks

- 실행 명령:
- 결과:

### Excel / Data Checks

- 사용 파일:
- 사용 시트:
- 사용 컬럼:
- 행 수:
- 누락:
- 이상치:
- 합계 검증:

### Rendering Checks

- 확인한 화면:
- 확인한 브라우저/뷰어:
- 콘솔 오류:
- 화면 깨짐 여부:

### Login Checks

필요한 기능일 때만 작성.

- 로그인 성공:
- 로그인 실패:
- 세션 유지:
- 로그아웃:
- 권한별 접근:

### Docker Checks

필요한 기능일 때만 작성.

- `docker compose config`:
- `docker compose up`:
- healthcheck:
- restart policy:
- volume:
- `.env.example`:

### PDF Checks

필요한 기능일 때만 작성.

- PDF 생성:
- 페이지 잘림:
- 표/차트 표시:
- 로고/기준 기간/작성일 표시:

## Open Items

다음 actor가 이어받을 일.

## Risks Carried Over

아직 해소되지 않은 리스크.

## Lock State

잡고 있던 파일 락의 현재 상태. 모두 해제했어야 한다.
```

## 8. analysis_summary_<slug>.md (Codex)

```md
# analysis_summary_<slug>.md

## Data Scope

사용한 데이터 범위.

- 파일:
- 기간:
- 사용 시트:
- 사용 컬럼:
- 행/열 범위:
- 샘플/실데이터 구분:
- `실적G` 사용 여부:

## Key Findings

숫자로 확인 가능한 발견 5~10개.

각 발견은 다음 구조를 따른다.

### Finding 1. [발견 제목]

- 근거 지표:
- 가공 방식:
- 비교 기준:
- 신뢰 수준:
- 한계:

## Trends

시계열/기간별 패턴.

- 월별:
- 분기별:
- 반기별:
- 연간:

## Team Comparison

팀별 비교.

- SDT:
- TST1:
- TST2:
- MKT:
- EST1:
- EST2:
- EST3:
- 경영지원팀:

## Target Progress

목표 달성률 관련 요약.

- 목표 기준:
- 목표 달성률:
- 목표대비부족액:
- 월 목표 없음 처리:
- fallback 사용 여부:

## Anomalies

이상치, 누락, 추정으로 채운 영역.

- 빈 값:
- 음수:
- 수식 오류:
- 비정상 증감:
- 중복:
- 목표 없음:

## What This Does NOT Show

이 데이터로 답할 수 없는 질문. 추정 금지 영역.

- 미래 매출 전망
- 미확정 수주
- 영업 확률
- 고객사별 전망
- FCST 정식 반영
- 월 목표 달성률

## Suggested Frame for Final Report

Claude Code가 final_report 작성 시 참고할 비교 프레임.

- 핵심 메시지:
- 경영진 확인 포인트:
- 경영지원팀 액션:
- 주의할 표현:
```

## 9. data_quality_<slug>.md (Codex)

```md
# data_quality_<slug>.md

## Data Source

- 파일:
- 생성일/수정일:
- 사용 시트:
- 사용하지 않은 시트:
- 검증일:

## Sheet Checks

| 시트 | 존재 여부 | 사용 여부 | 비고 |
|---|---|---|---|
| 팀별실적 | | | |
| 목표 | | | |
| 실적G | | | |
| total | | | |
| 실적(23~26) | | | |
| 경영현황비교 | | | |

## Column Checks

| 시트 | 필수 컬럼/항목 | 존재 여부 | 비고 |
|---|---|---|---|
| 팀별실적 | 매출 | | |
| 팀별실적 | 매입 | | |
| 팀별실적 | 매출이익 | | |
| 팀별실적 | 영업이익 | | |
| 팀별실적 | 경상이익 | | |
| 팀별실적 | 직접 비용 | | |
| 팀별실적 | 간접 비용 | | |
| 팀별실적 | 경비 | | |
| 목표 | 팀별 목표값 | | |

## Value Checks

| 검증 항목 | 결과 | 영향 | 조치 |
|---|---|---|---|
| 빈 값 | | | |
| 0 값 | | | |
| 음수 값 | | | |
| 수식 오류 | | | |
| 비정상적으로 큰 값 | | | |
| 월/분기/연간 합계 불일치 | | | |
| 중복 팀명 | | | |
| 목표 없음 | | | |

## Formula / Derived Metric Checks

- 경비 계산:
  - `경비` 있음:
  - `직접 비용 + 간접 비용` 대체:
- 목표 달성률 계산:
- 목표대비부족액 계산:
- 전월 대비 증감률 계산:
- FCST 반영 여부:

## Data Quality Summary

- 사용 가능:
- 주의 필요:
- 사용 보류:
- 사용자 확인 필요:

## Recommendations

1. [조치]
2. [조치]
3. [조치]
```

## 10. dashboard_review_<slug>.md (Claude Code)

```md
# dashboard_review_<slug>.md

## Review Scope

- 리뷰 대상:
- 리뷰 일자:
- 참조 파일:
- 대시보드 URL 또는 파일:

## First Screen Review

| 항목 | 상태 | 코멘트 |
|---|---|---|
| 총매출 | | |
| 매출이익 | | |
| 영업이익 | | |
| 경비 | | |
| 목표 달성률 | | |
| 전월 대비 증감률 | | |
| 데이터 경고 | | |

## Readability Review

- 비전문가가 이해 가능한가:
- 경영회의에서 바로 설명 가능한가:
- 카드마다 한 줄 요약이 있는가:
- 숫자 단위가 명확한가:
- 기준 기간이 명확한가:

## Chart Review

| 차트 | 적합성 | 수정 필요 |
|---|---|---|
| 매출 추이 | | |
| 영업이익 추이 | | |
| 팀별 매출 비교 | | |
| 목표 달성률 | | |
| 목표대비부족액 | | |

## Risk / Warning Review

- 빨간색 과다 사용 여부:
- 목표 없음 표시:
- 월 목표 없음 표시:
- FCST 미사용 표시:
- 데이터 오류 표시:
- 기술지원팀 단순 매출 비교 주의 표시:

## PDF / Print Review

- 인쇄 가능:
- PDF 잘림 없음:
- 로고/기간/출력일 표시:
- 표/차트 가독성:

## Required Changes

1. [수정 요청]
2. [수정 요청]
3. [수정 요청]

## Approved / Not Approved

- 상태:
- 이유:
```

## 11. config_review_<slug>.md (Claude Code)

```md
# config_review_<slug>.md

## Review Scope

- 리뷰 대상:
- 참조 설정 파일:
  - `config/data-mapping.yml`
  - `config/metrics.yml`
  - `config/dashboard.yml`

## Data Mapping Review

| 항목 | config 분리 여부 | 코멘트 |
|---|---|---|
| 시트명 | | |
| 컬럼명 | | |
| 팀명 | | |
| 월/분기/연간 기준 | | |
| 목표 시트 매핑 | | |

## Metrics Review

| 지표 | config 분리 여부 | 계산 기준 명확성 | 코멘트 |
|---|---|---|---|
| 매출 | | | |
| 매출이익 | | | |
| 영업이익 | | | |
| 경비 | | | |
| 목표 달성률 | | | |
| 목표대비부족액 | | | |
| 전월 대비 증감률 | | | |

## Dashboard Config Review

| 항목 | config 분리 여부 | 코멘트 |
|---|---|---|
| KPI 표시 여부 | | |
| 차트 표시 여부 | | |
| 테이블 컬럼 | | |
| 필터 | | |
| 팀별 색상 | | |
| 금액 단위 | | |
| 목표 fallback | | |

## Hardcoding Risks

코드에 직접 박혀 있어 향후 수정이 어려운 항목.

- 항목:
- 위치:
- 영향:
- 개선 제안:

## Required Changes

1. [수정 요청]
2. [수정 요청]
3. [수정 요청]
```

## 12. deploy_review_<slug>.md (Claude Code)

```md
# deploy_review_<slug>.md

## Review Scope

- 리뷰 대상:
- 운영 환경:
- 참조 파일:
  - `deploy/compose.yaml`
  - `.env.example`
  - `deploy/README.md`

## Windows 11 Environment

| 항목 | 상태 | 코멘트 |
|---|---|---|
| Windows 11 PC | | |
| Docker Desktop | | |
| WSL2 | | |
| 24시간 가동 | | |
| 사내망 접근 | | |

## Docker Compose Review

| 항목 | 상태 | 코멘트 |
|---|---|---|
| 앱 서비스 | | |
| 포트 설정 | | |
| volume | | |
| 환경 변수 | | |
| restart policy | | |
| healthcheck | | |
| 로그 위치 | | |

## Security Review

- `.env` 커밋 방지:
- 비밀번호/세션 키 노출 방지:
- 사내 IP/포트 노출 방지:
- 로그인 필수:
- 외부 접근 차단:
- 계정/권한 관리:

## Operation Review

- 실행 방법:
- 중지 방법:
- 재시작 방법:
- 업데이트 방법:
- 백업 방법:
- 복구 방법:
- 장애 시 확인 순서:

## Risks

1. [리스크]
2. [리스크]
3. [리스크]

## Required Changes

1. [수정 요청]
2. [수정 요청]
3. [수정 요청]

## Approved / Not Approved

- 상태:
- 이유:
```

## 13. ops_guide_<slug>.md (Codex)

````md
# ops_guide_<slug>.md

## Purpose

이 문서는 Windows 11 운영 PC에서 경영현황 대시보드를 Docker Compose로 실행, 중지, 재시작, 업데이트, 백업하는 방법을 설명한다.

## Target Environment

- OS: Windows 11
- Runtime: Docker Desktop
- Deploy method: Docker Compose
- Access: 사내망
- Users: 경영진, 경영지원팀, 프로젝트 담당자

## Files

| 파일 | 역할 |
|---|---|
| `deploy/compose.yaml` | Docker Compose 구성 |
| `.env` | 실제 환경 변수. 커밋 금지 |
| `.env.example` | 환경 변수 예시 |
| `data/raw/` | 원천 Excel 보관 |
| `data/processed/` | 정제 데이터 |
| `config/` | 대시보드 설정 |
| `logs/` | 운영 로그 |

## First Setup

```bash
# 예시
docker compose -f deploy/compose.yaml config
docker compose -f deploy/compose.yaml up -d
````

## Start

```bash
docker compose -f deploy/compose.yaml up -d
```

## Stop

```bash
docker compose -f deploy/compose.yaml down
```

## Restart

```bash
docker compose -f deploy/compose.yaml restart
```

## Check Status

```bash
docker compose -f deploy/compose.yaml ps
docker compose -f deploy/compose.yaml logs
```

## Update Data

1. 새 Excel 파일을 지정된 위치에 복사한다.
2. 기존 원천 파일을 덮어쓸지, 날짜별로 보관할지 확인한다.
3. 재집계 명령을 실행한다.
4. 대시보드에서 기준 기간과 마지막 업데이트 시간을 확인한다.

## Backup

백업 대상:

* 원천 Excel
* 정제 데이터
* 설정 파일
* `.env`
* DB/캐시 파일
* 운영 로그

백업 주기:

* 초기: 수동 백업
* 향후: 정기 자동 백업 검토

## Restore

1. 서비스 중지
2. 백업 파일 복원
3. 설정 파일 확인
4. 서비스 재시작
5. 대시보드 접속 확인

## Login / Account Operation

* 초기 관리자 계정 생성 방법:
* 비밀번호 변경 방법:
* 계정 추가 방법:
* 계정 비활성화 방법:

## Troubleshooting

| 증상           | 확인할 것                            | 조치 |
| ------------ | -------------------------------- | -- |
| 대시보드 접속 안 됨  | 컨테이너 상태, 포트, 방화벽                 |    |
| 로그인 실패       | 계정, 비밀번호, 세션 설정                  |    |
| 데이터가 안 보임    | Excel 위치, 파싱 로그                  |    |
| 차트 오류        | config, 데이터 누락                   |    |
| Docker 실행 실패 | Docker Desktop, WSL2, compose 설정 |    |

## Security Notes

* `.env`는 커밋하지 않는다.
* 실제 Excel 파일은 공개 저장소에 올리지 않는다.
* 사내 IP/포트 정보는 외부 문서에 노출하지 않는다.
* 외부 공개 배포는 사용자 승인 없이 하지 않는다.

## Handoff

* 현재 운영 상태:
* 마지막 배포 시점:
* 마지막 데이터 업데이트:
* 남은 이슈:

```
```
