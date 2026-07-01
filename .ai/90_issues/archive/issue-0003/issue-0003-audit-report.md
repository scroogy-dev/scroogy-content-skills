# Issue #3 감사 리포트 install-skills 스킬 추가

> 감사 일시: 2026-07-01  
> 감사 모델: OpenAI, GPT-5  
> 감사 대상 브랜치: issue-0003  
> 스펙 출처: `.ai/90_issues/active/issue-0003/issue-0003-spec.md`

---

## Phase 1: 적합성 검증 (Compliance Check)

### 요구사항 대조

| # | 요구사항 | 판정 | 근거 |
|---|----------|------|------|
| 1 | `install-skills/SKILL.md` 추가, frontmatter `name: install-skills`/`description` 포함 | PASS | `install-skills/SKILL.md:1-4` 확인. DoD D1 명령 PASS. |
| 2 | 동적 스캔: repo 루트의 `*/SKILL.md` 보유 1차 하위 디렉토리 수집, `install-skills` 자기 제외, description 취득 | PASS | `install-skills/SKILL.md:34-47`에 문서화. 실행 재현 결과 설치 후보는 `blog-photo-draft`만 수집됨. |
| 3 | 6종 설치 대상 옵션과 `--clear` 문서화 | PASS | `install-skills/SKILL.md:12-30`, `:53` 확인. DoD D3 명령 PASS. |
| 4 | 클린 설치 + 배포 제외 SSoT: `rsync -a --exclude 'tests/' --exclude '*.test.*'`, fallback 포함 | PASS | `install-skills/SKILL.md:60-72`에 기존 삭제 후 복사, `rsync --exclude`, fallback, SSoT 문구가 있음. 중첩 `tests/` fallback 누락은 스펙 Out에 명시된 자매 repo 상속 결함으로 이 repo 수정 범위 밖. |
| 5 | `verify-install.sh`: skill 디렉토리, `SKILL.md`, 루트 배포 제외 경로 미포함, 레거시 점검, 실행권한 | PASS | `install-skills/scripts/verify-install.sh:70-115` 확인. 실행권한 PASS, `bash install-skills/tests/run-tests.sh` 9/9 PASS. 중첩 `tests/` 미검출은 스펙 DoD D7에서 범위 밖으로 명시됨. |
| 6 | `install-skills/tests/run-tests.sh`: 외부 의존 없는 경량 셸 러너 | PASS | `install-skills/tests/run-tests.sh` 실행 결과 9/9 PASS. 자매 repo 원본과 diff 없음. |
| 7 | Antigravity 레거시 점검(`--antigravity-legacy`)의 구 경로 리터럴은 `verify-install.sh`가 보유 | PASS | `install-skills/SKILL.md:79-84`는 플래그만 언급하고, 리터럴은 `install-skills/scripts/verify-install.sh:26-28`에 있음. |
| 8 | README `설치 방법` 섹션을 install-skills 안내로 갱신 | PASS | `README.md:17-33` 확인. DoD D8 명령 PASS. |
| 9 | ADR 0001은 이 repo에 harvest하지 않고 자매 repo URL로 교차 참조 | PASS | `install-skills/SKILL.md:10`에 자매 repo GitHub URL 참조, 로컬 `../.ai/50_adr` 참조 없음. |

### 완료의 정의(DoD) 대조

