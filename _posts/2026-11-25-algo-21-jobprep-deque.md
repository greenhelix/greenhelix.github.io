---
title: "[코테준비 #3] 기능개발 — Deque로 큐 구현하고, 자바 인터페이스 선언 패턴까지"
date: 2026-11-25 14:00:00 +0900
categories: [TIL, Algorithm]
tags: [algorithm, coding-test, java, python, deque, programmers]
series: algorithm
series_order: 21
group: jobprep
group_order: 3
description: 프로그래머스 기능개발 풀이, list.pop(0)의 O(n) 문제와 Deque(ArrayDeque/collections.deque)로 바꾸는 법, 자바 인터페이스 선언 패턴, Deque 핵심 메서드 정리
---

{% include series-nav.html series="algorithm" %}

> 문제 출처는 각 플랫폼(백준·프로그래머스 등)이며, 문제 지문은 옮기지 않고 번호와 이름으로만 표시합니다.
{: .prompt-info }

1주차 두 번째 문제 **기능개발**(프로그래머스, 스택/큐 카테고리)을 풀면서 정리한 내용입니다.

## 문제 아이디어
각 기능의 남은 작업일 = `ceil((100 - progress) / speed)`로 미리 계산해두고, **앞에서부터 순서대로 배포**합니다. 기능은 순서를 바꿀 수 없기 때문에, 먼저 큐에서 하나를 "기준"으로 뽑고, 그 뒤로 기준보다 작업일이 작거나 같은 기능들을 계속 같이 묶어서 배포합니다.

```
progresses=[93,30,55], speeds=[1,30,5]
작업일수: 7, 3, 9
→ 7을 기준으로 뽑음 → 다음 3은 7 이하라 같이 배포(묶음 크기 2) → 다음 9는 7보다 커서 중단 → [2]
→ 9를 기준으로 뽑음 → 더 없음 → [1]
결과: [2, 1]
```

## 처음 작성한 Python 풀이 (검증)

```python
def solution(progresses, speeds):
    answer = []
    temp = []
    for i in range(len(progresses)):
        if (100-progresses[i]) % speeds[i] == 0:
            temp.append((100-progresses[i]) // speeds[i])
        else:
            temp.append((100-progresses[i]) // speeds[i] + 1)

    while temp:
        cnt = 1
        work = temp.pop(0)
        while temp and work >= temp[0]:
            cnt += 1
            temp.pop(0)
        answer.append(cnt)

    return answer
```

위 예시로 손으로 따라가 보면 `[2, 1]`이 정확히 나와서 **로직은 맞습니다**. 다만 `temp.pop(0)`은 리스트 맨 앞을 지울 때마다 뒤 원소를 전부 한 칸씩 당겨야 해서 호출마다 **O(n)**이 걸립니다. 이번 문제 제약(최대 100개)에서는 상관없지만, 큰 입력을 받는 문제에서는 양쪽 끝 삽입/삭제가 전부 O(1)인 **deque(데크)**로 바꾸는 습관을 들이는 게 좋습니다.

## 자바로 — Deque 선언, 그리고 인터페이스 패턴

```java
import java.util.*;

class Solution {
    public int[] solution(int[] progresses, int[] speeds) {
        Deque<Integer> days = new ArrayDeque<>();
        for (int i = 0; i < progresses.length; i++) {
            int remain = 100 - progresses[i];
            int d = remain / speeds[i];
            if (remain % speeds[i] != 0) d++;
            days.add(d);
        }

        List<Integer> answer = new ArrayList<>();
        while (!days.isEmpty()) {
            int standard = days.poll();
            int count = 1;
            while (!days.isEmpty() && days.peek() <= standard) {
                days.poll();
                count++;
            }
            answer.add(count);
        }

        return answer.stream().mapToInt(Integer::intValue).toArray();
    }
}
```

**`Deque<Integer> days = new ArrayDeque<>();`에서 왼쪽과 오른쪽이 다른 이유**
- 오른쪽 `new ArrayDeque<>()`는 실제로 만들어지는 **구현체 객체**(배열 기반 Deque 구현).
- 왼쪽 `Deque<Integer>`는 그 객체를 **인터페이스 타입으로 참조**하겠다는 선언. 이렇게 하면 `days` 변수로는 `Deque` 인터페이스에 정의된 메서드만 쓰게 되어 구현체에 종속되지 않고, 나중에 `new LinkedList<>()`로 바꿔도 `days`를 쓰는 아래 코드는 손댈 필요가 없습니다. 자바·C++ 계열 정적 타입 언어에서 흔히 쓰는 "인터페이스로 선언, 구현체로 생성" 관습입니다.

**`ArrayDeque` vs `LinkedList`** (둘 다 `Deque` 구현체)
- `ArrayDeque`: 배열 기반, 캐시 지역성이 좋아 대부분 더 빠름, `null` 저장 불가 → 코테 기본값으로 적합
- `LinkedList`: 노드(포인터) 기반, 메모리 오버헤드가 좀 더 있음, `null` 저장 가능

