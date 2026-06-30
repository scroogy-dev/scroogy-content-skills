# Issue #0001 재감사 리포트 이미지 흐름 기반 블로그 초안 작성 스킬 추가

> 감사 일시: 2026-06-28  
> 감사 모델: OpenAI, GPT-5.5  
> 감사 대상 브랜치: issue-0001  
> 스펙 출처: `.ai/90_issues/active/issue-0001/issue-0001-spec.md`

---

## Phase 1: 적합성 검증 (Compliance Check)

### 요구사항 대조

| # | 요구사항 | 판정 | 근거 |
|---|----------|------|------|
| 1 | `blog-photo-draft/SKILL.md` 추가 및 frontmatter `name`/`description` 작성 | PASS | `blog-photo-draft/SKILL.md` 1-4행에 frontmatter가 있고 `name: blog-photo-draft`가 명시됨. |
| 2 | 개별 이미지와 `--dir` 입력, `jpg/jpeg/png/heic/webp` 수집 문서화 | PASS | `SKILL.md` 10행, 24-30행, 49-50행에서 입력 방식과 포맷을 문서화함. |
| 3 | HEIC JPEG 변환, EXIF `DateTimeOriginal` → 파일명 자연정렬 → `mtime` 폴백 | PASS | `SKILL.md` 61-77행에 HEIC 변환과 정렬 폴백이 명시됨. |
| 4 | 외부 템플릿 우선, 생략 시 내장 기본 골격 폴백 | PASS | `SKILL.md` 18행, 28-30행, 114-120행에 외부 템플릿 SSoT와 내장 폴백이 명시됨. |
| 5 | 글 유형은 템플릿 frontmatter `type`로 지정하고 CLI `--type`는 두지 않음 | PASS | `SKILL.md` 12행, 30행, 101행, 122-131행에 frontmatter 기반 판별이 명시되고 `--type` 잔존 검색 결과 없음. |
| 6 | 내장 기본 템플릿은 `how-to` 1종, `narrative`/`expository` 내장 파일은 비포함 | PASS | `SKILL.md` 12행, 18행, 117-120행 및 `blog-photo-draft/templates/how-to.md`가 범위와 일치함. `narrative`/`expository` 템플릿 파일은 추가되지 않음. |
| 7 | 설명·논설형 근거·출처 정책 및 목차/출처 가드 | PASS | `SKILL.md` 135-159행에 근거 원칙, `## 목차`/`## 출처` 후처리 가드, 실패 시 보강/경고가 명시됨. |
| 8 | 네이버 블로그 본문 + 이미지 자리표시자 교차 배치 | PASS | `SKILL.md` 161-166행 및 예시 168-248행에서 `[사진: <원본파일명> (<순번>/<총장수>)]` 형식을 문서화함. |
| 9 | 대량 이미지 배치 처리, `--max-images` 가드 | PASS | `SKILL.md` 57-89행에 맵-리듀스 배치 캡션, `--batch-size`, `--max-images` 초과 처리와 로그 원칙이 명시됨. |
| 10 | `README.md` Skill 목록 등록 | PASS | `README.md` 13-15행에 `blog-photo-draft` 행이 등록됨. |

### 완료의 정의(DoD) 대조

| # | DoD 항목 | 판정 | 근거 |
|---|----------|------|------|
| 1 | `SKILL.md` 존재 및 `name`/`description` frontmatter | PASS | 스펙의 결정적 검증 명령 통과. |
| 2 | `--template`(선택, 생략 시 폴백), `--dir`, `--batch-size` 문서화 | PASS | 스펙 문구가 선택 인자 기준으로 보정됐고, 결정적 검증 명령 통과. |
| 3 | `how-to`, `DateTimeOriginal`, `mtime` 키워드 | PASS | 스펙의 결정적 검증 명령 통과. |
| 4 | 목차·출처 규약 문서화 | PASS | 스펙의 결정적 검증 명령 통과. |
| 5 | expository 목차·출처 결정적 후처리 가드 문서화 | PASS | 스펙의 결정적 검증 명령 통과. |
| 6 | README에 `blog-photo-draft` 등록 | PASS | 스펙의 결정적 검증 명령 통과. |
| 7 | HEIC, `--max-images`, 배치 처리 문서화 | PASS | 스펙의 결정적 검증 명령 통과. |
| 8 | `templates/how-to.md` 존재 및 `type: how-to` | PASS | 스펙의 결정적 검증 명령 통과. |
| 9 | `--template` 생략 시 내장 기본 골격 폴백 문서화 | PASS | 스펙의 결정적 검증 명령 통과. |
| 10 | 샘플 템플릿+이미지 출력이 템플릿 구조·톤을 반영 | PARTIAL | 구조·톤 반영 지침은 `SKILL.md`에 있으나, 감사 시 샘플 이미지/템플릿 실행 산출물이 제공되지 않아 출력 품질 채점은 미실행. |
| 11 | 설명·논설형 출력이 근거 없는 단정을 배제 | PARTIAL | 정책과 구조 가드는 문서화됐으나, 실제 expository 샘플 출력의 단정 배제·출처 타당성 채점은 미실행. |
| 12 | 50장+ 입력 시 컨텍스트 한계 없이 배치 캡션 추출 | PARTIAL | 배치 설계는 문서화됐으나, 50장+ 샘플 실행 증거는 없음. |
| 13 | 네이버 에디터 붙여넣기 시 자리표시자 흐름 자연스러움 | N/A | 실제 네이버 에디터 UX 확인은 사람 리뷰 항목으로, 현재 감사 환경에서 판정하지 않음. |

결정적 DoD 9개는 모두 PASS했다.

### 범위 검증

