# Issue #3 실행계획 install-skills 스킬 추가

> 스펙: [issue-0003-spec.md](./issue-0003-spec.md)

---

## Tasks

> AI가 순서대로 실행할 작업 단위를 정의합니다.
> 각 작업은 독립적으로 검증 가능해야 하며, **완료 기준은 자연어 대신 실행 명령 + 임계값**으로 적습니다.
> 명령으로 환원이 어려운 항목만 판정 주체(다른 AI / 사람)를 적고 강등 사유를 남깁니다.
> 마지막 고정 Task(교차모델 issue-audit)는 삭제하지 말고 그대로 둡니다.

### Task 1: install-skills/SKILL.md 이식·작성

- [x] 완료
- **목표**: 자매 repo install-skills의 SKILL.md를 이 repo로 이식하고, 이 repo 고유 사정(ADR 참조·skill 목록)을 조정한다.
- **작업 내용**:
  1. 자매 repo `install-skills/SKILL.md`를 기준으로 개요·설치 경로 옵션(6종)·`--clear`·동적 스캔·설치 절차(클린 설치 + 배포 제외)·설치 검증(결정적 + AI 크로스체크)·Antigravity 레거시 절차를 이식한다.
  2. 배포 제외 패턴(`tests/`, `*.test.*`)을 설치 절차 안에 **SSoT로** 명시한다(`rsync --exclude` + `cp -r` fallback).
  3. **ADR 참조 조정**: 원본의 로컬 상대경로(`../.ai/50_adr/active/0001-...`)를 이 repo에서 깨지지 않게 **자매 repo ADR URL 교차 참조**로 교체한다(이 repo에는 ADR을 harvest하지 않음 — 사용자 확정).
  4. frontmatter에 `name: install-skills`와 이 repo 맥락에 맞는 `description`을 작성한다.
- **완료 기준**: `test -f install-skills/SKILL.md && grep -q '^name: install-skills' install-skills/SKILL.md && grep -q '^description:' install-skills/SKILL.md` 성공 / `grep -q 'for f in \*/SKILL.md' install-skills/SKILL.md` 성공 / `for o in --claude --agents --antigravity --codex --junie --all --clear; do grep -q -- "$o" install-skills/SKILL.md || exit 1; done` 성공 / `grep -q "exclude 'tests/'" install-skills/SKILL.md && grep -q "'\*.test.\*'" install-skills/SKILL.md` 성공 / `grep -q 'github.com/scroogy-dev/scroogy-agent-skills' install-skills/SKILL.md && ! grep -q '\.\./\.ai/50_adr' install-skills/SKILL.md` 성공

---

### Task 2: scripts/verify-install.sh 이식

- [x] 완료
- **목표**: 설치 결과를 사람·AI 판단 없이 exit code로 검증하는 결정적 스크립트를 둔다.
- **작업 내용**:
  1. 자매 repo `install-skills/scripts/verify-install.sh`를 이식한다(대상별 skill 디렉토리·`SKILL.md` 존재, 배포 제외 경로 `tests/`·`*.test.*` 미포함, `--target`/`--legacy-dir`/`--antigravity-legacy` 옵션, 배열·`"$@"` 기반 단어분리 회피).
  2. 실행권한을 부여한다(`chmod +x`).
- **완료 기준**: `test -x install-skills/scripts/verify-install.sh` 성공 / 임시 대상에 skill 복사 후 `install-skills/scripts/verify-install.sh --target <tmp> blog-photo-draft`가 exit 0(PASS) / skill 미복사 또는 `tests/` 주입 시 exit 1(FAIL)

---

### Task 3: tests/run-tests.sh 이식

- [ ] 완료
- **목표**: verify-install.sh의 회귀를 잡는 외부 의존 없는 경량 셸 러너를 둔다.
- **작업 내용**:
  1. 자매 repo `install-skills/tests/run-tests.sh`를 이식한다(고정 입력·기대 출력 비교, PASS/FAIL 케이스 포함).
  2. 이 repo 경로·skill명(`blog-photo-draft` 등)에 맞게 픽스처를 조정한다.
- **완료 기준**: `bash install-skills/tests/run-tests.sh` 실행 시 전체 통과(exit 0)

---

### Task 4: README 「설치 방법」 갱신

- [ ] 완료
- **목표**: 수동 `cp -r` 안내를 install-skills 기반 설치 안내로 갱신한다.
- **작업 내용**:
  1. README 「설치 방법」 섹션에 `/install-skills` 사용법(기본/`--all`/`--clear` 등)을 안내한다. 수동 `cp -r` 방식은 대안/폴백으로 남기거나 install-skills 안내로 대체한다.
- **완료 기준**: `grep -q 'install-skills' README.md` 성공

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
