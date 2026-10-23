---
title: "[LiDAR Mapper #5] 지도가 이상할 땐? 세션 제어와 자동 저장 설계"
date: 2026-10-02 10:00:00 +0900
categories: [Project, Robotics]
tags: [ros2, flutter, architecture, supervisor]
series: lidarmapper
series_order: 5
description: 필드 테스트 피드백에서 시작해 매핑 시작/일시정지/클리어/중지를 supervisor 노드로 설계한 과정
image:
  path: /assets/img/posts/lidarmapper/02-tracking-flow.png
  alt: 센서, Orange Pi, 스마트폰 세 트랙으로 본 트래킹 동작 흐름
---

{% include series-nav.html series="lidarmapper" %}

## 🇰🇷 필드 테스트에서 나온 요구
- "시작 지점을 깨끗하게 0도로 잡고 싶다" (임의 기준점 문제)
- "지도가 이상하면 지우고 다시 하고 싶다"
- "매핑 안 할 땐 라이다가 배터리를 갉아먹지 않았으면"
- "지도 저장이 네트워크에 의존하면 안 된다" (저장은 보드 로컬 작업인데 폰 연결이 끊기면 못 하는 구조)

모두 **매핑 파이프라인의 생명주기를 명시적으로 제어**해야 한다는 하나의 요구였습니다.

## 설계
- `mapping_supervisor_node`가 rf2o, slam_toolbox, EKF를 서브프로세스로 직접 관리합니다. 앱에서 JSON 명령(`start | stop | pause | resume | clear`)을 보내면 상태를 발행합니다.
- **시작/클리어**: 프로세스를 새로 띄워 그 순간을 원점으로 삼습니다.
- **일시정지/재개**: 프로세스는 두고 slam_toolbox의 측정 일시정지 서비스만 토글하므로 가볍고 빠릅니다.
- **자동 체크포인트**: 보드 안에서 5분마다 지도를 저장해, 폰 연결·네트워크와 무관한 안전망을 만들었습니다.
- 라이다는 전원(모터)을 앱에서 끄고 켤 수 있게 했습니다.

![트래킹 동작 흐름](/assets/img/posts/lidarmapper/02-tracking-flow.png){: w="900" }
_센서 / Orange Pi / 스마트폰 세 트랙으로 본 시작·중단 복구·저장 흐름_

## 앱(Flutter) 쪽
상태에 따라 버튼이 바뀝니다: 정지 → 시작 / 실행 → 일시정지·클리어·중지 / 일시정지 → 재개·클리어·중지. 되돌릴 수 없는 동작은 확인 다이얼로그를 거칩니다.

## 🇺🇸 Summary

Field-test feedback (clean origin, clear a bad map, stop the lidar motor to save battery, make saving independent of the phone link) all boiled down to explicit lifecycle control. A supervisor node manages rf2o, slam_toolbox and EKF as subprocesses (start/clear restart them; pause/resume toggles slam_toolbox measurements), an on-board checkpoint node autosaves every 5 minutes, and the Flutter app exposes state-dependent controls.
