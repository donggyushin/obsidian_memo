# LLM Wiki — 개인 세컨드 브레인

Andrej Karpathy의 [LLM Wiki 아이디어](https://github.com/karpathy/llm-wiki)를 구현한 개인 지식 베이스.

> RAG는 매 질의마다 지식을 *다시 찾는다*.
> LLM Wiki는 지식을 한 번 *컴파일하고* 계속 *유지한다*.

---

## 구조

```
.
├── CLAUDE.md        # LLM 에이전트용 스키마 + 워크플로
├── raw/             # 원본 소스 (내가 큐레이션, LLM은 읽기만)
└── wiki/            # LLM이 생성·유지하는 마크다운 네트워크
    ├── index.md     # 카탈로그
    ├── log.md       # 연대기
    ├── entities/    # 고유명사 (인물·책·제품)
    ├── concepts/    # 개념·이론·패턴
    ├── sources/     # 소스별 요약 (raw/ 에 1:1 대응)
    ├── syntheses/   # 합성·비교·분석 (질의 결과 보존)
    └── assets/      # 이미지·첨부
```

---

## 역할 분담

| | 사용자 | LLM |
|---|--------|-----|
| 소스 큐레이션 | O | X |
| 질문 던지기 | O | X |
| 읽기·요약 | X | O |
| 교차 참조·링크 유지 | X | O |
| 페이지 생성·갱신 | X | O |
| index/log 유지 | X | O |

**나는 읽기만. LLM이 쓴다.**

---

## 사용 방법

Obsidian 으로 이 저장소를 vault 로 열고, Claude Code 를 옆에 띄워 대화.

### 새 소스 추가
```
1. raw/ 에 파일 드롭 (Obsidian Web Clipper, PDF, txt, png 무엇이든)
2. Claude 에게: "raw/xxx.md ingest 해줘"
3. Claude 와 핵심 takeaway 대화
4. Claude 가 위키 10~15개 페이지 생성/갱신
```

### 질문
```
"persistent wiki vs RAG 차이?"
→ Claude 가 wiki 페이지들 참조해서 인용 포함 답변
→ 답변 가치 있으면 syntheses/ 에 저장
```

### 건강검진
```
"위키 lint 돌려줘"
→ orphan, stale, 모순, 누락 개념, 깨진 링크 리포트
```

자세한 규칙은 [CLAUDE.md](./CLAUDE.md) 참조.

---

## Obsidian 추천 플러그인

- **Web Clipper** (브라우저 확장) — 웹 아티클 → markdown
- **Graph view** (내장) — 위키의 형태 시각화
- **Dataview** — frontmatter 쿼리
- **Marp** — 슬라이드 출력

Attachment folder 를 `wiki/assets/` 로 설정 권장.

---

## 철학

> 위키 유지보수의 지루한 부분 — 교차 참조 갱신, 요약 최신화, 모순 추적 — 은 인간이 지친다.
> LLM은 지치지 않는다. 15개 파일을 한 번에 만진다.
>
> 사람은 큐레이션·질문·의미 부여. LLM은 그 외 전부.
