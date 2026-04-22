---
title: Trigger Keywords (사용자 정의 자동 워크플로우)
type: concept
tags: [claude-code, automation, claude-md, workflow, conventions]
created: 2026-04-22
updated: 2026-04-22
sources: [[claude-code-study-notes]]
---

# Trigger Keywords

## 정의

사용자가 미리 정해 둔 **짧은 키워드**(예: `배포`, `ship`, `커밋`, `lint`)를 입력하면 Claude 가 즉시 **사전 정의된 다단계 워크플로우**를 자동 실행하게 만드는 패턴. 키워드와 워크플로우 정의는 `CLAUDE.md` (또는 위키 스키마) 에 박제한다.

## 동작 흐름

```
사용자: "배포"
  ↓
Claude: CLAUDE.md 에서 "배포" 트리거 정의 조회
  ↓
다단계 워크플로우 자동 실행:
  1. 테스트 실행
  2. 결과 보고 (commit/push 안 함)
  3. git add → 변경 분석 → 한국어 커밋 메시지 자동 작성
  4. git commit
  5. git push origin <현재 브랜치>
  6. push 완료 후 결과 요약 출력
```

## 원 노트의 예시 (`배포` / `ship`)

원 학습 노트 본문에서 직접 인용:

1. `cd apps/api && python -m pytest` 로 백엔드 테스트 전체 실행
2. **테스트 실행 결과 보고**: 실패 내용을 보고하고 중단. **commit/push 하지 않음**
3. **테스트 통과 시**: 아래 순서로 실행
   - `git add -A` (변경된 파일 전체 스테이징)
   - 변경 내용을 분석하여 적절한 한국어 커밋 메시지 자동 작성
   - `git commit`
   - `git push origin <현재 브랜치>`
4. push 완료 후 결과 요약 출력

## 왜 좋은가

1. **인지 부하 ↓** — 매번 5 줄짜리 명령을 떠올려 입력하지 않아도 됨
2. **일관성 ↑** — 누가 입력하든 똑같은 절차로 실행
3. **실수 차단** — 테스트 실패 시 자동 중단 같은 가드레일 박제 가능
4. **온보딩 단축** — 신규 합류자가 단어 하나로 팀 표준 절차 사용

## 이 vault 의 적용

본 위키 스키마(`CLAUDE.md`) 가 정의한 트리거 키워드:

- `ingest <제목>` / `위키에 추가` — 새 소스 → 위키 페이지 생성·갱신
- `wiki <질문>` / `위키에서 찾아줘` — 인덱스 → 후보 페이지 → 인용 응답
- `lint` / `위키 점검` — 모순·스테일·고아 검사
- `커밋` / `commit` — 변경 분석 → 한국어 커밋 메시지 → commit (push 는 명시 요청 시)

또한 사용자의 글로벌 `~/.claude/CLAUDE.md` 에서 `주간보고` 트리거를 정의해 git log 1주일치를 한국어 보고서로 자동 정리.

## 작성 가이드

- **키워드는 짧고 모호하지 않게**: `배포` 는 OK, `ㄷㅍ` 는 X
- **각 단계의 종료 조건**을 명시: "실패 시 중단", "성공 시만 다음 단계"
- **언어 톤**도 박제 가능: "한국어로", "이모지 금지"
- 키워드 충돌 주의: `commit` 처럼 너무 일반적이면 일반 대화에서 오발화 위험 → 동의어 묶음으로 보완

## [[harness-engineering|Harness]] 관점

Trigger Keywords = Harness Engineering 첫째 기둥(**기계가 읽는 컨텍스트**) 의 한 형태. 사람이 매번 같은 절차를 상기시키는 대신 `CLAUDE.md` 에 한 번 박제하면 AI 가 자동으로 따른다 — 통제·규율의 자동화.

## 관련 개념

- [[compound-engineering]] — Trigger Keywords 도 프로젝트 `CLAUDE.md` 에 두면 팀 전체 공유
- [[harness-engineering]] — 본 패턴의 상위 프레이밍
- [[wat-framework]] — Workflows (W) 층의 한 구현 패턴

## 관련 엔티티

- [[claude-code]] — 호스트
- [[hooks]] — 이벤트 기반 자동 실행 (Trigger Keywords 는 키워드 기반, Hooks 는 이벤트 기반 — 상호보완)

## 근거 소스

- [[claude-code-study-notes]] l.12 (트리거 키워드 절)
- 스크린샷: `![[스크린샷 2026-04-17 오전 10.28.02.png]]`
