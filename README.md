# scroogy-content-skills

[![License](https://img.shields.io/badge/License-Apache%202.0-blue.svg)](https://opensource.org/licenses/Apache-2.0)

블로그, 숏폼 영상 등 다양한 콘텐츠를 제작할 때 필요한 [Agent Skills](https://agentskills.io/)를 모아두는 프로젝트입니다.

Agent Skills는 특정 벤더에 종속되지 않는 오픈 포맷으로, Claude Code, Codex, Antigravity, GitHub Copilot 등 다양한 AI 개발 도구에서 호환됩니다.

> 개발 워크플로우용 skill 모음인 [scroogy-agent-skills](https://github.com/scroogy-dev/scroogy-agent-skills)의 **콘텐츠 제작 버전**입니다.

## Skill 목록

| Skill | 설명 |
|-------|------|
| [blog-photo-draft](./blog-photo-draft/) | 여러 이미지의 흐름(시간·장면)을 분석해 사용자 템플릿 구조·톤에 맞춘 네이버 블로그 초안을 글 유형별로 생성. 대량·고해상도 이미지는 캡션 추출과 글쓰기를 배치로 분리 |

## 설치 방법

[install-skills](./install-skills/) skill을 사용하여 원하는 skill을 `~/.claude/skills/` 등 여러 도구 경로에 선택 설치할 수 있습니다.

```
/install-skills            # ~/.claude/skills/ 에 선택 설치 (기본)
/install-skills --all      # 지원하는 모든 도구 경로에 설치
/install-skills --clear    # 대상 skills 디렉토리를 비우고 클린 설치
```

설치 시 `tests/` 등 개발 전용 경로는 배포 결과물에서 제외되며, 설치 결과는 `install-skills/scripts/verify-install.sh`로 결정적으로 검증됩니다. 도구별 설치 경로(`--agents`·`--antigravity`·`--codex`·`--junie`)와 상세 옵션은 [install-skills](./install-skills/SKILL.md)를 참고하세요.

수동 설치가 필요하면 원하는 skill 폴더를 직접 복사할 수도 있습니다.

```
cp -r <skill-name> ~/.claude/skills/
```

## 라이선스

이 프로젝트는 [Apache License 2.0](./LICENSE)에 따라 라이선스가 부여됩니다.

- 상업적 이용, 수정, 배포, 특허 사용, 개인 사용이 자유롭게 허용됩니다.
- 수정된 파일에는 변경 사항을 명시해야 합니다.
- 원본의 [NOTICE](./NOTICE) 파일을 배포 시 포함해야 합니다.
- 소스 파일 헤더 템플릿은 [LICENSE_HEADER.txt](./LICENSE_HEADER.txt)를 참고하세요.

### 개인 저작물 고지

이 프로젝트는 **scroogy-dev**의 개인 저작물입니다. 특정 기업이나 조직의 업무와 무관하게, 개인적인 목적으로 개발 및 관리되고 있습니다.

```
Copyright 2026 scroogy-dev (scroogy@swtest.co.kr)
```
