---
title: WAT 프레임워크 (Workflows · Agents · Tools)
type: concept
tags: [framework, claude-code, agents, tools, workflow]
created: 2026-04-22
updated: 2026-04-22
sources: [[claude-code-study-notes]]
---

# WAT 프레임워크

Claude Code 에서 일을 조합하는 3 층 멘탈 모델. 각 층은 **관심사 분리**로 서로 다른 생산성 자원을 다룬다.

## 구성

### W — Workflows (작업 흐름)

- **핵심 도구**: `todo.md` (로컬 마크다운)
- 장기·중기 작업의 단계·의존성을 문서로 박제
- Claude 가 스스로 "다음 할 일"을 참조할 수 있도록
- 출처 원문: *"작업 흐름을 정의 → AI가 스스로 판단하고 복구 → 도구 조합"* (l.112)

### A — Agents (에이전트)

- **핵심 도구**: `/agents` 명령, 서브에이전트, [[sub-agents]]
- **장점**: 메인 컨텍스트와 분리된 독립 컨텍스트 → 컨텍스트 오염 방지
- **권장 모델**: Sonnet (opus 비권장, 병렬 서브에이전트 시 특히)

#### Agents 층의 두 가지 핵심 패턴

**1. Self-Healing 메커니즘** — 에이전트가 자기 실수를 스스로 복구하는 루프:

```
에러 발생  →  로그 읽기  →  원인 파악  →  코드 수정  →  재실행 ↻
```

→ [[harness-engineering|Harness Engineering]] 의 둘째 기둥(자동 교정 루프) 와 같은 사상.

**2. Sub-agent 병렬 처리** — 메인 Claude(Coordinator) 가 하위 에이전트에 작업 분배:

```
        메인 Claude (Coordinator)
       /            |             \
   Sub A           Sub B          Sub C
   테스트 작성·실행  문서 업데이트   린트 & 타입 체크
```

⏱ **체감 효과**: 순차 실행 10 분 → 병렬 3~4 분으로 단축.

(스크린샷: `![[스크린샷 2026-04-17 오후 3.20.42.png]]`)

→ [[sub-agents]], [[agent-teams]]

### T — Tools (도구)

- **핵심 도구**: MCP·Skills·bash 스크립트·Hooks·커스텀 도구
- [[skills]] 가 간단한 작업에는 MCP 보다 더 가벼운 선택
- [[mcp]] 는 양날의 검 — 커스텀 MCP 가 최적

#### Tools 의 핵심 원칙: **원자성 (atomicity)**

도구 하나는 한 가지 일만 한다. 거대한 통합 스크립트보다 **작은 단위 스크립트들의 조합**이 디버깅·재사용·실패 격리에 압도적으로 유리.

| ❌ 나쁜 예 — 거대한 배포 스크립트 | ✅ 좋은 예 — 원자적 도구들 |
|---|---|
| `# deploy-all.sh (200줄짜리 괴물)`<br>빌드 → 테스트 → DB 마이그레이션<br>→ 배포 → 알림 → 롤백 처리... | `scripts/build.sh   # 빌드만`<br>`scripts/test.sh    # 테스트만`<br>`scripts/migrate.sh # DB 마이그레이션만`<br>`scripts/deploy.sh  # 배포만`<br>`scripts/notify.sh  # 알림만` |

**Tools 층의 3 가지 형태**:

- 🖥️ **Bash 스크립트** — 가장 가볍고 직접적
- 🔌 **MCP 서버** — 외부 시스템 연결 ([[mcp]])
- 🪝 **Hooks** — 이벤트 기반 자동화 ([[hooks]])

(스크린샷: `![[스크린샷 2026-04-17 오후 3.21.17.png]]`)

## 왜 이 분리가 중요한가

각 층이 **서로 다른 자원**을 다룸:

| 층 | 다루는 자원 | 실패 모드 |
|---|---|---|
| Workflows | **기억 · 지속성** | todo.md 없으면 세션 간 단절 |
| Agents | **컨텍스트 · 병렬성** | 메인에 다 밀어넣으면 오염·윈도우 터짐 |
| Tools | **능력 · 외부 세계** | 과도 연결 시 시작부터 컨텍스트 잠식 |

## 이 위키와의 대응

- 이 위키 시스템 = Workflows 의 구체적 인스턴스 (Ingest/Query/Lint 가 `todo.md` 의 자리를 대신)
- [[wiki-operations]] — 위키 Ops 자체가 WAT 의 W 에 해당
- [[three-layer-architecture]] — 본 프레임워크와 유사하게 **관심사를 3 층으로 분리**한다는 점에서 정신적 동류

## Harness Engineering 과의 차이

[[harness-engineering]] 는 **통제·규율**에, WAT 는 **조합·구성**에 방점. 같은 재료(CLAUDE.md · Hooks · Agents · Skills · MCP)를 서로 다른 앵글로 정리. 실전에선 둘 다 필요:

- WAT 관점: "이 기능을 **어떻게 조립**할까?"
- Harness 관점: "AI 가 **어떻게 못하게** 만들까?"

## 관련 개념

- [[harness-engineering]] — 동일 재료의 통제 축
- [[context-as-king]] — WAT 의 궁극 목적은 컨텍스트 최적화
- [[plan-mode-workflow]] — Workflows 의 가장 기본 루프

## 근거 소스

- [[claude-code-study-notes]] l.109-124
