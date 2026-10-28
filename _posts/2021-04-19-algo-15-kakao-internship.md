---
title: "[카카오 #2] 카카오 인턴십 기출 정리 — 이분 탐색, 투 포인터, 유니온 파인드"
date: 2021-04-19 10:00:00 +0900
categories: [TIL, Algorithm]
tags: [algorithm, kakao, binary-search, two-pointers, union-find, python]
series: algorithm
series_order: 15
group: kakao
group_order: 2
description: 징검다리 건너기, 보석 쇼핑, 호텔 방 배정 등 카카오 인턴십 기출에서 반복된 패턴
---

{% include series-nav.html series="algorithm" %}

> 문제 출처는 각 플랫폼이며, 문제 지문은 옮기지 않고 이름으로만 가리킵니다. 풀이와 설명은 직접 작성한 것입니다.
{: .prompt-info }

## 🇰🇷 14문제를 패턴으로 묶기
2019~2021 카카오 인턴십 기출을 풀면서 **같은 패턴이 반복**되는 것을 정리합니다. 효율성 테스트에서 O(n²)가 막히는 문제들이 특히 배울 게 많았습니다.

| 문제 | 패턴 |
|---|---|
| 징검다리 건너기 | **이분 탐색**(답에 대한 이분 탐색) |
| 보석 쇼핑 | **투 포인터**(슬라이딩 윈도우) |
| 호텔 방 배정 | **유니온 파인드**(다음 빈 방 찾기) |
| 크레인 인형뽑기 | 스택 |
| 튜플 | 집합 차 |
| 수식 최대화 | 순열 + 우선순위 시뮬레이션 |
| 표 편집 | 스택(삭제 복구) |
| 키패드 누르기 | 격자 거리 시뮬레이션 |

## 징검다리 건너기 — 답에 대해 이분 탐색
"몇 명까지 건널 수 있나"를 하나씩 시뮬레이션하면 시간 초과입니다. 반대로 **"m명이 건널 수 있는가?"** 는 한 번 훑어 판정할 수 있고, m이 커질수록 건널 수 없게 되는 **단조성**이 있어서 이분 탐색이 됩니다. 건널 수 없다는 조건은 `stone < m`인 칸이 k개 이상 연속될 때입니다.
```python
def solution(stones, k):
    lo, hi = 1, max(stones) + 1          # hi는 불가능한 값
    def ok(m):
        run = 0
        for s in stones:
            run = run + 1 if s < m else 0
            if run >= k:
                return False
        return True
    while lo < hi - 1:
        mid = (lo + hi) // 2
        lo, hi = (mid, hi) if ok(mid) else (lo, mid)
    return lo
```
처음 풀이는 한 명씩 건너 보는 `while` 시뮬레이션이었고, 이런 방식은 돌 개수가 커지면 시간이 문제가 되는 유형입니다. 복잡도는 O(n log max)입니다.

## 보석 쇼핑 — 투 포인터
모든 종류를 포함하는 **가장 짧은 구간**을 찾습니다. 오른쪽 끝을 늘려 가며 종류를 채우고, 다 채워지면 왼쪽을 줄여 가며 최단 구간을 갱신합니다.
```python
from collections import defaultdict

def solution(gems):
    need = len(set(gems))
    cnt, best, l = defaultdict(int), [0, len(gems) - 1], 0
    for r, x in enumerate(gems):
        cnt[x] += 1
        while len(cnt) == need:
            if r - l < best[1] - best[0]:
                best = [l, r]
            cnt[gems[l]] -= 1
            if cnt[gems[l]] == 0:
                del cnt[gems[l]]
            l += 1
    return [best[0] + 1, best[1] + 1]
```
저장소에는 시작점마다 처음부터 다시 훑는 O(n²) 풀이와 투 포인터 풀이가 같이 있습니다. 후자는 O(n)입니다.

## 호텔 방 배정 — 유니온 파인드
"원하는 방이 비어 있으면 그 방, 아니면 **그보다 큰 번호 중 가장 가까운 빈 방**"을 큰 방 수에서 빠르게 찾아야 합니다. 방을 배정할 때마다 `다음 후보 = 방 + 1`로 연결하고, 찾을 때 **경로를 압축**합니다.
```python
import sys
sys.setrecursionlimit(10**7)

def solution(k, room_number):
    nxt, answer = {}, []
    def find(x):
        if x not in nxt:
            return x
        nxt[x] = find(nxt[x])      # 경로 압축
        return nxt[x]
    for r in room_number:
        a = find(r)
        answer.append(a)
        nxt[a] = a + 1
    return answer
```
`k=10`, `[1,3,4,1,3,1]`이면 `[1,3,4,2,5,6]`이 나옵니다.

## 배운 점
- 최댓값/최솟값을 묻는 문제는 **"이 값이 가능한가?"** 로 바꿔서 이분 탐색이 되는지 먼저 봅니다.
- 재귀를 쓰는 유니온 파인드는 깊이가 커질 수 있어 `setrecursionlimit`이 필요했습니다.
- 저장소에 미완성인 풀이(미로 탈출)도 있습니다. 이 글에는 정답을 확인한 풀이만 실었습니다.

## 🇺🇸 Summary
Kakao internship problems reward recognizing patterns: binary search on the answer (stepping stones), two pointers for the shortest window covering all kinds (gem shopping), and union-find with path compression for "next free room". Naive simulations pass the examples but fail efficiency tests. I include only solutions I verified; one unfinished solution stays out.
