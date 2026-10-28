---
title: "[알고리즘 #3] 에라토스테네스의 체와 괄호 변환 — 구현하다 발견한 off-by-one"
date: 2020-08-18 10:00:00 +0900
categories: [TIL, Algorithm]
tags: [algorithm, prime, recursion, kakao, python]
series: algorithm
series_order: 3
description: 소수 구하기 체의 경계 버그와 카카오 기출 괄호 변환의 재귀 분할
---

{% include series-nav.html series="algorithm" %}

> 문제 출처는 각 플랫폼이며, 문제 지문은 옮기지 않고 이름으로만 가리킵니다. 풀이와 설명은 직접 작성한 것입니다.
{: .prompt-info }

## 🇰🇷 1. 에라토스테네스의 체
범위 안의 소수를 모두 구할 때, 소수의 **배수를 지워 가는** 방식입니다. 각 숫자를 나눠 보는 방식(O(n√n))보다 빠른 O(n log log n)입니다.

처음 구현은 이렇습니다.
```python
def prime_list(n):
    show = [True] * (n + 1)
    for i in range(2, int(n ** 0.5) + 1):
        if show[i]:
            for j in range(i + i, n, i):     # ← n이 빠진다
                show[j] = False
    return [i for i in range(2, n + 1) if show[i]]
```
`prime_list(10)`을 실행하면 `[2, 3, 5, 7, 10]`이 나옵니다. `range(i + i, n, i)`는 **n을 포함하지 않아서** n 자신이 합성수여도 지워지지 않기 때문입니다. 예제가 n=10이라 눈에 띄었습니다.

고친 버전입니다. 시작도 `i*i`로 당기면 중복 작업이 줄어듭니다.
```python
def prime_list(n):
    is_p = [True] * (n + 1)
    is_p[0:2] = [False, False]
    for i in range(2, int(n ** 0.5) + 1):
        if is_p[i]:
            for j in range(i * i, n + 1, i):   # n까지 포함
                is_p[j] = False
    return [i for i, p in enumerate(is_p) if p]
```
> 범위를 다루는 코드는 **끝 값이 포함되는지** 먼저 의심합니다. 이 실수는 이후 풀이에서도 반복됐습니다.
{: .prompt-tip }

## 2. 괄호 변환 (카카오 기출)
균형 잡힌 괄호 문자열을 올바른 괄호 문자열로 바꾸는 재귀 문제입니다. 규칙은 이렇게 요약됩니다.
1. 빈 문자열이거나 이미 올바르면 그대로 둡니다.
2. 더 쪼갤 수 없는 균형 문자열 `u`와 나머지 `v`로 나눕니다.
3. `u`가 올바르면 `u + solution(v)`입니다.
4. 아니면 `"(" + solution(v) + ")"`에 `u`의 양 끝을 뗀 뒤 **괄호 방향을 뒤집어** 붙입니다.

```python
def is_right(s):
    c = 0
    for ch in s:
        c += 1 if ch == '(' else -1
        if c < 0:
            return False
    return c == 0

def split_uv(p):
    c = 0
    for i, ch in enumerate(p):
        c += 1 if ch == '(' else -1
        if c == 0:
            return p[:i + 1], p[i + 1:]

def solution(p):
    if not p or is_right(p):
        return p
    u, v = split_uv(p)
    if is_right(u):
        return u + solution(v)
    flipped = ''.join(')' if ch == '(' else '(' for ch in u[1:-1])
    return '(' + solution(v) + ')' + flipped
```
`"()))((()"`는 `"()(())()"`로 바뀝니다. 직접 실행해서 확인했습니다.

## 배운 점
- 올바른 괄호 판정은 스택 대신 **카운터 하나**로 충분합니다. 카운터가 음수가 되면 바로 실패입니다.
- 문제 설명이 길면 **규칙을 그대로 코드의 분기로 옮기는** 게 가장 안전했습니다.

## 🇺🇸 Summary
Two small lessons: my first sieve used `range(i+i, n, i)`, which excludes n, so `prime_list(10)` wrongly returned 10 as prime — fixed by iterating to `n+1` and starting at `i*i`. For Kakao's "braces change", a single balance counter validates parentheses, and the rules translate directly into a recursion on the (u, v) split.
