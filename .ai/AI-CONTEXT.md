# AI-CONTEXT.md

> last updated: 2026-06-27
> SSoT: 소스 코드. 이 파일은 안내도일 뿐 진실의 원천이 아니다.

이 파일은 AI 어시스턴트를 위한 프로젝트 가이드입니다.
사람을 위한 안내는 프로젝트 루트의 [README.md](../README.md)를 참고하세요.

---

## 프로젝트 도메인

| 항목 | 값 |
|------|----|
| domain | 블로그·숏폼 등 콘텐츠 제작을 돕는 Agent Skills 모음 |
| keywords | content, blog, short-form, 콘텐츠 제작, agent-skills, SKILL.md, Claude Code |

---

## 프로젝트 목적

블로그 글, 숏폼 영상 등 다양한 콘텐츠를 제작할 때 반복적으로 필요한 작업을 자동화·표준화하는 **Agent Skills**를 모아두는 저장소입니다.

각 skill은 특정 벤더에 종속되지 않는 오픈 포맷([Agent Skills](https://agentskills.io/))으로 작성되어 Claude Code, Codex, GitHub Copilot 등 다양한 AI 도구에서 호환됩니다. 개발 워크플로우용 skill 모음인 자매 프로젝트 [scroogy-agent-skills](https://github.com/scroogy-dev/scroogy-agent-skills)의 **콘텐츠 제작 버전**에 해당합니다.

> 현재는 초기 스캐폴딩 단계로 등록된 skill이 없습니다. 콘텐츠 기획·작성·편집·배포 단계별 skill을 추가해 나갈 예정입니다.

---

## 프로젝트 규칙

<!-- 간단한 인라인 규칙은 여기에 적고, 상세 규칙은 `.ai/10_rules/`에 파일로 두고 아래 테이블에 등록하세요. -->

**아래 규칙은 모든 작업(스킬 실행 포함)에 선행 적용됩니다.**

| 파일 | 설명 | 사용 시점 |
|------|------|----------|
| `.ai/10_rules/architecture.md`       | 프로젝트 아키텍처 방향     | 코드 작성·리뷰·아키텍처 변경 시 |
| `.ai/10_rules/coding-convention.md`  | 코딩 컨벤션                | 코드 작성 시      |
| `.ai/10_rules/context-loading.md`    | 작업 전 컨텍스트 확인 절차 | 코드·문서 작업 전 |
| `.ai/10_rules/file-change-policy.md` | 파일 변경 규칙             | 파일 추가·삭제 시 |

---

## 기술 스택

- **포맷**: [Agent Skills](https://agentskills.io/) 오픈 포맷 (skill별 `SKILL.md` 정의)
- **주 언어**: Shell / Markdown
- **호환 도구**: Claude Code, Codex, GitHub Copilot 등 (벤더 독립)
- **설치**: 각 skill을 `~/.claude/skills/`에 선택 설치

---

## 디렉토리 구조

스킬마다 최상위 폴더 하나를 차지하는 구조입니다 (자매 repo와 동일 컨벤션). 현재는 등록된 skill이 없어 안내 파일만 있습니다.

```
.
├── .ai/        # AI 협업 가이드 (상세는 ".ai 디렉토리 구조" 섹션)
└── README.md   # 프로젝트 개요 및 Skill 목록
```

> skill을 추가하면 `<skill-name>/SKILL.md` 형태의 폴더가 최상위에 늘어납니다 (예: `blog-draft/`, `short-form-script/`).

---

## .ai 디렉토리 구조

디렉토리명 앞의 숫자는 AI가 문서를 읽는 우선순위를 나타냅니다.
숫자가 낮을수록 먼저 읽어야 하며, 상위 우선순위 문서가 하위 우선순위 문서보다 우선합니다.

```
.ai/
├── 10_rules/        # [1순위] AI 행위 규칙
├── 20_templates/    # 필요 시 참조하는 템플릿
├── 30_contract/     # [2순위] 소프트웨어 계약 (index.md로 선택적 참조)
├── 40_domain/       # [3순위] 비즈니스 도메인 (index.md로 선택적 참조)
│   ├── policies/    # common/ (공통 정책, 동기화 대상) + local/ (이 repo 고유 정책)
│   └── specs/       # 기능 명세
├── 50_adr/          # [4순위] 의사결정 기록 (index.md로 선택적 참조)
├── 60_codebase/     # [5순위] 소스코드 엔트리포인트·호출 흐름 색인 (index.md로 선택적 참조)
├── 90_issues/       # 이슈 단위 작업 (active/ + archive/)
└── 99_workspace/    # AI 임시 작업공간
```

## 에이전트 운영 지침

> 전제: 상위 디렉토리에 `.ai/AI-CONTEXT.md`가 있으면 이 repo는 멀티 워크스페이스의 일부, 없으면 단독 repo다. 별도 메타 명시 없이 자동 판정한다 (CoC).

### 진입 절차 (질의 → 답변)

1. **진입 경로 식별**
   - **상위 워크스페이스에서 진입한 경우** (상위 안내도를 먼저 읽고 이 repo로 들어옴): `## 프로젝트 도메인`의 `domain` / `keywords`가 상위 안내도 `Repos` 행과 일치하는지 확인한다.
   - **이 repo를 직접 열고 진입한 경우** (IDE가 이 repo 폴더만 연 상태):
     - `../.ai/AI-CONTEXT.md`가 존재하면 상위 워크스페이스의 일부 — 인접 repo가 필요한 질의면 그 경로로 거슬러 올라가 다른 repo를 참조한다.
     - `../.ai/AI-CONTEXT.md`가 없으면 단독 repo — 이 안내도만으로 답변을 시작한다.
2. `.ai/10_rules/context-loading.md`를 먼저 적재하고, 질의 유형에 따라 `30_contract/index.md`(계약) → `40_domain/index.md`(도메인 본문/정책) → `50_adr/index.md`(결정 이력) → `60_codebase/index.md`(코드 진입점) 중 필요한 항목만 **선택 적재**한다.
3. 답변 직전 정보 충돌 시 우선순위: **소스 코드 > 이 repo 안내도 > 상위 워크스페이스 안내도**.

### 작성 규칙 (이 파일을 손볼 때)

- 도메인 본문은 `40_domain/`, 계약은 `30_contract/`, 결정 이력은 `50_adr/`, 코드 상세는 `60_codebase/`에 둔다. 이 파일에는 포인터만.
- `domain`/`keywords`를 바꾸면 상위 워크스페이스 안내도의 `Repos` 행도 같이 갱신한다 (멀티 워크스페이스의 일부일 때에 한함).

## Git 정책

아래 skill이 설치되어 있으면 해당 skill의 지침을 따릅니다.

| Skill | 설명 | 사용 시점 |
|-------|------|----------|
| `/git-commit` | 커밋 메시지 규칙 | 커밋 생성 시 |
| `/git-pr` | PR 생성 규칙 | PR 생성 시 |
| `/git-review-context` | 리뷰 전 변경사항 사전 분석 | 사용자 요청 시 |
| `/git-review` | 리뷰 수행 절차 | 리뷰 수행 시 |

## 이슈 작업 워크플로우

아래 skill이 설치되어 있으면 해당 skill의 지침을 따릅니다.

| Skill | 설명 | 사용 시점 |
|-------|------|----------|
| `/issue-work` | 이슈 단위 스펙/계획/요약 관리 | 이슈 작업 시 |
| `/issue-audit` | 이슈 스펙 대비 구현 독립 감사 | 구현 검증 시 |