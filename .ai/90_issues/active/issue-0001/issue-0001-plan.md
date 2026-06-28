# Issue #1 실행계획 이미지 흐름 기반 블로그 초안 작성 스킬 추가

> 스펙: [issue-0001-spec.md](./issue-0001-spec.md)

---

## Tasks

> AI가 순서대로 실행할 작업 단위를 정의합니다.
> 각 작업은 독립적으로 검증 가능해야 하며, **완료 기준은 자연어 대신 실행 명령 + 임계값**으로 적습니다.
> 명령으로 환원이 어려운 항목만 판정 주체(다른 AI / 사람)를 적고 강등 사유를 남깁니다.
> 마지막 고정 Task(교차모델 issue-audit)는 삭제하지 말고 그대로 둡니다.

### Task 1: SKILL.md 골격 + 인자 정의

- [x] 완료
- **목표**: `blog-photo-draft/SKILL.md`의 frontmatter·개요·사용법·인자 표를 저장소 컨벤션에 맞게 작성한다.
- **작업 내용**:
  1. `blog-photo-draft/` 폴더와 `SKILL.md` 생성, frontmatter `name`/`description` 작성
  2. 개요·사용법(`/blog-photo-draft ...`)·인자 표(`<images...>`/`--dir`/`--template`/`--order`/`--max-images`/`--batch-size`/`--out`) 작성. 글 유형은 CLI 플래그가 아니라 템플릿 frontmatter `type`로 정함을 명시
- **완료 기준**: `test -f blog-photo-draft/SKILL.md && grep -q '^name: blog-photo-draft' blog-photo-draft/SKILL.md && grep -q '^description:' blog-photo-draft/SKILL.md` 통과, `grep -q -- '--template' blog-photo-draft/SKILL.md && grep -q -- '--dir' blog-photo-draft/SKILL.md && grep -q -- '--batch-size' blog-photo-draft/SKILL.md && grep -q -- '--max-images' blog-photo-draft/SKILL.md`

---

### Task 2: 이미지 흐름 분석 + 대량 이미지 배치 처리 명세

- [x] 완료
- **목표**: 비전 + 메타데이터 결합으로 이미지 순서·흐름을 잡고, 다량 이미지에서도 컨텍스트가 폭발하지 않도록 배치(캡션 추출 ↔ 글쓰기 분리) 절차를 SKILL.md에 기술한다.
- **작업 내용**:
  1. 포맷 정규화: `HEIC`는 비전 미지원 → 캡션 단계 전 JPEG 변환(`sips` 등, best-effort, 실패 시 건너뛰고 로그)
  2. 정렬: EXIF `DateTimeOriginal` → 파일명 자연정렬 → mtime 폴백(best-effort). 스크린샷은 EXIF 없을 수 있어 파일명/mtime 폴백
  3. 배치 캡션 추출(이미지는 이 단계에서만 소비): N장씩 배치로 구조화 캡션 추출, 서브에이전트 병렬 가능. 글쓰기는 캡션 텍스트만 사용
  4. 상한·가드: `--max-images` 초과 시 유사·연속 컷 그룹화/대표 샘플링, 묶음/생략을 로그로 명시
- **완료 기준**: `grep -q 'DateTimeOriginal' blog-photo-draft/SKILL.md && grep -q 'mtime' blog-photo-draft/SKILL.md && grep -q '자연정렬\|natural' blog-photo-draft/SKILL.md && grep -qi 'heic' blog-photo-draft/SKILL.md && grep -q '배치\|batch' blog-photo-draft/SKILL.md && grep -q -- '--max-images' blog-photo-draft/SKILL.md`

---

### Task 3: 템플릿 규약 + 글 유형 분기 명세

- [x] 완료
- **목표**: 사용자 제공 템플릿 규약과 `narrative`/`expository` 유형 분기를 SKILL.md에 기술한다.
- **작업 내용**:
  1. 템플릿 규약: 마크다운 + 선택적 frontmatter(`type`/`require_sources`/`sections`) + 선택적 플레이스홀더(`{{...}}`)
  2. 템플릿 해석 순서: `--template <path>`(외부) → 생략 시 추론 유형의 **내장 기본 골격**(`blog-photo-draft/templates/<type>.md`) 폴백. `--template narrative|expository`로 내장 직접 지정 가능
  3. 유형 판별: (외부 템플릿이 있으면) frontmatter `type` → 구조·이미지 성격으로 추론 (별도 CLI 플래그 없음). 외부 템플릿 생략 시 이미지 흐름으로 추론
- **완료 기준**: `grep -q 'narrative' blog-photo-draft/SKILL.md && grep -q 'expository' blog-photo-draft/SKILL.md && grep -q 'require_sources' blog-photo-draft/SKILL.md && grep -q '내장 기본' blog-photo-draft/SKILL.md`

---

### Task 4: 근거·출처 정책 + 네이버 출력 포맷 명세

