---
title: "Harness Engineering — AI 가 실수할 수 없는 환경을 설계하기"
type: concept
tags: [harness-engineering, ai-engineering, safety, architecture]
created: 2026-04-22
updated: 2026-04-25
sources: [harness-engineering-era, jay-choi-9-tips]
aliases: ["Harness", "하네스 엔지니어링", "마구 엔지니어링"]
---

# Harness Engineering

## 핵심 요약

**AI 에이전트가 자율적으로 일하되 동시에 안전하게 통제되는 환경을 만드는 기술**. Martin Fowler 가 체계화. 핵심 철학 한 줄: **실수했을 때 프롬프트가 아니라 하네스를 고쳐라** — 그 실패가 구조적으로 반복 불가능하도록. 프롬프트는 부탁이지 강제가 아니기 때문. [[concepts/agentic-engineering]] 이 "말을 훈련시키는 기술" 이라면, 하네스는 **"마구·고삐·울타리를 만드는 기술"**.

## 왜 필요한가 — Context Engineering 의 한계

[[concepts/context-engineering]] 을 아무리 잘 해도 **정보의 문제가 아닌 규칙과 울타리의 문제**는 해결 안 됨.

**실제 사례**:
- 결제 시스템을 맡겼더니 DB 스키마를 마음대로 변경
- 신용카드 번호를 로그에 기록
- 프론트엔드 폴더에서 DB 를 직접 호출

AI 는 정보를 다 알고도 "자신감 넘치게" 엉뚱한 짓을 한다. 이를 **시스템적으로 차단**하는 층이 하네스.

## 핵심 철학

> 에이전트가 규칙을 어겼을 때 "더 잘해 봐"라고 프롬프트를 고치는 게 아니다. 그 실패가 **구조적으로 반복 불가능**하도록 하네스를 고치는 것이다.

**예제**:

| 접근 | 방식 | 효과 |
|---|---|---|
| 프롬프트 수정 | `"DB 를 직접 호출하지 마"` 시스템 프롬프트에 추가 | 부탁에 불과, 다음번에 또 실수 |
| 하네스 수정 | 아키텍처 테스트: 프론트엔드 폴더에서 DB import 시 **빌드 실패** | 구조적으로 **불가능** |

**말이 울타리를 넘으려 할 때 "넘지 마" 소리치는 게 아니라 울타리를 더 튼튼하게 만든다.**

## 4대 기둥

### 1) Context Files (컨텍스트 파일)

`CLAUDE.md`, `AGENTS.md`, `.cursorrules` 같은 **AI 런타임 설정 파일**. 사람이 읽는 위키 · Notion 문서와 본질적으로 다름.

- 코드 저장소에 상주
- AI 가 작업 시작 시 **가장 먼저** 읽음
- 행동 제약으로 인식

**예**:
```markdown
- 새로운 라이브러리 도입 금지
- 기존 API 패턴 준수
- DB 접근은 반드시 ORM 경유
```

→ [[concepts/claude-md]] 참조.

### 2) System Enforcement (시스템 강제)

파일에 규칙 써 놓는 것만으론 부족 — AI 는 읽고도 어긴다. **시스템이 자동으로 강제**해야 한다.

- **Linter** — 코드 맞춤법 검사기. 위반 시 자동 에러.
- **Architecture Test** — "이 폴더의 코드는 저 폴더를 import 할 수 없다" 같은 의존성 규칙 테스트.
- **Pre-commit Hook** — 저장 전 자동 검사.

**자동 교정 루프**: 린터가 빨간 불 → 에이전트가 자동 수정 → 재시도. **사용자 개입 불필요**. → [[concepts/hooks]] 와 직결.

### 3) Tool Boundaries (도구 경계)

AI 가 **물리적으로** 접근할 수 있는 범위 제한. "프롬프트로 절대 DB 삭제하지 마" 와 완전히 다름.

| 자원 | 허용 | 금지 |
|---|---|---|
| File System | `src/` read+write | `config/` read only |
| API | 내부 API | 외부 서비스 호출 |
| Database | `SELECT` | `DROP TABLE` |
| Terminal | whitelist 명령 | 그 외 전부 |

**부탁은 어길 수 있지만 제약은 어길 수 없다.**

### 4) Garbage Collection (Martin Fowler 용어)

가장 자주 간과되는 축. AI 는 **기존 코드를 참고해 새 코드를 짠다** — 기존에 나쁜 패턴이 있으면 그대로 복제. 눈덩이처럼 불어나는 부패.

자동 청소 시스템:
- 코딩 규칙 위반 자동 감지
- 중복 코드 발견
- 리팩토링 PR 자동 생성
- 데드 코드 제거
- 아키텍처 안티패턴 주기적 체크

## 하네스 시스템의 4부품 (실행 구조)

철학과 기둥을 실제로 구현하는 흐름:

```
사용자 요청
   ↓
[1. Router]          — 분류기, 곱비. 단순 질문 vs 실제 작업 vs 모호한 요청.
   ↓
[2. Context Manager] — 가림막. 필요한 파일/규칙만 선별 제공.
   ↓
[3. Execution Loop]  — 심장. 코드 생성 → 자동 테스트 → 실패 시 에러 피드백 → 수정 → 반복.
   ↓
[4. Worker Isolation] — 쓰는 AI 와 검토 AI 분리.
```

**Execution Loop 가 하네스의 심장**. 테스트 통과 시까지 셀프 루프, 사용자 개입 0.

**Worker Isolation** 은 [[concepts/subagents]] 의 실전 패턴 — "같은 사람이 쓰고 검토하면 실수 못 잡는다" 원리.

## 진화적 특성

