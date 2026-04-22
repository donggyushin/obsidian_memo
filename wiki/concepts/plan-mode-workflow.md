---
title: Plan Mode 워크플로우 (Plan → 검토 → Execute)
type: concept
tags: [workflow, claude-code, tokens, plan-mode]
created: 2026-04-22
updated: 2026-04-22
sources: [[claude-code-study-notes]]
---

# Plan Mode 워크플로우

## 정의

Claude Code 에서 실행 전 **계획 제시 → 사용자 검토 → Execute** 의 3 단계를 강제하는 작업 패턴. `Shift + Tab` 으로 Plan Mode ↔ Accept Mode 를 토글.

## 핵심 루프

```
작업 설명 → Plan 제시 → 검토 & 피드백 → Execute
          ↑                    ↓
          └────── 필요시 리플랜 ──┘
```

## 왜 쓰는가 — 4 가지 실무 이유

| # | 이유 | 설명 |
|---|---|---|
| 1 | **토큰 최적화** | "잘못된 코드 → 수정 요청 → 재생성" 악순환 차단. output 토큰이 가장 비싸므로 한 방에 맞추기 |
| 2 | **컨텍스트 윈도우 보존** | 불필요한 trial & error 로 히스토리 채우지 않음 |
| 3 | **실행 정확도 향상** | Claude 가 사용자 의도를 구조화해서 재기술 → 오해 조기 발견 |
| 4 | **비용 관리** | output 토큰 재생성 회수 감소 |

## 습관화 팁

- **Plan 단계 출력만 보고** 머릿속으로 "이게 내가 원한 거 맞나?" 체크
- 조금이라도 어긋나면 Execute 전에 **피드백 라운드** 추가
- Thinking log 스크롤하며 **잘못된 추론** 발견되면 즉시 `Escape`

## 이 위키와의 관계

- 위키 스키마 [[CLAUDE.md|CLAUDE]] 의 ingest 플로우 **"먼저 요약·라우팅 제안부터"** 조항이 바로 이 원칙의 적용.
- Query 응답 전에 index 후보 페이지를 먼저 제시하고 피드백 받는 것도 Plan-first 확장.

## 실전 워크플로우 (4 단계)

원 노트의 그림이 정의하는 가장 구체적인 운영 패턴:

```
1. Plan Mode 에서 전체 설계
       ↓
2. /clear 또는 새 세션 (Plan 만 머릿속에 남기고 컨텍스트는 비움)
       ↓
3. 첫 번째 단계만 구현
       ↓
4. 완료 → /clear → 다음 단계로
```

핵심: **설계와 실행을 컨텍스트적으로 분리**. 큰 그림은 Plan 단계에서 한 번에 잡고, 실제 코드 작성은 단계마다 새로운 깨끗한 컨텍스트에서 한다. → [[context-as-king|"한 세션 = 한 피쳐"]] 원칙의 가장 직접적 구현.

(스크린샷: `![[스크린샷 2026-04-17 오후 3.08.37.png]]`)

## 관련 개념

- [[context-as-king]] — Plan Mode 는 컨텍스트 보존의 구체적 전술
- [[wat-framework]] — Workflows 레이어의 기본 루프
- [[compound-engineering]] — Plan 결과를 프로젝트 `CLAUDE.md` 에 박제해 팀 전체로 누적시키는 확장

## 근거 소스

- [[claude-code-study-notes]] l.22-34, l.87, l.94-96
