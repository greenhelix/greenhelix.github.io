---
title: "[Pear #11] 다크모드 수동 전환과 화면폭 보간 반응형 UI"
date: 2026-11-02 16:00:00 +0900
categories: [Project, Mobile]
tags: [flutter, ui, dark-mode, responsive]
series: pear
series_order: 11
description: 시스템 추종만 하던 다크모드에 수동 토글을 더하고, 폰부터 큰 모니터까지 글자 크기를 선형 보간하는 반응형 시스템을 만든 과정
---

{% include series-nav.html series="pear" %}

{% include project-progress.html project="pear" %}

## 🇰🇷 다크모드: 시스템 추종에서 수동 선택으로
처음엔 OS의 라이트/다크 설정을 그대로 따르기만 했습니다. 그런데 사용해보니 "집에서는 다크, 밭에서 햇빛 아래서는 라이트가 낫다"처럼 시스템 설정과 무관하게 바꾸고 싶은 상황이 있었습니다. 피드백을 받고 설정 화면에 **시스템 / 라이트 / 다크** 세 가지를 직접 고르는 토글을 추가했습니다.

## 반응형 글자 크기: 360dp~2000dp 선형 보간
이 앱은 폰뿐 아니라 사무실 큰 모니터에서도 씁니다. 화면폭에 따라 글자 크기를 **360dp일 때 1.0배, 2000dp일 때 2.0배**로 두고 그 사이는 선형 보간하는 방식을 택했습니다. 고정된 몇 개의 브레이크포인트(폰/태블릿/데스크톱)로 나누는 대신 연속적으로 커지게 해서, 화면이 조금만 넓어져도 어색하게 뚝뚝 끊기지 않게 했습니다.

주문현황 "표 보기"처럼 한 화면에 정보가 많은 경우에는 글자가 커지면 오히려 한 줄에 다 안 들어가는 문제가 생겨서, 품종·개수·무게·박스수 같은 값은 **아이콘 + 알약형 뱃지**로 압축해 표기하는 쪽으로 바꿨습니다.

## 트러블슈팅
- **Material3 자동 배경색**: Material3가 다크모드에서 위젯 배경을 자동으로 밝게/어둡게 조정하는데, 의도한 색과 달라져 대비가 깨지는 경우가 있었습니다. 색을 명시적으로 지정해 자동 조정을 우회했습니다.
- **고정폭 + 반응형 글자 조합**: 일부 화면에서 컨테이너 폭은 고정해두고 글자 크기만 반응형으로 키우다 보니, 특정 화면폭 구간에서 줄바꿈이 깨지는 버그가 반복적으로 나왔습니다. 고정폭 레이아웃에는 글자 확대 배율에 상한을 따로 두는 식으로 대응했습니다.

## 배운 점
반응형 디자인을 "화면 크기별 분기"로 접근하면 화면 종류가 늘어날수록 분기도 늘어납니다. **연속적인 보간 함수** 하나로 처리하니 분기 자체가 줄었지만, 그 값이 고정폭 요소와 만나는 지점에서는 여전히 개별적으로 상한을 둬야 했습니다. "일관된 규칙 하나"와 "예외 처리"는 결국 같이 필요했습니다.

## 🇺🇸 Summary
Dark mode started as pure OS-following, but user feedback (wanting dark at home, light under field sunlight regardless of system setting) led to adding a manual system/light/dark toggle. For responsive sizing, instead of fixed phone/tablet/desktop breakpoints, font scale is linearly interpolated from 1.0x at 360dp width to 2.0x at 2000dp, giving continuous scaling without awkward jumps; the busy "table view" screen compensates by compressing variety/count/weight/box-count into icon + pill-badge form so larger text doesn't overflow. Bugs along the way: Material3's automatic background-color adjustment broke intended dark-mode contrast (fixed with explicit colors), and combining fixed-width containers with responsive font scaling caused line-wrap breakage at certain widths (fixed by capping the scale factor specifically for fixed-width layouts).