- **스펙 비포함(Out) 침범 여부**: PASS. 티스토리/velog 어댑터, 분야별 프리셋, `narrative`/`expository` 내장 템플릿 파일, 네이버 자동 발행/업로드는 추가되지 않았다.
- **스펙에 없는 추가 구현 여부**: PASS. 변경 범위는 이슈 문서, README 등록, `blog-photo-draft` 스킬 문서와 `how-to` 템플릿으로 한정된다.

### 도메인/계약 정합성

- `.ai/30_contract/index.md`: 관련 계약 문서 없음.
- `.ai/40_domain/index.md`: `glossary.md`는 비어 있어 충돌할 도메인 규칙 없음.
- `.ai/50_adr/index.md`: 관련 ADR 없음.
- `.ai/60_codebase/index.md`: 코드 색인은 비어 있으나, 이번 이슈는 문서형 skill 추가라 실제 소스 파일 직접 확인으로 대체했다. 색인과 코드의 불일치는 발견하지 못했다.

---

## Phase 2: 비판적 검증 (Critical Review)

### 재검증 결과

| 이전 지적 | 재검증 판정 | 근거 |
|-----------|-------------|------|
| F-1: `--template` 필수/선택 문구 혼재 | RESOLVED | 스펙 DoD #2가 `--template`(선택, 생략 시 내장 기본 폴백)으로 보정됨. `SKILL.md`도 선택 인자로 일관됨. |
| F-2: `expository` 내장 기본 현재 존재처럼 보이는 문구 | MOSTLY RESOLVED | `SKILL.md`의 `require_sources` 설명은 "내장 expository 템플릿은 추후 추가"로 보정됨. 계획 문서 Task 3에도 설계 변경 4 주석이 추가됨. |
| F-3: QD/ND 품질 검증 증거 부족 | ACCEPTED RISK | summary의 Task N에 후속 QA 이관 리스크로 명시됨. |

### 발견 사항

| # | 위험도 | 분류 | 설명 | 관련 파일 |
|---|--------|------|------|-----------|
| F-1 | LOW | 스펙 모호성 | `SKILL.md`와 스펙의 최종 상태는 정합하지만, summary의 과거 Task 기록에는 `--template`(필수), 내장 템플릿 2종, expository 내장 기본 true, Task 5에 2종 반영 예정 같은 표현이 남아 있다. 뒤쪽 Task 5와 Task N에서 보정 맥락을 제공하므로 기능 실패는 아니지만, summary만 훑는 독자는 잠시 혼동할 수 있다. | `.ai/90_issues/active/issue-0001/issue-0001-summary.md:26`, `:31`, `:56`, `:60`, `:62` |
| F-2 | INFO | 누락된 검증 | QD/ND DoD는 샘플 이미지·템플릿·네이버 에디터 환경이 있어야 완결된다. summary에서 후속 QA 이관을 명시했으므로 closure 판단에는 치명적이지 않다. | `.ai/90_issues/active/issue-0001/issue-0001-spec.md:51-54`, `.ai/90_issues/active/issue-0001/issue-0001-summary.md:102` |

### 상세 분석

#### F-1: summary의 과거 Task 기록이 최종 상태와 일부 다름

- **위험도**: LOW
- **분류**: 스펙 모호성
- **설명**: 최종 스펙과 `SKILL.md`는 `--template` 선택, 내장 `how-to` 1종, `expository` 내장 템플릿 추후 추가로 정합하다. 다만 summary의 Task 1/3 기록에는 당시 결정 또는 당시 남은 작업이 그대로 남아 있어, 독자가 시간순 기록이라는 점을 놓치면 현재 상태로 오해할 수 있다.
- **영향**: 구현 요구사항 충족에는 영향이 없으나, 감사·인수인계 문서의 읽기 비용이 올라간다.
- **권장 조치**: summary의 과거 Task 기록을 유지한다면 각 stale 문구에 "당시 기록, 이후 Task 5에서 대체" 주석을 붙인다. 더 단순하게는 Task 5 또는 Task N에 "summary 상 과거 기록은 chronology이며 최종 계약은 spec/SKILL.md 기준"이라고 한 줄 추가한다.

#### F-2: QD/ND 품질 검증은 후속 QA로 이관됨

- **위험도**: INFO
- **분류**: 누락된 검증
- **설명**: 결정적 검증은 모두 통과했지만, 생성형 출력 품질은 실제 샘플 이미지/템플릿으로 실행해 봐야 확인된다. summary에서 이 잔여 리스크를 명시하고 후속 QA로 이관했으므로 현재 이슈 종료 판단을 막을 수준은 아니다.
- **영향**: 실제 사용 품질에는 잔여 리스크가 있다.
- **권장 조치**: 후속 QA에서 샘플 이미지 세트 1개와 expository 외부 템플릿 1개로 별도 채점 기록을 남긴다.

---

## 종합 의견

Issue #1의 핵심 구현 산출물은 스펙을 충족한다. `blog-photo-draft/SKILL.md`, `templates/how-to.md`, `README.md` 등록은 모두 존재하고, 스펙의 결정적 DoD 9개도 재검증에서 전부 통과했다. 이전 감사의 핵심 지적이었던 `--template` 필수/선택 충돌과 `expository` 내장 템플릿 현재형 표현은 실질적으로 해소됐다.

남은 것은 기능 결함이 아니라 문서 해석 리스크다. summary에 남아 있는 과거 Task 기록을 최종 상태로 오해할 수 있는 지점이 일부 있고, QD/ND 품질 검증은 후속 QA 증거가 필요하다. 현재 기준으로는 HIGH/MEDIUM 리스크 없이 closure 가능하다고 판단한다.
