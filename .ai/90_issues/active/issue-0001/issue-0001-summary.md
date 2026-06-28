# Issue #1 실행요약 이미지 흐름 기반 블로그 초안 작성 스킬 추가

> 스펙: [issue-0001-spec.md](./issue-0001-spec.md) | 계획: [issue-0001-plan.md](./issue-0001-plan.md)

## 다음 작업
  
> ▶️ 다음 작업: Task N(고정) — 교차모델 `issue-audit` 검증 (**사용자가 직접** 다른 벤더 모델로 수행 후 audit 모델·결과를 아래 모델 기록·Task별 결과에 반영). 구현 AI는 이 Task를 자동 수행·종료하지 않음.

## 모델 기록

| 구분 | 모델 |
|------|------|
| 계획·구현 모델 | Anthropic, Claude Opus 4.8 (claude-opus-4-8) |
| audit 모델 | <!-- 구현 모델과 다른 벤더 모델. 형식: 벤더, 모델명. 마지막 교차모델 audit Task에서 사용자가 기록 --> |

---

## Task별 수행 결과

### Task 1: SKILL.md 골격 + 인자 정의

- **결과**: ✅ 완료
- **수행 내용 요약**:
  - `blog-photo-draft/SKILL.md` 생성. frontmatter `name: blog-photo-draft` + 트리거 키워드 포함 `description` 작성.
  - 섹션: `개요`(입력/흐름파악/유형/출력 요약) + `설계 원칙`(캡션↔글쓰기 분리, 사용자 템플릿 SSoT, 환각 방지, 벤더 독립) + `사용법`(`/blog-photo-draft` 호출 + 예시 3종) + `인자` 표.
  - 인자 표: `<images...>`/`--dir`/`--template`(필수)/`--order`/`--max-images`/`--batch-size`/`--out` 7종 문서화.
  - 후속 Task용 섹션 헤더 3개를 골격으로 남김(이미지 흐름·배치 / 템플릿·유형 / 근거·출처·출력).
