---
title: "신선한 컨텍스트 원칙"
type: concept
tags: [claude-code, context, performance, principle]
created: 2026-04-25
updated: 2026-04-25
sources: [jay-choi-9-tips, claude-code-2h-mastery, harness-engineering-era]
aliases: ["Fresh Context Principle", "신선한 컨텍스트", "fresh > bloated", "컨텍스트 신선도"]
---

# 신선한 컨텍스트 원칙

## 핵심 요약

> **신선한 컨텍스트가 비대한 컨텍스트를 이긴다.**

[[sources/jay-choi-9-tips]] 가 한 문장으로 못 박은 표어. 직관과 반대로, **정보를 더 많이 주는 것이 더 잘하는 것이 아니다**. LLM 은 그렇게 동작하지 않는다 — 관련 없는 정보가 많을수록 모델이 잘못된 방향으로 빠진다. [[concepts/context-engineering|Context Engineering]] 의 모든 기법이 결국 이 원칙의 응용.

## 왜 직관과 반대인가

### 사람의 직관

> *"정보 많이 주면 더 잘하겠지."*

회의에서 배경 자료를 많이 주면 결정이 더 잘 되는 경험에서 옴. 사람의 작업기억은 능동적으로 관련 정보만 골라낸다.

### LLM 의 실제 동작

LLM 은 **모든 토큰을 같은 비중으로 취급**하지 않는다. 그러나 동시에 **무엇이 관련 있는지를 안정적으로 가려내지도 못한다**. 결과:

- 관련 없는 토큰이 attention 을 분산
- 비슷하지만 부정확한 패턴을 인용
- 길이가 길수록 *중간* 의 중요한 규칙이 무시됨 (lost-in-the-middle)
- 길이에 따라 비용·지연 선형 증가

### 측정된 사례

| 사례 | 결과 |
|---|---|
| CLAUDE.md 길이 | Claude 는 **80% 정도만 따른다**는 통념 — 길수록 중요 규칙 무시 ([[sources/jay-choi-9-tips]]) |
| 47K 단어 CLAUDE.md | Claude 가 느려지고 규칙 무시 시작. 조건부 로딩 재구성 후 루트 메모리 80% 감소 ([[sources/jimcoding-claude-rules]]) |
| MCP 10개 연결 | 사용 여부 무관하게 도구 설명 전부 상시 점유 ([[concepts/context-engineering#3-mcp-다이어트]]) |

## 원칙의 4가지 적용 단위

| 단위 | 적용 도구 | 페이지 |
|---|---|---|
| 파일 (정적) | CLAUDE.md 150~200줄, lazy loading, 폴더 분할 | [[concepts/claude-md]] |
| 도구 (정적) | MCP 다이어트, Skills 의 2단계 로딩 | [[concepts/claude-skills]] |
| 세션 (동적) | 한 세션 = 한 피처, `/clear`, `/compact` | [[concepts/context-engineering#4-세션-위생]] |
| 메시지 (미세 단위) | `Esc Esc` 리와인드 — 실패 시도 도려내기 | [[concepts/rewind-pattern]] |

**같은 원칙이 4단계 줌으로 반복**된다. 가장 큰 단위에서 가장 미세 단위까지 *"필요한 만큼만, 신선하게"*.

## 안티패턴

### 1) 풀 세팅 광시병

좋은 거 다 깔기 → 매 세션이 무거운 컨텍스트로 시작. 보리스조차 **vanilla** 사용 ([[entities/boris-cherny]]). → [[concepts/harness-engineering#vanilla-우선-원칙]].

### 2) 실패 위에 보정 메시지 쌓기

A 시도 실패 → *"B 로 해 줘"* 보내기. A 의 실패 흔적이 컨텍스트에 남아 B 도 영향받음. 해법: 리와인드. → [[concepts/rewind-pattern]].

### 3) 모든 정보를 CLAUDE.md 에 박아넣기

API 스펙 50개 + DB 스키마 40개 → 매 세션 수천 토큰 낭비. 해법: 참조 기반 lazy loading. → [[concepts/claude-md#참조-기반-lazy-loading]].

### 4) 한 세션에 여러 피처

로그인 + 결제 + 알림을 한 세션에서 연달아 → 컨텍스트 누적 오염. 해법: 한 세션 = 한 피처. → [[concepts/context-engineering#4-세션-위생]].

### 5) `/compact` 를 방향 없이 호출

요약 방향을 지시하지 않으면 중요한 부분이 누락. 해법: *"/compact 이번엔 A 에 집중"* 처럼 명시.

## 균형점 — 너무 신선해도 손해

이 원칙이 *"항상 짧게"* 를 의미하진 않는다. **필요한 정보까지 쳐내면** Claude 가 매번 같은 파일을 다시 탐색해 토큰을 더 쓴다 ([[concepts/claude-md]] 의 존재 이유). 균형점:

- *항상 필요한 핵심 규칙* → CLAUDE.md (상시 로드, 단 짧게)
- *가끔 필요한 상세* → 참조 파일 / Skills (lazy 로드)
- *작업별 일회성* → 사용자 프롬프트로 즉시 주입 후 세션 종료 시 폐기

신선도는 **양 = 0** 이 아니라 **양 = 그 작업에 정확히 필요한 만큼** 을 뜻한다.

## 한 줄 정의

> **컨텍스트는 양이 아니라 신호 대 잡음 비로 평가하라.**

## 관련 페이지

- [[concepts/context-engineering]] — 본 원칙의 4가지 기법 묶음
- [[concepts/claude-md]] — 정적 파일 단위 적용
- [[concepts/claude-skills]] — 도구 단위 적용 (2단계 로딩)
- [[concepts/rewind-pattern]] — 메시지 단위 적용
- [[concepts/plan-mode]] — fresh context 로 구현 진입
- [[entities/boris-cherny]] — vanilla 우선 일화

## 출처

- [[sources/jay-choi-9-tips]] — 표어의 1차 근거
- [[sources/claude-code-2h-mastery]] — 4가지 기법 묶음의 종합
- [[sources/harness-engineering-era]] — Context Engineering 을 4축 중 2축으로 위치
