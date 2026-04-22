---
title: Compound Engineering (팀 공유 × 개인 전용 분리)
type: concept
tags: [claude-code, claude-md, team, scaling, configuration]
created: 2026-04-22
updated: 2026-04-22
sources: [[claude-code-study-notes]]
---

# Compound Engineering

## 정의

같은 코드베이스에서 일하는 **팀 전원**이 동일한 Claude 운영 규칙을 공유하면서도, **개인별로 달라야 하는 설정**(로컬 경로·API 키·개인 단축키 등)은 분리해서 보존하는 설계 원칙. 두 종류의 `CLAUDE.md` 를 의도적으로 분리해 운영한다.

> 이름의 함의: "Compound" = 같은 규칙을 팀이 공유할수록 **결과가 복리(compound)**로 누적된다. 한 명이 발견한 베스트 프랙티스가 팀 전체 Claude 의 출력 품질을 즉시 끌어올린다.

## 두 층의 `claude.md`

| 층 | 위치 | 내용 | Git 추적 |
|---|---|---|---|
| **프로젝트 `claude.md`** | 레포 루트 (`./CLAUDE.md`) | 팀 공통 규칙·아키텍처·코딩 스타일 | ✅ 커밋 |
| **글로벌 `claude.md`** | `~/.claude/CLAUDE.md` | 개인 설정 (로컬 경로, API 키, 개인 단축키) | ❌ 절대 커밋 금지 |

**핵심 원칙**: 팀 공통 = 프로젝트 / 개인 전용 = 글로벌. 한 줄도 섞이지 않게 깔끔하게 분리.

## 왜 이 분리가 중요한가

1. **팀 일관성** — 코드 리뷰·테스트 규칙이 사람마다 다르면 PR 마다 톤이 흔들림. 프로젝트 `CLAUDE.md` 가 박제하면 신규 합류자도 동일한 Claude 출력 품질을 즉시 얻음.
2. **개인정보 보호** — API 키·로컬 절대경로가 레포에 새는 사고 방지.
3. **세팅의 휴대성** — 프로젝트 간 이동해도 `~/.claude/CLAUDE.md` 의 개인 선호는 유지됨.
4. **온보딩 단축** — 새로 합류한 사람이 `git clone` 만으로 팀 규칙을 자동 상속.

## [[plan-mode-workflow|Plan Mode]] 와의 관계

원 노트(l.14) 에서는 Compound Engineering 을 [[plan-mode-workflow]] 의 **확장판**으로 위치시킴. 한 명의 Plan 이 팀 전체에 즉시 전파될 수 있게 만드는 구조적 토대가 프로젝트 `CLAUDE.md` 다. → Plan 결과를 `CLAUDE.md` 로 박제하면 다음 사람의 Plan 이 그 위에서 시작되는 **누적 효과**가 발생.

## 운영 가드레일

- 프로젝트 `CLAUDE.md` 에는 **사람 이름·계정 정보·로컬 경로** 절대 금지. PR 단계에서 grep 으로 차단.
- 글로벌 `CLAUDE.md` 는 dotfiles 레포 등 **개인 비공개 저장소**에서만 관리.
- 프로젝트 `CLAUDE.md` 가 비대해지면 [[context-as-king|LazyLoading]] 으로 분할.

## 이 vault 와의 대응

- 이 vault 의 `CLAUDE.md` (LLM 위키 스키마) = 프로젝트 층. 누구든 클론하면 동일한 위키 운영 규칙 상속.
- 개인 `~/.claude/CLAUDE.md` (오-마이-클로드코드 OMC 글로벌 매뉴얼) = 글로벌 층. 본 vault 와 무관하게 모든 프로젝트에 적용.

## 관련 개념

- [[plan-mode-workflow]] — 본 개념의 토대 (Plan 의 누적성)
- [[context-as-king]] — 두 `CLAUDE.md` 모두 LazyLoading 적용 대상
- [[harness-engineering]] — `CLAUDE.md` 자체가 Harness 의 첫째 기둥(기계가 읽는 컨텍스트) 의 구체 산물

## 근거 소스

- [[claude-code-study-notes]] l.14
- 스크린샷: `![[스크린샷 2026-04-17 오전 10.29.53.png]]`
