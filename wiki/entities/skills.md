---
title: Skills (Claude Code)
type: entity
tags: [claude-code, feature, extension, manual]
created: 2026-04-22
updated: 2026-04-22
sources: [[claude-code-study-notes]]
---

# Skills

## 한 줄 정의

Claude Code 의 확장 기능. **AI 에게 주는 업무 매뉴얼** — 특정 작업 유형의 절차·규약을 마크다운으로 기술해 필요 시 Claude 가 참조하게 한다.

## 왜 좋은가

- **비활성 시 컨텍스트 거의 안 먹음** — `/memory` 나 MCP 대비 가벼움 (l.134)
- **간단 작업엔 MCP 보다 Skills 가 적절** — MCP 는 시작부터 컨텍스트 점유
- **로컬 파일**이라 버전 관리·공유 쉬움

## BEFORE / AFTER — 왜 Skills 인가

**BEFORE (매번 지시)** — 사용자가 매 세션마다 같은 규칙을 말로 반복:

- "PR 올릴 때 체크리스트 확인해줘"
- "테스트 코드 작성 규칙 지켜줘"
- "코드 리뷰할 때 이 기준으로…"
- "새 컴포넌트 만들 때 패턴은…"

**AFTER (매뉴얼 1번)** — `SKILL.md` 한 파일이 위 4 가지를 자동 트리거:

```
SKILL.md
  ├─ PR 체크리스트
  ├─ 테스트 작성
  ├─ 코드 리뷰
  └─ 컴포넌트 생성
```

(스크린샷: `![[스크린샷 2026-04-17 오후 5.20.38.png]]`)

## 프롬프트 vs Skills 비교

| 항목 | 💬 프롬프트 | 📘 Skills |
|---|---|---|
| 재사용성 | 매번 재입력 | 파일로 영구 보존 |
| 일관성 | 매번 다를 수 있음 | 항상 동일한 품질 |
| 팀 공유 | 공유 어려움 | Git 으로 공유 |
| 트리거 방식 | 수동 입력 | 자동 감지 & 실행 |

(스크린샷: `![[스크린샷 2026-04-17 오후 5.21.24.png]]`)

→ Skills 는 [[compound-engineering]] 의 핵심 자산. 한 명의 베스트 프랙티스가 SKILL 로 박제되면 팀 전체가 즉시 같은 품질을 사용 가능.

## 폴더 구조

```
.claude/skills/
└─ ppt-generator/             ← 스킬 폴더
   ├─ SKILL.md                ← 핵심 파일 (필수)
   ├─ template.md             ← Claude 가 채울 템플릿 (선택)
   ├─ examples/
   │   └─ sample.md           ← 예제 출력 (선택)
   └─ scripts/
       └─ generate.py         ← 실행 가능한 스크립트 (선택)
```

> ⚠️ **핵심 포인트**: `SKILL.md` 의 `description` 필드가 **스킬 매칭의 핵심**. 사용자 입력과 description 을 비교해서 자동으로 스킬을 선택한다.

(스크린샷: `![[스크린샷 2026-04-17 오후 5.23.32.png]]`)

## 좋은 description 의 3 가지 조건

| 등급 | 예 | 평가 |
|---|---|---|
| ❌ 나쁨 | "문서를 생성하는 스킬" | 너무 추상적. Claude 가 언제 사용할지 판단 불가 |
| 🟡 보통 | "PPT 발표자료를 자동 생성합니다" | 좀 더 구체적이지만, 트리거 표현 없음 |
| ✅ 좋음 | "PPT 발표자료 자동 생성. 'PPT 만들어줘', '발표자료 작성', '슬라이드 제작' 요청 시 트리거." | 핵심 기능 + 트리거 표현 3 개 포함 |

**좋은 description 의 3 가지 조건**:

1. **핵심 기능 첫 문장** — 무엇을 하는지 한 줄
2. **트리거 표현 3 개 이상** — 사용자가 실제로 입력할 자연어 표현
3. **차별성** — 비슷한 다른 스킬과 구별되는 포인트

(스크린샷: `![[스크린샷 2026-04-17 오후 5.28.40.png]]`)

## 만드는 두 가지 방법

### 방법 1 — 수동으로 직접 만들기

```bash
# 1. 폴더 생성
mkdir -p ~/.claude/skills/ppt-generator

# 2. SKILL.md 작성
code ~/.claude/skills/ppt-generator/SKILL.md
```

### 방법 2 — `skill-creator` 플러그인 (추천)

자연어로 스킬 요청:

```
"PPT 발표자료를 자동 생성하는 스킬을 만들어줘"
```

데모 플로우:

```
"PPT 스킬 만들어줘"
  → skill-creator 자동 트리거
  → SKILL.md 생성 완료
  → "PPT 만들어줘"
  → .pptx 발표자료 출력
```

(스크린샷: `![[스크린샷 2026-04-17 오후 5.29.01.png]]`)

## MCP 와의 경계

| 판단 | 선택 |
|---|---|
| 외부 시스템 실시간 연결 필요 | [[mcp]] |
| 로컬 매뉴얼·규약·절차 | **Skills** |
| 자주 안 쓰지만 있으면 좋은 능력 | **Skills** (비활성 시 무부담) |
| 커스텀 깊이 필요 | 커스텀 MCP |

## 이 vault 에서의 관련성

- 이 위키의 [[wiki-operations|Ingest/Query/Lint]] 자체가 Skills 로 포팅 가능 — 각 오퍼레이션을 별도 Skill 파일로 분리하면 [[context-as-king|LazyLoading]] 극대화

## 관련 엔티티

- [[claude-code]] — 호스트 도구
- [[mcp]] — 보완적·대립적 확장 수단
- [[sub-agents]] — 또 다른 확장 수단

## 근거 소스

- [[claude-code-study-notes]] l.126-139
