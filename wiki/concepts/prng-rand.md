---
title: "C rand() 와 PRNG 기초"
type: concept
tags: [prng, cs-fundamentals, c-language, legacy, probability]
created: 2026-04-23
updated: 2026-04-23
sources: [maple-modulo-bias]
aliases: ["rand()", "C rand", "의사난수", "PRNG"]
---

# C `rand()` 와 PRNG 기초

## 핵심 요약

**의사난수 생성기**(Pseudo-Random Number Generator, PRNG) 는 시드로부터 결정론적 수열을 뱉는 함수. C 표준 `rand()` 는 역사적 대표 — `[0, RAND_MAX]` 범위 정수를 **이론상 균등** 분포로 반환. 실제로는 품질·기간·분포 모두 문제가 있어 현대 코드에선 비권장. `rand() % N` 관용구가 [[concepts/modulo-bias]] 를 유발하며 옛 게임 확률 버그의 온상이었음 ([[sources/maple-modulo-bias]]).

## PRNG 일반

- **시드 1개 → 결정론적 무한 수열**. 같은 시드면 같은 수열.
- 품질 지표: 분포 균등성, 주기(period), 통계적 독립성, 예측 불가능성.
- 게임/시뮬레이션: 균등성+주기 중요. 보안: 예측 불가능성 필수 → CSPRNG (`/dev/urandom`, `getrandom()`, `secrets` 모듈).
- `rand()` 는 **non-CSPRNG**. 보안 용도 절대 금지.

## C `rand()` 스펙

- 표준: `<stdlib.h>`, 시드는 `srand(unsigned)`.
- 반환: `int`, 범위 `[0, RAND_MAX]`.
- `RAND_MAX` 는 구현별. POSIX 최소 `32767` (2^15-1). glibc 는 `2^31-1`.
- 알고리즘: 전통적으로 **Linear Congruential Generator (LCG)** — `X_{n+1} = (a·X_n + c) mod m`. 구현별 계수 다름.

### 메이플 영상 맥락

영상이 언급한 "0 ~ 42억 9천" 은 `2^32 = 4,294,967,296` ≈ 32-bit unsigned 상한. 메이플은 `rand()` 자체가 아니라 **32-bit 출력 PRNG** 를 가정 (정확한 내부 함수는 비공개). 결론 구조(= 모듈로 편향 발생)는 동일.

## `rand()` 의 문제점

1. **품질 낮은 LCG** — 주기 짧음(`RAND_MAX` 이하), 하위 비트가 특히 비균등.
2. **스레드 안전성 없음** — 내부 정적 상태 공유. 멀티스레드에서 경쟁 조건 또는 `rand_r` (deprecated) 사용.
3. **분포 편향 유도** — `rand() % N` 관용구가 [[concepts/modulo-bias]] 직행.
4. **범위 제어 어려움** — `[0, N)` 균등 추출 API 없음. 직접 스케일링/모듈로 필요.
5. **비결정론적 이식성** — `RAND_MAX`, LCG 계수가 구현별 → 같은 시드라도 플랫폼 간 결과 다름.

## 현대 대안

| 언어 | 권장 |
|------|------|
| C++11+ | `<random>` — `std::mt19937` + `std::uniform_int_distribution` |
| C (최신) | `arc4random_uniform(N)` (BSD/macOS), `getrandom()` (Linux, CSPRNG) |
| Python | `random.randint` (일반), `secrets.randbelow` (보안) |
| Go | `math/rand/v2` (v1 은 레거시) |
| Rust | `rand` crate — `gen_range(0..N)` |
| Java | `ThreadLocalRandom.current().nextInt(N)` |

핵심: **모든 현대 API 는 범위 지정 버전을 제공**하고 내부에서 편향 처리. 원시 모듈로 관용구 불필요.

## PRNG 알고리즘 계보 (게임/시뮬용)

- **LCG** — 고전, 품질 낮음. `rand()` 유.
- **Mersenne Twister (MT19937)** — 주기 2^19937-1. C++ 표준. 느리고 큰 상태.
- **xorshift / xoroshiro / xoshiro** — 빠르고 작음. 현대 게임 엔진 흔함.
- **PCG** — 2014 M.E. O'Neill. 통계 품질 우수, 작음. 최근 권장.
- **CSPRNG** — ChaCha20, AES-CTR DRBG. 보안 필수.

## 게임 개발 관점 교훈

- **`rand()` + `%` 조합은 레거시 안티패턴**. 신규 코드에서 금지.
- 확률 시스템은 **단위 테스트 가능한 추상 레이어**로 분리. PRNG 를 주입(DI) 받으면 시드 고정 재현 테스트 가능.
- 샘플 사이즈 충분한 **분포 회귀 테스트** 필수 — 카이제곱 검정, 구간별 히스토그램.
- 확률 변경 시 **실제 확률을 측정**해 명세와 일치하는지 검증. 상수만 바꾸고 변경됐다고 가정 금지 (메이플 버프 사례).

## 관련 페이지

- [[concepts/modulo-bias]] — `rand() % N` 이 만드는 편향
- [[sources/maple-modulo-bias]] — 실전 사례
- [[entities/maplestory]] — 영향 받은 게임

## 출처

- [[sources/maple-modulo-bias]] — 코딩애플 영상 요약 + 일반 CS 상식
