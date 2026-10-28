---
title: "[알고리즘 #8] LeetCode 풀이 모음 — 슬라이딩 윈도우, 중심 확장, 비트 연산"
date: 2021-02-09 10:00:00 +0900
categories: [TIL, Algorithm]
tags: [algorithm, leetcode, python, sliding-window]
series: algorithm
series_order: 8
description: LeetCode 10문제 중 패턴이 뚜렷한 풀이와 고쳐 써 본 풀이
---

{% include series-nav.html series="algorithm" %}

> 문제 출처는 각 플랫폼이며, 문제 지문은 옮기지 않고 이름으로만 가리킵니다. 풀이와 설명은 직접 작성한 것입니다.
{: .prompt-info }

## 🇰🇷 풀이 목록
| 문제 | 핵심 |
|---|---|
| Longest Substring Without Repeating Characters | 슬라이딩 윈도우 + 딕셔너리 |
| Longest Palindromic Substring | 중심에서 확장 |
| Zigzag Conversion | 방향 전환 시뮬레이션 |
| Sum of All Subset XOR Totals | 반복 / 재귀 / 비트마스크 |
| Display Table of Food Orders | 딕셔너리 집계 |
| Count of Matches in Tournament | 관찰 |
| Median of Two Sorted Arrays, Reverse Integer, Add Two Numbers | 미완성 또는 개선 필요 |

## 슬라이딩 윈도우: 중복 없는 가장 긴 부분 문자열
각 문자의 **마지막 위치**를 기억하고, 중복이 나오면 윈도우 시작을 그 다음 칸으로 당깁니다. O(n)입니다.
```python
def lengthOfLongestSubstring(s):
    last, res, start = {}, 0, 0
    for i, ch in enumerate(s):
        if ch in last:
            start = max(start, last[ch] + 1)
        last[ch] = i
        res = max(res, i - start + 1)
    return res
```
`start = max(start, ...)`가 핵심입니다. 이미 지나간 중복이 윈도우를 **뒤로** 되돌리지 못하게 막습니다.

## 중심 확장: 가장 긴 팰린드롬 부분 문자열
모든 위치를 중심으로 놓고 양쪽으로 넓힙니다. 길이가 홀수/짝수인 두 경우를 모두 확인합니다. O(n²)입니다.
```python
def longestPalindrome(s):
    def expand(l, r):
        while l >= 0 and r < len(s) and s[l] == s[r]:
            l, r = l - 1, r + 1
        return s[l + 1:r]
    if len(s) < 2 or s == s[::-1]:
        return s
    best = ''
    for i in range(len(s) - 1):
        best = max(best, expand(i, i + 1), expand(i, i + 2), key=len)
    return best
```

## 같은 문제를 세 가지로: 부분집합 XOR 합
반복(부분집합을 하나씩 늘리기), 재귀(넣는다/안 넣는다), 비트마스크(`1 << n`개의 조합)로 풀었습니다. 셋 다 O(2ⁿ)입니다. 이 문제에는 수학적 지름길도 있습니다. **모든 원소의 OR 값 × 2^(n-1)** 이 답입니다. `[1,3]`은 6, `[5,1,6]`은 28로 직접 확인했습니다.
```python
from functools import reduce
def subsetXORSum(nums):
    return reduce(lambda a, b: a | b, nums, 0) * 2 ** (len(nums) - 1)
```

## 개선이 필요했던 풀이 (솔직하게)
- **Median of Two Sorted Arrays**: 두 배열을 합쳐 정렬하고 `median()`을 쓰는 O(n log n) 풀이입니다. 문제가 요구하는 O(log(m+n))이 아닙니다. 이분 탐색 풀이는 아직 못 했습니다.
- **Reverse Integer**: 범위 검사 조건이 거꾸로라서, 정상 범위의 값에는 0을 돌려주고 범위를 벗어난 값에서는 오류가 나는 미완성 코드였습니다. 다시 쓰면 이렇습니다.
```python
def reverse(x):
    r = int(str(abs(x))[::-1]) * (1 if x >= 0 else -1)
    return r if -2**31 <= r <= 2**31 - 1 else 0
```
`123 → 321`, `-123 → -321`, `1534236469 → 0`을 확인했습니다.
- **Count of Matches**: 재귀로 풀었지만, 한 경기마다 한 팀이 탈락하므로 답은 항상 `n - 1`입니다.

## 배운 점
- 관찰 하나로 풀이가 한 줄이 되는 문제가 있고(Count of Matches, 부분집합 XOR), 이 관찰은 **작은 입력을 손으로 계산**할 때 나옵니다.
- 통과한 풀이도 요구 복잡도와 다르면 미완성으로 기록해 둡니다.

## 🇺🇸 Summary
Ten LeetCode problems with recurring patterns: sliding window with last-seen positions, expand-around-center for palindromes, and three variants of subset-XOR (plus the O(n) closed form: OR of all elements × 2^(n-1)). I also note honest gaps: the median solution is O(n log n) instead of O(log(m+n)), and my original reverse-integer range check was inverted (rewritten and tested here).
