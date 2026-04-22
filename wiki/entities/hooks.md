---
title: Hooks (Claude Code)
type: entity
tags: [claude-code, hooks, automation, harness, settings]
created: 2026-04-22
updated: 2026-04-22
sources: [[claude-code-study-notes]], [[harness-engineering-note]]
---

# Hooks

## 한 줄 정의

Claude Code 의 **이벤트 기반 자동 실행 메커니즘**. 정해진 이벤트(예: 응답 시작·종료, 도구 호출 전후) 가 발생하면 사용자가 정의한 액션이 자동으로 실행된다. `~/.claude/settings.json` (개인) 또는 `.claude/settings.json` (프로젝트) 에 정의.

## 동작 흐름

```
이벤트 발생  →  Hook 감지  →  액션 실행
```

(스크린샷의 3 단계 그래프)

이벤트 종류 (대표):

- `PreToolUse` — 도구 호출 전 (검증·차단·파라미터 수정)
- `PostToolUse` — 도구 호출 후 (auto-format, 후처리, 알림)
- `Stop` — 응답 완료 시 (최종 검증, 완료 알림)
- `SessionStart`, `SessionEnd`, `UserPromptSubmit`, `PreCompact`, `Notification` 등

## 만드는 두 가지 방법

### 방법 1 — Claude 에게 자연어로 요청 (추천)

가장 쉬운 길. Claude 가 알아서 `settings.json` 에 Hook 항목을 추가해 준다.

```
프롬프트 예:
"Claude 가 응답 대기 상태에 들어가면
 macOS 알림과 효과음이 나오는 Hook 을 만들어줘"
```

흐름:
1. Claude 에게 요청
2. `settings.json` 자동 수정
3. `/hooks` 로 확인

### 방법 2 — `settings.json` 직접 편집

설정 파일을 직접 열어 JSON 으로 작성. 구조는 `claude.com/code` 공식 문서 참조.

설정 파일 경로:
- `~/.claude/settings.json` (개인용, 모든 프로젝트 공통)
- `.claude/settings.json` (프로젝트용, 레포에 커밋 가능)

## 핵심 활용 패턴

| 이벤트 | 자주 쓰는 액션 |
|---|---|
| `PreToolUse: Bash` | 위험 명령(`rm -rf`, `force push`) 차단, tmux 강제, 권한 확인 |
| `PostToolUse: Edit` | Prettier 자동 포맷, TypeScript 타입 체크 |
| `PostToolUse: Write` | 새 파일에 헤더 자동 삽입 |
| `Stop` | 최종 lint·테스트 실행, 완료 알림(소리·푸시) |
| `UserPromptSubmit` | 프롬프트에 매크로·컨텍스트 자동 주입 |
| `PreCompact` | 컴팩트 직전 중요 정보 별도 저장 |

## 위험·주의사항

- **무한 루프** — Hook 이 다시 도구 호출을 트리거하지 않도록 가드 필수
- **성능 저하** — 매 도구 호출마다 무거운 스크립트 실행 시 응답 지연
- **권한 과다** — Hook 이 임의 bash 실행 = 실질적으로 OS 권한 부여. `settings.json` 변경은 코드 리뷰 대상
- **무음 실패** — Hook 실패가 조용히 넘어가지 않도록 로그 노출

## [[harness-engineering|Harness Engineering]] 의 둘째 기둥

Hooks = Harness 4 기둥 중 **자동 교정 루프 (auto-correction loop)** 의 Claude Code 구현체. AI 가 잘못된 동작을 시도해도 Hook 이 가로채 차단·교정·기록.

> "AI 가 실수했을 때 프롬프트 말고 하네스를 고쳐라" 라는 Harness 의 핵심 격언이 가장 직접적으로 Hook 으로 구현된다.

→ [[harness-engineering]]

## [[wat-framework|WAT]] Tools 층 위치

WAT 의 **T (Tools)** 층 안에서 [[mcp]]·Skills·bash 스크립트와 함께 도구 원자성 카드의 한 축으로 등장 (스크린샷: `![[스크린샷 2026-04-17 오후 3.21.17.png]]`). MCP 가 외부 시스템 연결, Skills 가 매뉴얼 제공, **Hooks 는 라이프사이클 자동화** 담당.

## 이 vault 에서의 사용

본 vault 의 글로벌 `~/.claude/settings.json` 에는 다음 종류 Hooks 가 운영 중 (하위 호환 정보, 변경 가능):

- `PreToolUse`: tmux 권유, git push 전 Zed 리뷰, 불필요 `.md` 생성 차단
- `PostToolUse`: PR URL 로깅, Prettier 자동 포맷, TypeScript 체크, `console.log` 경고
- `Stop`: 변경 파일 `console.log` 감사

→ 본 vault 작업 중에도 PR 생성·문서 차단·tmux 권유 등이 활성화되어 있어, 본 위키 운영도 일종의 Harness 안에서 진행 중.

## [[trigger-keywords|Trigger Keywords]] 와의 차이

| 비교 | Hooks | Trigger Keywords |
|---|---|---|
| 트리거 | **이벤트** (도구 호출, 응답 종료) | **키워드** (사용자 입력) |
| 정의 위치 | `settings.json` (JSON) | `CLAUDE.md` (자연어) |
| 강제력 | 강제 (사용자가 우회하기 어려움) | 약함 (사용자 입력 의존) |
| 실패 시 | 도구 호출 자체 차단 가능 | 키워드 미입력 시 무동작 |

→ 둘은 보완관계. **자동화는 Hooks**, **단축어는 Trigger Keywords**.

## 관련 엔티티

- [[claude-code]] — 호스트
- [[oh-my-claudecode]] — Hooks 를 적극 활용한 하네스 오픈소스
- [[mcp]], [[skills]] — Tools 층 동료

## 관련 개념

- [[harness-engineering]] — 본 엔티티의 사상적 부모
- [[trigger-keywords]] — 다른 자동화 축
- [[wat-framework]] — Tools 층 내 위치

## 근거 소스

- [[claude-code-study-notes]] l.151-152
- 스크린샷:
  - `![[스크린샷 2026-04-19 오전 10.02.48.png]]` (Hook 동작 흐름)
  - `![[스크린샷 2026-04-19 오전 10.03.32.png]]` (만드는 두 방법)
  - `![[스크린샷 2026-04-17 오후 3.21.17.png]]` (WAT Tools 카드의 Hooks)
