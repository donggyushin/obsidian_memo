---
title: Agent Teams (Claude Code, COMING SOON)
type: entity
tags: [claude-code, agent, team, multi-agent, roadmap]
created: 2026-04-22
updated: 2026-04-22
sources: [[claude-code-study-notes]]
---

# Agent Teams

## 한 줄 정의

Claude Code 의 **다중 에이전트 조직화 기능 (예고 단계, COMING SOON)**. 현재의 단방향 Main → Sub-agent 구조를 넘어, 에이전트들이 **양방향으로 협업**하는 팀 토폴로지를 지원하는 차세대 기능.

## 현재 vs 미래 (스크린샷 핵심)

### 현재 — 단방향 (Main → Sub)

```
        Main (Coordinator)
       /  |   |  \
    Sub A Sub B Sub C Sub D
```

- 메인 Claude 가 모든 결과를 받아 통합
- Sub-agent 끼리 직접 통신 불가
- 단일 책임자(Main) 가 워크플로우 통제

### 미래 — 양방향 Teams (Agent ↔ Agent)

```
   Agent A ←→ Agent B
      ↕  ╲  ╱  ↕
   Agent C ←→ Agent D
```

- 에이전트끼리 **상호 통신 가능**
- 메시 토폴로지 — 모든 에이전트가 모든 에이전트와 연결
- 메인의 단일 통제 없이 **분산 협업** 가능

## 설계 함의 (예측)

> 추론: 양방향 통신은 강력하지만 새로운 위험 동반:

- **합의 알고리즘 필요** — 누가 최종 결정? 다수결? 우선순위?
- **순환 호출 차단** — A → B → A 무한 루프 방지 메커니즘 필수
- **컨텍스트 폭발** — 에이전트마다 컨텍스트 + 통신 로그까지 누적
- **디버깅 난도 ↑** — 누가 무엇을 트리거했는지 추적 어려움

## [[sub-agents|Sub-Agent]] 와의 관계

Agent Teams 는 [[sub-agents]] 의 **상위 진화형**. 현재 Sub-agent 의 4 가지 장점(병렬 처리·컨텍스트 보호·전문화·재사용) 은 그대로 유지되면서, 거기에 **에이전트 간 직접 협업** 차원이 추가됨.

| 비교 | Sub-Agents (현재) | Agent Teams (미래) |
|---|---|---|
| 토폴로지 | 단방향 (Main → Sub) | 메시 (Agent ↔ Agent) |
| 통합 책임 | 메인 Claude | 분산 또는 합의 |
| 가장 적합한 작업 | 독립 병렬 검증 | 상호 의존적 협업 (페어 코딩, 토론) |

## [[advisor-pattern|Advisor 패턴]] 과의 관계

> 추론: Advisor 는 Bottom-up (Sonnet 실행 → Opus 조언만), Agent Teams 는 **수평적 협업**. 두 모델은 다른 차원이라 공존 가능. 미래에는 "Sonnet 실행 + Opus Advisor + Sonnet 동료 에이전트들" 같은 하이브리드도 그릴 수 있음.

## 현재 사용 가능한 우회 경로

`oh-my-claudecode` (OMC) 의 `/oh-my-claudecode:team` 스킬이 이미 **CLI-팀 런타임**으로 다중 워커를 tmux 패널에서 병렬 운영. Claude Code 네이티브 Agent Teams 가 출시되기 전까지 OMC 가 **임시 다리** 역할.

→ [[oh-my-claudecode]]

## 관련 엔티티

- [[claude-code]] — 호스트 (출시 시)
- [[sub-agents]] — 직계 선조
- [[oh-my-claudecode]] — 현재의 우회 구현체
- [[skills]] — 팀 단위로 호출 가능한 능력 단위

## 관련 개념

- [[wat-framework]] — Agents (A) 층의 차세대 형태
- [[compound-engineering]] — 팀 단위 협업의 토대 위에서 작동

## 근거 소스

- [[claude-code-study-notes]] l.148
- 스크린샷: `![[스크린샷 2026-04-17 오후 6.32.40.png]]` (예고: Agent Teams)