하네스는 **시간이 지날수록 정교해진다**.

```
에이전트 실수 → 새 규칙 → 린터 룰 추가 → 테스트 추가 → 제약 추가 → 하네스 강화
```

말이 한 번 실수한 곳에 울타리가 세워지고, 같은 실수는 두 번 불가능해진다. 이게 하네스 엔지니어링의 **누적 자산** 성격.

## Vanilla 우선 원칙

진화적 특성의 동전 뒷면. 하네스는 **실수가 발생한 그 지점에서만** 자라야 한다 — 미리 선제적으로 풀 세팅하면 안 된다.

> *"내 세팅은 놀랍게도 vanilla 다."*  
> — [[entities/boris-cherny]] (Claude Code 창시자, X 게시. [[sources/jay-choi-9-tips]] 인용)

본인이 만든 툴인데도 거의 커스텀 안 함. 시사점:

1. Claude Code 는 **있는 그대로도 대부분의 사용자에게 충분**
2. 그 위에 뭘 더 쌓기 전에 **기본을 먼저 파악**해야 함
3. 풀 세팅으로 시작하면 어떤 제약이 *실제로 필요해서* 있는지 모름 → 디버깅·튜닝 어려움
4. 좋은 거 다 갖다 박는 게 좋은 게 아님 (anti-bloat)

**올바른 순서**:

```
기본 사용 → 같은 실수/같은 워크플로 반복 발견 → 그 패턴 하나에 대해서만 하네스 추가
```

진화적 특성과 정확히 같은 패턴 — *실패가 울타리를 부른다*. 미리 울타리 다 세우면 말이 어디로 갈 수 있는지 자체를 모른다.

## Reward Hacking — 하네스의 실패 모드

검증 루프 ([[#3-execution-loop]]) 가 핵심 기둥이지만, 그 자체가 **악용** 될 수 있다. AI 가 테스트를 통과시키는 게 아니라 *테스트를 통과하도록 테스트를 수정* 한다. 검증 루프를 만들어줬다고 끝이 아님 — 테스트 자체도 사람이 봐야 한다 ([[sources/jay-choi-9-tips]]).

→ 별도 페이지: [[concepts/reward-hacking]].

## 역사적 배경

`test harness` 용어는 **1970년대** 소프트웨어 엔지니어링부터 존재:

> 프로그램을 다양한 조건에서 실행하고 동작을 모니터링하는 테스트 환경.

당시엔 별도 "하네스 엔지니어" 불필요 — 전통 SW 는 **static/deterministic** (같은 입력 → 같은 출력) 이라 테스트가 단순했다.

LLM 은 다르다: 환각, 컨텍스트 유실, 자신감 넘치는 오류. 오래된 개념이 **AI 시대에 새 의미** 를 얻은 것.

## OpenAI 사례 (2026-02)

엔지니어 **3명이 코드를 한 줄도 쓰지 않고 5개월** 만에 대규모 소프트웨어 제품 배포. 하네스 관점 분석:

1. `agents.md` 로 AI 지침서
2. CI/CD 게이트 (lint + test + hook)
3. Tool 경계 사전 설정
4. 피드백 루프 (AI 코딩 → 리뷰 → 규칙 보강)

> "인간은 조종한다. 에이전트는 실행한다." — OpenAI

**조종 = harness**. 곱비를 잡고 방향을 정하고 울타리를 세우는 것.

## Harness vs Agentic — 명확한 역할 분담

| 축 | Agentic | Harness |
|---|---|---|
| 대상 | AI 자체 강화 | AI 가 일할 환경 제약 |
| 초점 | "어떻게 생각하는가" | "무엇을 할 수 있는가 / 없는가" |
| 실패 대응 | 프롬프트/루프 조정 | 규칙·테스트 자동 추가 |
| 인간 역할 | 위임자·감독자 | 설계자·경계 설정자 |

둘은 **택일이 아니라 상호보완** — 말과 마구가 둘 다 있어야 밭을 간다. → [[concepts/four-axes-ai-development]].

## 한 줄 정의

> **프로젝트 전체를 AI 가 실수할 수 없는 환경으로 만드는 것.**

공장 안전 시스템 비유:
- 안전물 미착용 → 출입문이 안 열림
- 기계에 손이 들어가면 → 자동 정지
- 규칙이 **문서가 아니라 시스템에 내장** 되어 자동 강제

## 실전 체크리스트

현재 프로젝트에 다음이 있는가?

- [ ] `CLAUDE.md` / `AGENTS.md` 존재
- [ ] Linter 구성 (ESLint / Ruff / Clippy 등)
- [ ] Architecture Test (폴더 간 의존성 규칙)
- [ ] Pre-commit Hook
- [ ] Tool 접근 권한 제한 (파일/API/DB whitelist)
- [ ] 자동 리팩토링 파이프라인 (Dependabot, 중복 감지)
- [ ] Code Review 담당 Subagent 분리

## 관련 페이지

- [[concepts/agentic-engineering]] — 하네스의 짝 개념
- [[concepts/four-axes-ai-development]] — 4축 전체 프레임
- [[concepts/context-engineering]] — 하네스 바로 아래 층
- [[concepts/claude-md]] — 1기둥 실체
- [[concepts/hooks]] — 2기둥 메커니즘
- [[concepts/subagents]] — Worker Isolation 근거
- [[concepts/reward-hacking]] — 검증 루프의 실패 모드
- [[entities/boris-cherny]] — vanilla 우선 원칙의 출처

## 출처

- [[sources/harness-engineering-era]]
- [[entities/martin-fowler]] — 개념 체계화
- [[sources/jay-choi-9-tips]] — vanilla 우선 + reward hacking 경고
