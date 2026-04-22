---
title: Git Worktree (with Claude Code)
type: entity
tags: [git, claude-code, worktree, parallel, workflow]
created: 2026-04-22
updated: 2026-04-22
sources: [[claude-code-study-notes]]
---

# Git Worktree

## 한 줄 정의

하나의 Git 저장소에서 **여러 브랜치를 별도 작업 디렉터리로 동시 체크아웃**하는 Git 기본 기능. Claude Code 환경에서는 이 위에 **인스턴스 병렬화** 패턴이 얹혀 있다.

## 왜 Claude Code 와 같이 다루는가

- 한 브랜치당 한 Claude 세션을 띄우면 **컨텍스트가 안 섞임** ([[context-as-king]] 의 "한 세션 = 한 피쳐" 강제 적용)
- 여러 피쳐·버그픽스·리팩터를 **동시 진행** 가능
- 디스크에 별도 디렉터리가 생기므로 IDE·파일 워처도 각자 독립

## 토폴로지

```
   main repo
   /     |     \
worktree-1   worktree-2   worktree-3
feature/auth bugfix/api   refactor/db
   ↑           ↑              ↑
 Claude #1  Claude #2     Claude #3
```

각 worktree 는 같은 `.git/` 을 공유하지만 작업 트리(파일 체크아웃) 는 독립.

## 수동 vs Claude 네이티브

### 수동 Worktree (3 줄)

```bash
$ git worktree add ../project-feature-auth feature/auth
$ cd ../project-feature-auth
$ claude
```

매 브랜치마다 위 3 줄 반복.

### Claude 네이티브 (1 줄)

```bash
$ claude -w feature-auth
```

`-w` 플래그가 worktree 생성·이동·세션 시작을 한 번에 수행. (스크린샷: `![[스크린샷 2026-04-19 오전 10.12.09.png]]`)

> 추론: 본 vault 의 작업 디렉터리도 `claude-squad/worktrees/.../screenshots_ocr_*` 형태 — 사실상 worktree 패턴의 변형. claude-squad 같은 외부 도구가 같은 사상으로 다중 인스턴스를 관리.

## 사용 시나리오

| 상황 | Worktree 활용 |
|---|---|
| 긴 리팩터 진행 중 급한 버그픽스 | 새 worktree 로 픽스만 분리 → 원 작업 컨텍스트 보존 |
| PR 리뷰어가 다른 PR 도 동시 검토 | 리뷰 대상별로 worktree → IDE 가 독립 |
| AI 다중 인스턴스 (Sub-Agent 와 다름) | 인간 개발자가 의식적으로 운영하는 병렬화 |
| 빌드 캐시·node_modules 분리 | 의존성 변경 비교 시 안전 |

## 한계·주의

- **디스크 사용** — worktree 마다 작업 트리 복제. 큰 모노레포에선 부담
- **환경 변수·로컬 빌드 산출물** — `.env`·`node_modules` 는 각 worktree 따로 관리 필요
- **DB·로컬 서비스 충돌** — 포트·DB 인스턴스가 worktree 간 공유면 충돌 위험
- **삭제 시** — `git worktree remove <path>` 로 정리해야 메타데이터 정합

## 멀티 인스턴스 운영 (원 노트 l.156)

원 학습 노트에선 "멀티인스턴스 운영" 항목이 worktree 와 함께 묶여 등장. 의미:

1. 터미널 분할 (tmux·iterm 등) → 각 패널에 worktree
2. 각 패널에 Claude 세션
3. 사람은 패널 간 시선만 옮기며 여러 작업을 진행

→ [[sub-agents]] 가 **AI 가 알아서 병렬화** 하는 거라면, worktree + 멀티인스턴스는 **사람이 의식적으로 병렬화** 하는 패턴. 둘은 다른 차원에서 보완.

## 관련 엔티티

- [[claude-code]] — `claude -w` 네이티브 지원
- [[sub-agents]] — AI 측 병렬 (vs 사람 측 병렬)
- [[oh-my-claudecode]] — `project-session-manager` 스킬이 worktree 우선 환경 매니저로 동작

## 관련 개념

- [[context-as-king]] — worktree 는 "한 세션 한 피쳐" 의 환경 강제
- [[harness-engineering]] — 환경에 컨텍스트 격리를 박제하는 사례

## 근거 소스

- [[claude-code-study-notes]] l.156-160
- 스크린샷: `![[스크린샷 2026-04-19 오전 10.12.09.png]]`
