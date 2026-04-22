# Wiki Log

위키 오퍼레이션 연대기. Append-only. 최신이 아래로.

`grep "^## \[" wiki/log.md | tail -5` 로 최근 활동 확인.

---

## [2026-04-22] schema | 위키 시스템 초기 부트스트랩

- `CLAUDE.md` 를 LLM 위키 스키마로 재작성 (3층 아키텍처·운영 규약·트리거 키워드 정의)
- 폴더 구조 생성: `raw/`, `wiki/{sources,concepts,entities}/`
- `wiki/index.md`, `wiki/log.md` 초기화

## [2026-04-22] ingest | llm-wiki.md (Karpathy 스타일 LLM 위키 패턴)

- 원본: `raw/llm-wiki.md` (기존 vault 루트에서 이동)
- 생성 페이지 (6):
  - Source: [[llm-wiki-karpathy]]
  - Concepts: [[llm-wiki-pattern]], [[three-layer-architecture]], [[wiki-operations]]
  - Entities: [[obsidian]], [[memex]]
- 핵심 주장: LLM 이 RAG 처럼 매 쿼리마다 재탐색하는 대신, **지속·증분적으로 유지되는 위키**를 상시 유지하면 지식이 누적된다. 인간은 소스 큐레이션과 질문, LLM 은 요약·상호참조·메인터넌스 담당.
- 후속 탐구 아이디어: `[[qmd]]`(Tobi Lütke 의 로컬 마크다운 검색 엔진), `[[dataview]]` 플러그인, `[[marp]]` 슬라이드 통합.

## [2026-04-22] schema | STUDY/ 폴더 → raw/ 로 이동 확인

- 사용자가 기존 `STUDY/` 폴더 (Advisor.md, CLAUDE study.md, Harness Engineering.md) 를 `raw/` 로 이동
- `CLAUDE.md` 의 "STUDY/ 는 기존 보존" 조항은 이제 무효 — 다음 schema 갱신 대상
- `raw/` 에 미수집 소스 2 건 대기: `Advisor.md`, `Harness Engineering.md`

## [2026-04-22] ingest | CLAUDE study.md (사용자의 Claude Code 학습 노트)

- 원본: `raw/CLAUDE study.md` (원 출처: YouTube vxEvo2BLM6A)
- 생성 페이지 (8):
  - Source: [[claude-code-study-notes]]
  - Concepts: [[plan-mode-workflow]], [[wat-framework]], [[context-as-king]]
  - Entities: [[claude-code]], [[skills]], [[sub-agents]], [[mcp]]
- 갱신 페이지 (2): [[llm-wiki-pattern]] (세컨드 브레인 교차참조 보강), `index.md`
- 핵심 주장: **"컨텍스트는 왕"**. Plan Mode · LazyLoading · WAT(Workflows/Agents/Tools) 로 토큰·컨텍스트·에이전트 관심사를 분리하라.
- ⚠️ **Lint 후보 발견**: 이 vault 의 `CLAUDE.md` 가 ~250 줄 → [[context-as-king|LazyLoading]] 원칙에 저촉. 향후 분할·다이어트 제안.
- 📌 **미처리**: 원 노트의 스크린샷 14 장(Compound Engineering, Hooks, Agent Teams, Git worktree 등). 텍스트 요약 없이 임베드만 연결 — 추후 OCR ingest 로 확충.
- 후속 탐구 아이디어: [[git-worktree]], Compound Engineering 개념 페이지, Hooks 엔티티, `raw/Advisor.md`·`raw/Harness Engineering.md` ingest.

## [2026-04-22] ingest | Advisor.md (계층형 모델 아키텍처)

- 원본: `raw/Advisor.md` (저자: 사용자 본인, Anthropic 선언 기반)
- 생성 페이지 (3):
  - Source: [[advisor-pattern-note]]
  - Concepts: [[advisor-pattern]], [[model-routing]]
