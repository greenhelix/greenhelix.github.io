---
title: "[개념 #10] 시간복잡도 — O(1), O(log n), O(n), O(n log n), O(n²) 체감 차이"
date: 2026-11-26 20:00:00 +0900
categories: [TIL, Algorithm]
tags: [algorithm, time-complexity, big-o]
series: algorithm
series_order: 25
group: concept
group_order: 10
description: Big-O 표기법(O(1)/O(log n)/O(n)/O(n log n)/O(n²))의 의미를 숫자로 체감하기, 코딩테스트 입력 크기로 허용 복잡도를 역산하는 실전 감
---

{% include series-nav.html series="algorithm" %}

코딩테스트를 준비하면서 "이 코드 시간복잡도 어떻게 돼?"라는 질문을 스스로 계속 던지게 되는데, 정작 O(n)과 O(n²)이 숫자로 얼마나 차이 나는지 감이 없으면 질문 자체가 공허해진다. 그래서 체감 가능한 숫자로 정리해봤다.

## Big-O가 보는 건 "정확한 시간"이 아니라 "늘어나는 모양"

Big-O는 "이 코드가 1.2초 걸린다"처럼 정확한 시간을 말하는 게 아니라, **입력 크기 `n`이 커질 때 연산 횟수가 어떤 모양으로 늘어나는지**를 나타낸다. 상수 배(2배, 10배 빠르다 느리다)는 무시하고, "늘어나는 차수(class)"만 본다 — 그래서 실제 체감 속도와 어긋나 보일 때도 있지만(상수항이 큰 O(n)이 작은 O(n log n)보다 느릴 수도 있음), **n이 충분히 커지면 결국 차수가 이깁니다.**

## 각 복잡도, 익숙한 예시로 매칭하기

| 복잡도 | 의미 | 익숙한 예시 |
|---|---|---|
| O(1) | n과 무관하게 항상 일정 | 배열 인덱스 접근, 해시맵 조회, `deque`의 앞/뒤 push·pop |
| O(log n) | n이 두 배 되어도 "한 단계"만 늘어남 | 이진 탐색, 힙(우선순위 큐)의 삽입/삭제 |
| O(n) | n에 비례 | 배열 한 번 순회, `deque`로 만든 큐의 전체 처리 |
| O(n log n) | n번 × (매번 log n) | 효율적인 정렬(퀵/병합/힙 정렬) |
| O(n²) | 이중 루프 | 모든 쌍을 비교, 비효율적인 전체 스캔 반복 |
| O(2ⁿ) | n이 하나 늘 때마다 "두 배" | 모든 부분집합을 다 시도하는 완전탐색 |

## 숫자로 체감하기 — n이 커질수록 벌어지는 차이

| n | O(log n) | O(n) | O(n log n) | O(n²) |
|---|---|---|---|---|
| 10 | ~3 | 10 | ~33 | 100 |
| 100 | ~7 | 100 | ~664 | 10,000 |
| 1,000 | ~10 | 1,000 | ~9,966 | 1,000,000 |
| 10,000 | ~13 | 10,000 | ~132,877 | 100,000,000 |
| 100,000 | ~17 | 100,000 | ~1,660,964 | 10,000,000,000 |
| 1,000,000 | ~20 | 1,000,000 | ~19,931,569 | 1,000,000,000,000 |

n=1,000,000일 때 O(n)과 O(n²)의 차이가 **100만 대 1조** — 자릿수가 6개나 벌어집니다. 작은 입력에서는 "둘 다 순식간"이라 구분이 안 되다가, 어느 순간부터 하나는 통과하고 하나는 시간초과가 나는 지점이 반드시 생깁니다.

## 실전 꿀팁 — 입력 크기로 "허용되는 복잡도" 역산하기

코딩테스트 환경은 보통 1~2초 제한이고, 컴퓨터는 1초에 대략 **1억(10⁸)번 정도의 단순 연산**을 처리한다는 경험칙이 있다. 이걸로 문제의 입력 크기 제한(`n ≤ ...`)만 보고 "이 복잡도로 풀어도 되는지"를 미리 가늠할 수 있다.

| 문제의 n 제한 | 대략 허용되는 복잡도 |
|---|---|
| n ≤ 11 | O(n!) (완전탐색도 가능) |
| n ≤ 25~30 | O(2ⁿ) |
| n ≤ 500 | O(n³) |
| n ≤ 2,000~5,000 | O(n²) |
| n ≤ 1,000,000 | O(n log n) |
| n ≤ 10,000,000 이상 | O(n) 혹은 그 이하 |

이건 절대적인 공식이 아니라 "대략적인 감"이지만, 문제를 읽자마자 "n이 10만이네, 그럼 O(n²)은 위험하겠다"는 판단을 코드 짜기 전에 먼저 할 수 있다는 게 핵심이다.

## 지금까지 공부한 문제들과 연결

- **기능개발**: `list.pop(0)`을 n번 반복하면 O(n²), `deque.popleft()`로 바꾸면 O(n).
- **프로세스**: 매번 남은 큐를 전체 스캔하면 O(n²), 힙으로 "지금까지 최댓값"을 관리하면 O(n log n).
- **주식가격**: 감소하는 스택으로 각 원소를 "최대 한 번씩만" push/pop하므로 O(n).
- **다중 기기 테스트 부하 분산**: 매번 전체 기기를 스캔하면 O(n·k), 최소 힙으로 관리하면 O(n log k).

패턴이 보인다 — **"매번 전체를 다시 훑어보는 구조"가 보이면 O(n²) 후보**고, 거기서 "지금까지의 최댓값/최솟값"이나 "지금까지의 누적 상태"만 알면 되는 경우라면 힙이나 적절한 자료구조로 O(n log n) 혹은 O(n)까지 줄일 여지가 있다는 것.

## 배운 점

- Big-O는 정확한 시간이 아니라 "n이 커질 때 늘어나는 모양"을 비교하는 도구다.
- 체감이 안 될 땐 숫자 표를 직접 만들어보면 된다 — n=100만일 때 O(n)과 O(n²)의 차이는 100만 대 1조.
- "컴퓨터는 1초에 약 1억 번 연산"이라는 경험칙으로, 문제의 입력 크기 제한만 보고 어떤 복잡도가 필요한지 코드 짜기 전에 먼저 가늠할 수 있다.

## 🇺🇸 Summary

A reference post on Big-O growth rates, built to make the abstract classes (O(1), O(log n), O(n), O(n log n), O(n²), O(2ⁿ)) concrete with actual numbers. Built a table showing how O(n) vs O(n²) diverge as n grows (at n=1,000,000, roughly 1 million operations vs 1 trillion — a six-order-of-magnitude gap), and introduced the practical coding-test rule of thumb: assuming a computer handles on the order of 10⁸ operations per second within a typical 1–2 second limit, a problem's stated input-size bound can be used to back-estimate which complexity class is safe before writing any code (e.g., n ≤ 2,000–5,000 tolerates O(n²), n ≤ 1,000,000 needs O(n log n), n ≤ 10,000,000+ needs O(n) or better). Tied this back to concrete examples from this prep series: `list.pop(0)` vs `deque.popleft()` in Feature Development, full-queue scanning vs a heap in Process, and the monotonic stack in Stock Price — all instances of the same underlying pattern: spotting a "rescan everything each time" structure and replacing it with a data structure that tracks the needed running state directly.
