---
title: "Rewind Pattern — 실패 기록을 지우고 재시도"
type: concept
tags: [claude-code, context, session-hygiene, workflow]
created: 2026-04-25
updated: 2026-04-25
sources: [jay-choi-9-tips, claude-code-2h-mastery]
aliases: ["리와인드", "Rewind", "ESC 두 번", "Esc×2"]
---

# Rewind Pattern

## 핵심 요약

`Esc` 를 두 번 누르면 이전 메시지 지점으로 되감기 + **그 이후 기록을 싹 지움**. *"컨텍스트 관리 잘하는 사람을 가르는 단 하나의 습관"* 으로 Claude Code 팀이 공식 강조한 패턴 (출처: [[sources/jay-choi-9-tips]]). 이유는 단순한 UX 가 아니라 **컨텍스트 오염 차단**: 실패한 시도가 컨텍스트에 남아 있으면 Claude 가 그 실패에 계속 영향받음.

## 작동 방식

```
대화 중 Esc Esc → 메시지 지점 선택 UI → 선택 시점 이후 모든 기록 삭제
```

여러 프롬프트가 쌓인 세션에서만 의미가 있다. 단일 프롬프트 세션에서는 되감을 대상이 없음 ([[concepts/plan-mode#관련-단축키]]).

## 왜 *"메시지 다시 보내기"* 보다 좋은가

### 안티패턴 — 실패 위에 보정 메시지 쌓기

```
사용자: "X 를 A 방법으로 구현해 줘"
Claude: (A 시도, 실패)
사용자: "A 말고 B 로 해 줘"
Claude: B 시도
```

문제: **A 시도와 그 실패 출력이 컨텍스트에 그대로 남는다**. Claude 가 다음 답변에서:

- 실패한 A 의 잔재를 인용
- *"이전 시도와 달리..."* 같은 비교적 사고에 토큰 낭비
- A 의 잘못된 가정에 여전히 영향받음

### 올바른 패턴 — 리와인드 후 다시

```
사용자: "X 를 A 방법으로 구현해 줘"
Claude: (A 시도, 실패)
사용자: [Esc Esc, 메시지 시점 선택]  ← A 시도 흔적 전부 삭제
사용자: "X 를 B 방법으로 구현해 줘"
Claude: (B 시도, A 의 영향 0)
```

결과물이 훨씬 깨끗하게 나옴. 이는 [[concepts/fresh-context-principle|신선한 컨텍스트 원칙]] 의 미세 단위 적용.

## 동반 명령어

| 명령어 | 용도 | 단위 |
|---|---|---|
| `Esc Esc` (Rewind) | 특정 메시지 이후 삭제 | **부분** — 한 시도만 도려냄 |
| `/clear` | 전체 히스토리 초기화 | **세션 종료** — 작업 끝났을 때 |
| `/compact` | 지금까지 대화를 요약으로 압축 | **세션 연장** — 토큰만 줄이고 계속 |

### `/compact` 의 방향 지시

`/compact` 만 호출하면 Claude 가 *알아서* 요약 → 중요한 부분이 누락되거나 사용자가 원치 않는 강조가 들어감. 항상 **방향을 직접 지시**:

```
/compact 이번엔 A 에 집중하고 B 는 버려
/compact 결정한 아키텍처와 다음 작업만 남기고 디버깅 로그는 제거
```

이는 *"AI 한테 명시적으로 시켜라"* 원칙 ([[sources/jay-choi-9-tips#part-2--해야-할-것-3가지]]) 의 응용.

## 다른 패턴과의 관계

- [[concepts/plan-mode]] — Plan 세션과 구현 세션을 분리하면 리와인드가 덜 필요해짐 (애초에 컨텍스트가 작음)
- [[concepts/context-engineering]] — 4번째 축 *세션 위생* 의 미세 단위 도구
- [[concepts/fresh-context-principle]] — 같은 원칙의 다른 도구

## 한 줄 정의

> **실패한 시도가 컨텍스트에 남지 않도록 도려내는 가위.**

## 관련 페이지

- [[concepts/fresh-context-principle]]
- [[concepts/context-engineering]]
- [[concepts/plan-mode]]
- [[concepts/claude-md]]

## 출처

- [[sources/jay-choi-9-tips]] — *"컨텍스트 관리 잘하는 사람의 단 하나의 습관"* 강조 + `/compact` 방향 지시 사례
- [[sources/claude-code-2h-mastery]] — Esc×2 단축키 소개
