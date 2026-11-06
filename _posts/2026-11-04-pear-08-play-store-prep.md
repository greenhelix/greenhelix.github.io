---
title: "[Pear #8] 플레이스토어 출시 준비 — 패키지명을 영구히 고정하다"
date: 2026-11-04 10:00:00 +0900
categories: [Project, Mobile]
tags: [flutter, android, play-store, release]
series: pear
series_order: 8
description: 되돌릴 수 없는 패키지명부터 먼저 바꾸고, 아이콘·개인정보처리방침·서명키를 하나씩 준비한 기록
---

{% include series-nav.html series="pear" %}

{% include project-progress.html project="pear" %}

## 🇰🇷 패키지명부터 — 되돌릴 수 없는 것을 먼저
개발 중에는 Flutter 기본값인 `com.example.pear`를 그대로 쓰고 있었습니다. 하지만 이건 **플레이스토어에 한 번 올라가면 사실상 바꿀 수 없는 값**입니다. 출시 직전이 아니라 지금, 되돌릴 수 없는 결정부터 먼저 내렸습니다.

`com.example.pear` → `com.kani.pear`로 영구 변경했습니다.

## 출시 준비 체크리스트
- **패키지명 변경** (위)
- **앱 아이콘** 제작
- **개인정보처리방침 초안** — 1편에서 세운 "최소 수집, 외부 배포 금지, 보관 기간" 원칙을 문서로 옮김
- **서명키(keystore) 가이드** 정리 — 분실하면 같은 패키지로 다시 배포할 수 없는 값이라 백업 절차를 문서로 남김
- **스크린샷 1차 확보**

## 현재 배포 방식
지금은 정식 출시 전 단계라, 사이드로드(APK 직접 설치)로 배포하고 있습니다. 버전은 v0.1.14입니다.

## 배운 점
출시 준비는 기능을 더 만드는 일이 아니라 **되돌릴 수 없는 값을 미리 확정하는 일**이었습니다. 패키지명과 서명키가 대표적입니다. 둘 다 나중에 고치려면 사실상 새 앱으로 다시 시작해야 하는 값이라, 기능이 덜 완성된 지금 단계에서 먼저 처리해 뒀습니다.

## 🇺🇸 Summary
Before adding more features, I locked down the values that can't be changed after a Play Store release: the application ID (`com.example.pear` → a permanent `com.kani.pear`) and the signing keystore (documented backup steps, since losing it means never updating the same listing again). Also drafted the privacy policy from the principles set in post #1, made an app icon, and took first-pass screenshots. Currently distributed via sideloaded APK at v0.1.14, pre-release.
