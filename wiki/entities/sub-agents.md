---
title: Sub-Agents (Claude Code)
type: entity
tags: [claude-code, feature, agent, parallel, context]
created: 2026-04-22
updated: 2026-04-22
sources: [[claude-code-study-notes]], [[advisor-pattern-note]]
---

# Sub-Agents

## 한 줄 정의

Claude Code 의 **독립 컨텍스트 에이전트**. 메인 세션의 컨텍스트를 점유하지 않으면서 병렬로 작업 수행. `/agents` 로 생성·관리.

## 핵심 이점

- **메인 컨텍스트 비오염** — 긴 탐색·많은 파일 읽기를 서브에이전트에 위임하면 메인은 깨끗하게 유지
- **병렬 실행** — 독립 과제 여러 개 동시 진행 가능
- **실행 중 가장 효율적** (l.143)

## 주의

- **opus 사용 비권장** (l.143) — 비용·속도 면에서 서브에이전트에는 과함
- 병렬로 많이 돌릴 땐 **Sonnet** 이 권장 모델 (l.47)

## 생성 방식

- `/agents` 명령어가 가장 좋음 (l.145)

## Sub-Agent 핵심 장점 4 가지

| 장점 | 의미 |
|---|---|
| 🔀 **병렬 처리** | 여러 작업을 동시에 실행해 전체 소요 시간 단축 |
| 🛡️ **컨텍스트 보호** | 메인 에이전트의 컨텍스트를 오염시키지 않음 |
| 🎯 **전문화** | 각 Sub-agent 에 전문 역할을 부여 |
| ♻️ **재사용** | 한 번 만든 에이전트를 여러 워크플로우에서 활용 |

### Do's ✅

- 독립적인 작업을 병렬로 분배
- Sub-agent 에 명확한 역할과 범위 지정
- 결과를 메인 에이전트에서 통합 처리
- 에러 발생 시 graceful fallback 설계

### Don'ts ❌

- 의존성 있는 작업을 무리하게 병렬화
- 하나의 Sub-agent 에 너무 많은 역할 부여
- Sub-agent 간 직접 통신 시도 (현재 단방향 토폴로지 — [[agent-teams]] 가 미래에 해결 예정)
- 결과 검증 없이 그대로 사용

(스크린샷: `![[스크린샷 2026-04-17 오후 6.21.40.png]]`)

## 언제 쓰나

| 상황 | 서브에이전트 적합 |
|---|---|
| 큰 코드베이스 탐색 | ✅ |
| 독립된 여러 검증 동시 실행 (보안·성능·스타일) | ✅ |
| 작은 단일 작업 | ❌ (오버헤드) |
| 메인의 현재 맥락과 강결합된 편집 | ❌ |

## Advisor 패턴과의 대조 (중요)

Sub-agent 는 **Top-down 오케스트레이션** (Opus 메인 → Sonnet 서브). 반면 [[advisor-pattern|Advisor]] 는 **Bottom-up** (Sonnet 실행 → Opus 조언만). 두 패턴은 대립이 아니라 **상호보완**:

| 상황 | 선택 |
|---|---|
| 넓게 병렬 탐색 필요 | **Sub-Agent** |
| 실행은 싸고 판단만 비싸야 | **[[advisor-pattern\|Advisor]]** |
| 관리 부담 최소화 | Advisor (단일 API) |
| 독립 컨텍스트 격리 | Sub-Agent |

상세 비교 표는 [[advisor-pattern]] 참조.

## WAT 프레임워크 내 위치

[[wat-framework]] 의 **A (Agents)** 층. Workflows 와 Tools 사이에서 "실행 단위"를 담당.

## Agent Teams 로의 확장

- 여러 서브에이전트를 조직화해 **팀**으로 운영 가능 (원 소스 l.148)
- 현재의 단방향 (Main → Sub) 토폴로지를 양방향 메시 (Agent ↔ Agent) 로 확장하는 차세대 기능
- 상세는 → [[agent-teams]]

## 관련 엔티티

- [[claude-code]] — 호스트
- [[skills]] — 대안적 확장
- [[mcp]] — 도구 레이어

## 관련 개념

- [[context-as-king]] — 서브에이전트는 컨텍스트 보존의 핵심 전술
- [[wat-framework]] — A 층의 구체 구현

## 근거 소스

- [[claude-code-study-notes]] l.141-148, l.47
