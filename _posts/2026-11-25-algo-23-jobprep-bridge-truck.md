---
title: "[코테준비 #5] 다리를 지나는 트럭 — 컨베이어 벨트처럼 시뮬레이션하기"
date: 2026-11-25 18:00:00 +0900
categories: [TIL, Algorithm]
tags: [algorithm, coding-test, java, python, deque, simulation, programmers]
series: algorithm
series_order: 23
group: jobprep
group_order: 5
description: 프로그래머스 다리를 지나는 트럭 풀이, 다리를 고정 길이 큐(컨베이어 벨트)로 모델링하는 시뮬레이션 패턴, peek/poll 구분, 시뮬레이션 유형 문제 공략 순서
---

{% include series-nav.html series="algorithm" %}

> 문제 출처는 각 플랫폼(백준·프로그래머스 등)이며, 문제 지문은 옮기지 않고 번호와 이름으로만 표시합니다.
{: .prompt-info }

1주차 네 번째 문제 **다리를 지나는 트럭**(프로그래머스, 스택/큐 카테고리)을 풀면서 정리한 내용입니다.

## 문제 아이디어

길이가 `bridge_length`, 최대 하중이 `weight`인 다리가 있습니다. 트럭들이 순서대로 다리를 건너는데, 다리 위에 동시에 있는 트럭들의 무게 합이 `weight`를 넘을 수 없습니다. 모든 트럭이 다리를 건너는 데 걸리는 총 시간(초)을 구합니다.

## 막혔던 지점 — "다리"를 숫자 하나로만 보면 안 된다

처음 접근할 때 다리를 "현재 총 하중"이라는 숫자 하나로만 생각하면, "트럭이 다리 위에 몇 초째 있는지", "언제 다리를 완전히 벗어나는지"를 추적할 방법이 없어서 막히게 됩니다. 이 문제의 핵심은 **다리 위의 각 칸(slot)까지 표현**해야 한다는 점입니다.

## 모범답안 — 다리를 고정 길이 큐로 모델링

핵심 아이디어는 다리를 `bridge_length`칸짜리 큐로 만들고 전부 `0`(빈 칸)으로 채운 뒤, 매초 **컨베이어 벨트처럼** 한 칸씩 미는 것입니다.

- 맨 앞 칸이 다리를 벗어난다 (큐에서 꺼냄 → 그 무게만큼 총 하중에서 뺀다)
- 맨 뒤에 새 칸이 생긴다 (다음 트럭이 들어갈 수 있으면 넣고, 안 되면 `0`으로 채움)

```java
import java.util.*;

class Solution {
    public int solution(int bridge_length, int weight, int[] truck_weights) {
        Deque<Integer> bridge = new ArrayDeque<>();
        for (int i = 0; i < bridge_length; i++) {
            bridge.addLast(0);               // 다리를 빈 칸(0)으로 초기화
        }

        Deque<Integer> waiting = new ArrayDeque<>();
        for (int w : truck_weights) {
            waiting.addLast(w);              // 대기 줄
        }

        int total = 0;   // 현재 다리 위 총 하중
        int time = 0;

        while (!waiting.isEmpty()) {
            time++;
            total -= bridge.pollFirst();     // 맨 앞 칸이 다리를 벗어남 → 하중 차감

            int nextTruck = waiting.peekFirst();   // 다음 트럭은 "보기만" (아직 안 뺌)
            if (total + nextTruck <= weight) {
                waiting.pollFirst();                // 이제 진짜로 꺼냄
                bridge.addLast(nextTruck);
                total += nextTruck;
            } else {
                bridge.addLast(0);               // 못 들어가면 빈 칸으로 채움
            }
        }

        return time + bridge_length;   // 대기 줄이 비면, 남은 시간은 bridge_length로 바로 계산
    }
}
```

`waiting.peekFirst()`로 "다음 트럭이 들어갈 수 있는지 미리 확인만" 하고, 조건을 통과했을 때만 `pollFirst()`로 실제로 꺼내는 것이 `peek`/`poll`을 구분해 쓰는 전형적인 패턴입니다.

마지막 `return time + bridge_length`가 포인트인데, 대기 줄이 비는 순간 이미 마지막 트럭은 다리에 올라탄 상태이므로, 그 트럭이 완전히 다리를 건너는 데 필요한 나머지 시간은 정확히 `bridge_length`초입니다. 다리 위가 완전히 빌 때까지 계속 돌릴 필요가 없습니다.

### 이 표현을 파이썬으로는 이렇게 사용 가능합니다

