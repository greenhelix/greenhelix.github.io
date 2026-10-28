---
title: "[알고리즘 #18] C로 구현한 큐·트리·분리 집합 — 구현하며 다시 읽어 본 코드"
date: 2024-05-26 10:00:00 +0900
categories: [TIL, Algorithm]
tags: [c, data-structure, queue, tree, disjoint-set]
series: algorithm
series_order: 18
description: 책을 따라 C로 구현한 자료구조와, 다시 읽다가 발견한 개선점
---

{% include series-nav.html series="algorithm" %}

> 문제 출처는 각 플랫폼(백준·프로그래머스·카카오 등)이며, 문제 지문은 옮기지 않고 번호와 링크로 대신합니다. 풀이와 설명은 직접 작성한 것입니다.
{: .prompt-info }

## 🇰🇷 무엇을 구현했나
책 "뇌를 자극하는 알고리즘"을 따라 C로 구현했습니다. 책의 내용을 옮기지 않고, 구현 구조와 다시 읽으며 얻은 메모만 적습니다.

| 자료구조 | 구현 내용 |
|---|---|
| 링크드 큐 | 노드를 연결 리스트로 이어 enqueue/dequeue |
| 순환 큐 | 고정 배열 + front/rear, 용량보다 1칸 더 확보 |
| 이진 트리·수식 트리 | 노드 구조체, 순회, 수식 계산 |
| LCRS 트리 | 왼쪽 자식–오른쪽 형제 표현 |
| 분리 집합 | 부모 포인터로 집합을 표현, Find/Union |

## 순환 큐: 용량 + 1칸을 두는 이유
front == rear를 "비었음"으로 정하면 "가득 참"과 구별이 안 되므로, 배열을 **용량 + 1** 크기로 잡아 한 칸을 비워 둡니다.
```c
(*Queue)->Nodes = (Node*)malloc(sizeof(Node) * (Capacity + 1));
```

## 다시 읽다가 발견한 문제점
> 아래는 코드를 다시 읽으며 발견한 것이고, 실행해서 확인한 것은 아닙니다.
{: .prompt-warning }
- **순환 큐 enqueue**: `Rear == Capacity`일 때 rear만 0으로 돌리고 **데이터를 저장하지 않는** 분기가 있습니다. 저장 문장이 `else` 안에만 있기 때문입니다. 저장을 분기 밖으로 빼야 합니다.
- **분리 집합 Union**: `set2`는 루트를 찾지만 `set1`은 찾지 않고 붙입니다. 루트끼리 합치지 않으면 트리 구조가 깨질 수 있습니다.
- **성능**: Find가 부모를 끝까지 따라가므로 트리가 길어질 수 있습니다. **경로 압축**과 **랭크 기반 합치기**를 넣으면 거의 O(1)에 가까워집니다.

## 배운 점
- 직접 만들어 보면 자료구조의 **경계 조건**(꽉 참/빔, 루트 판별)이 어디서 틀리는지 보입니다.
- 며칠 뒤에 다시 읽어 보면 처음 쓸 때 놓친 버그가 보입니다. 테스트 코드를 같이 두는 이유입니다.

## 🇺🇸 Summary
I implemented queues, binary/LCRS trees and a disjoint set in C while studying. On re-reading I spotted (by inspection, not by running) a circular-queue enqueue that skips storing data on wraparound, a union that doesn't resolve set1's root, and a find without path compression. Lesson: boundary conditions are where hand-written data structures break.
