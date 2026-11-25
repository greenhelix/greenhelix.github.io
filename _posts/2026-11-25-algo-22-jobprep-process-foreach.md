---
title: "[코테준비 #4] 프로세스 — 큐 시뮬레이션과 파이썬 for...else 뜯어보기"
date: 2026-11-25 16:00:00 +0900
categories: [TIL, Algorithm]
tags: [algorithm, coding-test, java, python, queue, programmers]
series: algorithm
series_order: 22
group: jobprep
group_order: 4
description: 프로그래머스 프로세스(구 프린터) 풀이, 큐를 이용한 우선순위 시뮬레이션, 파이썬 for...else 문법과 자바로 옮길 때 필요한 boolean 플래그 패턴 비교
---

{% include series-nav.html series="algorithm" %}

> 문제 출처는 각 플랫폼(백준·프로그래머스 등)이며, 문제 지문은 옮기지 않고 번호와 이름으로만 표시합니다.
{: .prompt-info }

1주차 세 번째 문제 **프로세스**(프로그래머스, 스택/큐 카테고리 — 예전에는 "프린터"라는 이름으로 불렸음)를 풀면서 정리한 내용입니다.

## 문제 아이디어

프로세스별 우선순위가 담긴 배열과, 내가 확인하고 싶은 프로세스의 위치가 주어집니다. 큐에서 다음 과정을 반복합니다.

1. 맨 앞 프로세스를 꺼낸다.
2. 큐에 남은 프로세스 중 지금 꺼낸 것보다 우선순위가 높은 게 있으면, 다시 맨 뒤로 넣는다.
3. 없으면 실행한다(실행 순서를 센다).

이 과정을 반복하다가, 내가 궁금한 프로세스가 몇 번째로 실행되는지 구하는 문제입니다.

## 파이썬 풀이와 `for...else`

```python
def solution(prior, loc):
    answer = 0
    docs = list((prior[i], i) for i in range(len(prior)))

    while docs:
        task = docs.pop(0)
        for i in docs:
            if task[0] < i[0]:
                docs.append(task)
                break
        else:
            answer += 1
            if task[1] == loc:
                return answer
    return answer
```

핵심은 자바에는 없는 파이썬의 `for...else` 구문입니다. 이 `else`는 **for 루프가 `break` 없이 끝까지 다 돌았을 때만** 실행됩니다.

- 루프 중간에 더 높은 우선순위를 찾아서 `break` → `else`는 건너뜀 → 아직 실행할 때가 아니므로 큐 맨 뒤로 재삽입.
- 끝까지 돌았는데 더 높은 우선순위가 없음(`break` 없음) → `else` 실행 → 지금 바로 실행 순서로 카운트.

즉 `break`가 "더 높은 걸 찾아서 조기 종료"의 신호, `else`가 "못 찾고 끝까지 돌았다"의 신호로 쓰인 것으로, 원래 알고리즘 설명과 1:1로 대응됩니다.

### 예시로 트레이스

`priorities=[1,1,9,1,1,1]`, `location=0` → 정답 5

| 턴 | 꺼낸 task(우선순위, 인덱스) | 남은 큐에 더 높은 우선순위? | 결과 |
|---|---|---|---|
| 1 | (1,0) | 있음(9) | 맨 뒤로 재삽입 |
| 2 | (1,1) | 있음(9) | 맨 뒤로 재삽입 |
| 3 | (9,2) | 없음 | 실행 1번째 |
| 4 | (1,3) | 없음 | 실행 2번째 |
| 5 | (1,4) | 없음 | 실행 3번째 |
| 6 | (1,5) | 없음 | 실행 4번째 |
| 7 | (1,0) | 없음 | 실행 5번째 → 인덱스 0 == `loc` → 정답 |

## 자바로 옮기면: `for-else`가 없어서 플래그가 필요

```java
import java.util.*;

class Solution {
    public int solution(int[] priorities, int location) {
        Deque<int[]> docs = new ArrayDeque<>();
        for (int i = 0; i < priorities.length; i++) {
            docs.addLast(new int[]{priorities[i], i});
        }

        int answer = 0;
        while (!docs.isEmpty()) {
            int[] task = docs.pollFirst();
            boolean hasHigher = false;   // for-else의 "break 여부"를 대신하는 변수
            for (int[] d : docs) {
                if (task[0] < d[0]) {
                    hasHigher = true;
                    break;
                }
            }
            if (hasHigher) {
                docs.addLast(task);
            } else {
                answer++;
                if (task[1] == location) return answer;
            }
        }
        return answer;
    }
}
```

자바는 `for` 블록에 바로 `else`를 붙일 수 없기 때문에, `hasHigher`라는 boolean 변수로 "루프가 끝까지 돌았는지"를 직접 추적해야 합니다. 파이썬의 `for...else`는 결국 이 플래그 관리를 언어 차원에서 대신해주는 문법 설탕(syntactic sugar)인 셈입니다.

## 배운 점

- 같은 "큐 시뮬레이션" 문제라도 언어마다 표현 방식이 다를 수 있다 — 파이썬의 `for...else`처럼 자바에 없는 제어 흐름 문법을 만나면, 그 문법이 실제로 어떤 상태(여기선 "break 여부")를 대신 추적해주는지 뜯어보면 이해가 쉽다.
- 압축적인 코드를 만났을 때 바로 "이게 맞나?" 의심하기보다, 작은 예제로 직접 트레이스를 돌려보면 로직이 맞는지 가장 빠르게 검증할 수 있다.

## 🇺🇸 Summary

Solved the "Process" problem (Programmers, formerly called "Printer") — a queue simulation: repeatedly dequeue the front task; if a higher-priority task remains in the queue, requeue it at the back; otherwise execute it and count the turn. Found and dissected a Python `for...else` solution: the `else` clause runs only when the inner `for` loop completes without hitting `break`, which maps exactly onto "no higher-priority task was found" in the algorithm. Translated the same logic to Java, which has no `for-else`, by introducing an explicit boolean flag (`hasHigher`) to track whether the loop exited early. Traced through an example (`[1,1,9,1,1,1]`, target index 0 → answer 5) to verify correctness step by step.
