---
title: "메이플스토리 (MapleStory)"
type: entity
tags: [game, mmorpg, nexon, 한국, 2d]
created: 2026-04-23
updated: 2026-04-23
sources: [maple-modulo-bias]
aliases: ["메이플", "MapleStory", "Maple", "메이플 스토리"]
---

# 메이플스토리

## 핵심 요약

[[entities/nexon]] 이 2003년 출시한 한국 대표 2D 횡스크롤 MMORPG. 장수 서비스와 **확률형 아이템 기반 경제** 로 유명. 20년 이상 운영되며 축적된 레거시 코드가 드랍률 산정에서 **[[concepts/modulo-bias]]** 를 노출 — 표기 드랍률보다 실제 드랍률이 높은 버그가 2026 년 밝혀짐 ([[sources/maple-modulo-bias]]).

## 위키 관점에서의 역할

이 페이지는 게임 자체의 포괄 정보가 아니라 **확률 시스템 버그 맥락**에서 참조. 본격 게임 메타는 외부 출처 필요.

## 확률 문제 관련 사건

### 2026 모듈로 편향 드랍률 이슈

- **증상**: 드랍률 29% 이하 아이템의 실제 드랍 확률이 표기값보다 약 25% 부풀려짐.
- **파생**: 아이템 드롭률 버프 +5~10% 가 거의 무효. +17% 이상 올려야 유의미.
- **원인**: `rand() % 10억` 매핑 과정의 모듈로 편향 (추정).
- **대상**: 오리지널 메이플스토리 + 메이플 키우기 (스핀오프 모바일).
- **유저 영향**: 개이득 방향 편향 → 쌀값(게임 내 재화 시세) 하락 외 체감 피해 없음.
- **운영사 영향**: 희귀템 과대 배포로 경제 인플레 가속.
- 상세: [[sources/maple-modulo-bias]], 메커니즘: [[concepts/modulo-bias]]

## 관련 페이지

- [[entities/nexon]] — 운영사
- [[concepts/modulo-bias]] — 원인 메커니즘
- [[concepts/prng-rand]] — 관련 PRNG 맥락
- [[sources/maple-modulo-bias]] — 사건 해설

## 출처

- [[sources/maple-modulo-bias]] — 코딩애플 영상
- 일반 게임 상식 (출시 연도 등)
