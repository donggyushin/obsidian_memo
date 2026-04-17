> source: https://www.youtube.com/watch?v=vxEvo2BLM6A

# Claude Code 학습 노트

## CLAUDE.md 운영

- 파일이 너무 커지면 **토큰 사용량이 증가**하므로 폴더별로 분할해 관리하는 것이 좋다.
- 수동으로 직접 수정할 필요는 없다. 클로드에게 "자동으로 업데이트해줘"라고 요청하는 방식이 가장 편하다.

## Trigger Keywords(그냥 Command 쓰는게 더 좋음)

![[스크린샷 2026-04-17 오전 10.28.02.png]]

## Compound Engineering

![[스크린샷 2026-04-17 오전 10.29.53.png]]

## 핵심 단축키

| 단축키 | 동작 |
| --- | --- |
| `Shift + Tab` | Plan Mode ↔ Accept Mode 전환 |
| `Escape` | 즉시 중단 |
| `Escape × 2` | 입력 삭제 / 복원 |

## Plan Mode를 써야 하는 실무적 이유

1. **토큰 최적화** — "잘못된 코드 생성 → 수정 요청 → 재생성" 악순환을 끊을 수 있다.
2. **컨텍스트 윈도우 보존**
3. **실행 정확도 향상**
4. **비용 관리** — input / output 중 output 토큰을 절약할 수 있다.

> 결론: **Plan → 검토 → Execute** 패턴을 습관화하자.


### 이미지를 넘겨주는거 좋음
### ! 접두사로 bash 명령어 직접 실행 가능

### 컨텍스트 관리 커맨드
1. /clear
2. /compact
3. /context
	1. 바(bar) 형태로 현재 사용량 보여줌
4. /model
	1. 병렬로 서브에이전트들 많이 다룰때는 Sonet 사용하는게 좋음
5. /resume
6. /mcp
	1. supabase, notion 추천
	2. 양날의 검임. 아무것도 안해도 시작부터 Context 를 어느정도 차지하고 싶어함. 
	3. mcp 를 무자비하게 사용하는건 비추 함. 
	4. custom mcp 만들어서 사용하는게 제일 좋음 
7. /export
	1. 컨텍스트를 파일로 추출해줌
8. /output-style





