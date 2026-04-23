---
title: "Modulo Bias (모듈로 편향)"
type: concept
tags: [prng, probability, game-development, cs-fundamentals, bug-pattern]
created: 2026-04-23
updated: 2026-04-23
sources: [maple-modulo-bias]
aliases: ["모듈로 편향", "Modulo Bias", "% bias", "rand() % N 편향"]
---

# Modulo Bias

## 핵심 요약

**균등 분포 난수**에 모듈로 연산(`%`)을 씌워 더 작은 범위로 매핑할 때, 원 범위 크기가 divisor 의 배수가 아니면 **낮은 값 구간이 과대표집**되는 구조적 편향. 난수 생성기 품질과 무관 — rand 가 완벽히 균등해도 `%` 단계에서 균등성이 깨짐. **옛 게임 확률 버그의 대표 원인**. 메이플스토리 드랍률 문제 ([[sources/maple-modulo-bias]]) 가 교과서 사례.

## 발생 조건

공식:

```
rand() ∈ [0, R-1]   (R 개 값, 균등)
result = rand() % N

편향 없음  ↔  R mod N == 0
편향 크기   ∝  (R mod N) / R
```

- `R % N == 0` 이면 모든 값이 `R/N` 번씩 동일 출현 → 균등.
- `R % N != 0` 이면 `0 ~ (R%N - 1)` 값은 `⌈R/N⌉` 번, 나머지는 `⌊R/N⌋` 번 출현.
- **낮은 값 구간이 1회 더** (rand 가 `[0, R-1]` 에서 0부터 시작하므로).

## 최소 예시

`rand ∈ [0, 8]`, `N = 6`:

| rand | `% 6` |
|------|-------|
| 0 | **0** |
| 1 | **1** |
| 2 | **2** |
| 3 | 3 |
| 4 | 4 |
| 5 | 5 |
| 6 | **0** |
| 7 | **1** |
| 8 | **2** |

`0, 1, 2` 가 각 2회, `3, 4, 5` 가 각 1회. **낮은 구간 확률 2배**.

## 메이플 케이스 (영상 요약)

- `rand()` 상한 ~ 42억 (2^32)
- `rand() % 10억` → 나머지 ~2.9억
- 결과값 `0 ~ 2.9억` 이 나머지 구간보다 1회 과대표집
- 드랍률 계산이 이 0~N 분포를 전제로 함 → **29% 이하 아이템 실제 드랍률 > 표기 드랍률** (약 25% 부풀려짐)

## 2차 증상: 확률 조정 무력화

편향 환경에서는 **임계값 상수 변경 = 실제 확률 증가** 라는 직관이 깨짐.

- `if (rand()%10억 < 임계값)` 에서 임계값 `2억 → 2.2억` 수정 (+10% 버프).
- 편향 질량이 0~2.9억 구간에 이미 초과 분포 → 작은 임계값 이동이 그 질량에 먹힘.
- 메이플 사례: **+17% 이상 상향해야 유의미 변화**. +5/+10% 버프 무효.

## 해결책

### 1. 현대 PRNG API 사용 (권장)

- C++: `std::uniform_int_distribution<int>(0, N-1)(rng)` — 표준이 편향 처리
- Python: `random.randint(a, b)` 또는 `secrets.randbelow(n)`
- Go: `math/rand/v2`, `rand.IntN(n)` (v2 부터 편향 처리)
- Rust: `rand::thread_rng().gen_range(0..n)`

언어 표준 API 는 내부적으로 rejection 또는 wide-multiplication 기법으로 균등성 보장.

### 2. Rejection Sampling

```c
int unbiased_mod(int N) {
  int limit = (RAND_MAX / N) * N;   // N의 배수 중 최대
  int r;
  do { r = rand(); } while (r >= limit);
  return r % N;
}
```

편향 구간(`[limit, RAND_MAX]`) 에 떨어지면 버리고 재추출. 정확하지만 루프.

### 3. rand_max ≫ N 이면 무시 가능

`R ≫ N` 이면 `(R mod N) / R` 이 극히 작음 → 편향 질량 무시 수준.
실무 가이드: `R / N > 10^6` 정도면 현실적 영향 없음.

### 4. 모듈로 회피

`rand() == 0` 식 직접 비교 (범위가 맞을 때). 1/R 확률.

## 왜 옛 게임에 흔한가

- C 시대 `rand()` 가 표준이었고 **확률 구현 = `rand() % N`** 이 관용구.
- 테스트 샘플 사이즈 부족 시 **표기-실제 괴리가 눈에 안 띔** (큰 N 일수록 편향 비율 작음, 작은 N 일수록 티남).
- 편향이 **유저 유리 방향**(희귀템 과대표집)일 때 버그 리포트가 거의 안 옴.
- QA 가 확률을 평균 루트 드랍 횟수로 측정하면 편향을 감지 못함 — 구간별 분포 비교 필요.

## 관련 페이지

- [[concepts/prng-rand]] — C `rand()` 계열 PRNG 의 구조적 한계
- [[sources/maple-modulo-bias]] — 메이플스토리 실제 사례
- [[entities/maplestory]] / [[entities/nexon]]

## 출처

- [[sources/maple-modulo-bias]] — 코딩애플 영상 (2026)
