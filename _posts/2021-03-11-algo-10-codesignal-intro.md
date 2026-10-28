---
title: "[알고리즘 #10] CodeSignal Arcade Intro (Java) — 60문제에서 건진 패턴"
date: 2021-03-11 10:00:00 +0900
categories: [TIL, Algorithm]
tags: [algorithm, codesignal, java, backtracking, string]
series: algorithm
series_order: 10
description: CodeSignal 입문 아케이드 Java 풀이 중 배울 점이 있던 문제들
---

{% include series-nav.html series="algorithm" %}

> 문제 출처는 각 플랫폼이며, 문제 지문은 옮기지 않고 이름으로만 가리킵니다. 풀이와 설명은 직접 작성한 것입니다.
{: .prompt-info }

## 🇰🇷 개요
CodeSignal(구 CodeFights)의 Arcade **Intro** 구간을 Java로 풀었습니다. 약 60문제이고 난이도가 낮은 것부터 섞여 있습니다. 모든 풀이를 나열하지 않고, **배울 점이 있었던 문제**만 골랐습니다. 풀이는 플랫폼 규칙상 전부 공개하지 않고 대표 문제만 싣습니다.

| 구간 | 주제 |
|---|---|
| The Journey Begins ~ Edge of the Ocean | 기초 문자열/배열, 팰린드롬, 인접 곱 |
| Smooth Sailing | 문자열 처리, 괄호 안 뒤집기 |
| Exploring the Waters ~ Island of Knowledge | 배열 변환, 지뢰 찾기 |
| Through the Fog ~ Diving Deeper | 백트래킹, 수학, 구간 합 |
| Dark Wilderness ~ Land of Logic | 구현, 파일 이름 중복, 스도쿠 |

## 대표 1: Strings Rearrangement — 백트래킹
문자열 배열을 순서만 바꿔 **인접한 문자열이 정확히 한 글자만 다르게** 나열할 수 있는지 묻습니다. 모든 순열을 시도하되, 이전 문자열과 한 글자 차이가 아니면 **가지를 자릅니다.**
```java
boolean success = false;

boolean stringsRearrangement(String[] input) {
    findSequence(input, null, new boolean[input.length], 0);
    return success;
}

void findSequence(String[] a, String prev, boolean[] used, int n) {
    if (n == a.length) { success = true; return; }
    for (int i = 0; i < a.length; i++) {
        if (!used[i] && (prev == null || differByOne(prev, a[i]))) {
            used[i] = true;
            findSequence(a, a[i], used, n + 1);
            used[i] = false;                 // 되돌리기
        }
    }
}

boolean differByOne(String a, String b) {
    int count = 0;
    for (int i = 0; i < a.length(); i++)
        if (a.charAt(i) != b.charAt(i)) count++;
    return count == 1;
}
```
앞선 백트래킹 글과 같은 틀(선택 → 재귀 → 되돌리기)입니다. 성공하면 `success`를 올리지만 나머지 탐색을 멈추지는 않으므로, 성공 시 곧바로 `return`하게 하면 더 빠릅니다.

## 대표 2: File Naming — 직접 짠 풀이 vs 단순한 풀이
같은 이름이 나오면 `name(1)`, `name(2)`처럼 번호를 붙입니다. 처음에는 이중 반복문과 인덱스 되돌리기(`j = -1`)로 짰는데, 이해하기 어려웠습니다. 저장소에는 더 단순한 풀이(`elite…`라는 이름으로 남김)도 같이 있습니다. 핵심은 **"결과 리스트에 이미 있으면 번호를 올려가며 비어 있는 이름을 찾는다"** 입니다.
```java
List<String> result = new ArrayList<>();
for (String s : names) {
    if (result.contains(s)) {
        int i = 1;
        while (result.contains(s + "(" + i + ")")) i++;
        s += "(" + i + ")";
    }
    result.add(s);
}
```
`contains`가 O(n)이라 전체가 O(n²)입니다. `HashSet`을 쓰면 O(n)에 가깝게 줄일 수 있습니다.

## 대표 3: Reverse in Parentheses — 재귀로 안쪽부터
가장 안쪽 괄호부터 뒤집어 풀어 나갑니다. 문자열을 앞에서 훑다가 첫 `)`를 만나면, 그 앞에서 가장 가까운 `(`와 사이를 뒤집고 괄호를 제거한 문자열로 **다시 호출**합니다.

## 배운 점
- 쉬운 문제도 **더 단순한 표현**이 있는지 보는 습관이 생겼습니다(File Naming).
- 같은 틀이 반복됩니다. 백트래킹은 이 구간에서 처음 몸에 익었습니다.
- 모든 문제를 풀어야 한다고 생각하면 지칩니다. 구간별 대표 문제만 복기하는 방식이 효율적이었습니다.

## 🇺🇸 Summary
I solved CodeSignal's Arcade Intro (≈60 problems) in Java. This post keeps the instructive ones: Strings Rearrangement (backtracking with pruning), File Naming (simplified from a tangled double loop to "find the first free suffix"), and Reverse in Parentheses (recursion from the innermost pair). Only representative solutions are shown, not the full set.
