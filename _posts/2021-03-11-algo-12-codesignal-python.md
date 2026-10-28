---
title: "[알고리즘 #12] CodeSignal Python Arcade — 파이썬다운 풀이 익히기"
date: 2021-03-11 12:00:00 +0900
categories: [TIL, Algorithm]
tags: [algorithm, codesignal, python, generator, lambda]
series: algorithm
series_order: 12
description: urlparse, translate, yield, 정렬 key 등 파이썬 내장 기능으로 짧게 푸는 법
---

{% include series-nav.html series="algorithm" %}

> 문제 출처는 각 플랫폼이며, 문제 지문은 옮기지 않고 이름으로만 가리킵니다. 풀이와 설명은 직접 작성한 것입니다.
{: .prompt-info }

## 🇰🇷 개요
CodeSignal Arcade의 **Python 구간**(약 40문제)은 알고리즘보다 **파이썬 내장 기능을 얼마나 아는가**를 묻습니다. 정리한 구간은 Meet Python, Lurking in Lists, Lambda Illusions, Slithering in Strings, 문자열 파싱, Showing Class(클래스), Yin and Yang of Yields(제너레이터) 등입니다.

## 문자열: `translate`로 치환표 한 번에
소문자 26자를 다른 26자로 바꾸는 암호는 치환표를 만들어 한 번에 적용합니다.
```python
import string

def permutationCipher(password, key):
    table = str.maketrans(string.ascii_lowercase, key)
    return password.translate(table)
```
반복문으로 문자를 하나씩 바꾸는 풀이보다 짧고 빠릅니다.

## 람다 정렬: 성(姓) 기준 정렬
```python
def sortStudents(students):
    students.sort(key=lambda x: x.split()[-1])
    return students
```
`sort`는 안정 정렬이라 성이 같으면 **원래 순서**가 유지됩니다.

## 파싱: URL 유사도
`urllib.parse`로 스킴, 호스트, 경로, 쿼리를 나눠서 구간별 점수를 더합니다. 경로는 앞에서부터 **같은 구간이 이어지는 만큼만** 세므로 `takewhile`이 어울립니다.
```python
from urllib.parse import urlparse, parse_qs
from itertools import takewhile

def urlSimilarity(url1, url2):
    o1, o2 = urlparse(url1), urlparse(url2)
    s = (o1.scheme == o2.scheme) * 5 + (o1.netloc == o2.netloc) * 10
    p1, p2 = o1.path.split('/')[1:], o2.path.split('/')[1:]
    s += len(list(takewhile(lambda x: x[0] == x[1], zip(p1, p2))))
    q1, q2 = parse_qs(o1.query), parse_qs(o2.query)
    s += sum(2 if q1[k] == q2[k] else 1 for k in q1.keys() & q2.keys())
    return s
```
직접 문자열을 자르던 풀이보다 훨씬 짧고, 경계 조건을 표준 라이브러리가 처리합니다.

## 제너레이터: `yield`
구매 목록에서 n번째마다 조건을 만족하는 경우만 뽑아내는 문제를 **이터레이터 클래스**와 `yield` 두 방식으로 풀었습니다.
```python
def prizes(purchases, n, d):
    for i in range(n - 1, len(purchases), n):
        if purchases[i] % d == 0:
            yield i + 1

def superPrize(purchases, n, d):
    return list(prizes(purchases, n, d))
```
`yield`를 쓰면 상태를 클래스에 저장하는 코드가 사라지고 **필요할 때 하나씩** 계산합니다.

## 배운 점
- 파이썬 구간은 "알고리즘"이 아니라 **표준 라이브러리 어휘집**입니다. `translate`, `takewhile`, `urlparse`를 알면 반복문 20줄이 5줄이 됩니다.
- 안정 정렬, 제너레이터 같은 개념은 실무 코드에서도 바로 씁니다.

## 🇺🇸 Summary
The Python section of CodeSignal Arcade is about idiomatic use of the standard library: `str.maketrans/translate` for substitution ciphers, `key=lambda` with stable sort, `urllib.parse` plus `itertools.takewhile` for URL similarity, and generators with `yield` instead of hand-written iterator classes.
