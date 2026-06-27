# Issue #1 스펙 이미지 흐름 기반 블로그 초안 작성 스킬 추가

> GitHub 이슈: https://github.com/scroogy-dev/scroogy-content-skills/issues/1

## 목표 (Goal)

여러 이미지(또는 디렉토리)를 입력받아 **이미지 흐름(시간·장면)**을 분석하고, **사용자 제공 템플릿**의 구조·톤에 맞춰 글 유형별(서사형 / 설명·논설형)로 **네이버 블로그 초안**을 생성하는 `blog-photo-draft` skill을 추가한다. 고해상도·다량 이미지(맥북 캡쳐, 아이폰 Pro 사진)에도 컨텍스트 한계를 넘지 않도록 **캡션 추출 ↔ 글쓰기를 분리(배치)**한다.

---

## 범위 (Scope)

**포함 (In)**

- `blog-photo-draft/SKILL.md` 추가 (저장소 컨벤션: frontmatter `name`/`description` + 개요·사용법·인자)
- 입력: 개별 이미지 나열 + `--dir` 디렉토리 일괄 수집 (`jpg/jpeg/png/heic/webp`). Claude 비전 미지원 포맷인 `HEIC`(아이폰 Pro)는 캡션 단계 전 JPEG로 변환(`sips` 등, best-effort)
- 이미지 흐름 파악: 비전(장면·객체·이미지 내 텍스트) + 메타데이터(EXIF `DateTimeOriginal` → 파일명 자연정렬 → mtime 폴백, best-effort)
- 템플릿 기반 생성: 외부 사용자 템플릿(`--template`, 마크다운 + 선택적 frontmatter `type` + 선택적 플레이스홀더) 우선, **생략 시 유형별 내장 기본 골격(`narrative`/`expository`)으로 폴백** — 내장 골격은 폴백 대상 겸 사용자 템플릿 작성 예시
- 글 유형 2종: `narrative`(서사형) / `expository`(설명·논설형). **템플릿 frontmatter `type`로 지정**(별도 CLI 플래그 없음), 미지정 시 템플릿 구조·이미지 성격으로 자동 추론
- 설명·논설형 근거·출처 정책: 상단 **목차** + 하단 **출처** 섹션, 근거 없는 단정 배제(환각 방지)
- 네이버 블로그 출력: 본문 텍스트 + 이미지 자리표시자 교차 배치, 파일명·순서 표기
- 대량 이미지 배치 처리: 캡션 추출 ↔ 글쓰기 분리(맵-리듀스)로 컨텍스트·비용 한계 회피, `--max-images` 가드(묶음/생략 로그 명시)
- `README.md` Skill 목록 표에 항목 등록

**비포함 (Out)**

- 티스토리·velog 등 다른 플랫폼 어댑터
- **분야별** 템플릿(여행/맛집/리뷰) 프리셋 — 내장 기본은 유형 골격 2종(`narrative`/`expository`)까지만, 분야별 프리셋은 비포함
- 네이버 자동 발행/업로드 연동

---

## 완료의 정의 (Definition of Done)

> **검증 레벨** — 낮을수록 좋다(자동 검증에 가까움). 기본은 L1, 한 레벨 내릴 때마다 강등 사유를 함께 적는다.
>
> - `[D]`  L1 결정적   — 명령이 합/불을 판정, 사람 판단 없음
> - `[QD]` L2 준결정적 — 다른 AI·기준 체크리스트가 채점
> - `[ND]` L3 비결정적 — 사람이 직접 읽고 판단

