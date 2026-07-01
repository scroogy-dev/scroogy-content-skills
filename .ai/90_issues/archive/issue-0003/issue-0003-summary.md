# Issue #3 실행요약 install-skills 스킬 추가

> 스펙: [issue-0003-spec.md](./issue-0003-spec.md) | 계획: [issue-0003-plan.md](./issue-0003-plan.md)

## 다음 작업

> ▶️ 다음 작업: ✅ 모든 작업이 완료되었습니다.

## 모델 기록

<!--
형식: "벤더, 모델명". 계획·구현 모델도 audit 모델도 어느 벤더·모델이든 가능하다
(예: Anthropic, Claude Opus 4.8 (claude-opus-4-8) / OpenAI, GPT-5.x / Google, Gemini 3.x).
"audit 모델 ≠ 구현 모델" 조건을 나중에 명령으로 확인 가능하도록 기록한다. 모델 전환은 사람이 수행하므로 기계로 강제하기 어렵다.
-->

| 구분 | 모델 |
|------|------|
| 계획·구현 모델 | Anthropic, Claude Opus 4.8 (claude-opus-4-8) |
| audit 모델 | OpenAI, GPT-5 |

---

## Task별 수행 결과

### Task 1: install-skills/SKILL.md 이식·작성

- **결과**: 완료
- **수행 내용 요약**: 자매 repo `install-skills/SKILL.md`를 이식(6종 설치 경로 옵션 + `--clear`, 동적 스캔, 클린 설치 + 배포 제외 SSoT, 결정적 검증 + AI 크로스체크, Antigravity 레거시 절차). frontmatter `description`·예시 skill 배열(`skills=(git-commit git-pr issue-work)`)은 원본과 동일하게 유지.
- **특이 사항**: **원본 완전 동일 우선**(사용자 지시)에 따라 `description`·예시 배열을 원본대로 유지. 단 개요의 ADR 링크만 예외로 자매 repo ADR URL 교차 참조 유지 — 원본의 로컬 상대경로(`../.ai/50_adr/...`)는 이 repo에 해당 파일이 없어 깨진 링크가 되기 때문. Task 1 관련 `[D]` 검증 5종(D1~D5) 전부 PASS.

---

### Task 2: scripts/verify-install.sh 이식

- **결과**: 완료
- **수행 내용 요약**: 자매 repo `install-skills/scripts/verify-install.sh`를 이식. 로직·옵션(`--target`/`--legacy-dir`/`--antigravity-legacy`/`--`/`-h`)·검사 항목(skill 디렉토리·`SKILL.md` 존재, `tests/`·`*.test.*` 배포 제외 위반, 레거시 구 경로 심링크/부재/빈 디렉토리=PASS·비어있지 않은 실제 디렉토리=FAIL)을 원본 그대로 유지. 배열·`"$@"` 기반으로 단어분리를 회피. `chmod +x` 부여.
- **특이 사항**: **원본 완전 동일 우선**(사용자 지시)에 따라 주석까지 원본과 바이트 단위로 동일하게 유지(초기 `자매 repo` 명시 보정은 원복). DoD 검증 완료 — `test -x` PASS / 정상 설치 exit 0 / skill 미복사 exit 1 / `tests/` 주입 exit 1 / `*.test.*` 주입 exit 1 / `--target` 누락 exit 2 / 레거시 4종(부재·심링크·빈 디렉토리 PASS, 비어있지 않은 실제 디렉토리 FAIL) 모두 기대대로.

---

### Task 3: tests/run-tests.sh 이식

- **결과**: 완료
- **수행 내용 요약**: 자매 repo `install-skills/tests/run-tests.sh`를 이식. 구조(sandbox 런타임 픽스처 생성, `assert_exit` 기반 exit code 비교, 9개 케이스)를 원본 그대로 유지. `chmod +x` 부여. `bash install-skills/tests/run-tests.sh` 실행 시 9/9 통과(exit 0).
- **특이 사항**: **원본 완전 동일 우선**(사용자 지시)에 따라 픽스처(`alpha`/`beta`/`gamma`)와 헤더 주석을 원본과 바이트 단위로 동일하게 유지(초기 이 repo 맥락 조정은 원복). `bash install-skills/tests/run-tests.sh` 9/9 통과 재확인.

---

### Task 4: README 「설치 방법」 갱신

- **결과**: 완료
- **수행 내용 요약**: README 「설치 방법」을 수동 `cp -r` 안내에서 `/install-skills` 기반 안내로 갱신. 기본/`--all`/`--clear` 사용법과 도구별 경로(`--agents`·`--antigravity`·`--codex`·`--junie`)·상세 옵션 참조 링크를 추가하고, 수동 `cp -r`은 폴백으로 유지. DoD `grep -q 'install-skills' README.md` PASS.
- **특이 사항**: 자매 repo README와 일관되게 `install-skills`는 「Skill 목록」 표에 넣지 않음(동적 스캔에서 자기 자신을 제외하는 메타 skill).

---

### Task N (고정): 교차모델 issue-audit 검증

- **결과**: 교차모델 audit 수행·검토 완료 (구현=Anthropic, Claude Opus 4.8 ≠ audit=OpenAI, GPT-5). spec `[D]` 9/9 PASS 재확인. 리포트: `.ai/99_workspace/issue-0003-audit-report.md`. **단, plan Task N 체크박스 확정(완료 처리)은 사용자 몫.**
- **수행 내용 요약**: 사용자가 OpenAI GPT-5로 `issue-audit` 실행. 발견사항 2건을 `--response`로 피드백·항목별 승인 후 처리 — **두 건 모두 SSoT(자매 repo) 상속 결함**이라 이 repo에서 미수정하고 **자매 repo(upstream)로 이관** 결정.
- **특이 사항**:
  - **F-1** (리포트 MEDIUM / 이 repo 실사용 LOW): 중첩 `tests/`(`nested/tests/`)를 verifier(`verify-install.sh`의 `[ -d "$inst/tests" ]` 루트-only 검사)와 cp fallback(`rm -rf "$target/$s/tests"` 루트만)이 미검출 → `RESULT: PASS`. 재현 확인. 코드가 **원본과 바이트 동일** → upstream 수정 대상. **이관**.
  - **F-2** (LOW): `--clear` 예시 `rm -rf <target-skills-dir>/*`가 dotfile/dotdir 미삭제. 원본 SKILL.md와 **동일** → upstream. **이관**.
  - 두 결함 모두 자매 repo 원본에 동일 존재함을 `diff`로 확인. 이 repo 산출물(sh 2종 = 원본 바이트 동일, SKILL.md = ADR 링크 1줄만 예외)은 미러링 상태 유지 — 이 repo(#3) 구현 결함 아님.
  - **이관 후속(미완)**: 자매 repo `scroogy-agent-skills`에 이슈 등록 예정(F-1 수정 + 중첩 `tests/` 테스트 케이스 추가, F-2 문구 정정). 등록 시 이 자리에 링크를 남길 것.
