---
title: "[LiDAR Mapper #6] 정확도 올리는 중 — 드리프트 진단 도구와 필드 테스트 프로토콜"
date: 2026-10-20 10:00:00 +0900
categories: [Project, Robotics]
tags: [ros2, slam, rosbag, debugging, testing]
series: lidarmapper
series_order: 6
description: 실시간 스캔은 선명한데 누적 지도가 번지는 문제를 진단하기 위한 도구와 반복 가능한 테스트 절차
---

{% include series-nav.html series="lidarmapper" %}

{% include project-progress.html project="lidarmapper" %}

## 🇰🇷 문제
실시간 스캔은 선명한데 **누적 지도는 방사형으로 번지는** 현상이 계속됐습니다. 원인 후보는 크게 둘입니다.
1. IMU가 제대로 보정되지 않은 채 방향을 알려주고 있다.
2. 오도메트리/SLAM 오차가 루프 클로저로 보정되고 있는지 알 수 없다.

지금까지는 둘 다 **확인할 방법이 없었기 때문에** 먼저 진단 도구를 만들었습니다.

## 진단 도구 3종
| 도구 | 목적 |
|---|---|
| IMU 캘리브레이션 상태 표시 | BNO055의 SYS/자이로/가속도/지자기 보정 단계(0~3)를 앱에서 확인 → 방향 드리프트가 SLAM 문제인지 IMU 미보정인지 구분 |
| 진단용 rosbag 녹화 토글 | 같은 걸음을 **다시 걷지 않고** 여러 SLAM 파라미터로 오프라인 재생·비교 (세션당 30~40MB) |
| 장소 생성 시 폰 GPS 기록 | 장소 메타데이터 보강(참고용, 실내 정확도는 기대하지 않음) |

> 센서마다 보정 동작이 다릅니다. 자이로는 가만히, 지자기는 8자(∞)로, 가속도는 여러 자세로 멈춰가며 돌려야 하고, "8자로 돌리면 다 된다"는 오해였음을 실기로 확인했습니다. 이후 한 번 완전 보정되면 값을 파일로 저장해 다음 부팅에 자동 복원하도록 했습니다.
{: .prompt-info }

## 치명적 버그 하나
누적 지도가 아예 쌓이지 않던 원인을 끝까지 추적해 보니, rf2o의 `init_pose_from_topic` 파라미터 **기본값이 빈 문자열이 아니었습니다.** 아무도 발행하지 않는 토픽을 기다리며 콜백 전체를 무시하고 있었고, `/scan`은 정상 발행되는데 오도메트리가 (0,0)에 고정되어 있었습니다. 소스를 확인해 YAML 파라미터 파일로 명시적 빈 값을 지정해 해결했습니다. *"기본값이겠지"라는 가정이 가장 비싼 버그였습니다.*

## 반복 가능한 필드 테스트 프로토콜
매번 "일단 걸어보자"로는 결과를 비교할 수 없어서 절차를 고정했습니다.
1. **출발 전**: IMU 보정 3/3 확인, 라이다 전원 확인
2. **기록**: 장소 지정, 필요 시 진단 녹화 시작
3. **직선 왕복**: 거리를 아는 구간을 왕복해 **스케일 오차** 확인
4. **폐루프 한 바퀴**: 돌아왔을 때 지도가 스스로 맞춰지는지(**루프 클로저**) 확인 — 안 맞으면 오도메트리/EKF 쪽, 맞으면 정상 누적 오차일 가능성
5. **마무리**: 저장 후 직선·루프 지점의 매끄러움 확인, 개발 로그에 결과 기록

## 지금까지 조정한 SLAM 파라미터

| 파라미터 | 기본 | 현재 | 이유 |
|---|---|---|---|
| `resolution` | 0.05 | 0.025 | 확대 시 칸이 굵다는 피드백 (CPU·메모리 증가가 트레이드오프) |
| `minimum_time_interval` | 0.5 | 0.3 | 라이다 Hz와 무관하게 새 스캔 반영 문턱을 낮춤 |
| `minimum_travel_distance` | 0.5 | 0.1 | 도보가 바퀴 로봇보다 빠름 |
| `min_pass_through` | 2 | 2 | 1로 낮췄더니 노이즈 한 번에도 벽이 찍혀 방사형 줄무늬 발생 → 2로 복귀 |

## 다음 단계
- 프로토콜대로 직선 왕복과 폐루프 데이터를 쌓아 수치로 오차를 기록하기 (**결과는 다음 글에서**)
- rosbag 재생으로 파라미터 조합을 오프라인에서 비교하기

## 🇺🇸 Summary

Live scans were crisp but the accumulated map smeared radially, and I had no way to tell IMU calibration problems from odometry/SLAM drift. I built three diagnostics (IMU calibration status, toggleable rosbag recording for offline parameter comparison, GPS tagging), fixed a nasty bug where rf2o ignored every scan because of a non-empty default for `init_pose_from_topic`, and defined a repeatable field-test protocol (straight-line round trip for scale error, closed loop for loop-closure). Quantitative results are still being collected.
