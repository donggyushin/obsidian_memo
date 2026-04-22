---
title: "Wiki Log"
type: meta
---

# Wiki Log

Append-only 연대기. 각 항목은 `## [YYYY-MM-DD] <op> | <title>` 형식.

빠른 조회: `grep "^## \[" wiki/log.md | tail -5`

---

## [2026-04-22] bootstrap | 위키 시스템 초기화
- 생성: [[concepts/llm-wiki-pattern]], `wiki/index.md`, `wiki/log.md`
- 스키마 확정: `CLAUDE.md` (3-layer 아키텍처, 4대 operation, 페이지 포맷)
- 다음 단계: `raw/` 에 첫 소스 드롭 → ingest 시작
