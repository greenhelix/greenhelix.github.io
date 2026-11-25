---
title: "[코테준비 #2] 같은 숫자는 싫어 — 스택적 사고와 자바 Stream(mapToInt) 제대로 뜯어보기"
date: 2026-11-25 10:00:00 +0900
categories: [TIL, Algorithm]
tags: [algorithm, coding-test, java, stream, python, programmers]
series: algorithm
series_order: 20
group: jobprep
group_order: 2
description: 프로그래머스 같은 숫자는 싫어 풀이와, 자바 stream().mapToInt(Integer::intValue).toArray() 동작을 단계별로 정리
---

{% include series-nav.html series="algorithm" %}

> 문제 출처는 각 플랫폼(백준·프로그래머스 등)이며, 문제 지문은 옮기지 않고 번호와 이름으로만 표시합니다.
{: .prompt-info }

1주차 자료구조 복습 첫 문제로 **같은 숫자는 싫어**(프로그래머스, 스택/큐 카테고리)를 풀면서 정리한 내용입니다.

## 문제 아이디어
배열을 앞에서부터 훑으면서, **결과의 마지막 값과 지금 보는 값이 같으면 버리고 다르면 추가**합니다. "연속된 같은 숫자 중 첫 번째만 남긴다"는 뜻입니다.

```
arr = [1, 1, 3, 3, 0, 1, 1]
→ [1, 3, 0, 1]
```

왜 "스택" 카테고리인가 하면, 지금 본 값을 쌓아두고 바로 그 **직전 값(맨 위)과만 비교**하는 흐름 자체가 스택의 전형적인 쓰임(peek)이기 때문입니다. 실제 `push`/`pop`으로 순서를 뒤집거나 되돌리는 문제는 아니라서, 스택적 사고에 입문하는 워밍업 문제로 보면 됩니다.

## 자바 풀이, 한 줄씩

```java
import java.util.*;

class Solution {
    public int[] solution(int[] arr) {
        List<Integer> stack = new ArrayList<>();
        for (int num : arr) {
            if (stack.isEmpty() || stack.get(stack.size() - 1) != num) {
                stack.add(num);
            }
        }
        return stack.stream().mapToInt(Integer::intValue).toArray();
    }
}
```

- `List<Integer> stack = new ArrayList<>()`: 진짜 `Stack` 클래스 대신 `ArrayList`로 스택을 흉내 냅니다. 자바의 `Stack` 클래스는 레거시 취급이라 보통 `ArrayList`나 `Deque`를 씁니다. 리스트의 **끝**을 스택의 **맨 위**로 약속하고 쓰는 것입니다.
- `stack.isEmpty() || stack.get(stack.size() - 1) != num`: 핵심 조건. `stack.get(stack.size() - 1)`이 "리스트 맨 뒤(= 스택 맨 위) 값을 들여다보는" 동작이라, 스택의 `peek()`을 직접 구현한 것과 같습니다. 즉 "스택이 비었거나, 맨 위 값이 지금 값과 다르면" 추가합니다.
- `stack.add(num)`: 조건을 통과하면 끝에 추가 = push.

### `stream().mapToInt(Integer::intValue).toArray()` 타입 추적

| 단계 | 타입 | 무슨 일이 일어나는가 |
|---|---|---|
| `stack` | `List<Integer>` | 박싱된 Integer 리스트 |
| `.stream()` | `Stream<Integer>` | 스트림으로만 바뀜. **아직 Integer(박싱) 그대로** |
| `.mapToInt(Integer::intValue)` | `IntStream` | 여기서 비로소 Integer → int **언박싱**이 일어나며 기본형 전용 스트림으로 갈아탐 |
| `.toArray()` | `int[]` | `IntStream`의 `toArray()`라서 바로 `int[]` |

`mapToInt`는 "각 원소에 함수를 적용해 `IntStream`을 만들겠다"는 **스트림 연산(틀)**일 뿐이고, 실제 언박싱이라는 동작은 인자로 넘긴 `Integer::intValue`(= `i -> i.intValue()`)가 수행합니다. `mapToInt` 없이 `stack.stream().toArray()`만 쓰면 `Object[]`가 나와서, 문제가 요구하는 `int[]`를 바로 얻을 수 없습니다.

## 파이썬이라면

```python
def solution(arr):
    answer = []
    for num in arr:
        if not answer or answer[-1] != num:
            answer.append(num)
    return answer
```

- `stack.isEmpty() || stack.get(stack.size()-1) != num` → `not answer or answer[-1] != num` (파이썬은 `answer[-1]`로 "맨 뒤 값"을 바로 꺼낼 수 있어서 `get(size-1)` 같은 길이 계산이 필요 없습니다.)
- `stack.stream().mapToInt(Integer::intValue).toArray()` → 애초에 파이썬 리스트에는 박싱/언박싱 개념이 없어서 이 변환 자체가 필요 없고, `return answer`로 끝.

이 지점이 두 언어의 체감 난이도 차이를 가장 잘 보여주는 부분이라, 앞으로는 자바 코드를 기준으로 자세히 뜯고 "이 표현은 파이썬으로는 이렇게 됩니다"를 붙이는 방식으로 정리해 나갈 예정입니다.

## 🇺🇸 Summary
Worked through Programmers' "I don't like the same number" (stack/queue category): keep a value only if it differs from the last one kept, which is really a stack's `peek` pattern, not full push/pop. Traced the Java one-liner `stack.stream().mapToInt(Integer::intValue).toArray()` type by type — `Stream<Integer>` stays boxed until `mapToInt` applies `Integer::intValue` (the actual unboxing call) and switches to the primitive `IntStream`, which is what lets `toArray()` return `int[]` directly. Noted the same logic needs no such conversion in Python, since there's no boxing/unboxing distinction there.
