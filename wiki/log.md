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

## [2026-04-22] ingest | 클로드 코드 2시간 안에 마스터하기 (YouTube 3부작 풀버전)
- 소스: `raw/[전체강의 통합본] 클로드 코드 2시간  안에 마스터하기 ｜ 입문→실전→심화 완전 정복 (풀버전).ko.vtt` (yt-dlp 한국어 자동자막)
- 전처리: `raw/claude-code-2h-mastery-transcript.md` (중복 제거, 2547 라인)
- 생성 (Source): [[sources/claude-code-2h-mastery]]
- 생성 (Concepts): [[concepts/claude-md]], [[concepts/plan-mode]], [[concepts/context-engineering]], [[concepts/claude-skills]], [[concepts/subagents]], [[concepts/hooks]]
- 갱신: `wiki/index.md` (1 → 8 페이지)
- 메모: 핵심 6개 개념만 선별. 통합 데모 · WAT 프레임워크 · 음성입력 · worktree · `/loop` 등 세부 토픽은 소스 페이지에 요약만 유지 (필요 시 별도 페이지로 분리 가능)

## [2026-04-22] ingest | 프롬프트 엔지니어링은 끝났습니다: 이제 '하네스'의 시대입니다
- 소스: `raw/프롬프트 엔지니어링은 끝났습니다： 이제 '하네스'의 시대입니다.ko.vtt` (yt-dlp 한국어 자동자막)
- 전처리: `raw/harness-engineering-era-transcript.md` (중복 제거, 558 라인)
- 저자: 실벨 개발자 ([[sources/claude-code-2h-mastery]] 와 동일 채널)
- 생성 (Source): [[sources/harness-engineering-era]]
- 생성 (Concepts): [[concepts/harness-engineering]], [[concepts/agentic-engineering]], [[concepts/four-axes-ai-development]]
- 생성 (Entities): [[entities/martin-fowler]], [[entities/silbel-developer]]
- 갱신 (Concepts): [[concepts/context-engineering]] (4축 중 2번째 축 맥락 추가), [[concepts/claude-md]] (하네스 1기둥 연결), [[concepts/hooks]] (하네스 2기둥 메커니즘), [[concepts/subagents]] (Worker Isolation 패턴)
- 갱신 (Source): [[sources/claude-code-2h-mastery]] (동일 저자 교차참조)
- 갱신: `wiki/index.md` (8 → 14 페이지)
- 미해결: [[entities/martin-fowler]] 의 "harness" · "garbage collection" 용어 귀속 1차 출처 미확인 (영상 저자의 주장에 의존), OpenAI 2026-02 블로그 URL 미제공 — 추후 lint 시 확인 플래그

## [2026-04-22] lint
- orphan 0 · stale 0 · 모순 0 · broken 3 · missing 4(유의미) · oversized 0 · gap 4
- 리포트: [[syntheses/lint-2026-04-22]]
- 최우선 조치: [[concepts/rag]] 생성 (broken link 해결 + llm-wiki-pattern 가독성 향상)
- 다음 라운드: [[entities/andrej-karpathy]], Agent Teams 독립 페이지, WAT Framework 독립 페이지, Gap 4블록 웹검색 검증

## [2026-04-23] ingest | 20년 만에 밝혀진 메이플 확률문제의 원인
- 소스: `raw/20년 만에 밝혀진 메이플 확률문제의 원인 [RVngRYqs7kA].ko.vtt` (yt-dlp 자동자막)
- 저자: 코딩애플 (https://www.youtube.com/@codingapple)
- URL: https://youtu.be/RVngRYqs7kA
- 생성 (Source): [[sources/maple-modulo-bias]]
- 생성 (Concepts): [[concepts/modulo-bias]], [[concepts/prng-rand]]
- 생성 (Entities): [[entities/coding-apple]], [[entities/maplestory]], [[entities/nexon]]
- 갱신: `wiki/index.md` (15 → 21 페이지)
- 도메인: 새 섬 진입 — 게임/PRNG/확률. 기존 AI·하네스 도메인과 교차 참조 없음 (의도적 분리).
- 미해결: 메이플 실제 PRNG 함수 비공개 (rand 가정은 영상 저자 명시), "17% 경계" 수치 해석적 재산출 여지.

## [2026-04-23] lint
- orphan 0 · stale 0 · 모순 0 · broken 3(이월) · missing 0(신규) · oversized 0 · gap 5 blocks
- 리포트: [[syntheses/lint-2026-04-23]]
- 평가: 신규 ingest 6페이지 orphan 0 / broken 신규 0 / 모순 0 — 새 도메인 자체 클러스터 내부 연결성 양호.
- 최우선 조치: 이월 broken 3건 중 [[concepts/rag]] 우선 생성 권고. 나머지 2건(memex, andrej-karpathy) 후속 ingest 시 자연 생성 대기.
