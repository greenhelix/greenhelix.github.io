---
title: "[LiDAR Mapper #2] 보드 선택과 폼팩터 전환 — Pi 5에서 Orange Pi 4 Pro로"
date: 2026-09-08 10:00:00 +0900
categories: [Project, Robotics]
tags: [orangepi, sbc, hardware, 3d-printing, lidar]
series: lidarmapper
series_order: 2
description: 가격이 급등한 Raspberry Pi 5 대신 Orange Pi 4 Pro를 고른 과정과, 텀블러형에서 어깨끈 마운트로 바뀐 폼팩터 이야기
---

{% include series-nav.html series="lidarmapper" %}

## 🇰🇷 보드 결정: 스펙표보다 중요했던 것

처음엔 Raspberry Pi 5를 쓰려 했지만 메모리 수급 문제로 실구매가가 크게 올라 있었습니다. 그래서 SLAM(slam_toolbox)을 실시간으로 돌릴 수 있는 대안을 후보별로 비교했습니다.

| 후보 | 판단 |
|---|---|
| Orange Pi Zero 3 | 쿼드코어 A53, SLAM 실시간 연산엔 CPU가 약함 → 기각 |
| Orange Pi 5 / 5 Max | 스펙은 좋지만 상위 모델과 혼동되는 리스팅, 이력이 불분명한 매물 → 기각 |
| **Orange Pi 4 Pro** | 2×Cortex-A76 + 6×Cortex-A55, 4GB LPDDR5, GPIO 40핀(I2C/SPI/UART), USB-C 5V 입력(보조배터리 구동 적합) → **채택** |

판단 기준은 세 가지였습니다.
1. SLAM에 필요한 **빅코어 유무**
2. IMU 확장을 위한 **GPIO/I2C**
3. 보조배터리로 돌릴 수 있는 **전원 입력**

> 같은 이름의 상품도 리스팅마다 스펙 배너가 다르거나, 같은 세대 안에 Plus/Max 같은 상위 등급이 섞여 있습니다. 모델명 뒤 접미사와 판매 이력을 꼭 확인해야 합니다.
{: .prompt-tip }

Docker 기반 설계 덕분에 보드가 바뀌어도 소프트웨어 리스크는 작았습니다. 같은 이미지를 arm64로 빌드해서 옮기면 되는 구조였기 때문입니다.

## 폼팩터 전환: 텀블러 → 어깨끈 마운트
처음 구상은 텀블러 모양 케이스였지만, 실제로 들고 걷기엔 불편해서 **백팩 어깨끈 마운트**로 바꿨습니다. 라이다와 IMU는 어깨 위 브래킷에 고정하고, Orange Pi와 배터리는 가방 안에 넣습니다. 브래킷은 직접 설계해 3D 프린트했습니다.

![어깨끈 마운트 컨셉 렌더](/assets/img/posts/lidarmapper/04-mount-concept-render.jpg){: w="700" }
_라이다를 어깨 높이에 올려 시야를 확보하는 구조_

## 🇺🇸 Summary

With Raspberry Pi 5 prices inflated, I compared several SBCs and chose the Orange Pi 4 Pro (2×A76 + 6×A55, GPIO for I2C, USB-C 5V input suited to power banks). Because everything runs in Docker, switching boards was low-risk. The form factor also changed from a tumbler-style case to a backpack-strap mount with a 3D-printed bracket.