- **특이 사항**:
  - 저장소 규칙(`coding-convention`·`file-change-policy`)은 아직 스캐폴딩(빈 주석) 상태라 자매 repo 설치 스킬(`readme-sync`·`git-commit`)의 frontmatter·섹션 포맷을 참조 기준으로 삼음.
  - **설계 변경 1(사용자 결정)**: 글 유형을 CLI 플래그 `--type`로 받지 않고 **템플릿 frontmatter `type`로 흡수**, 플래그 제거. 근거: 사용자가 템플릿을 만들 때 유형을 함께 정의하므로 별도 플래그가 중복. 영향 반영: SKILL.md(인자/사용법/예시/개요) + spec(범위·DoD #2 검증 명령) + plan(Task 1 인자·완료기준, Task 3 유형 판별). 유형 판별은 `템플릿 frontmatter type → 구조·이미지 성격 추론`으로 단순화. 상세 규약은 Task 3에서 확정.
  - **설계 변경 2(사용자 결정, C안)**: 템플릿을 **외부 제공 + 내장 기본 골격 2종 병행**으로 결정. `--template`을 **필수→선택**으로 바꿔, 생략 시 추론 유형의 내장 기본(`templates/narrative.md`/`expository.md`)으로 폴백하고 `narrative`/`expository` 이름으로 직접 지정도 허용. 내장은 **유형 골격 2종까지만**이며 분야별 프리셋(여행/맛집/리뷰)은 여전히 비포함. 근거: 외부만 두면 첫 실행 장벽, 내장 골격이 폴백 겸 템플릿 계약 예시 역할. 영향 반영: SKILL.md(설계원칙·사용법·예시·인자·Task3 스텁) + spec(범위 포함/비포함·DoD에 내장 템플릿/폴백 검증 2건 추가) + plan(Task 3 해석 순서, Task 5 "샘플(선택)"→"내장 기본 2종(필수 산출물)" 승격).
  - 갱신된 완료기준 검증 전부 PASS(`name/description` 존재, `--template`·`--dir`·`--batch-size`·`--max-images` 존재). SKILL.md·spec·plan에 `--type` 잔존 없음 확인.

---

### Task 2: 이미지 흐름 분석 + 대량 이미지 배치 처리 명세

- **결과**: ✅ 완료
- **수행 내용 요약**:
  - SKILL.md `이미지 흐름 분석과 대량 이미지 배치 처리` 스텁을 4단계 절차로 상세화: ① 포맷 정규화 → ② 정렬 → ③ 배치 캡션 추출 → ④ 상한·가드.
  - **① 포맷 정규화(HEIC)**: 캡션 단계 전 `sips -s format jpeg`로 HEIC→JPEG 변환(best-effort), 실패/비macOS는 건너뛰고 로그. 변환 산출물은 임시 디렉토리, 원본 파일명·순서는 자리표시자용으로 보존.
  - **② 정렬**: `--order auto` = EXIF `DateTimeOriginal` → 파일명 자연정렬(`IMG_2 < IMG_10`) → `mtime` 폴백. `exif/name/mtime` 직접 지정 가능. 스크린샷은 EXIF 부재로 파일명/mtime 폴백 의존 명시. 정렬 근거 로그.
  - **③ 배치 캡션 추출(맵)**: 이미지는 이 단계에서만 소비, 글쓰기는 캡션 텍스트만 입력 → 토큰 폭발 회피. `--batch-size` 단위 병렬(서브에이전트). 캡션에 원본 파일명·순번 동반.
  - **④ 상한·가드(`--max-images`)**: 초과 시 유사·연속 컷 그룹화/대표 샘플링, 묶음·생략 로그 명시(조용한 누락 금지).
- **특이 사항**:
  - Task 1이 남긴 스텁 위치(본문 섹션 헤더)를 그대로 채워, 후속 Task 3·4 스텁은 손대지 않음.
  - 완료 기준 grep 6종 전부 PASS: `DateTimeOriginal`/`mtime`/`자연정렬|natural`/`heic`/`배치|batch`/`--max-images`.

---

### Task 3: 템플릿 규약 + 글 유형 분기 명세

- **결과**: ✅ 완료 (Task 4 다음에 수행)
- **수행 내용 요약**:
  - SKILL.md `템플릿 규약과 글 유형 분기` 스텁을 3개 하위 섹션으로 상세화.
  - **템플릿 규약**: 마크다운 + 선택적 frontmatter + 선택적 플레이스홀더. frontmatter 키 3종을 표로 정의 — `type`(narrative/expository), `require_sources`(true 시 Task 4 결정적 출처 가드 활성화; expository 내장 기본 true, narrative 생략), `sections`(기대 섹션 헤딩 순서). 플레이스홀더 `{{title}}`/`{{date}}`/`{{location}}`는 캡션·메타데이터에서 채우고 미해결 시 지어내지 않음(환각 방지).
  - **템플릿 해석 순서**: ① `--template <경로>` 외부 SSoT → ② `--template narrative|expository` 내장 이름 직접 지정 → ③ 생략 시 유형 추론 후 내장 기본 골격 폴백(어떤 기본 썼는지 로그).
  - **유형 판별**: 외부 템플릿 있으면 frontmatter `type`→구조·이미지 추론, 없으면 이미지 흐름으로 추론(시간순 경험→narrative / 정보·비교·설명→expository), 모호하면 narrative 기본 + 판별 근거 로그.
- **특이 사항**:
  - **Task 4와의 연결**: frontmatter `require_sources: true`를 Task 4의 **결정적 후처리 출처 가드 트리거**로 못박음. 즉 "출처 강제 여부"가 템플릿 계약(frontmatter)에서 선언되고, 생성물에서 `grep -q '^## 출처'`로 결정적 검증되는 구조. expository 내장 기본은 `require_sources: true`(Task 5 내장 템플릿에 반영 예정).
  - 완료 기준 grep 전부 PASS: `narrative`/`expository`/`require_sources`/`내장 기본`. Task 1·2·4 키워드(`DateTimeOriginal`/`mtime`/`heic`/`--max-images`/`--template`/`목차`/`출처`/`가드`/가드 명령 문자열) 회귀 없음.
  - 이로써 SKILL.md 본문 스텁(Task 2·3·4)이 모두 채워짐. 남은 산출물은 Task 5의 내장 템플릿 2종 + README 등록.

---

### Task 4: 근거·출처 정책 + 네이버 출력 포맷 명세

- **결과**: ✅ 완료 (Task 3보다 먼저 수행 — 사용자 질문 흐름)
- **수행 내용 요약**:
  - SKILL.md `근거·출처 정책과 네이버 출력 포맷` 스텁을 4개 하위 섹션으로 상세화.
  - **근거·출처 정책(expository)**: 사실 주장 출처 뒷받침, 미확보 주장 추측 표시(`~로 보입니다`/`추정`)/제외, 근거 없는 단정 금지, 캡션 사실 vs 글쓴이 해석 구분, 상단 `## 목차` + 하단 `## 출처`. narrative는 출처 섹션 비강제(단, 외부 사실 단정 시 동일 원칙).
  - **결정적 출처 가드(B안)**: 생성 직후 expository면 `grep -q '^## 목차' && grep -q '^## 출처'`로 섹션 존재를 후처리 검사, 실패 시 보강 재생성/경고(조용한 누락 금지). 가드는 "존재"만 결정적 보장, 출처 타당성·단정 배제는 범위 밖(사람/다른 AI)임을 명시.
  - **네이버 출력 포맷**: 본문 + 자리표시자 `[사진: <원본파일명> (<순번>/<총장수>)]` 교차 배치, 흐름 순서·원본 파일명 노출, 자리표시자는 문단 사이 단독 줄.
  - **출력 예시 2종**: narrative(제주 여행), expository(미러리스 카메라 — 목차·본문·출처 풀세트). expository 예시는 가드를 실제 통과하도록 작성.
- **특이 사항**:
  - **설계 변경 3(사용자 결정, B안)**: 출처 섹션 "유무"를 의미 판단이 아닌 구조적 속성으로 보고 **결정적 후처리 가드**로 강제. 영향 반영:
    - spec DoD: `[D] expository 생성물 목차·출처 강제 후처리 가드 문서화` 항목 **신규 추가**, 기존 묶인 `[QD]`(근거 없는 단정 배제 **및** 목차+출처 포함)에서 "섹션 포함"을 **떼어내** "단정 배제·출처 타당성"만 채점으로 축소.
    - plan Task 4: 목표·작업 내용에 (B)가드 단계 추가, 완료 기준에 가드 grep(`grep -q '^## 출처'` 명시 + `가드` 키워드) 추가.
  - 완료 기준 grep 전부 PASS: `목차`/`출처`/`근거`/`가드`/후처리 가드 명령 문자열/예시 코드블록 2개. Task 1~2 키워드(`DateTimeOriginal`/`mtime`/`heic`/`--max-images`/`--template`/`narrative`/`expository`) 회귀 없음.
  - Task 3(템플릿 규약) 스텁은 손대지 않음 — 다음 작업에서 채움.

---

### Task 5: 내장 기본 템플릿 `how-to` 1종 + SKILL.md 유형 체계 정리 + README 등록

- **결과**: ✅ 완료
- **수행 내용 요약**:
  - `blog-photo-draft/templates/how-to.md` 신규 생성. frontmatter `type: how-to` + `require_sources: false` + `sections`. 본문은 따라하기 계약 예시 — `## 목차` + `## 준비물` + 순서 있는 `## Step N — …`(각 단계에 `[사진: <원본파일명> (N/<총장수>)]` 자리표시자 1:1) + `## 마무리`. 상단 주석으로 단계·이미지 1:1, 플레이스홀더 비지어내기, 출처 비강제 규약 명시.
  - SKILL.md 유형 체계 보강(Task 1~4의 "2종" 프레이밍 정리): ① 개요·frontmatter `type` 값·유형 판별에 `how-to`(따라하기형) 추가 — 순서 있는 절차·연속 스크린샷이면 how-to로 추론, 모호 시 기본값을 `narrative`→**`how-to`**로 변경. ② 내장 기본을 'how-to 1종(현행)·다른 유형 추후'로 명시하고 폴백 기본·내장 이름·인자 표·사용 예시·해석 순서를 how-to로 통일(`--template <path|how-to>`). ③ `expository` 출처 가드·정책은 `require_sources` 트리거 메커니즘으로 문서 유지(how-to는 false라 미발동). ④ how-to 출력 예시(맥북 스크린샷 저장 위치 변경)를 서사형 앞에 추가. ⑤ stale 주석("Task 2~5에서 상세화") 제거.
  - README Skill 목록 표: 주석 처리된 자리표시 표를 풀고 `blog-photo-draft` 행 1건 등록.
- **특이 사항**:
  - **설계 변경 4(사용자 결정)**: 내장 기본 템플릿을 한 번에 2종 만들지 않고 **`how-to`(따라하기형) 1종부터** 만들고 필요할 때 추가하기로 결정. 영향 반영: spec(목표·범위 In/Out·DoD #3 키워드 `how-to`/#8 템플릿 파일 `how-to.md`로 변경), plan(Task 5 산출물을 how-to 1종 + SKILL.md 유형 체계 정리로 재정의). `narrative`/`expository`는 SKILL.md에 인식 유형·정책(출처 가드 등)으로 **문서 유지**하되 내장 템플릿 파일은 추후.
  - **결정적 검증 전부 PASS**: Task 5 완료 기준 4건(how-to.md 존재·`type: how-to`·SKILL.md `how-to`·README 등록) + spec DoD `[D]` 9건 회귀 검증 통과. SKILL.md에 stale 참조(`2종`/`Task`/`templates/narrative.md|expository.md` 직접 참조/`--template narrative|expository` 예시) 잔존 없음 확인.

---

### Task N (고정): 교차모델 issue-audit 검증

- **결과**: 미착수 (사용자 수동 수행)
- **수행 내용 요약**:
- **특이 사항**: 구현 모델과 다른 벤더 모델로 사용자가 직접 `issue-audit` 실행 후 결과·audit 모델을 위 모델 기록 표에 반영한다.
