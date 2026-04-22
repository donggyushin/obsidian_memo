# Raw Sources

원본 소스 저장소. **LLM은 이 디렉토리를 절대 수정/삭제하지 않는다.** 읽기 전용.

## 포맷

무엇이든 OK:
- `.md` — Obsidian Web Clipper 결과, 개인 노트
- `.pdf` — 논문, 전자책
- `.txt` — 전사본, 로그
- `.png/.jpg` — 스크린샷, 다이어그램
- `.html` — 웹 페이지 원본

## 네이밍

자유. 다만 ingest 시 LLM이 `wiki/sources/` 에 대응 페이지를 만들 때 slug를 생성하므로,
가급적 의미있는 이름 권장.

예: `karpathy-llm-wiki-idea.md`, `paper-attention-is-all-you-need.pdf`.

## 구조

도메인별로 하위 폴더 자유 생성:
```
raw/
├── articles/
├── papers/
├── books/
├── podcasts/
└── personal/
```
