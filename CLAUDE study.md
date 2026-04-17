source: https://www.youtube.com/watch?v=vxEvo2BLM6A
# Claude.md
너무 커지면 토큰 사용량이 많음. 폴더별로 분할 관리해주는게 좋음. 직접적으로 수정할 필요는 없음. 
클로드에게 자동으로 업데이트 해줘. 라고 요청하는게 가장 좋음. 

## Trigger Keywords
![[스크린샷 2026-04-17 오전 10.28.02.png]]


## Compound Engineering
![[스크린샷 2026-04-17 오전 10.29.53.png]]

## 핵심 단축키
`Shift + Tab`  -> Plan Mode <-> Accept Mode 전환 
`Escape` -> 즉시 중단 
`Excape x 2` -> 입력 삭제/복원 


### Plan Mode 사용해야 하는 실무적 이유
1. 토큰 최적화
	1. "잘못된 코드 생성 -> 수정 요청 -> 재생성" 악순환을 끊을 수 있음. 
2. 컨텍스트 윈도우 보존
3. 실행 정확도 향상 
4. 비용 관리
	1. input/output에서 output 을 절약할 수 있음
결론: Plan -> 검토 -> Execute 패턴 습관화