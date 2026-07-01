# Issue #3 스펙 install-skills 스킬 추가

> GitHub 이슈: https://github.com/scroogy-dev/scroogy-content-skills/issues/3

## 목표 (Goal)

이 저장소의 skill을 **실행 시점에 동적으로 스캔**해 선택 설치하는 `install-skills` 스킬을 추가한다. 여러 도구 경로에 **클린 설치**하고, `tests/`·`*.test.*` 등 개발 전용 경로를 **배포에서 제외**하며, 설치 결과를 **결정적 스크립트(exit code) + AI 크로스체크**로 검증한다. 자매 repo [scroogy-agent-skills / install-skills](https://github.com/scroogy-dev/scroogy-agent-skills/tree/main/install-skills)를 SSoT 패턴으로 삼아 충실히 미러링하되, 이 repo 고유 사정(ADR 미보유 등)만 조정한다.

---

## 범위 (Scope)

**포함 (In)**

- `install-skills/SKILL.md` 추가 (frontmatter `name: install-skills`/`description` + 개요·옵션·동적 스캔·설치 절차·검증)
- **동적 스캔**: repo 루트에서 `*/SKILL.md`를 보유한 1차 하위 디렉토리를 수집하고 `install-skills` 자신은 제외, 각 후보 설명은 해당 `SKILL.md` frontmatter `description`에서 취득
- **6종 설치 대상 옵션 + `--clear`**: `--claude`(기본)·`--agents`·`--antigravity`·`--codex`·`--junie`·`--all`, `--clear`는 대상 디렉토리 내부만 비우는 클린 설치
- **클린 설치 + 배포 제외**: 기존 삭제 후 복사, `rsync -a --exclude 'tests/' --exclude '*.test.*'`(미가용 시 `cp -r` fallback). 이 `--exclude` 목록이 배포 제외의 **단일 출처(SSoT)**
- `install-skills/scripts/verify-install.sh`: 결정적 검증(exit code PASS=0/FAIL=1), skill 디렉토리·`SKILL.md` 존재·배포 제외 경로 미포함 검사, `--antigravity-legacy` 레거시 점검, 실행권한 부여
- `install-skills/tests/run-tests.sh`: 외부 의존 없는 경량 셸 러너
- Antigravity 레거시 점검(`--antigravity-legacy`) — 구 경로 리터럴은 SKILL.md가 아니라 `verify-install.sh`가 보유
- `README.md` 「설치 방법」 섹션을 install-skills 안내로 갱신
- **ADR 처리**: 배포 제외 규약의 근거 ADR은 **원본(자매) repo에만 두고**, 이 repo에서는 [자매 repo ADR 0001](https://github.com/scroogy-dev/scroogy-agent-skills/blob/main/.ai/50_adr/active/0001-skill-deterministic-helper-test-convention.md)을 **교차 참조 링크**로만 가리킨다. 이식하는 SKILL.md의 기존 상대경로(`../.ai/50_adr/...`)는 이 repo에서 깨지므로 자매 repo 링크로 교체한다

**비포함 (Out)**

- 자매 repo에 없는 신규 대상 경로·기능 추가
- ADR 0001을 이 repo `.ai/50_adr/`로 harvest — 원본 repo 참조로 갈음(사용자 확정)
- `blog-photo-draft`에 `tests/`·`scripts/` 신설 (별도 이슈)
- 티스토리·velog 등 다른 플랫폼/도구용 설치 로직 확장
- **SSoT(자매 repo) 상속 결함의 이 repo 내 수정** — 교차모델 audit(OpenAI GPT-5)이 찾은 F-1(중첩 `tests/` 미검출)·F-2(`--clear` dotfile 미삭제)는 자매 repo 원본 코드와 바이트 동일한 상속 결함이므로, "충실 미러링" 스코프상 이 repo에서 수정하지 않고 upstream(자매 repo)으로 이관한다 (상세: summary Task N)

---

## 완료의 정의 (Definition of Done)

> **검증 레벨** — 낮을수록 좋다(자동 검증에 가까움). 기본은 L1, 한 레벨 내릴 때마다 강등 사유를 함께 적는다.
>
> - `[D]`  L1 결정적   — 명령이 합/불을 판정, 사람 판단 없음
> - `[QD]` L2 준결정적 — 다른 AI·기준 체크리스트가 채점
> - `[ND]` L3 비결정적 — 사람이 직접 읽고 판단

- [ ] [D] `install-skills/SKILL.md`가 존재하고 frontmatter에 `name: install-skills`와 `description`이 있다 (검증: `test -f install-skills/SKILL.md && grep -q '^name: install-skills' install-skills/SKILL.md && grep -q '^description:' install-skills/SKILL.md`)
- [ ] [D] SKILL.md에 동적 스캔(`SKILL.md` 보유 1차 하위 디렉토리 수집, `install-skills` 자기 제외)이 문서화돼 있다 (검증: `grep -q 'for f in \*/SKILL.md' install-skills/SKILL.md && grep -q 'install-skills' install-skills/SKILL.md`)
- [ ] [D] 6종 설치 대상 옵션과 `--clear`가 모두 문서화돼 있다 (검증: `for o in --claude --agents --antigravity --codex --junie --all --clear; do grep -q -- "$o" install-skills/SKILL.md || exit 1; done`)
- [ ] [D] 배포 제외 패턴(`tests/`, `*.test.*`)이 SKILL.md 설치 절차에 SSoT로 명시돼 있다 (검증: `grep -q "exclude 'tests/'" install-skills/SKILL.md && grep -q "'\*.test.\*'" install-skills/SKILL.md`)
- [ ] [D] SKILL.md의 ADR 참조가 이 repo 로컬 상대경로가 아니라 자매 repo URL을 가리킨다 (검증: `grep -q 'github.com/scroogy-dev/scroogy-agent-skills' install-skills/SKILL.md && ! grep -q '\.\./\.ai/50_adr' install-skills/SKILL.md`)
- [ ] [D] `install-skills/scripts/verify-install.sh`가 존재하고 실행권한이 있다 (검증: `test -x install-skills/scripts/verify-install.sh`)
- [ ] [D] verify-install.sh가 정상 설치엔 PASS(exit 0), skill 누락·**루트** 배포 제외 위반(루트 `tests/`·`*.test.*`)엔 FAIL(exit 1)을 낸다 (검증: 임시 대상 디렉토리에 설치/미설치·루트 `tests/` 주입 시나리오로 exit code 확인) — ⚠️ 중첩 `tests/` 미검출(F-1)은 SSoT(자매 repo) 상속 결함으로 이 이슈 범위 밖, upstream 이관(상세: summary Task N)
- [ ] [D] `install-skills/tests/run-tests.sh`가 존재하고 통과한다 (검증: `bash install-skills/tests/run-tests.sh`)
- [ ] [D] `README.md` 「설치 방법」에 install-skills 안내가 반영됐다 (검증: `grep -q 'install-skills' README.md`)
- [ ] [QD] 실제 skill(`blog-photo-draft`)을 임시 대상 경로에 설치 → verify PASS 흐름이 재현된다 (검증: 다른 AI·세션이 채점) ← 강등 사유: 설치 후 도구 인식까지의 end-to-end 흐름은 환경 의존이라 단일 명령의 합/불로 고정하기 어려움
- [ ] [ND] Codex·Antigravity 등 실제 도구가 설치본을 skill로 인식한다 (검증: 사람 확인) ← 강등 사유: 실제 도구의 인식 여부는 각 도구 런타임에 의존하는 사람 판단 영역

---

## 연관 문서

> 현재 `30_contract/`·`40_domain/`·`50_adr/` index는 모두 비어 있어(스캐폴딩 단계) 인용할 이 repo 내부 도메인/계약/ADR 문서가 없다. 아래는 작업 시 실제로 참고할 문서다.

| 문서 | 역할 |
|------|------|
| [GitHub Issue #3](https://github.com/scroogy-dev/scroogy-content-skills/issues/3) | 원본 이슈 — 결정 사항·배경·범위·완료 조건의 SSoT |
| [scroogy-agent-skills / install-skills](https://github.com/scroogy-dev/scroogy-agent-skills/tree/main/install-skills) | **이식 원본** — SKILL.md·`scripts/verify-install.sh`·`tests/run-tests.sh`의 패턴 SSoT |
| [자매 repo ADR 0001](https://github.com/scroogy-dev/scroogy-agent-skills/blob/main/.ai/50_adr/active/0001-skill-deterministic-helper-test-convention.md) | 배포 제외(`tests/`) 규약의 근거 — **이 repo에는 harvest하지 않고 교차 참조만** |
| `.ai/AI-CONTEXT.md` | 프로젝트 도메인·skill 폴더 컨벤션(`<skill-name>/SKILL.md`) |
| `README.md` | 「Skill 목록」·「설치 방법」 — 완료 시 갱신 대상 |
| `blog-photo-draft/SKILL.md` | 이 repo의 첫 skill — 동적 스캔·설치 검증의 실제 대상 |
