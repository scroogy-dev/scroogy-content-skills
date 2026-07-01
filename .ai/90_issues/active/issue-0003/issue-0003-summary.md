# Issue #3 실행요약 install-skills 스킬 추가

> 스펙: [issue-0003-spec.md](./issue-0003-spec.md) | 계획: [issue-0003-plan.md](./issue-0003-plan.md)

## 다음 작업

> ▶️ 다음 작업: Task 2 — scripts/verify-install.sh 이식

## 모델 기록

<!--
형식: "벤더, 모델명". 계획·구현 모델도 audit 모델도 어느 벤더·모델이든 가능하다
(예: Anthropic, Claude Opus 4.8 (claude-opus-4-8) / OpenAI, GPT-5.x / Google, Gemini 3.x).
"audit 모델 ≠ 구현 모델" 조건을 나중에 명령으로 확인 가능하도록 기록한다. 모델 전환은 사람이 수행하므로 기계로 강제하기 어렵다.
-->

| 구분 | 모델 |
|------|------|
| 계획·구현 모델 | Anthropic, Claude Opus 4.8 (claude-opus-4-8) |
| audit 모델 | <!-- 구현 모델과 다른 벤더 모델. 형식: 벤더, 모델명. 마지막 교차모델 audit Task에서 사용자가 기록 --> |

---

## Task별 수행 결과

### Task 1: install-skills/SKILL.md 이식·작성

- **결과**: 완료
- **수행 내용 요약**: 자매 repo `install-skills/SKILL.md`를 이식(6종 설치 경로 옵션 + `--clear`, 동적 스캔, 클린 설치 + 배포 제외 SSoT, 결정적 검증 + AI 크로스체크, Antigravity 레거시 절차). 예시 skill 배열을 이 repo의 `blog-photo-draft`로 조정.
- **특이 사항**: ADR 참조를 원본의 로컬 상대경로(`../.ai/50_adr/...`)에서 자매 repo ADR URL 교차 참조로 교체(이 repo에는 harvest 안 함, 사용자 확정). Task 1 관련 `[D]` 검증 5종 전부 PASS.

---

### Task 2: scripts/verify-install.sh 이식

- **결과**:
- **수행 내용 요약**:
- **특이 사항**:

---

### Task 3: tests/run-tests.sh 이식

- **결과**:
- **수행 내용 요약**:
- **특이 사항**:

---

### Task 4: README 「설치 방법」 갱신

- **결과**:
- **수행 내용 요약**:
- **특이 사항**:

---

### Task N (고정): 교차모델 issue-audit 검증

- **결과**:
- **수행 내용 요약**:
- **특이 사항**:
