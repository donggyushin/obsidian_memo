---
title: Claude Code
type: entity
tags: [tool, cli, anthropic, agent]
created: 2026-04-22
updated: 2026-04-22
sources: [[claude-code-study-notes]], [[advisor-pattern-note]]
---

# Claude Code

## 한 줄 정의

Anthropic 이 제공하는 **공식 CLI 코딩 에이전트**. claude.ai/code 에서도 접근. 대화형으로 파일을 읽고 편집·실행하며 소프트웨어 엔지니어링 작업을 돕는다. 이 위키의 **유지보수자**이기도 함.

## 주요 기능 (사용자 노트 기반)

### Modes

- **Plan Mode** ↔ **Accept Mode**: `Shift + Tab` 토글. [[plan-mode-workflow]]
- 즉시 중단: `Escape`
- 입력 삭제/복원: `Escape × 2`

### Commands

컨텍스트 관리군 — 상세 치트시트는 [[context-as-king]] 참조.

주요: `/clear`, `/compact`, `/context`, `/model`, `/resume`, `/mcp`, `/export`, `/output-style`, `/memory`, `/agents`, `/advisor` (→ [[advisor-pattern]])

### Extensibility

- [[sub-agents]] — 독립 컨텍스트 에이전트 (`/agents` 로 생성)
- [[skills]] — AI 업무 매뉴얼, 비활성 시 컨텍스트 미점유
- [[mcp]] — 외부 도구 연결 프로토콜, 양날의 검
- [[hooks]] — 이벤트 기반 자동 실행. [[harness-engineering]] 의 **둘째 기둥(자동 교정 루프)** 의 Claude Code 구현
- [[agent-teams]] — 다중 에이전트 조직화 (COMING SOON). 양방향 협업 토폴로지
- [[git-worktree|`claude -w`]] — 브랜치별 worktree + 세션을 1 줄로

### I/O 확장

- **이미지 입력** — 아키텍처·UI 이해 시 글보다 효과적 (l.36-37, 81-82)
- **`!` 접두사** — bash 직접 실행
- **음성 입력 + bash 스크립트** 조합 (l.162)

## 사용 철학 (사용자 본인)

1. [[plan-mode-workflow|Plan → 검토 → Execute]] 를 습관화
2. [[context-as-king|컨텍스트를 왕으로]] 대우 — LazyLoading, 한 세션 한 피쳐
3. [[wat-framework|WAT]] 로 작업 조합
4. **Thinking log 관찰** — 잘못된 판단 즉시 Escape
5. **다른 AI 교차검증** — `/export` 후 경쟁 모델에 비평 요청
6. **에러 로그는 원문 그대로** — 해석해서 주지 말 것 (⚠️ 원 소스 l.104)

## 이 vault 에서의 역할

- 이 위키 시스템([[three-layer-architecture]]) 의 Schema 실행자
- `CLAUDE.md` 를 읽고 [[wiki-operations|Ingest/Query/Lint]] 수행
- 사용자는 Obsidian 에서 결과 관찰 ([[obsidian]])

## 관련 엔티티

- [[obsidian]] — 이 위키의 뷰어·IDE
- [[skills]] [[sub-agents]] [[mcp]] — Claude Code 의 확장 요소들

## 근거 소스

- [[claude-code-study-notes]] 전체