| # | DoD 항목 | 판정 | 근거 |
|---|----------|------|------|
| 1 | `install-skills/SKILL.md` 존재 및 frontmatter name/description | PASS | D1 PASS. |
| 2 | 동적 스캔 문서화 | PASS | D2 PASS. |
| 3 | 6종 옵션과 `--clear` 문서화 | PASS | D3 PASS. |
| 4 | 배포 제외 패턴(`tests/`, `*.test.*`)이 SKILL.md 설치 절차에 SSoT로 명시 | PASS | D4 PASS. |
| 5 | ADR 참조가 자매 repo URL이며 이 repo 로컬 상대경로가 아님 | PASS | D5 PASS. |
| 6 | `verify-install.sh` 존재 및 실행권한 | PASS | D6 PASS. |
| 7 | 정상 설치 PASS, skill 누락 및 루트 배포 제외 위반 FAIL | PASS | `bash install-skills/tests/run-tests.sh`에서 정상 설치, skill 누락, SKILL.md 부재, 루트 `tests/`, `*.test.*`, 레거시 4종까지 9/9 PASS. |
| 8 | `install-skills/tests/run-tests.sh` 통과 | PASS | 9/9 PASS, exit 0. |
| 9 | README 설치 방법에 install-skills 반영 | PASS | D8 PASS. |
| 10 | 실제 skill(`blog-photo-draft`) 임시 설치 후 verify PASS 흐름 재현 | PASS | 임시 대상에 `rsync --exclude 'tests/' --exclude '*.test.*'`로 설치 후 `verify-install.sh --target <tmp> blog-photo-draft` 결과 `RESULT: PASS`. |
| 11 | Codex/Antigravity 등 실제 도구가 설치본을 skill로 인식 | N/A | 스펙상 [ND] 사람 확인 영역. 이번 감사에서는 실제 도구 런타임 인식 확인은 수행하지 않음. |

### 범위 검증

- **스펙 비포함(Out) 침범 여부**: PASS. 자매 repo에 없는 신규 설치 대상 경로/플랫폼 확장은 보이지 않음. ADR 0001도 이 repo에 harvest하지 않았고, `blog-photo-draft`에 `tests/` 또는 `scripts/`를 신설하지 않음.
- **스펙에 없는 추가 구현 여부**: PASS. 변경은 `install-skills` skill/검증 스크립트/테스트/README/이슈 문서에 한정됨.
- **자매 repo 미러링 정합성**: PASS. 로컬 자매 repo와 비교했을 때 `verify-install.sh` 및 `tests/run-tests.sh`는 원본과 diff 없음. `SKILL.md`는 스펙대로 ADR 참조 한 줄만 자매 repo GitHub URL로 다름.

### 도메인/계약/ADR 정합성

- `.ai/30_contract/index.md`: 참조할 계약 문서 없음.
- `.ai/40_domain/index.md`: 이 작업과 직접 충돌하는 도메인 문서 없음.
- `.ai/50_adr/index.md`: 이 repo 내부 ADR 없음. 스펙대로 자매 repo ADR 0001을 교차 참조.
- `.ai/60_codebase/index.md`: 코드베이스 색인 항목 없음. 실제 소스와의 불일치 사항은 발견하지 못함.

---

## Phase 2: 비판적 검증 (Critical Review)

### 발견 사항

| # | 위험도 | 분류 | 설명 | 관련 파일 |
|---|--------|------|------|-----------|
| F-1 | INFO | 범위 확인 / 누락된 검증 | 중첩 `tests/` 미검출은 여전히 재현되지만, 현재 스펙에서 자매 repo 상속 결함으로 Out 처리되어 이 repo 구현 FAIL로 보지 않음. | `install-skills/SKILL.md`, `install-skills/scripts/verify-install.sh` |
| F-2 | INFO | 범위 확인 / 스펙 모호성 | `--clear` 예시의 dotfile 미삭제도 현재 스펙에서 자매 repo 상속 결함으로 Out 처리되어 이 repo 구현 FAIL로 보지 않음. | `install-skills/SKILL.md` |
| F-3 | LOW | 추적성 | summary에는 upstream 이슈 등록 예정이라고 되어 있으나, 현재 리포지토리 문서에는 자매 repo 이슈 링크가 아직 없다. | `.ai/90_issues/active/issue-0003/issue-0003-summary.md` |

### 상세 분석

#### F-1: 중첩 `tests/` 미검출은 남아 있으나 이 repo 수정 범위 밖

