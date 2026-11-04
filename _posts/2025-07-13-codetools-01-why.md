---
title: "[코드 구조 파악 도구 #1] 왜 만들었나 — 방대한 코드를 혼자 읽는 문제"
date: 2025-07-13 10:00:00 +0900
categories: [Project, Tooling]
tags: [aosp, android, tooling, plantuml, maven]
series: codetools
series_order: 1
description: AOSP와 여러 벤더·고객사 코드를 혼자 읽어야 하는 상황에서, 작은 도구 세 개를 만들게 된 동기
---

{% include series-nav.html series="codetools" %}

## 🇰🇷 문제
업무 특성상 AOSP 기반의 방대한 코드베이스를 다뤄야 했습니다. 한 기능이 어디서 시작해서 어디까지 퍼지는지, 어떤 클래스가 어떤 클래스에 의존하는지를 코드만 눈으로 쫓아가며 파악하는 데 한계를 느꼈습니다.

같이 겪은 문제가 두 가지 더 있었습니다.

1. **외부 라이브러리를 AOSP 빌드 시스템에 넣는 일이 매번 반복 작업이었습니다.** Maven 라이브러리 하나를 쓰려면 그 라이브러리가 의존하는 다른 라이브러리까지 전부 찾아서 `Android.bp`에 손으로 추가해야 했습니다.
2. **SoC 벤더, 고객사, 구글 각각이 올리는 패치가 어디에 어떤 영향을 주는지 전체적으로 추적하고 싶었습니다.** 이건 지금도 완성하지 못한 구상이고, 이 시리즈 마지막 편에서 지금까지 생각한 것만 정리해 둘 생각입니다.

## 접근: 쓸모 있으면 공유되게
"아무도 안 만들어 주니 직접 만든다"는 마음으로 세 가지를 만들었습니다. 전부 개인 시간에 AI 도구의 도움을 받아 만든 작은 유틸리티입니다.

| 도구 | 역할 |
|---|---|
| 의존성 변환기 | Maven 좌표 → 전이 의존성 전체 다운로드 → `Android.bp`/`Android.mk` 자동 생성 |
| 코드 다이어그램 생성기 | Java/Kotlin 코드를 읽어 PlantUML 클래스 다이어그램 생성 |
| Kani Diagram | 업로드한 코드 폴더에서 핵심 파일을 점수화하고 5종 다이어그램으로 시각화 (Flutter Web) |

세 도구 모두 "나 혼자 보려고" 만든 것이라 다듬어지지 않은 부분이 많습니다. 그래도 아무것도 없는 것보다는 낫다는 생각으로 계속 썼습니다.

## 배운 점
이 시리즈를 시작하며 다시 느낀 건, **도구를 만드는 기준이 "완성도"가 아니라 "지금 이 문제를 줄여주는가"**였다는 점입니다. 세 도구 모두 투박하지만, 매번 반복하던 작업이나 눈으로 쫓기 힘들던 구조를 조금씩 줄여줬습니다.

다음 글부터는 각 도구를 하나씩 다룹니다.

## 🇺🇸 Summary
Working with a large AOSP-based codebase alone, I kept running into two frictions: manually wiring transitive Maven dependencies into `Android.bp`, and having no good way to visualize how a change in one class ripples through others. A third, still-unfinished idea was tracking how patches from different sources (SoC vendor, customer, Google) affect the codebase overall. I built three small, unpolished tools to chip away at these — this series covers each one.
