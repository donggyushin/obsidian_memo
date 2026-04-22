# LLM Wiki — Schema & Workflow

이 저장소는 **Karpathy LLM Wiki 패턴**을 구현한 세컨드 브레인이다.
당신(LLM)은 이 위키의 **유일한 작성자이자 유지보수자**다. 사용자는 소스를 큐레이션하고 질문을 던진다.

> 핵심 원칙: 위키는 매 질의마다 재생성되는 RAG 인덱스가 아니다.
> **누적되는 지속 가능한 아티팩트**다. 소스가 들어올 때마다 한 번 컴파일되고, 이후 최신 상태로 유지된다.

---

## 3-Layer 아키텍처

| Layer | 경로 | 소유자 | 변경 가능? |
|-------|------|--------|-----------|
| **Raw sources** | `raw/` | 사용자 | 추가만. LLM은 **절대 수정/삭제 금지** |
| **Wiki** | `wiki/` | LLM | LLM이 전적으로 소유. 생성/갱신/재구성 모두 수행 |
| **Schema** | `CLAUDE.md` (이 파일) | 공동 | 사용자와 LLM이 함께 진화시킴 |

---

## Wiki 디렉토리 구조

```
wiki/
├── index.md              # 카탈로그 — 모든 페이지 목록 + 한 줄 요약 (매 ingest마다 갱신)
├── log.md                # 연대기 — ingest/query/lint 이력 (append-only)
├── entities/             # 고유명사 페이지 (인물, 책, 제품, 조직, 장소)
├── concepts/             # 개념/주제 페이지 (아이디어, 이론, 패턴)
├── sources/              # 원본 소스별 요약 페이지 (raw/의 각 문서에 1:1 대응)
├── syntheses/            # 종합/비교/분석 페이지 (질의 결과 중 보존 가치 있는 것)
└── assets/               # 이미지/첨부
```

---

## 페이지 포맷 규칙

모든 위키 페이지는 YAML frontmatter로 시작한다.

```markdown
---
title: "페이지 제목"
type: entity | concept | source | synthesis
tags: [태그1, 태그2]
created: YYYY-MM-DD
updated: YYYY-MM-DD
sources: [source-페이지-slug, ...]   # 이 페이지가 근거로 삼은 소스
aliases: [별칭1, 별칭2]               # 다른 이름으로 언급될 때
---

# 제목

## 핵심 요약
(3~5줄 TL;DR)

## 본문
...

## 관련 페이지
- [[다른-페이지-slug]]
- [[또-다른-페이지]]

## 출처
- [[source-xxx]] — 구체적 인용/참고 위치
```

### 링크 규칙
- 페이지 간 링크는 **Obsidian wiki-link 문법** `[[페이지-slug]]` 사용
- 파일명은 **kebab-case** (예: `karpathy-llm-wiki.md`, `rag-vs-persistent-wiki.md`)
- 한국어 제목도 kebab-case 슬러그로: `ragvs-위키.md`가 아니라 `rag-vs-wiki.md` 권장
- 이미지는 `![[assets/파일명.png]]`

---

## 4대 Operation

### 1) Ingest — 새 소스 처리

**트리거**: 사용자가 "ingest 해줘", "이 소스 넣어줘", 또는 raw/ 에 새 파일이 들어왔다고 알림.

**절차**:
1. `raw/` 의 소스를 읽는다. 이미지 포함 시 별도로 View.
2. 사용자와 **핵심 takeaway 2~5개를 먼저 대화**한다 (즉시 쓰지 말 것).
3. `wiki/sources/source-XXX.md` 로 소스 요약 페이지 작성.
4. 관련 `entities/`, `concepts/` 페이지를 생성하거나 갱신.
   - 새 고유명사 → `entities/` 신규 페이지
   - 새 개념 → `concepts/` 신규 페이지
   - 기존 페이지면 정보 병합 + `updated` 날짜 갱신
5. 모순 발견 시 페이지에 `> [!warning] 모순` 블록으로 명시.
6. `index.md` 갱신 (새 페이지 항목 추가).
7. `log.md` 에 항목 추가:
   `## [YYYY-MM-DD] ingest | <소스 제목>` + 영향받은 페이지 목록.
8. 사용자에게 **어떤 페이지가 생성/수정되었는지** 보고.

**한 번의 ingest가 10~15개 페이지를 건드릴 수 있다.** 정상이다.

### 2) Query — 위키에 질문

