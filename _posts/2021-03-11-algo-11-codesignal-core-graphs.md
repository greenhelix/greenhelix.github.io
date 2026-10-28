---
title: "[알고리즘 #11] CodeSignal Core · Graphs · Interview Practice — 비트 연산과 그래프 문제"
date: 2021-03-11 11:00:00 +0900
categories: [TIL, Algorithm]
tags: [algorithm, codesignal, python, graph, bit]
series: algorithm
series_order: 11
description: 비트 반전, 첫 중복 찾기, 2-hop 도로망 판정 등 CodeSignal Python 풀이
---

{% include series-nav.html series="algorithm" %}

> 문제 출처는 각 플랫폼이며, 문제 지문은 옮기지 않고 이름으로만 가리킵니다. 풀이와 설명은 직접 작성한 것입니다.
{: .prompt-info }

## 🇰🇷 비트 연산: Mirror Bits
정수를 2진수로 쓴 뒤 **좌우로 뒤집은** 값을 구합니다. 문자열 변환이 가장 짧습니다.
```python
def mirrorBits(a):
    return int(format(a, 'b')[::-1], 2)
```
`67`(`1000011`)은 `1100001` = `97`입니다. 같은 문제를 `& 1`과 `>>`로 비트를 하나씩 옮기는 방식으로도 풀 수 있지만, 문자열 풀이가 읽기 쉬웠습니다.

## 해시 집합: First Duplicate
배열에서 **두 번째 등장 위치가 가장 빠른** 값을 찾습니다. 지나온 값을 집합에 넣어 두고 처음 다시 만나는 값을 반환합니다. O(n)입니다.
```python
def firstDuplicate(a):
    seen = set()
    for x in a:
        if x in seen:
            return x
        seen.add(x)
    return -1
```
`[2,1,3,5,3,2]`는 `3`입니다. (2가 먼저 나왔어도 **두 번째 등장이 먼저인** 3이 답입니다.)

## 그래프: Efficient Road Network
모든 도시 쌍이 **최대 2개의 도로**(2-hop)로 연결되는지 판정합니다. 각 도시에서 1-hop 이웃과 그 이웃의 이웃을 합쳐, 모든 도시를 덮는지 확인합니다.
```python
def efficientRoadNetwork(n, roads):
    adj = [[] for _ in range(n)]
    for a, b in roads:
        adj[a].append(b)
        adj[b].append(a)
    for city in range(n - 1):
        one = set(adj[city])
        two = {c for x in one for c in adj[x]}
        if len({city} | one | two) < n:
            return False
    return True
```
마지막 도시는 검사하지 않아도 되는 이유는, 앞의 모든 도시가 2-hop 안에 마지막 도시를 포함한다면 대칭이기 때문입니다.

## 그래프: Roads Building
이미 있는 도로를 제외한 **지을 수 있는 모든 도로**를 구합니다. 가능한 모든 순서쌍에서 기존 도로(양방향)를 빼고, `a < b`인 쌍만 정렬해 반환합니다.

## 다 풀지 못한 것
Merging Cities처럼 인접 행렬을 직접 병합하는 풀이는 저장소에서 디버깅 출력이 남은 **작업 중 상태**입니다. 이 글에는 싣지 않았습니다.

## 배운 점
- 그래프 문제는 인접 리스트로 바꾸는 순간 문제의 절반이 풀립니다.
- "모든 쌍"을 직접 훑는 풀이보다, 각 정점에서 **한두 단계만 퍼뜨리는** 풀이가 더 짧고 빠릅니다.

## 🇺🇸 Summary
CodeSignal Core/Graphs/Interview Practice in Python: Mirror Bits via string reversal, First Duplicate with a hash set (O(n)), and Efficient Road Network by checking that every city reaches all others within two hops. An unfinished adjacency-matrix merge is intentionally left out.
