---
title: Claude Code 학습 노트 (사용자 본인)
type: source
tags: [claude-code, workflow, personal-notes, second-brain]
created: 2026-04-22
updated: 2026-04-22
origin: raw/CLAUDE study.md
origin_ref: https://www.youtube.com/watch?v=vxEvo2BLM6A
---

# Claude Code 학습 노트 — 소스 요약

> 원본: `raw/CLAUDE study.md`. 저자 = **이 vault 소유자 본인**. 유튜브 영상([링크](https://www.youtube.com/watch?v=vxEvo2BLM6A))을 보며 자기 언어로 정리한 Claude Code 운영 매뉴얼. 스크린샷 다수 임베드.

## 한 줄 요약

**컨텍스트는 왕이다.** Plan Mode·LazyLoading·WAT 프레임워크로 토큰·컨텍스트·에이전트 관심사를 분리해 Claude Code 의 생산성을 최대화하는 개인 플레이북.

## 핵심 테제 5

1. **Plan → 검토 → Execute 를 습관화하라** — "잘못 생성 → 수정 요청 → 재생성" 악순환이 토큰 최대 낭비.
2. **CLAUDE.md 는 얇게, LazyLoading 하라** — 매 세션 로드되는 파일이므로 **규칙·참조만**, 상세는 별도 파일 file-path 로 기억시키기.
3. **컨텍스트 관리 = 1 순위 기술** — `/clear`·`/compact`·`/context`·`/export`·`/model` 을 상황별 무기로.
4. **세컨드 브레인을 로컬 마크다운으로** — 의사결정의 이유를 저장. `/memory` + "기억해줘" 로 자동 누적.
5. **WAT(Workflows · Agents · Tools) 로 일을 조합하라** — Workflows = `todo.md`, Agents = `/agents`, Tools = MCP·Skills.

## 구조적 원칙

### Plan Mode 의 4 가지 실무 이유 (l.26-34)
1. **토큰 최적화** — 재생성 악순환 차단
2. **컨텍스트 윈도우 보존**
3. **실행 정확도 향상**
4. **비용 관리** — output 토큰 절약

→ [[plan-mode-workflow]]

### LazyLoading (l.66-70)
- `claude.md` 하나에 다 담지 말기 — 매 세션 로드 비용
- 참조·규칙만 루트에, **file path 포인터**로 상세 분리
- 폴더별로 `claude.md` 여러 개 두는 것도 유효
- **핵심: 루트 CLAUDE.md 를 비대하게 두지 않는다**

→ [[context-as-king]]

> 📌 현재 이 vault 의 `CLAUDE.md` 는 ~250 줄. 이 원칙 관점에서 **재검토 대상**. 향후 `CLAUDE.md` 규약·예시를 별도 `wiki/concepts/*` 로 분리하고 루트는 포인터 위주로 다이어트 가능. → **lint 후보**.

### WAT 프레임워크 (l.109-124)
- **W**orkflows — `todo.md` 중심의 작업 흐름 정의
- **A**gents — 독립 컨텍스트, `/agents` 로 생성
- **T**ools — MCP·Skills·커스텀 스크립트 조합

→ [[wat-framework]]

## 도구·기능 언급 (→ 엔티티 페이지)

- [[claude-code]] — 도구 자체
- [[skills]] — AI 업무 매뉴얼. 비활성 시 공간 거의 안 먹음. `skill-creator` 플러그인 권장
- [[sub-agents]] — 별도 컨텍스트, 병렬 실행. **opus 비권장**
- [[mcp]] — 양날의 검. 무자비 사용 비추, **커스텀 MCP** 가 최적

## 단축키·커맨드 치트시트

| 요소 | 동작 | 소스 줄 |
|---|---|---|
| `Shift + Tab` | Plan Mode ↔ Accept Mode | l.22 |
| `Escape` | 즉시 중단 | l.23 |
| `Escape × 2` | 입력 삭제/복원 | l.24 |
| `!` 접두사 | bash 직접 실행 | l.38 |
| `/clear` | 컨텍스트 초기화 | l.42 |
| `/compact` | 컨텍스트 압축 | l.43 |
| `/context` | 사용량 바 | l.44 |
| `/model` | Sonnet 병렬 서브에이전트에 권장 | l.47 |
| `/resume` | 세션 재개 | l.48 |
| `/mcp` | MCP 관리 | l.49 |
| `/export` | 컨텍스트 파일 추출 → 다른 AI 비평용 | l.54 |
| `/output-style` | 출력 포맷 | l.56 |
| `/memory` | 자동 기억 | l.64 |

## 코딩 철학 (l.86-112)

- **TDD 스마트 코딩**: 작은 변경 → 테스트 → 린트 → 커밋 → 작은 변경
- **Thinking log 관찰** → 잘못된 판단 즉시 Escape
- **다른 AI 교차검증**: `/export` 후 경쟁 모델에 비평 요청
- **에러 로그는 원문 그대로 붙여넣기** — 해석해서 주지 말 것
- **Mermaid·이미지 아키텍처** — 글보다 그림이 Claude 이해도 높음

## 고급 팁 (l.156-165)

- 멀티인스턴스 운영
- [[git-worktree|Git worktree]] — 동시 브랜치 작업 (스크린샷 l.160)
- 음성입력 + bash 스크립트
- **커스텀 MCP 서버** — 무거운 공용 MCP 대신 직접 제작

## 임베드된 스크린샷 (14 개, Resources/)

✅ **2026-04-22 OCR 완료** (이슈 #1). 각 스크린샷은 아래 위키 페이지에 텍스트로 통합됨.

| 스크린샷 (l.) | 내용 | 통합된 페이지 |
|---|---|---|
| Trigger Keywords (l.12) | `배포`/`ship` 워크플로우 | [[trigger-keywords]] (신규) |
| Compound Engineering (l.14) | 프로젝트 vs 글로벌 `claude.md` | [[compound-engineering]] (신규) |
| 멀티 터미널 (l.76) | (Sub-Agents 섹션과 통합) | [[sub-agents]] |
| Mermaid 아키텍처 (l.81) | `CLAUDE.md` 안의 다이어그램 전술 | [[context-as-king]] |
| WAT Agents (l.120) | Self-Healing + Sub-agent 병렬 | [[wat-framework]] (A 섹션) |
| WAT Tools (l.124) | 도구 원자성 + 3 가지 형태 | [[wat-framework]] (T 섹션) |
| Skills (l.130-137, ×5) | BEFORE/AFTER, 비교, 폴더, description, 데모 | [[skills]] |
| Sub-Agents (l.144) | 4 가지 장점 + Do/Don't | [[sub-agents]] |
| Agent Teams (l.148) | 단방향 → 양방향 메시 (COMING SOON) | [[agent-teams]] (신규) |
| Hooks (l.151-152, ×2) | 동작 흐름, 만드는 두 방법 | [[hooks]] (신규) |
| Git worktree (l.160) | `claude -w` 1 줄 | [[git-worktree]] (신규) |

→ 추가 [[plan-mode-workflow]] "실전 워크플로우 4 단계" 섹션도 본 노트의 워크플로우 도식(l.76 인접) 에서 파생.

## 이 소스에서 파생된 위키 페이지

- Concepts: [[plan-mode-workflow]], [[wat-framework]], [[context-as-king]], [[compound-engineering]], [[trigger-keywords]]
- Entities: [[claude-code]], [[skills]], [[sub-agents]], [[mcp]], [[hooks]], [[agent-teams]], [[git-worktree]]
- Cross-ref: [[llm-wiki-pattern]] (세컨드 브레인 섹션 l.62-74), [[harness-engineering]] (Hooks·Trigger Keywords 의 사상적 부모)
