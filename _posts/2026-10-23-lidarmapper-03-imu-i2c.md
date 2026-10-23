---
title: "[LiDAR Mapper #3] IMU가 I2C에서 안 잡힐 때 — 범인은 납땜 안 된 두 핀"
date: 2026-09-30 10:00:00 +0900
categories: [Project, Robotics]
tags: [imu, bno055, i2c, debugging, orangepi]
series: lidarmapper
series_order: 3
description: BNO055가 i2cdetect에 나타나지 않던 문제를 단계별로 좁혀 간 디버깅 기록
image:
  path: /assets/img/posts/lidarmapper/03-imu-wiring.png
  alt: Orange Pi 4 Pro와 BNO055 IMU 배선도
---

{% include series-nav.html series="lidarmapper" %}

## 🇰🇷 증상
IMU(BNO055)를 배선했는데 `i2cdetect`에 아무 주소도 나타나지 않았습니다(모든 주소 NAK).

![Orange Pi와 BNO055 배선도](/assets/img/posts/lidarmapper/03-imu-wiring.png){: w="700" }
_Orange Pi 4 Pro ↔ BNO055 배선 (Cirkit Designer로 작성)_

## 원인을 좁힌 순서

| 단계 | 확인 | 결과 |
|---|---|---|
| 1 | 보드 쪽 I2C 설정(핀 기능 모드) | 해당 핀이 I2C 기능으로 정상 설정됨 → 보드 쪽은 정상 |
| 2 | 전원 전압, 배선 연속성 | 모두 정상 |
| 3 | 다른 I2C 버스(I2C1 → I2C0)로 변경 | 동일하게 실패 → 버스 문제 아님 |
| 4 | IMU 보드 육안 점검 | **S0, S1 핀이 납땜되지 않고 떠 있음** |

데이터시트를 확인하니 S0/S1은 BNO055의 **PS0/PS1(프로토콜 선택 핀)** 이고, 둘 다 GND일 때만 I2C 모드로 동작합니다. 플로팅 상태는 데이터시트에서 금지된 상태였습니다.

## 해결
S0, S1을 GND에 연결하자 해당 버스의 `0x29`에서 BNO055가 바로 인식됐습니다.

## 배운 점
- 전원·배선이 모두 정상이어도 **모듈 자체의 모드 설정 핀**이 원인일 수 있습니다.
- 같은 진단을 반복하게 될 것 같아, 버스 목록·스캔·핀 모드·dmesg를 한 번에 출력하는 **진단 스크립트**를 만들었습니다. 이후 문제 분석이 훨씬 빨라졌습니다.

## 🇺🇸 Summary

A BNO055 IMU wasn't detected on I2C (all addresses NAK) although power and wiring checked out and the board's pin mux was correct. Switching buses didn't help; inspection showed the module's S0/S1 (PS0/PS1 protocol-select) pins were unsoldered and floating. Tying both to GND selected I2C mode and the sensor appeared at 0x29. Lesson: check mode-select pins on breakout modules, and keep a diagnostic script handy.
