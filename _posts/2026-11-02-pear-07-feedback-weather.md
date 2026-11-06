---
title: "[Pear #7] 개발자 문의 채널과 날씨 연동 — 작지만 매일 쓰는 기능 두 가지"
date: 2026-11-02 10:00:00 +0900
categories: [Project, Mobile]
tags: [flutter, github-api, weather-api, ux]
series: pear
series_order: 7
description: 입력 UI 없이 기기만으로 발신자를 구분하는 문의 기능과, 배송 일정에 바로 쓰는 날씨 연동
---

{% include series-nav.html series="pear" %}

{% include project-progress.html project="pear" %}

## 🇰🇷 개발자 문의
사용자가 불편한 점을 바로 전달할 창구가 필요했습니다. 다만 사용자가 농부라는 걸 감안해서, **입력 항목을 최대한 줄이는 방향**으로 설계했습니다.

- 텍스트 또는 음성으로 등록
- 등록된 내용은 GitHub 이슈로 올라감
- **발신자 식별에 사용자 입력 UI가 전혀 없음**: 기기 고유 Android ID를 화이트리스트와 대조해서 누가 보냈는지 자동으로 구분
- 어느 화면에서 보냈는지 `[위키]`처럼 화면별 태그가 자동으로 붙음

"이름을 입력하세요" 같은 칸 하나도 사용자에게는 진입장벽입니다. 기기 자체로 식별되니 사용자는 그냥 쓰고 보내면 됩니다.

## 날씨 연동
기상청 단기예보 API를 실제로 연동했습니다. 배송 일정을 잡을 때 날씨가 바로 참고가 되도록, 별도 화면을 띄우지 않고 평소 보는 화면에 바로 노출하는 방식을 택했습니다.

## 배운 점
두 기능 다 "크지 않지만 매일 쓰는" 쪽입니다. 문의 기능은 **입력 단계를 없애는 것 자체가 설계**였고, 날씨는 **정보를 어디에 놓을지**가 기능 구현보다 중요했습니다. 화려한 기능보다 매일 쓰는 흐름 안에 자연스럽게 들어가는 쪽이 체감 효과가 컸습니다.

## 🇺🇸 Summary
Added a low-friction feedback channel (text or voice, posted as a GitHub issue, with the sender identified automatically via a whitelisted device ID — no input field at all — and auto-tagged by originating screen) and a real weather API integration surfaced directly where delivery scheduling happens rather than behind a separate screen. Both are small but daily-use features where removing friction mattered more than adding functionality.