**절차**:
1. `wiki/index.md` 를 먼저 읽어 관련 페이지 후보 식별.
2. 후보 페이지들을 읽고 교차 참조 따라가며 합성.
3. 답변에 **반드시 인용** `[[페이지명]]` 포함.
4. 답변이 **재사용 가치 있으면** → `wiki/syntheses/` 에 페이지로 저장.
5. `log.md` 에 `## [YYYY-MM-DD] query | <질문 요약>` 기록.

### 3) Lint — 건강검진

**트리거**: "lint 돌려줘", 또는 주기적(소스 10개마다 등).

**체크 항목**:
- **Orphan**: inbound 링크 0개인 페이지
- **Stale**: `updated` 가 오래되고 새 소스가 모순/보완하는 페이지
- **Contradiction**: 서로 충돌하는 주장을 담은 페이지 쌍
- **Missing page**: 다수 페이지에서 언급되는데 독립 페이지 없는 개념
- **Broken link**: `[[...]]` 가 존재하지 않는 파일 가리키는 경우
- **Oversized**: 800줄 초과 페이지 (분할 제안)
- **Gap**: 웹 검색/추가 소스로 채울 수 있는 공백

결과는 `wiki/syntheses/lint-YYYY-MM-DD.md` 로 저장 + `log.md` 기록.

### 4) Quick Add — 가볍게 한 페이지만

Ingest만큼 full-touch는 아니고, 간단한 메모/발견을 빠르게 페이지화.
`log.md` 기록만 하고 index 갱신은 선택.

---

## index.md 포맷

```markdown
# Wiki Index

*마지막 갱신: YYYY-MM-DD · 총 N개 페이지*

## Entities
- [[entity-slug]] — 한 줄 요약
...

## Concepts
- [[concept-slug]] — 한 줄 요약
...

## Sources
- [[source-slug]] — 원본 제목 · 저자 · 유형(article/paper/book/podcast/video)
...

## Syntheses
- [[synthesis-slug]] — 무엇을 합성했는지
...
```

---

## log.md 포맷

append-only. 최신이 아래(시간순).

```markdown
## [2026-04-22] ingest | Karpathy LLM Wiki Idea
- 생성: [[sources/karpathy-llm-wiki-idea]], [[concepts/persistent-wiki]], [[concepts/memex-vannevar-bush]]
- 갱신: [[entities/andrej-karpathy]] (+ 아이디어 추가)

## [2026-04-22] query | RAG vs 위키 차이가 뭐야?
- 참조: [[concepts/persistent-wiki]], [[concepts/rag]]
- 저장: [[syntheses/rag-vs-persistent-wiki]]

## [2026-04-22] lint
- orphan 2건, stale 0건, contradiction 1건 발견 → [[syntheses/lint-2026-04-22]]
```

간단한 파싱 팁: `grep "^## \[" wiki/log.md | tail -5` 로 최근 5건.

---

## 사용자와의 협업 스타일

- **한국어로 대화**. 기술 용어는 원어 유지 OK.
- 소스 ingest 전에 **"핵심 takeaway 요약 → 사용자 피드백 → 쓰기"** 순서. 바로 쓰지 말 것.
- 쓰기 후엔 **무엇을 건드렸는지** 간결히 보고 (페이지 경로 목록).
- 위키 페이지는 **사용자가 읽기 좋게**. 불릿 남발 금지. 단락형 설명 + 강조(`**`) + 콜아웃(`> [!note]`) 적절히.
- 사용자가 모르는 주장은 **반드시 출처 명시**. 출처 불명이면 `> [!question] 출처 확인 필요` 표시.

---

## Anti-patterns (하지 말 것)

- `raw/` 파일을 수정/삭제/이동
- 출처 없이 주장
- 한 페이지에 너무 많은 주제 섞기 (→ 분할)
- 위키 링크 `[[...]]` 없이 평문만 쓰기 (교차참조 없으면 위키가 아니다)
- index.md/log.md 갱신 누락
- 모순을 조용히 덮어쓰기 (반드시 명시할 것)

---

## 확장 여지 (나중에)

- `qmd` 로컬 검색 엔진 도입
- Obsidian Dataview 쿼리용 frontmatter 추가 필드
- Marp 슬라이드 출력
- 멀티모달: 이미지 View 후 설명 추가

---

*이 스키마는 살아있다. 사용자와 함께 진화시킨다.*
