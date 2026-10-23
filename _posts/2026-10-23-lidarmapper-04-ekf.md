---
title: "[LiDAR Mapper #4] EKF 센서 퓨전의 함정 — 위치와 방향을 분리하면 지도가 깨진다"
date: 2026-10-01 10:00:00 +0900
categories: [Project, Robotics]
tags: [ros2, ekf, robot_localization, slam, debugging]
series: lidarmapper
series_order: 4
description: 라이다 오도메트리와 IMU를 융합하다 지도가 방사형으로 깨진 원인과 수정 과정
---

{% include series-nav.html series="lidarmapper" %}

## 🇰🇷 배경
바퀴가 없는 휴대형이라 라이다 스캔 매칭 오도메트리(rf2o)만으로는 **회전 추정이 약해** 경로 방향이 실제와 어긋났습니다. 그래서 `robot_localization`의 EKF로 IMU를 섞기로 했습니다.

## 첫 설계 (실패)
- 위치(x, y) → rf2o를 신뢰
- 방향(yaw) → IMU를 신뢰

그럴듯해 보였지만 첫 필드 테스트에서 **누적 지도가 사방으로 방사형 줄무늬**가 되며 완전히 깨졌습니다.

## 원인 추적
1. **1차 가설**: IMU는 지자기 기준의 절대 방향, rf2o는 시작 시점 기준의 상대 방향이라 기준점이 다르다(실측 차이 128°). `imu0_differential: true`로 바꿨지만 **근본 해결이 아니었습니다.**
2. **진짜 원인**: rf2o가 계산한 x, y 이동량은 *rf2o 자신의 방향 추정을 전제로* 만들어진 값입니다. 그런데 EKF의 방향은 IMU만 독립적으로 보니 "A 방향으로 움직였다면서 방향은 B"라는 **모순된 pose**가 SLAM에 들어갔습니다. (실측: rf2o −120°, EKF 방향 거의 0°)

## 수정
- rf2o의 **위치와 방향을 통째로** 신뢰하도록 변경
- IMU는 **회전 각속도(vyaw)만 보조**로 사용

수정 후 EKF 방향과 rf2o 방향이 **1° 이내**로 일치하는 것을 실측으로 확인했습니다.

> 센서 융합은 "누가 무엇을 잘 아는가"로 나누기 전에, **각 값이 서로 어떤 가정 위에 계산됐는지**부터 봐야 합니다.
{: .prompt-tip }

## 함께 정리한 문제들
- 폰 핫스팟 보안 방식(WPA3 ↔ WPA2) 불일치로 보드가 고립 → 핫스팟 보안 설정을 맞춰 해결
- 보드 시스템 시간대가 UTC라 지도 저장 시각이 틀어짐 → 시간대 설정 수정

## 🇺🇸 Summary

Fusing rf2o (lidar odometry) for position with an IMU for heading looked reasonable but produced radial streaks in the map. The root cause: rf2o's x/y displacement is computed under rf2o's own heading estimate, so pairing it with an independent IMU heading yielded an inconsistent pose (rf2o −120° vs EKF ≈ 0°). Trusting rf2o's full pose and using the IMU only for yaw rate made both headings agree within 1°.
