# LLM Wiki

Obsidian + Claude Code 로 굴리는 **개인 세컨드 브레인**. Karpathy 의 [LLM 위키 패턴](wiki/concepts/llm-wiki-pattern.md)을 따른다.

사용자는 소스를 큐레이트하고 질문을 던지고, LLM 이 요약·링크·파일링을 맡아 위키를 증분적으로 키운다.

## 구조

```
raw/       # 원본 소스 (read-only)
wiki/      # LLM 이 생성·유지하는 마크다운
  ├── index.md        # 콘텐츠 카탈로그
  ├── log.md          # 연대기 로그 (append-only)
  ├── sources/        # 소스별 요약
  ├── concepts/       # 개념·패턴
  └── entities/       # 사람·도구·조직
CLAUDE.md  # LLM 운영 매뉴얼 (스키마)
```

3층 아키텍처 — **원본 / 생성물 / 규칙** 을 분리해 각 레이어의 불변성을 지킨다.

## 사용법

Obsidian 으로 `wiki/` 를 열어 그래프 뷰·위키링크로 브라우징. Claude Code 에 아래 키워드로 작업 지시.

- `ingest <제목>` — 새 소스 추가, 요약·라우팅·파일링
- `wiki <질문>` — 위키에서 인용과 함께 답변 합성
- `lint` — 모순·스테일·고아 페이지 점검
- `커밋` — 변경 분석 후 한국어 커밋

전체 규약·운영 매뉴얼은 [CLAUDE.md](CLAUDE.md) 참고.

## 현재 상태

- Sources: 4
- Concepts: 9
- Entities: 7
- Total: 20 pages

최신 카탈로그는 [wiki/index.md](wiki/index.md), 활동 로그는 [wiki/log.md](wiki/log.md).
