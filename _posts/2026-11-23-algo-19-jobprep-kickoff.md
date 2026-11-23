---
title: "[코테준비 #1] 기업 코딩테스트 대비 스터디 시작 — 4주 플랜과 추적 시트"
date: 2026-11-23 10:00:00 +0900
categories: [TIL, Algorithm]
tags: [algorithm, coding-test, study-plan, baekjoon, programmers]
series: algorithm
series_order: 19
group: jobprep
group_order: 1
description: 기업 코딩테스트 대비 4주 학습 플랜(자료구조 → 정렬/탐색/그래프 → DP/최단경로 → 모의고사)을 세우고 추적 시트를 만들며 시작한 기록
---

{% include series-nav.html series="algorithm" %}

> 문제 출처는 각 플랫폼(백준·프로그래머스 등)이며, 문제 지문은 옮기지 않고 번호와 이름으로만 표시합니다.
{: .prompt-info }

## 왜 다시 시작했나
최근 지원한 포지션 중 코딩테스트 단계가 있는 곳이 있어서, 서류 결과가 나오기 전에 미리 알고리즘 감을 되살려 두기로 했습니다. Java/C/C++/Python은 실무로 계속 써왔지만 CP(Competitive Programming) 경험은 적은 편이라, "문법은 아는데 시간 안에 정확히 구현하는 감각"을 되살리는 게 목표입니다.

## 4주 플랜
1주 단위로 범위를 좁혀서 매일 2~3문제씩 푸는 걸 목표로 잡았습니다.

- **1주차 — 자료구조 복습**: 배열, 해시맵, 스택/큐, 힙. "이 자료구조를 언제 꺼내 쓰는지" 감 잡기.
- **2주차 — 핵심 알고리즘**: 정렬, 이분탐색, BFS/DFS, 그리디. 로봇/경로 탐색 쪽 포지션이라 BFS/DFS 비중을 높게 잡았습니다.
- **3주차 — DP·그래프 심화**: 동적 계획법(배낭, LIS류), 다익스트라, Union-Find. 막히면 해설 보고 바로 복기.
- **4주차 — 실전 모의고사**: 90~120분 타이머 걸고 2~4문제 세트를 통째로 풀어보는 연습.

## 추적 방법
문제를 많이 쌓아두면 뭘 풀었는지 놓치기 쉬워서, 주차/카테고리/문제명/플랫폼/상태(미시작·진행중·완료)/난이도 체감/링크/메모 컬럼으로 된 체크리스트를 만들어서 하루에 푼 만큼 상태를 바꿔가며 진행하기로 했습니다. 백준 자료구조 몸풀기(스택·큐·이분탐색 맛보기)부터 시작해서 프로그래머스 고득점 Kit, 이후 그래프 탐색 비중을 높인 백준 문제들로 이어집니다.

## 다음 글부터
문제를 풀면서 막혔던 부분, 다시 보니 놓쳤던 부분, 실무에서 비슷한 패턴을 어디에 썼는지를 주차 단위로 정리해 올릴 예정입니다.

## 🇺🇸 Summary
Kicked off a 4-week algorithm refresher ahead of a coding test stage in a job process: week 1 data structures, week 2 sorting/binary search/BFS-DFS/greedy (extra weight on graph traversal), week 3 DP/shortest-path/union-find, week 4 timed mock tests. Built a simple checklist (week/category/problem/platform/status/difficulty/link/notes) to track progress day by day. Future posts will log what tripped me up each week and where similar patterns show up in real work.
