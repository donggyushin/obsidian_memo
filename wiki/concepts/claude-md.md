---
title: "CLAUDE.md — 프로젝트 컨텍스트 허브"
type: concept
tags: [claude-code, context, configuration]
created: 2026-04-22
updated: 2026-04-25
sources: [claude-code-2h-mastery, harness-engineering-era, jimcoding-claude-rules, jay-choi-9-tips]
aliases: ["CLAUDE.md", "클로드 MD", "프로젝트 설정 파일", "AGENTS.md"]
---

# CLAUDE.md

## 핵심 요약

`CLAUDE.md` 는 프로젝트 루트에 두는 **Claude 전용 컨텍스트 파일**. Claude 는 매 세션 시작 시 자동으로 이 파일을 로드하므로, 프로젝트의 규칙·아키텍처·도메인 지식을 한 곳에 모아 두면 **매번 파일을 탐색하는 토큰 소모를 막을 수 있다**. 다만 이 파일이 비대해지면 반대 효과가 나므로 길이 상한을 유지하고, 상세 내용은 [[concepts/context-engineering|lazy loading]] 으로 별도 파일에 분리한다.

> [!note] 길이 권장
> - **이상적**: 150~200줄 안에서 끝낼 것 ([[sources/jay-choi-9-tips]])
> - **상한**: 300줄
> - **이유**: Claude 는 CLAUDE.md 의 **약 80% 정도만 따른다**고 알려져 있다. 길수록 중요 규칙이 묻힌다 — [[concepts/fresh-context-principle]] 의 정적 파일 단위 적용.

## 생성 방법

```bash
# 프로젝트 루트에서 Claude 실행 후
/init
```

`/init` 이 프로젝트 구조를 스캔해 초기 CLAUDE.md 를 생성한다.

## 권장 구성 요소

1. **절대 규칙** — 절대 하면 안 되는 것, 꼭 해야 하는 것
   - 예: "프로덕션 DB 에 직접 쿼리 금지", "`.env` 커밋 금지"
2. **아키텍처** — 트리 형식으로 디렉토리 구조 표현 (Claude 가 읽기 좋음)
3. **빌드 · 테스트 명령어** — `pnpm build`, `pnpm test`, `pnpm lint` 등
4. **도메인 컨텍스트** — 비즈니스 로직 요약 (상품 속성, 재고 규칙 등)
5. **코딩 컨벤션** — 네이밍 (컴포넌트는 PascalCase, 유틸은 camelCase 등)
6. **핵심 패턴** — "서버 컴포넌트 기본, 클라이언트 상태 필요 시에만 클라이언트"

## 관리 전략

### 분할 (컨텍스트 분산)

루트 `CLAUDE.md` 가 커질수록 토큰을 매 세션 잡아먹는다. **폴더별 분할**로 해결:

```
프로젝트/
├── CLAUDE.md              # 전체 규칙 (300줄 이내)
├── apps/
│   ├── api/CLAUDE.md      # API 전용 규칙
│   └── web/CLAUDE.md      # 웹 프론트 전용
└── supabase/CLAUDE.md     # DB 전용
```

Claude 는 작업 경로에 따라 해당 폴더의 `CLAUDE.md` 만 읽으므로 루트 비대화를 막는다.

### 파일 타입별 조건부 로딩

`.claude/rules/*.md` + frontmatter **glob 조건**으로 파일 패턴별 자동 로드도 가능. 폴더 분할이 *도메인 단위* 분리라면, 이 패턴은 *파일 타입 단위* 분리. 둘을 함께 쓰면 토큰 절감 극대화 → [[concepts/conditional-rule-loading]].

> [!note] 실패 모드 — 비대화 주의
> 저자 [[entities/jimcoding|짐코딩]] 보고 사례: 모노레포 `CLAUDE.md` 가 47,000 단어까지 비대 → Claude 가 느려지고 규칙 무시 시작 → 조건부 로딩으로 재구성 후 루트 메모리 80% 감소.

### 참조 기반 (lazy loading)

루트 `CLAUDE.md` 는 규칙과 구조만 담고, 상세는 참조로 연결한다:

```markdown
## 참조

- API 엔드포인트 목록: `docs/api-spec.md`
- DB 스키마: `docs/db-schema.md`
- 아키텍처 다이어그램: `docs/architecture.md`
```

Claude 는 **필요할 때만** 참조 파일을 읽는다 → [[concepts/context-engineering]].

### 수정

수동 편집 불필요. Claude 에게 `"방금 정한 패턴을 CLAUDE.md 에 추가해 줘"` 라고 말하면 자동 업데이트된다.

## 트리거 키워드

CLAUDE.md 안에 워크플로우를 키워드로 등록 가능:

```markdown
## 트리거

- **배포** 키워드 감지 시:
  1. `pnpm test` 실행
  2. 실패 시 중단 및 보고
  3. 성공 시 `git add -A` → `git commit` → `git push`
  4. 요약 출력
```

사용자가 "배포해줘" 입력 시 Claude 가 4단계 자동 실행.

> [!note] 슬래시 명령어 vs 트리거 키워드
> 비슷한 효과지만 슬래시 명령어 (`.claude/commands/*.md`) 가 **탭 자동완성 + 구조적 인식** 측면에서 더 안정적. 반복 워크플로는 슬래시 명령어 권장.

## Compound Engineering (팀 공유)

팀이 CLAUDE.md 를 git 에 커밋하면 전원이 동일한 규칙으로 Claude 를 사용 → Anthropic 용어로 **Compound Engineering**.

커밋 전 체크:
- 개인 API 키 제거
- 로컬 전용 경로 제거

## 하네스 엔지니어링 관점

`CLAUDE.md` 는 [[concepts/harness-engineering|Harness Engineering]] 의 **1번째 기둥 (Context Files)** 의 실체. 사람이 읽는 문서가 아니라 **AI 가 작업 시작 시 먼저 읽는 런타임 설정 파일** — 행동 제약으로 인식된다. 같은 역할의 파일: `AGENTS.md` (OpenAI 계열), `.cursorrules` (Cursor). OpenAI 가 3명으로 제품을 배포한 사례에서도 `agents.md` 가 핵심이었다.

## 관련 페이지

- [[concepts/harness-engineering]] — 1번째 기둥 실체
- [[concepts/context-engineering]] — lazy loading, 세컨드 브레인, 세션 위생
- [[concepts/fresh-context-principle]] — 80% 준수 통념의 상위 원칙
- [[concepts/conditional-rule-loading]] — `.claude/rules/*.md` + glob 로 파일 타입별 자동 로드
- [[concepts/plan-mode]] — Plan 모드에서 CLAUDE.md 의 영향
- [[concepts/claude-skills]] — Skills 와의 역할 분담
- [[concepts/llm-wiki-pattern]] — 누적형 지식 관리 관점

## 출처

- [[sources/claude-code-2h-mastery]] — 입문편 전반, 실전편 "컨텍스트 관리" 섹션
- [[sources/harness-engineering-era]] — Context Files 기둥으로의 재해석
- [[sources/jimcoding-claude-rules]] — 비대화 실패 모드 + `.claude/rules` 조건부 로딩 사례
- [[sources/jay-choi-9-tips]] — *"Claude 는 CLAUDE.md 의 80% 정도만 따른다"*, 150~200줄 권장
