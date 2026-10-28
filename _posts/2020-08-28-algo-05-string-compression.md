---
title: "[알고리즘 #5] 문자열 압축 — 단위를 바꿔 가며 전부 해 보는 완전 탐색"
date: 2020-08-28 10:00:00 +0900
categories: [TIL, Algorithm]
tags: [algorithm, brute-force, string, kakao]
series: algorithm
series_order: 5
description: 카카오 기출 '문자열 압축'을 압축 단위별 길이 계산으로 푼 기록
---

{% include series-nav.html series="algorithm" %}

> 문제 출처는 각 플랫폼(백준·프로그래머스·카카오 등)이며, 문제 지문은 옮기지 않고 번호와 링크로 대신합니다. 풀이와 설명은 직접 작성한 것입니다.
{: .prompt-info }

## 🇰🇷 문제 요약
문자열을 같은 길이 단위로 잘라 **연속으로 반복되는 조각을 `횟수+조각`으로** 줄일 때, 가능한 최단 길이를 구합니다. (카카오 기출 "문자열 압축")

## 아이디어
- 압축 단위는 1부터 `len(s)//2`까지만 보면 됩니다. 그보다 길면 반복이 생길 수 없습니다.
- 단위마다 앞에서부터 잘라 **직전 조각과 같으면 횟수만 올리고**, 다르면 직전 조각의 길이와 횟수 자릿수를 더합니다.
- 압축된 문자열 자체는 만들지 않고 **길이만** 계산합니다. 모든 단위의 결과 중 최솟값이 답입니다.

## 핵심 코드
```python
def solution(s):
    answer = len(s)
    for x in range(1, len(s) // 2 + 1):
        length, prev, cnt = 0, '', 1
        for i in range(0, len(s), x):
            cur = s[i:i + x]
            if cur == prev:
                cnt += 1
            else:
                length += len(cur) + (len(str(cnt)) if cnt > 1 else 0)
                cnt, prev = 1, cur
        if cnt > 1:
            length += len(str(cnt))
        answer = min(answer, length)
    return answer
```
복잡도는 단위 개수 × 문자열 길이, 즉 O(n²)입니다. 입력이 작아 충분합니다.

## 배운 점
- "압축한 결과"가 아니라 **"압축한 길이"만 묻는 경우** 문자열을 만들지 않고 길이만 세면 코드가 단순해집니다.
- 마지막 조각의 횟수 처리를 반복문 밖에서 한 번 더 해야 한다는 점이 틀리기 쉬운 지점입니다.

## 🇺🇸 Summary
For the Kakao "string compression" problem, try every unit size from 1 to len/2, count consecutive repeats while scanning, and compute only the compressed length (not the string). O(n²). The easy bug is forgetting to flush the final run's count after the loop.
