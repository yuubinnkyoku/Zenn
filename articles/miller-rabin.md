---
title: Miller–Rabin素数判定 + Brent版Pollard Rho 素因数分解
emoji: 📝
type: tech
topics:
- atcoder
- codon
published: true
---

```python
import random


# -------------------------
# 基本演算
# -------------------------

def gcd(a: int, b: int) -> int:
    while b != 0:
        a, b = b, a % b
    return a


# a * b % mod
# 64ビット同士の積を128ビットで計算する
def mul_mod(a: int, b: int, mod: int) -> int:
    return int(
        (UInt[128](a) * UInt[128](b))
        % UInt[128](mod)
    )


# (x * x + c) % mod
# 加算も含めて128ビットで行う
def rho_next(x: int, c: int, mod: int) -> int:
    return int(
        (
            UInt[128](x) * UInt[128](x)
            + UInt[128](c)
        )
        % UInt[128](mod)
    )


def pow_mod(a: int, e: int, mod: int) -> int:
    result = 1

    while e > 0:
        if e & 1:
            result = mul_mod(result, a, mod)

        a = mul_mod(a, a, mod)
        e >>= 1

    return result


# -------------------------
# Miller-Rabin素数判定
# -------------------------

MR_BASES = (
    2,
    325,
    9375,
    28178,
    450775,
    9780504,
    1795265022,
)

SMALL_PRIMES = (
    2, 3, 5, 7, 11, 13,
    17, 19, 23, 29, 31, 37,
)


def is_prime(n: int) -> bool:
    if n < 2:
        return False

    for p in SMALL_PRIMES:
        if n % p == 0:
            return n == p

    # n - 1 = d * 2^s
    d = n - 1
    s = 0

    while d % 2 == 0:
        d //= 2
        s += 1

    for base in MR_BASES:
        a = base % n

        if a == 0:
            continue

        x = pow_mod(a, d, n)

        if x == 1 or x == n - 1:
            continue

        reached_minus_one = False

        for _ in range(s - 1):
            x = mul_mod(x, x, n)

            if x == n - 1:
                reached_minus_one = True
                break

        if not reached_minus_one:
            return False

    return True


# -------------------------
# Pollard Rho（Brent法）
# -------------------------

def pollard_rho(n: int) -> int:
    if n % 2 == 0:
        return 2

    if n % 3 == 0:
        return 3

    while True:
        y = random.randrange(1, n)
        c = random.randrange(1, n)

        # gcdをまとめて取る個数
        m = 128

        g = 1
        r = 1
        q = 1

        x = 0
        ys = 0

        while g == 1:
            x = y

            # y = f^r(y)
            for _ in range(r):
                y = rho_next(y, c, n)

            k = 0

            while k < r and g == 1:
                ys = y
                limit = min(m, r - k)

                for _ in range(limit):
                    y = rho_next(y, c, n)

                    if x >= y:
                        diff = x - y
                    else:
                        diff = y - x

                    q = mul_mod(q, diff, n)

                g = gcd(q, n)
                k += limit

            r *= 2

        # 積が0になってgcdがnになった場合、
        # 1個ずつ戻って因数を探す
        if g == n:
            while True:
                ys = rho_next(ys, c, n)

                if x >= ys:
                    diff = x - ys
                else:
                    diff = ys - x

                g = gcd(diff, n)

                if g > 1:
                    break

        if g != n:
            return g


# -------------------------
# 素因数分解
# -------------------------

def factorize_rec(n: int, factors: list[int]):
    if n == 1:
        return

    if is_prime(n):
        factors.append(n)
        return

    d = pollard_rho(n)

    factorize_rec(d, factors)
    factorize_rec(n // d, factors)


# 素因数を重複ありのリストで返す
def prime_factorize_list(n: int) -> list[int]:
    factors: list[int] = []

    if n <= 1:
        return factors

    factorize_rec(n, factors)
    factors.sort()

    return factors


# {素因数: 指数} を返す
def prime_factorize(n: int) -> dict[int, int]:
    factors = prime_factorize_list(n)
    result: dict[int, int] = {}

    for p in factors:
        result[p] = result.get(p, 0) + 1

    return result


# -------------------------
# 使用例
# -------------------------

n = int(input())

pf = prime_factorize(n)

for p in sorted(pf):
    print(p, pf[p])
```