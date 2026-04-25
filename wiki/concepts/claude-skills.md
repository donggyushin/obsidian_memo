---
title: "Claude Skills — AI 업무 매뉴얼"
type: concept
tags: [claude-code, skills, automation]
created: 2026-04-22
updated: 2026-04-25
sources: [claude-code-2h-mastery, jay-choi-9-tips]
aliases: ["Skills", "스킬", "skill.md", "Agent Skills"]
---

# Claude Skills

## 핵심 요약

Skills 는 **반복 작업을 자동화하는 재사용 단위**. `skill.md` 파일 하나에 워크플로우를 적어 두면 Claude 가 적절한 순간에 자동 트리거한다. **2단계 로딩** 덕분에 CLAUDE.md 보다 훨씬 가볍다 (평소엔 description 만 로드, 본문은 호출 시). 공개 표준인 **Agent Skills 규격** 을 따르므로 다른 AI 도구에서도 재사용 가능.

## 프롬프트 vs Skills

| 관점 | 프롬프트 | Skills |
|---|---|---|
| 재사용성 | 매번 복붙 | 한 번 작성 → 자동 트리거 |
| 일관성 | 같은 요청도 결과 상이 | 포맷/체크리스트로 품질 강제 |
| 확장성 | 텍스트 덩어리로만 | 참조 문서/스크립트/서브에이전트 통합 |
| 적합 상황 | 일회성 작업 | 반복·복잡한 업무 |

**정의**: 프롬프트 = 일회성 지시, Skills = 재사용 가능한 업무 시스템.

## 파일 구조

```
.claude/skills/
└── ppt-generator/
    ├── skill.md              # 필수 진입점
    ├── sample.md             # 참조 파일 (선택)
    └── generate.py           # 지원 스크립트 (선택)
```

`skill.md` 구성:

```markdown
---
name: ppt-generator
description: PPT 발표자료 자동 생성. PPT 만들어줘 / 슬라이드 제작 / 발표자료 작성 요청 시 트리거.
---

# PPT 생성기

## 워크플로우

1. 입력 수집 (주제, 청중, 발표 시간)
2. 슬라이드 기획 (표준 구조)
3. 콘텐츠 작성 (청중 맞춤 조정)
4. PPTX 생성 (16:9, 컬러 팔레트, 폰트)
5. 검증 체크리스트

## 검증

- 슬라이드 수가 발표 시간에 적절한가?
- 핵심 메시지 포함되었는가?
- 흐름이 자연스러운가?
```

## 2단계 로딩 (진짜 핵심)

### 스테이지 1 — Description

이름 + 한 줄 설명만 **상시 로드**. 50~100 bytes 수준.

### 스테이지 2 — 전체 프롬프트

Claude 가 "이 요청엔 이 스킬이 적합"이라고 판단하는 순간에만 **on-demand** 로딩. 참조 파일도 필요할 때만 읽음.

### CLAUDE.md 와의 차이

| | CLAUDE.md | Skills |
|---|---|---|
| 로드 | 매 세션 자동 | 필요 시 on-demand |
| 크기 | 전체가 로드됨 | description 만 상시 |
| 적합 | 프로젝트 전체 규칙 | 특정 작업 매뉴얼 |

> [!tip] 역할 분담
> - **CLAUDE.md** → 항상 지켜야 할 핵심 규칙
> - **Skills** → 가끔 쓰는 참조 자료 · 반복 작업

## Description 품질이 트리거 성공을 결정

Claude 는 단순 키워드 매칭이 아니라 **description 의미 해석** 으로 트리거 여부 판단.

### 나쁜 예

```
description: 문서를 생성하는 스킬
```

너무 추상적 → Claude 가 언제 써야 할지 모름.

### 좋은 예

```
description: PPT 발표자료 자동 생성. "PPT 만들어줘" / "발표자료 작성" / "슬라이드 제작" 같은 요청에 사용.
```

**체크리스트**:
- 핵심 기능이 첫 문장에 있는가?
- 사용자가 실제로 쓸 법한 표현이 3개 이상인가?
- 비슷한 다른 스킬과 구별되는가?

## 생성 방법

### 1) 수동 작성 (비권장)

직접 `skill.md` 작성. 번거로움.

### 2) Skill Creator 플러그인 (권장)

Claude 플러그인 마켓플레이스에서 `skill-creator` 설치 후:

```
"PPT 발표자료 자동 생성하는 스킬을 만들어줘.
발표 주제/청중/시간 입력하면 슬라이드 구성 나와야 해.
아웃풋은 PPTX 파일로 생성, 자체 검증 체크리스트 포함."
```

→ Skill Creator 가 자동 트리거되어 `skill.md` 생성. 처음부터 완벽할 필요 없이 **점진적으로 정교화**.

## 채택 순서 — 남의 스킬 먼저

[[sources/jay-choi-9-tips]] 의 권고 순서:

1. **남이 만든 좋은 스킬을 먼저** 가져다 쓴다. GitHub 에 유용한 스킬 多.
2. 본인이 **반복하는 작업이 있을 때만** 자기 스킬을 만든다.
3. 처음부터 풀 세팅하지 말 것 — 보리스([[entities/boris-cherny]]) 도 vanilla 사용. → [[concepts/harness-engineering#vanilla-우선-원칙]].

> 스킬은 유지 비용이 거의 없으면서도 유용하기 때문에 어느 선에선 자산이라고도 할 수 있습니다.  
> — [[sources/jay-choi-9-tips]]

명시적으로 시켜야 작동: 스킬을 만들어 놨어도 Claude 가 자동으로 쓴다는 보장은 없다. 트리거를 위해 description 의 명확성 (위 섹션) + 사용자의 명시적 호출 ([[concepts/plan-mode#명시적-지시-원칙]]) 모두 필요.

## 저장 범위

| 위치 | 사용 범위 |
|---|---|
| `.claude/skills/` (프로젝트) | 해당 프로젝트만 |
| `~/.claude/skills/` (개인) | 모든 프로젝트 |
| **플러그인 배포** | 조직 전체 공유 |
| **CLI 일회성** | 해당 세션만 (컨텍스트 절약) |

## 번들 스킬

Claude Code 내장 스킬. 예: `simplify` (코드 품질 자동 검토). 자주 쓰는 기능은 이미 제공됨.

## 왜 MCP 가 아닌 Skills 를 써야 하나

간단한 기능 래핑이라면 MCP 보다 **로컬 스크립트를 스킬로 감싸는** 쪽이 훨씬 가볍다. MCP 는 연결만 해도 전체 도구 설명이 매 세션 로드되지만, 스킬은 description 만 상시 → [[concepts/context-engineering#3-mcp-다이어트]].

## 관련 페이지

- [[concepts/context-engineering]] — 2단계 로딩의 토큰 절약 원리
- [[concepts/fresh-context-principle]] — 도구 단위 적용 사례
- [[concepts/claude-md]] — 항상 로드되는 규칙의 영역
- [[concepts/conditional-rule-loading]] — 다른 트리거 방식 (파일 패턴 매칭 vs description 의미 해석)
- [[concepts/subagents]] — 실행 중 격리가 목적일 때
- [[concepts/hooks]] — 이벤트 기반 자동화 (Skills 는 요청 기반)
- [[entities/boris-cherny]] — vanilla 우선 원칙

## 출처

- [[sources/claude-code-2h-mastery]] — 심화편 "스킬" 섹션
- [[sources/jay-choi-9-tips]] — 채택 순서 (남의 스킬 먼저) + 명시적 호출 원칙