- [x] 완료
- **목표**: 설명·논설형 근거·출처 정책과 네이버 블로그 출력 포맷(자리표시자 교차)을 SKILL.md에 기술한다. **(B안)** 출처 섹션 "유무"는 결정적 후처리 가드로 강제하고, spec DoD의 묶인 QD 항목을 `[D]`(섹션 포함)/`[QD]`(단정 배제)로 분리한다.
- **작업 내용**:
  1. 근거·출처 정책: 사실 주장은 출처로 뒷받침, 미확보 주장은 추측 표시/제외, 하단 `출처` 섹션 + 상단 `목차`
  2. **결정적 출처 가드(B)**: expository 생성물에 대해 `grep -q '^## 목차' && grep -q '^## 출처'`로 섹션 존재를 후처리 검사, 실패 시 보강 재생성/경고(조용한 누락 금지). 가드는 "존재"만 보장하고 출처 타당성은 범위 밖임을 명시
  3. 출력: 본문 + 이미지 자리표시자(`[사진: <파일명> (<순번>/<총장수>)]`) 교차 배치. 서사형/설명·논설형 출력 예시 2종
  4. spec DoD 갱신: 결정적 가드 문서화 `[D]` 항목 추가 + 기존 묶인 `[QD]`에서 "섹션 포함"을 떼어 "단정 배제"만 채점으로 축소
- **완료 기준**: `grep -q '목차' blog-photo-draft/SKILL.md && grep -q '출처' blog-photo-draft/SKILL.md && grep -q '근거' blog-photo-draft/SKILL.md` 통과, 후처리 가드 명시 `grep -q "grep -q '\^## 출처'" blog-photo-draft/SKILL.md && grep -q '가드' blog-photo-draft/SKILL.md`, 출력 예시 코드블록 2개 이상 존재

---

### Task 5: 내장 기본 템플릿 `how-to` 1종 + SKILL.md 유형 체계 정리 + README 등록

- [x] 완료
- **목표**: 따라하기형(`how-to`) 내장 기본 템플릿 1종을 추가하고, SKILL.md의 유형 체계를 '확장 가능 + `how-to` 우선 내장'으로 정리하며, README Skill 목록에 항목을 등록한다. 다른 유형(`narrative`/`expository`) 내장 템플릿은 추후 추가한다.
- **작업 내용**:
  1. `blog-photo-draft/templates/how-to.md` 추가. frontmatter `type: how-to`, `require_sources: false`. 구조는 따라하기 계약 예시 — 선택적 `## 목차` + `## 준비물` + 순서가 있는 `## Step N — …`(각 단계에 이미지 자리표시자 1:1 매핑) + `## 마무리`. `--template` 생략 시 폴백 대상 겸 사용자 템플릿 작성 예시
  2. SKILL.md 보강(Task 1~4의 "2종" 프레이밍 정리): ① 유형 목록·판별에 `how-to`(따라하기형) 추가 — 시간·절차 순서 이미지(스크린샷 단계 흐름)면 `how-to`로 추론. ② 내장 기본 템플릿을 'how-to 1종(현행), narrative/expository 등은 추후 추가'로 명시하고 폴백 기본을 `how-to`로 정리. ③ `narrative`/`expository`는 인식 유형·정책으로 문서 유지(출처 가드 등)
  3. `README.md` Skill 목록 표 주석을 풀고 `blog-photo-draft` 행 등록
- **완료 기준**: `test -f blog-photo-draft/templates/how-to.md && grep -q '^type: how-to' blog-photo-draft/templates/how-to.md && grep -q 'how-to' blog-photo-draft/SKILL.md && grep -q 'blog-photo-draft' README.md`

---

### Task N (고정): 교차모델 issue-audit 검증 — 사용자 수동 수행

<!--
이 블록은 모든 이슈 계획의 마지막 Task로 고정한다. 삭제하지 말 것.
audit은 L2 [QD] 보완 검증 — L1 [D] 결정적 게이트의 대체가 아니라 보완이다.
이 Task는 사용자가 직접 수행하며, 구현 AI는 자동으로 닫지 않는다.
-->

- [ ] 완료
- **목표**: 스펙 위반·누락·소스코드와의 모순을 구현 모델과 다른 시각으로 잡는다.
- **실행 주체**: **사용자가 직접** 수행한다. 구현 AI는 이 Task를 **자동으로 닫지 않으며**, `issue-audit`를 자동 실행하지도 않는다.
- **작업 내용**:
  1. spec `완료의 정의`의 `[D]` 항목 검증 명령을 전부 재실행해 통과를 확인한다.
  2. 계획·구현을 수행한 모델과 **다른 벤더 모델**(Non-Anthropic 포함, 최소 동급 이상 역량)로 사용자가 직접 `issue-audit`를 실행한다. 방향은 칭찬이 아니라 허점 탐색("스펙 위반·누락·소스코드와의 모순을 찾아라").
  3. 사용자가 audit 결과(지적 사항·감사 모델)를 summary에 반영·기록한다. 발견사항 보정은 issue-work `--response`로 검토한다 — **피드백 먼저, 항목별 승인 후에만** 앞 Task를 보정하며, 리포트를 받자마자 자동 보정하지 않는다.
- **완료 기준**: `[D]` 검증 명령 전부 통과 + 구현 모델 ≠ audit 모델 기록이 summary 모델 칸("벤더, 모델명" 형식)에 남는다.
