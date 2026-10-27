---
title: "[LiDAR Mapper #7] 정확도 전에 버그부터 — 지도가 쌓이기 시작했다"
date: 2026-10-24 10:00:00 +0900
categories: [Project, Robotics]
tags: [ros2, debugging, flutter, rosbridge, testing]
series: lidarmapper
series_order: 7
description: 정확도 튜닝에 들어가기 전, 필드 테스트를 가로막던 버그들을 정리하고 고친 기록 (수치 측정은 다음 글)
---

{% include series-nav.html series="lidarmapper" %}

{% include project-progress.html project="lidarmapper" %}

## 🇰🇷 이번 글의 범위
6편에서 만든 진단 도구와 테스트 프로토콜로 정확도를 재려 했지만, 실제로는 **측정 이전 단계의 버그**가 계속 나왔습니다. 이 기간(10/21~)은 벤치마크가 아니라 버그 수정 기간이었고, **직선 왕복 오차나 루프 클로저 같은 정량 결과는 아직 없습니다.** 숫자는 측정한 뒤 다음 글에서 다루겠습니다.

## 1. 누적 지도가 쌓이지 않던 진짜 원인
라이다 오도메트리(rf2o) 노드가 설정 문제로 **스캔을 하나도 처리하지 않고** 멈춰 있었습니다. 이전 필드 테스트에서 지도가 번지거나 쌓이지 않았던 근본 원인이 이것이었고, 수정 후에는 지도가 **실제로 채워지는 것**을 확인했습니다. 다만 "채워진다"와 "정확하다"는 다른 문제라서, 정확도는 아직 검증 전입니다.

> 증상은 "SLAM 파라미터가 나쁘다"처럼 보였습니다. 파라미터를 만지기 전에 입력 노드가 살아 있는지부터 확인했어야 했습니다.
{: .prompt-tip }

## 2. 앱 쪽 버그
| 증상 | 상태 |
|---|---|
| 재연결 시 화면이 조용히 멈춤 (에러 표시 없음) | 수정 |
| 지도 이미지가 잘려 보임 (크롭 오류) | 수정 |
| IMU 캘리브레이션 상태가 잘못 표시됨 | 수정 |
| 앱 업데이트가 캐시 때문에 늦게 반영됨 | 수정 |
| 기기 연결 안정성 (자동 탐색·재연결) | 개선 중, 실기 검증 전 |

공통점은 **"실패했는데 사용자에게 아무 신호가 없는"** 유형이라는 점입니다. 현장에서는 화면이 멈추면 원인을 알 수 없어 테스트 자체를 망치기 때문에, 조용한 실패를 먼저 없애는 쪽으로 우선순위를 잡았습니다.

## 3. 현재 상태와 다음 단계
- 지도 누적: 동작 확인 ✅
- 연결 안정성: 코드 개선 중, **실기 미검증**
- 다음: 필드 테스트 프로토콜(6편)대로 직선 왕복 + 폐루프를 측정하고, 결과를 8편에서 공유

진행률 막대의 "지도 정확도"를 낮춘 것은 후퇴가 아니라, 근본 버그를 찾은 뒤 **기존 추정이 과했다**고 판단해 보수적으로 다시 잡은 것입니다.

## 🇺🇸 Summary
Before measuring accuracy, a string of bugs blocked the field tests. The big one: the laser-odometry node was silently processing no scans due to a configuration issue, which explains why accumulated maps never built up earlier. After the fix the map fills in, though distance error and loop-closure numbers are not measured yet. Several app bugs were fixed too (silent freeze on reconnect, map cropping, IMU calibration display, delayed update cache); connection stability work is still unverified on hardware. The common thread was "silent failures" with no user-visible signal. Quantitative results will come in the next post.