**`add`/`poll`/`peek`가 왜 앞/뒤 중 어느 쪽을 가리키는가**
`Deque`는 `Queue` 인터페이스도 구현하고 있어서, 접미사 없는 메서드는 자바 명세상 이렇게 매핑됩니다.
- `add(e)` → `addLast(e)` (뒤에 삽입)
- `poll()` → `pollFirst()` (앞에서 꺼내며 제거)
- `peek()` → `peekFirst()` (앞을 들여다만 봄, 제거 안 함)

그래서 `add`로 뒤에 넣고 `poll`/`peek`로 앞을 다루면 "뒤로 넣고 앞으로 뺀다" = **FIFO(큐)** 동작이 됩니다. 같은 `ArrayDeque` 객체도 `push`/`pop`(둘 다 **앞 기준**)을 쓰면 **스택**처럼 동작하니, 어떤 이름의 메서드를 썼는지로 "지금 큐로 쓰는지 스택으로 쓰는지"를 구분해야 합니다.

## Python으로 다시 — `collections.deque`

```python
from collections import deque

def solution(progresses, speeds):
    days = deque()
    for p, s in zip(progresses, speeds):
        remain = 100 - p
        d = remain // s
        if remain % s != 0:
            d += 1
        days.append(d)

    answer = []
    while days:
        standard = days.popleft()
        count = 1
        while days and days[0] <= standard:
            days.popleft()
            count += 1
        answer.append(count)

    return answer
```

`days.add(d)` → `days.append(d)`, `days.poll()` → `days.popleft()`(O(1)), `days.peek()`/`days[0]` 비교 → `days[0]`(deque는 인덱싱으로 양 끝을 바로 봐서 별도 `peek()` 메서드가 필요 없음).

## Deque 핵심 메서드 정리 (참고용)

**Python `collections.deque`**

| 동작 | 메서드 | 비고 |
|---|---|---|
| 오른쪽에 추가 | `d.append(x)` | |
| 왼쪽에 추가 | `d.appendleft(x)` | |
| 오른쪽에서 꺼내기 | `d.pop()` | O(1) |
| 왼쪽에서 꺼내기 | `d.popleft()` | list의 `pop(0)`과 달리 **O(1)** — deque를 쓰는 핵심 이유 |
| 양 끝 보기 | `d[0]`, `d[-1]` | 제거 없이 확인 |
| 여러 개 추가 | `d.extend(it)`, `d.extendleft(it)` | `extendleft`는 **순서가 뒤집혀서** 들어감 |
| 회전 | `d.rotate(n)` | 원형 큐 문제에 유용 |
| 크기 고정 | `deque(maxlen=k)` | 꽉 차면 반대쪽이 자동으로 밀려남 — 슬라이딩 윈도우에 유용 |

**Java `Deque`**

| 동작 | 메서드 | 비고 |
|---|---|---|
| 앞/뒤 추가 | `addFirst(e)` / `addLast(e)` | 접미사 없는 `add()` = `addLast()` |
| 앞/뒤 꺼내기 | `pollFirst()` / `pollLast()` | **비어있으면 `null`** |
| 앞/뒤 꺼내기(예외 버전) | `removeFirst()` / `removeLast()` | **비어있으면 `NoSuchElementException`** |
| 앞/뒤 보기 | `peekFirst()` / `peekLast()` | 비어있으면 `null` |
| 스택처럼 쓰기 | `push(e)` / `pop()` | 둘 다 **앞(head)** 기준 |

**함정**: `poll` 계열은 비었을 때 `null`, `remove` 계열은 **예외**를 던집니다. `while (!deque.isEmpty())`로 먼저 체크하지 않고 `removeFirst()`만 돌리면 런타임에 터질 수 있어서, 이번 코드처럼 `isEmpty()` 체크 + `poll()` 조합이 안전합니다.

## 🇺🇸 Summary
Solved Programmers' "Feature Development": compute each feature's remaining work days via ceiling division, then greedily group features that finish at or before the current group's leader. The original Python solution using `list.pop(0)` was logically correct but O(n) per pop; rewrote it with `collections.deque` for O(1) pops. On the Java side, walked through why `Deque<Integer> days = new ArrayDeque<>()` declares the reference as the interface while instantiating the concrete class (so callers only see `Deque`'s methods and the implementation can be swapped later), compared `ArrayDeque` vs `LinkedList`, and laid out how `Deque`'s no-suffix methods (`add`/`poll`/`peek`) map to `addLast`/`pollFirst`/`peekFirst` — plus the `poll` (returns null) vs `remove` (throws) gotcha. Closed with a quick-reference table of core Python `deque` and Java `Deque` methods.
