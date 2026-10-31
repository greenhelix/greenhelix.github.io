---
title: "[GAH #6] 숫자가 안 맞는 버그들 — 데이터 정합성 집중 구간"
date: 2026-08-24 10:00:00 +0900
categories: [Work Log, Android]
tags: [debugging, data-integrity, xml-parsing]
series: gah
series_order: 6
description: 겉보기엔 비슷한 '파싱 버그'들이 사실은 전혀 다른 원인이었던 사례들, 그리고 추측 대신 원본을 까본 과정
---

{% include series-nav.html series="gah" %}

> 이 글이 다루는 실제 작업 기간: **2026-08-24 (하루 집중)**
{: .prompt-tip }

## 🇰🇷 문제
실사용자들이 DB를 들여다보며 "이 컬럼은 항상 0이다", "이 값은 항상 비어 있다" 같은 이상을 여러 건 제보했습니다. 겉보기엔 다 비슷한 "파싱 버그"였지만 원인은 제각각이었습니다.

## 사례별 원인과 해결
- **모듈 실패 수가 항상 0:** 원본 XML의 모듈 태그에는 애초에 실패 수 속성이 없고, 세션 레벨 요약에만 있었습니다. 모듈에도 있다고 착각하고 읽고 있었습니다. → 실제 케이스를 직접 세어 재계산하도록 수정.
- **디바이스ID·호스트명·실행 인자가 항상 비어 있음:** 전혀 다른 세 가지 원인(로직 자체 누락, 필드를 안 읽음, 속성명 표기 방식 불일치)이 겹쳐 있었습니다.
- **여러 디바이스로 분산 실행된 모듈의 디바이스ID가 사실상 무작위로 들어감:** 실측 로그로 "한 모듈이 여러 디바이스에 동시 분산 실행된다"는 사실을 먼저 검증한 뒤, 모호한 경우엔 오답을 넣는 대신 결측으로 남기는 방향으로 수정했습니다.

## 배운 점
이 구간의 작업 방식은 거의 항상 같았습니다. **추측하지 않고 실제 원본 XML/로그 샘플을 직접 까서 "정말로 그 값이 거기 있는지"부터 확인한 뒤 고친다.** 그리고 애매한 상황에서는 "오답보다 결측이 낫다"는 원칙을 세웠습니다.

## 🇺🇸 Summary
Several "always zero" / "always empty" column reports turned out to have unrelated root causes: reading a summary-level field as if it existed per-module, three separate bugs stacking up on the same columns, and device IDs becoming effectively random for modules sharded across multiple devices. The fix pattern was always the same — inspect the actual raw XML/log before touching code — and the guiding rule became "a missing value beats a wrong one" when a case is ambiguous.