- **위험도**: INFO
- **분류**: 범위 확인 / 누락된 검증
- **설명**: 임시 설치본에 `blog-photo-draft/nested/tests/leak.txt`를 만든 뒤 `verify-install.sh --target <tmp> blog-photo-draft`를 실행하면 현재도 `RESULT: PASS`가 나온다. 원인은 `verify-install.sh:81-83`이 루트 `tests/`만 검사하고, fallback 문서도 `install-skills/SKILL.md:71`에서 루트 `tests`만 제거하기 때문이다.
- **영향**: rsync가 없는 환경에서 fallback 설치를 쓰거나, 수동 설치 후 verifier만 믿는 경우 중첩 테스트 디렉토리가 남을 수 있다.
- **감사 판정**: 이전 리포트에서는 PARTIAL/MEDIUM으로 보았으나, 변경된 스펙의 Out 범위와 DoD D7이 이 결함을 자매 repo 상속 결함으로 명시한다. 또한 `verify-install.sh`와 `run-tests.sh`가 자매 repo 원본과 바이트 동일함을 확인했다. 따라서 Issue #3의 구현 미충족으로는 보지 않고, upstream 추적 대상으로 남긴다.
- **권장 조치**: 자매 repo에서 `find "$inst" -type d -name tests` 계열 검증과 중첩 `tests/` 회귀 테스트를 추가한 뒤, 이 repo로 재동기화한다.

#### F-2: `--clear` dotfile 미삭제도 upstream 상속 결함

- **위험도**: INFO
- **분류**: 범위 확인 / 스펙 모호성
- **설명**: `install-skills/SKILL.md:54-57`은 "모든 하위 항목" 삭제를 설명하지만 예시 명령 `rm -rf <target-skills-dir>/*`는 숨김 파일/디렉토리를 매칭하지 않는다.
- **영향**: 일반 skill 디렉토리는 숨김 이름을 쓰지 않으므로 실사용 영향은 낮다. 다만 문구와 셸 glob 동작은 엄밀히 일치하지 않는다.
- **감사 판정**: 현재 스펙 Out 범위가 이 문제를 자매 repo 상속 결함으로 명시하므로 Issue #3의 구현 미충족으로는 보지 않는다.
- **권장 조치**: 자매 repo에서 숨김 항목까지 지우려는 의도라면 `find "$target" -mindepth 1 -maxdepth 1 -exec rm -rf {} +` 형태로 고치고, 의도가 아니라면 설명을 좁힌다.

#### F-3: upstream 이관 링크가 아직 없어 추적성이 약함

- **위험도**: LOW
- **분류**: 추적성
- **설명**: summary Task N에는 F-1/F-2를 자매 repo upstream으로 이관한다고 기록되어 있고, 동시에 "이슈 등록 예정"이라고 남아 있다. 즉 Issue #3 자체의 구현 조건은 충족하지만, 후속 조치가 실제 외부 이슈로 연결되었는지는 현재 문서만으로 확인되지 않는다.
- **영향**: 나중에 왜 F-1/F-2를 이 repo에서 수정하지 않았는지 추적할 때 링크가 없어 감사 체인이 느슨해질 수 있다.
- **권장 조치**: 자매 repo 이슈를 만들면 summary와 이 리포트에 링크를 추가한다. 이 조치는 Issue #3 완료를 막는 결함은 아니다.

---

## 종합 의견

변경된 스펙 기준으로 Issue #3 구현은 적합합니다. 이전 감사의 F-1/F-2는 실제로 아직 재현되지만, 이번 스펙 변경에서 명시적으로 "자매 repo 상속 결함이며 이 repo 수정 범위 밖"으로 재분류되었습니다. 또한 자매 repo 비교 결과 `verify-install.sh`와 `tests/run-tests.sh`는 원본과 동일하고, `SKILL.md`는 깨지는 로컬 ADR 링크를 자매 repo URL로 바꾼 한 줄만 다릅니다.

따라서 Phase 1은 PASS로 정리합니다. 남는 리스크는 구현 결함이라기보다 upstream 추적성 문제이며, 자매 repo 이슈 링크가 생기면 F-3도 해소됩니다.
