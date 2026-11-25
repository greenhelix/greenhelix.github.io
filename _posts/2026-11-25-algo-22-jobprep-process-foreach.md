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

## 시간복잡도 다시 보기

`for...else`든 `any(...)`든, 결국 매번 "남은 큐를 전부 훑어보는" 연산이라 O(n)입니다. `deque`로 바꿔서 얻은 건 "꺼내는 동작" 자체를 O(n)→O(1)로 줄인 것뿐이고, 전체 알고리즘은 while 루프(최대 n번) × 스캔(O(n))이라 여전히 **O(n²)**입니다. 이 문제는 입력 크기가 작아 문제없지만, n이 커지면 "지금까지 중 최댓값"을 **최소/최대 힙**으로 관리해서 매번 전체 스캔 없이 O(log n)에 판단하는 방식으로 가야 진짜 O(n log n)이 됩니다.

## 실무 연결 — 다중 기기 테스트 부하 분산

이 "힙으로 개선" 아이디어는 실무의 **대규모 병렬 테스트 스케줄링**과 구조가 거의 같습니다.

인증 테스트 자동화 업무를 하다 보면, 수많은 테스트 케이스를 여러 대의 기기에 샤딩(sharding)해서 병렬로 돌리는 경우가 많습니다. 그런데 테스트 개수 기준으로 "균등하게" 나눠도, 테스트마다 실제로 걸리는 시간이 다르기 때문에 특정 기기에만 무거운 테스트가 몰려서 전체 완료 시간이 그 기기에 발목 잡히는 현상이 생길 수 있습니다.

이건 알고리즘적으로 **다중 머신 스케줄링(multiprocessor scheduling) / makespan 최소화** 문제로, 해법은 오늘 다룬 것과 같은 **그리디 + 최소 힙**입니다.

```python
import heapq

devices = [(0, i) for i in range(device_count)]  # (누적 부하, 기기 ID)
heapq.heapify(devices)

for test in test_queue:
    load, device_id = heapq.heappop(devices)
    assign(test, device_id)
    heapq.heappush(devices, (load + test.duration, device_id))
```

"항상 지금 가장 한가한 기기에 다음 작업을 준다"는 단순한 규칙만으로 최적해에 근접한 분배가 가능합니다(이론상 최적 대비 최대 4/3배 이내).

### 힙의 정렬 기준은 자유롭게 바꿀 수 있다

`heapq`는 튜플을 넣으면 첫 번째 원소부터 사전식으로 비교해서 가장 작은 걸 맨 위로 올립니다. `(load, device_id)`를 넣으면 1순위 기준은 `load`(낮을수록 먼저 = "제일 한가한 기기"가 항상 맨 위), `load`가 같을 때만 `device_id`로 동점을 깹니다. "프로세스" 문제에서는 **큰 값이 먼저** 나오게 우선순위를 줬었는데, 여기서는 **작은 값(한가한 정도)이 먼저** 나오게 — 튜플의 첫 원소를 뭘로 두느냐로 정렬 기준 자체를 바꿀 수 있습니다.

### `load`가 누적하는 건 — 배정된 작업의 추정 소요시간 합

`load`는 실시간 스톱워치가 아니라, **각 기기에 지금까지 배정된 테스트들의 소요시간 합계**(= 그 기기가 배정받은 작업을 순서대로 다 처리하면 걸릴 총 시간)를 누적한 값입니다. 테스트 5개(소요시간 `[5,3,8,2,4]`), 기기 3대로 트레이스하면:

| 단계 | 꺼낸 기기(load, id) | 배정한 테스트 | push하는 값 | 힙 상태 |
|---|---|---|---|---|
| 초기 | - | - | - | (0,0)(0,1)(0,2) |
| 1 | (0,0) | 5 | (5,0) | (0,1)(0,2)(5,0) |
| 2 | (0,1) | 3 | (3,1) | (0,2)(3,1)(5,0) |
| 3 | (0,2) | 8 | (8,2) | (3,1)(5,0)(8,2) |
| 4 | (3,1) | 2 | (5,1) | (5,0)(5,1)(8,2) |
| 5 | (5,0)* | 4 | (9,0) | (5,1)(8,2)(9,0) |

\* `(5,0)`과 `(5,1)`이 동점일 땐 `device_id`가 더 작은 쪽이 먼저 나옵니다.

최종 부하는 기기0=9, 기기1=5, 기기2=8 → 전체 완료시간(makespan)=9. 그냥 순서대로 뿌렸다면 한 기기에 8+2=10처럼 더 몰릴 수도 있었던 것과 비교하면 균형이 나아집니다.

단, 이건 **테스트 소요시간을 사전에 알고 있다는 전제**의 오프라인 시뮬레이션입니다. 실시간으로 기기가 "끝났다"고 보고하는 시스템이면, 사전 추정치 대신 **실제 경과시간**을 넣어 같은 힙 구조를 그대로 쓸 수도 있습니다. 혹은 아예 "미리 N등분해서 던지는(push)" 대신 **기기가 끝나는 대로 공용 큐에서 다음 걸 가져가는(pull) 방식**으로 바꾸면, 사전 계산 없이도 자연스럽게 부하가 균형을 맞춥니다.

## 배운 점

- 같은 "큐 시뮬레이션" 문제라도 언어마다 표현 방식이 다를 수 있다 — 파이썬의 `for...else`처럼 자바에 없는 제어 흐름 문법을 만나면, 그 문법이 실제로 어떤 상태(여기선 "break 여부")를 대신 추적해주는지 뜯어보면 이해가 쉽다.
- 압축적인 코드를 만났을 때 바로 "이게 맞나?" 의심하기보다, 작은 예제로 직접 트레이스를 돌려보면 로직이 맞는지 가장 빠르게 검증할 수 있다.
- Big-O 개선은 "자료구조를 바꾼다고 다 해결되는 게 아니다" — `pop(0)`→`deque.popleft()`는 꺼내는 비용만 줄였을 뿐, 전체 스캔(O(n))이 남아있으면 여전히 O(n²)이다. 클래스 자체를 낮추려면 힙처럼 "스캔 없이 최댓값/최솟값을 바로 꺼내는" 구조가 필요하다.
- 코딩테스트 알고리즘은 실무와 동떨어진 게 아니라, 병렬 작업 스케줄링·부하 분산 같은 실제 시스템 설계 문제로 바로 이어진다.

## 🇺🇸 Summary

Solved the "Process" problem (Programmers, formerly called "Printer") — a queue simulation: repeatedly dequeue the front task; if a higher-priority task remains in the queue, requeue it at the back; otherwise execute it and count the turn. Found and dissected a Python `for...else` solution: the `else` clause runs only when the inner `for` loop completes without hitting `break`, which maps exactly onto "no higher-priority task was found" in the algorithm. Translated the same logic to Java, which has no `for-else`, by introducing an explicit boolean flag (`hasHigher`) to track whether the loop exited early. Traced through an example (`[1,1,9,1,1,1]`, target index 0 → answer 5) to verify correctness step by step. Also discussed why swapping `list.pop(0)` for `deque.popleft()` only removes the O(n) dequeue cost and not the O(n) scan, leaving the algorithm at O(n²) overall, and connected the heap-based improvement to a real-world scenario: parallel test sharding across multiple devices, where equal test *counts* per device don't guarantee equal *durations*. Framed that as the classic multiprocessor scheduling (makespan minimization) problem, solvable with the same greedy + min-heap approach, and walked through a trace showing how the heap's cumulative "load" key drives the assignment, plus a pull-based work-queue model as a practical alternative when per-test durations aren't known in advance.