- 갱신 페이지 (2): [[sub-agents]] (Advisor 와의 대조 섹션), [[claude-code]] (`/advisor` 커맨드)
- 핵심 주장: Executor(Sonnet) 가 주도하고 Advisor(Opus) 는 조언만 하는 Bottom-up 구조. Sonnet 단독 대비 성능 ↑, Opus 단독 대비 비용 ↓.
- 핵심 인용 (Anthropic): *"정답은 더 큰 모델이 아니라 더 똑똑한 시스템 아키텍처다."*
- 스크린샷 4 장 미해석 (이슈 #1 대상에 추가 예정)

## [2026-04-22] ingest | Harness Engineering.md (AI 통제 엔지니어링)

- 원본: `raw/Harness Engineering.md` (저자: 사용자 본인)
- 생성 페이지 (3):
  - Source: [[harness-engineering-note]]
  - Concept: [[harness-engineering]]
  - Entity: [[oh-my-claudecode]]
- 갱신 페이지 (4): [[context-as-king]] (Harness 첫째 기둥 맥락), [[claude-code]] (Hooks 의 하네스 위치), [[wat-framework]] (Harness 와의 차이 섹션), [[llm-wiki-pattern]] (4 기둥 대응 테이블)
- 핵심 주장: AI 를 더 똑똑하게(Agentic) 만드는 것과 별개로 **더 통제·규율**하는 Harness 층을 프로젝트에 쌓아야 한다. 4 기둥 — 기계가 읽는 컨텍스트 / 자동 교정 루프 / 도구 경계 / 피드백 루프.
- 핵심 인용: *"AI 가 실수했을 때 프롬프트 말고 하네스를 고쳐라."*
- **메타 발견**: 이 위키 시스템 자체가 Harness Engineering 의 지식관리 도메인 적용 사례로 재해석 가능. [[llm-wiki-pattern]] 페이지에 대응 테이블 추가.
- [[oh-my-claudecode]] 는 이 vault 에서 **실제 사용 중**(`.omc/` 디렉터리 확인).
- 스크린샷 7 장 미해석 (이슈 #1 확장 대상).

## [2026-04-22] ingest | CLAUDE study.md 스크린샷 14 장 OCR (이슈 #1)

- 원본: `Resources/스크린샷 2026-04-{17,19} ....png` (14 장)
- 컨텍스트: [[claude-code-study-notes]] ingest 시 텍스트 본문만 반영 → 임베드 스크린샷이 미해석으로 남아 [GH 이슈 #1](https://github.com/donggyushin/obsidian_memo/issues/1) 등록 → 본 작업으로 해소
- 신규 페이지 (5):
  - Concepts: [[compound-engineering]], [[trigger-keywords]]
  - Entities: [[hooks]], [[agent-teams]], [[git-worktree]]
- 갱신 페이지 (7):
  - [[skills]] — BEFORE/AFTER, 프롬프트 vs Skills 비교, 폴더 구조, description 3 가지 조건, 만드는 두 방법 (스크린샷 5 장 통합)
  - [[sub-agents]] — 4 가지 핵심 장점 + Do/Don't, [[agent-teams]] 링크
  - [[claude-code]] — Hooks/Agent Teams/Git Worktree 신규 엔티티 링크 갱신
  - [[wat-framework]] — A 섹션에 Self-Healing + 병렬 처리 도식, T 섹션에 도구 원자성 대비표 + 3 형태(Bash/MCP/Hooks)
  - [[context-as-king]] — `CLAUDE.md` 안의 Mermaid 아키텍처 전술 섹션 추가
  - [[plan-mode-workflow]] — "실전 워크플로우 4 단계" 섹션 + [[compound-engineering]] 링크
  - [[claude-code-study-notes]] — 스크린샷 매핑 표 (14 → 통합 페이지) 로 갱신, 미해석 → 해석 완료
- 핵심 발견:
  - **[[compound-engineering]]** = 프로젝트 vs 글로벌 `claude.md` 분리 원칙. [[plan-mode-workflow]] 의 자연 확장
  - **[[hooks]]** = [[harness-engineering]] 4 기둥 중 둘째(자동 교정 루프) 의 직접 구현체. [[trigger-keywords]] 와 보완 (이벤트 vs 키워드)
  - **[[agent-teams]]** (COMING SOON) = [[sub-agents]] 의 양방향 메시 토폴로지 진화형. [[oh-my-claudecode|OMC]] `team` 스킬이 현재 우회 구현
- 후속 탐구 아이디어: `claude-squad` (이 vault 의 worktree 환경) 의 [[git-worktree]] 와의 관계, Hooks JSON 구조 슬라이드 ingest, Agent Teams 출시 후 토폴로지 패턴 정리

## [2026-04-22] lint | 위키 전체 건강 검진 (20 페이지)

- 검사 범위: sources/ 4, concepts/ 9, entities/ 7
- 결과 요약:
  - 고아 페이지 **0** (최소 4 inbound: [[memex]], [[model-routing]], [[oh-my-claudecode]])
  - 오버사이즈 **0** (최대 113 줄, 800 임계 대비 여유)
  - 모순·스테일 **0**
  - Placeholder `[[page-name]]`·`[[slug]]`·`[[스크린샷 ....png]]` — 전부 인라인 코드 백틱 안, Obsidian 렌더링 안전
- 수정 1 건: CLAUDE.md 스키마 드리프트 — "STUDY/ 기존 보존" 조항 삭제 (STUDY/ 는 이미 raw/ 로 이동됨)
- 리포트 항목 (수정 없음):
  - 누락 엔티티 후보: `[[dataview]]`, `[[marp]]`, `[[qmd]]` — 언급만 있고 페이지 없음. 필요 시 엔티티 승격
  - CLAUDE.md = 179 줄 — [[context-as-king]] LazyLoading 관점에서 다이어트 추적 대상이나 현재 임계 내
- 이슈 #1 확장 코멘트 추가 예정: Advisor(스크린샷 4장) + Harness(7장) 분 스크린샷 OCR 스코프 포함
