---
title: "[개념 #6] 백트래킹 — 선택, 재귀, 되돌리기 (N-Queen · N과 M · 스도쿠 · 연산자 끼워넣기)"
date: 2021-02-18 10:00:00 +0900
categories: [TIL, Algorithm]
tags: [algorithm, backtracking, recursion, python]
series: algorithm
series_order: 9
group: concept
group_order: 6
description: 백트래킹의 공통 틀과 가지치기를 네 문제로 정리
---

{% include series-nav.html series="algorithm" %}

> 문제 출처는 각 플랫폼(백준·프로그래머스·카카오 등)이며, 문제 지문은 옮기지 않고 번호와 링크로 대신합니다. 풀이와 설명은 직접 작성한 것입니다.
{: .prompt-info }

## 🇰🇷 공통 틀
```text
def dfs(깊이):
    if 끝이면: 기록하고 return
    for 후보:
        if 불가능하면: continue     # 가지치기
        선택하기
        dfs(깊이 + 1)
        선택 되돌리기              # 이것이 backtracking
```
**가능성이 없는 가지는 일찍 자르는 것**이 완전 탐색과의 차이입니다.

## N과 M (백준 15649)
1~N 중 중복 없이 M개를 고르는 수열. `checker[]`로 사용 여부를 기록하고, 재귀에서 돌아올 때 `pop()`과 `checker[i] = False`로 **되돌립니다**. 되돌리기를 빠뜨리면 다음 가지가 오염됩니다.

## N-Queen (백준 9663)
행마다 퀸을 하나씩 놓으니 **행이 겹치는 경우는 애초에 없고**, 열과 대각선만 확인합니다. `row[x]`가 x행 퀸의 열이고, 같은 열이거나 `abs(row[x]-row[i]) == x-i`이면 같은 대각선입니다.
```python
def ok(x):
    for i in range(1, x):
        if row[x] == row[i] or abs(row[x] - row[i]) == x - i:
            return False
    return True

def dfs(cnt):
    global result
    if cnt > n:
        result += 1
        return
    for c in range(1, n + 1):
        row[cnt] = c
        if ok(cnt):               # 충돌하면 더 내려가지 않는다
            dfs(cnt + 1)
```

## 스도쿠 (백준 2580)
빈칸 좌표 목록 `empty`를 만들고, 각 칸에서 **행·열·3×3 상자에 없는 숫자**만 후보로 시도합니다. 실패하면 칸을 0으로 되돌려 이전 선택으로 돌아갑니다. 정답을 찾으면 플래그를 올려 나머지 탐색을 멈춥니다.

## 연산자 끼워넣기 (백준 14888)
남은 연산자 개수를 **인자로 넘기며** 분기합니다. 개수가 0이면 그 가지는 들어가지 않습니다. 나눗셈은 문제 규정대로 **0 방향으로 버림**이므로 음수일 때 `-(-a // b)`로 처리했습니다.

## 배운 점
- 상태를 전역에 두면 되돌리기가 필요하고, 인자로 넘기면(연산자 끼워넣기) 되돌릴 필요가 없습니다.
- 디버깅용 출력이 많으면 시간 초과가 납니다. 제출 전 `print`를 반드시 지워야 합니다.

## 🇺🇸 Summary
Backtracking = choose, recurse, undo, with early pruning. N-Queen checks only column and diagonal conflicts (one queen per row), N-and-M needs undoing the "used" flags, Sudoku tries only digits absent from row/column/box, and operator insertion passes remaining operator counts as arguments (no undo needed). Remember that C-style truncating division for negatives is `-(-a // b)` in Python.