- [ ] [D] `blog-photo-draft/SKILL.md`가 존재하고 frontmatter에 `name: blog-photo-draft`와 `description`이 있다 (검증: `test -f blog-photo-draft/SKILL.md && grep -q '^name: blog-photo-draft' blog-photo-draft/SKILL.md && grep -q '^description:' blog-photo-draft/SKILL.md`)
- [ ] [D] SKILL.md에 핵심 인자 `--template`(필수)·`--dir`·`--batch-size`가 문서화돼 있다 (검증: `grep -q -- '--template' blog-photo-draft/SKILL.md && grep -q -- '--dir' blog-photo-draft/SKILL.md && grep -q -- '--batch-size' blog-photo-draft/SKILL.md`)
- [ ] [D] SKILL.md에 글 유형 2종과 흐름 정렬 폴백 키워드가 있다 (검증: `grep -q 'narrative' && grep -q 'expository' && grep -q 'DateTimeOriginal' && grep -q 'mtime'`)
- [ ] [D] SKILL.md에 설명·논설형 근거·출처 규약(목차·출처 섹션)이 문서화돼 있다 (검증: `grep -q '목차' && grep -q '출처' blog-photo-draft/SKILL.md`)
- [ ] [D] `README.md` Skill 목록 표에 `blog-photo-draft` 행이 등록됐다 (검증: `grep -q 'blog-photo-draft' README.md`)
- [ ] [D] SKILL.md에 대량 이미지 배치 처리·`--max-images`·HEIC 변환 절차가 문서화돼 있다 (검증: `grep -qi 'heic' blog-photo-draft/SKILL.md && grep -q -- '--max-images' blog-photo-draft/SKILL.md && grep -q '배치\|batch' blog-photo-draft/SKILL.md`)
- [ ] [D] 유형별 내장 기본 템플릿 2종이 존재하고 각 frontmatter에 `type`가 있다 (검증: `test -f blog-photo-draft/templates/narrative.md && test -f blog-photo-draft/templates/expository.md && grep -q '^type: narrative' blog-photo-draft/templates/narrative.md && grep -q '^type: expository' blog-photo-draft/templates/expository.md`)
- [ ] [D] SKILL.md에 `--template` 생략 시 내장 기본 골격 폴백 규칙이 문서화돼 있다 (검증: `grep -q '내장 기본' blog-photo-draft/SKILL.md && grep -qi 'fallback\|폴백' blog-photo-draft/SKILL.md`)
- [ ] [QD] 샘플 템플릿+이미지로 실행했을 때 출력이 템플릿 구조·톤을 반영한다 (검증: 다른 AI가 채점, 별도 세션) ← 강등 사유: 톤·구조 반영도는 생성형 출력 품질이라 명령으로 합/불 판정 불가
- [ ] [QD] 설명·논설형 출력이 근거 없는 단정을 배제하고 목차+출처 섹션을 포함한다 (검증: 샘플 출력을 다른 AI가 채점) ← 강등 사유: "근거 없는 단정" 여부는 의미 판단이라 결정적으로 거를 수 없음
- [ ] [QD] 다량 이미지(예: 50장+) 입력 시 컨텍스트 한계 없이 배치 캡션 추출로 초안이 생성된다 (검증: 샘플 실행 결과를 다른 AI가 채점) ← 강등 사유: "컨텍스트 한계 회피·흐름 보존" 품질은 명령으로 합/불 판정 불가
- [ ] [ND] 네이버 에디터에 붙여넣었을 때 이미지 자리표시자 흐름이 자연스럽다 (검증: 사람 리뷰) ← 강등 사유: 실제 에디터 UX·이미지 배치 자연스러움은 사람 판단 영역

---

## 연관 문서

> 현재 `30_contract/`·`40_domain/`·`50_adr/` index는 모두 비어 있어(스캐폴딩 단계) 인용할 도메인/계약/ADR 문서가 없다. 아래는 작업 시 실제로 참고할 문서다.

| 문서 | 역할 |
|------|------|
| [GitHub Issue #1](https://github.com/scroogy-dev/scroogy-content-skills/issues/1) | 원본 이슈 — 결정 사항(스킬 이름·템플릿 규약·EXIF·분량)·근거·출처 정책의 SSoT |
| `.ai/AI-CONTEXT.md` | 프로젝트 도메인·skill 폴더 컨벤션(`<skill-name>/SKILL.md`) |
| `README.md` | Skill 목록 표 — 완료 시 항목 등록 대상 |
| `.ai/10_rules/coding-convention.md`, `file-change-policy.md` | 코딩·파일 변경 규칙 (현재 비어 있으나 채워지면 준수) |
| 자매 repo `scroogy-agent-skills`의 SKILL.md (예: `readme-sync`) | frontmatter·섹션 구조 포맷 참고 |