```python
from collections import deque

def solution(bridge_length, weight, truck_weights):
    bridge = deque([0] * bridge_length)
    waiting = deque(truck_weights)
    total = 0
    time = 0

    while waiting:
        time += 1
        total -= bridge.popleft()

        if total + waiting[0] <= weight:
            truck = waiting.popleft()
            bridge.append(truck)
            total += truck
        else:
            bridge.append(0)

    return time + bridge_length
```

`waiting[0]`으로 "다음 트럭 무게를 보기만" 하고, 조건을 통과할 때만 `popleft()`로 실제 꺼내는 게 자바의 `peekFirst()`/`pollFirst()` 구분과 정확히 같습니다.

## 예제로 트레이스

`bridge_length=2`, `weight=10`, `truck_weights=[7,4,5,6]` → 정답 8

| 초 | 빠져나간 트럭(맨앞) | 하중 갱신 | 다음 대기트럭 | 들어갈 수 있나? | 결정 | 다리 상태 |
|---|---|---|---|---|---|---|
| 1 | 0 | total=0 | 7 | 0+7=7≤10 ✅ | 7 적재 | [0,7] |
| 2 | 0 | total=7 | 4 | 7+4=11>10 ❌ | 빈칸 | [7,0] |
| 3 | 7 | total=0 | 4 | 0+4=4≤10 ✅ | 4 적재 | [0,4] |
| 4 | 0 | total=4 | 5 | 4+5=9≤10 ✅ | 5 적재 | [4,5] |
| 5 | 4 | total=5 | 6 | 5+6=11>10 ❌ | 빈칸 | [5,0] |
| 6 | 5 | total=0 | 6 | 0+6=6≤10 ✅ | 6 적재(대기줄 끝) | [0,6] |

6초째에 대기 줄이 비므로 `time(6) + bridge_length(2) = 8` → 정답과 일치.

## 시뮬레이션 유형 문제 공략 순서

1. **문제를 분류한다**: 수학 공식이나 탐색이 아니라 "실제 과정을 그대로 흉내내는" 시뮬레이션 문제인지 먼저 알아챈다.
2. **상태(state)를 정의한다**: 이 문제에선 (1) 다리 위에 뭐가 있는지, (2) 현재 총 무게, (3) 대기 줄에 뭐가 남았는지 — 이 세 가지가 전부다.
3. **한 단위 시간에 무슨 일이 일어나는지 규칙으로 적는다**: 맨 앞이 빠진다 → 하중 갱신 → 다음 트럭이 들어갈 수 있는지 확인 → 들어가거나 빈 칸으로 채운다.
4. **코드 짜기 전에 손으로 작은 예제를 트레이스한다**: 트럭 2~3대짜리로 1초씩 적어보면 상태 전이가 맞는지 코드 없이도 검증된다.
5. **종료 조건을 정확히 구분한다**: "대기 줄이 비었을 때"와 "다리 위에도 완전히 아무것도 없을 때"는 다르다. 전자만으로 멈추고 `bridge_length`를 더하는 수학적 지름길을 캐치하는 게 최적화 포인트.

## 배운 점

- 다리처럼 "공간"이 있는 문제는 숫자 하나(합계)가 아니라 **고정 길이 큐로 그 공간 자체를 모델링**해야 풀린다 — 컨베이어 벨트 패턴.
- `peek`(들여다보기)과 `poll`(실제로 꺼내기)을 구분해서 써야 하는 상황이 반복적으로 등장한다 — "조건 확인은 꺼내지 않고, 통과했을 때만 꺼낸다".
- 시뮬레이션을 끝까지 다 돌리지 않고도, 종료 시점에 남은 결과를 수식으로 계산해 건너뛸 수 있는 지점이 있는지 항상 확인한다.

## 🇺🇸 Summary

Solved "Trucks Crossing a Bridge" (Programmers) — modeled the bridge as a fixed-length queue initialized with zeros (empty slots), advancing time by popping the front slot (a truck leaving reduces the running total weight) and pushing a new slot at the back (loading the next waiting truck if it fits under the weight limit, otherwise an empty placeholder) — effectively a conveyor-belt simulation. Highlighted the `peek`-then-`poll` pattern: checking whether the next truck fits without removing it from the queue, then actually dequeuing only once the check passes. Noted an optimization: once the waiting queue empties, the remaining time to finish is simply `bridge_length` more seconds, since the last loaded truck just needs that long to fully cross — no need to keep simulating until the bridge is completely empty. Traced a full example (`bridge_length=2, weight=10, truck_weights=[7,4,5,6]` → 8) step by step to verify. Outlined a general approach for simulation-style problems: classify the problem type, define the state explicitly, write the per-tick transition rule, hand-trace a small example before coding, and check for a shortcut at the termination condition.
