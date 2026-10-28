---
title: "[알고리즘 #3] 정렬 — 파이썬 key 정렬부터 카운팅 정렬, 그리고 C로 구현한 삽입·퀵"
date: 2020-08-24 10:00:00 +0900
categories: [TIL, Algorithm]
tags: [algorithm, sorting, python, c]
series: algorithm
series_order: 3
description: 정렬 문제에서 key 함수와 카운팅 정렬을 쓰는 방법, C로 직접 구현한 삽입·퀵 정렬
---

{% include series-nav.html series="algorithm" %}

> 문제 출처는 각 플랫폼(백준·프로그래머스·카카오 등)이며, 문제 지문은 옮기지 않고 번호와 링크로 대신합니다. 풀이와 설명은 직접 작성한 것입니다.
{: .prompt-info }

## 🇰🇷 1. key 함수로 풀리는 정렬 (프로그래머스)
**가장 큰 수**는 숫자를 문자열로 바꿔 **이어 붙였을 때 큰 순서**로 정렬하면 됩니다. 원소가 1000 이하라서 각 문자열을 3번 반복한 값으로 비교하면 길이가 달라도 올바르게 비교됩니다.
```python
def bigNumberSort(num):
    num = list(map(str, num))
    num.sort(key=lambda x: x * 3, reverse=True)
    return str(int(''.join(num)))     # 전부 0인 경우 "0"으로 정리
```
**H-Index**는 내림차순 정렬 후 `citations[i] < i+1`이 처음 되는 위치가 답입니다.
```python
def solution(citations):
    citations.sort(reverse=True)
    for i in range(len(citations)):
        if citations[i] < (i + 1):
            return i
    return len(citations)
```

## 2. 비교 정렬로 안 될 때: 카운팅 정렬 (백준 10989)
값의 범위(1~10000)가 작고 개수가 많으면, 값별 **등장 횟수만 세는** 카운팅 정렬이 맞습니다. 입력을 리스트에 모아 정렬하는 풀이는 메모리 제한에 걸렸습니다.
```python
import sys
n = int(sys.stdin.readline())
cnt = [0] * 10001
for _ in range(n):
    cnt[int(sys.stdin.readline())] += 1
for v in range(10001):
    for _ in range(cnt[v]):
        print(v)
```
**좌표 압축**(백준 18870)은 `sorted(set(...))`로 순위를 만들고 딕셔너리로 변환합니다. 중복을 만나면 매번 비교하는 이중 반복문은 시간 초과였습니다.
```python
n = int(input())
a = list(map(int, input().split()))
rank = {v: i for i, v in enumerate(sorted(set(a)))}
print(*[rank[v] for v in a])
```

## 3. C로 구현한 삽입 정렬과 퀵 정렬
책을 따라 C로 직접 구현했습니다.
- **삽입 정렬**: 손에 카드를 한 장씩 집어 알맞은 위치에 끼워 넣는 방식입니다. 이미 정렬된 구간은 비교를 건너뛰고, 밀어내는 작업은 `memmove`로 합니다. 평균 O(n²), 거의 정렬된 입력에서는 빠릅니다.
- **퀵 정렬**: 첫 원소를 피벗으로 잡고, 왼쪽은 피벗 이하, 오른쪽은 초과가 되도록 분할한 뒤 양쪽을 재귀 정렬합니다. 평균 O(n log n)이지만 이미 정렬된 입력에서 피벗이 첫 원소면 최악 O(n²)입니다.

## 배운 점
- 정렬은 **"어떤 기준으로 비교할 것인가"** 를 `key`로 표현하는 문제인 경우가 많습니다.
- 데이터 범위가 작으면 비교 정렬보다 카운팅이 낫습니다.

## 🇺🇸 Summary
Most sorting problems reduce to choosing the right key (e.g., concatenation order for "largest number"; `x*3` works when values are ≤1000). When the value range is small, counting sort avoids memory blow-ups; coordinate compression uses `sorted(set(...))` plus a dict. I also implemented insertion sort and quicksort in C: insertion is O(n²) but fast on nearly-sorted data, quicksort is O(n log n) on average with an O(n²) worst case.
