---
title: "Boris Cherny — Claude Code 창시자"
type: entity
tags: [anthropic, claude-code, creator, engineer]
created: 2026-04-25
updated: 2026-04-25
sources: [jay-choi-9-tips]
aliases: ["Boris Cherny", "보리스", "보리스 체르니", "Claude Code 창시자"]
---

# Boris Cherny

## 핵심 요약

**Claude Code 창시자**. Anthropic 소속. 자기가 만든 툴을 거의 커스터마이즈 없이 **vanilla** 로 사용한다고 공개해, "처음부터 풀 세팅하지 말라" 원칙의 1차 근거가 됨. 또한 *"단 하나의 팁을 고르라면 검증"* 발언으로 [[concepts/harness-engineering#3-execution-loop|자가 검증 루프]] 의 중요성을 못 박음.

> [!question] 1차 출처
> 본 위키에 등장하는 보리스 발언은 모두 [[sources/jay-choi-9-tips]] 의 전언. X 게시 원문 URL · Anthropic 공식 블로그 인터뷰 원문 미확보. 후속 lint 시 직접 인용 출처 보강 권고.

## 영상에서 전해진 발언

### vanilla 세팅 일화

> *"내 세팅은 놀랍게도 vanilla 다."* — X 게시 (영상 인용)

본인이 만든 툴을 거의 커스텀 없이 사용. 시사점:

- Claude Code 는 **있는 그대로도 대부분의 사용자에게 충분**
- 그 위에 뭘 더 쌓기 전에 **기본을 먼저 파악**하는 게 맞음
- 좋은 거 다 갖다 박는 게 좋은 게 아니다 (anti-bloat)

→ [[concepts/harness-engineering#vanilla-우선-원칙]] 으로 흡수.

### 검증을 단 하나의 팁으로 꼽음

> *"단 하나의 팁을 고르라면이라고 했을 때 고른 게 바로 [검증] 이다."*

자가 검증 루프를 줬을 때 **결과물 품질이 2~3배** 올라간다는 주장. 보리스의 우선순위가 명시된 드문 인용.

→ [[concepts/reward-hacking]] 도 함께 — 단 검증 루프를 만들어줬다고 끝이 아니라 테스트 자체도 사람이 봐야 한다는 보강.

## 관련 페이지

- [[sources/jay-choi-9-tips]] — 인용 출처
- [[concepts/harness-engineering]] — vanilla 우선 + 검증 루프
- [[concepts/fresh-context-principle]] — 보리스 인용은 아니지만 같은 영상의 핵심 표어
- [[concepts/reward-hacking]] — 검증 루프의 실패 모드

## 출처

- [[sources/jay-choi-9-tips]] (보리스 발언 모두 이 영상의 전언)
