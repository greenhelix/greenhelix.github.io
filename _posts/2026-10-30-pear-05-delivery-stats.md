---
title: "[Pear #5] 배송현황과 통계 대시보드 — 집계 테이블 없이 그때그때 계산"
date: 2026-10-30 10:00:00 +0900
categories: [Project, Mobile]
tags: [flutter, fastapi, postgresql, dashboard]
series: pear
series_order: 5
description: 날짜별 일괄 처리가 필요한 배송현황 화면과, 미리 집계해 두지 않고 그때그때 계산하는 통계 대시보드
---

{% include series-nav.html series="pear" %}

{% include project-progress.html project="pear" %}

## 🇰🇷 배송현황
주문이 쌓이면 다음은 "오늘 나갈 물건을 확인하고 상태를 바꾸는" 반복 작업입니다.

- 리스트뷰 / 테이블뷰 두 가지 보기 방식
- **날짜별 일괄 선택**: 하루치를 통째로 골라 상태를 한 번에 바꿀 수 있음
- 상태 변경(접수 → 포장 → 배송 → 완료 등)

농번기에는 하루에 여러 건을 한꺼번에 처리해야 해서, 한 건씩 누르는 것보다 **날짜 단위로 묶어서 처리**하는 흐름을 우선했습니다.

## 통계 대시보드
처음엔 통계용 집계 테이블을 따로 만들까 고민했지만, 지금 규모에서는 **그때그때 `GROUP BY`로 계산**하는 쪽을 택했습니다.

| 보여주는 것 | 기준 |
|---|---|
| 품종별 통계 | 품종 |
| 박스 규격별 통계 | 박스 규격 |
| 배송상태 분포 | 상태 |
| 기간 추이 | 일 / 주 / 월 |
| 지역별 통계 | 배송지 |
| 단골 TOP 10 | 주문 횟수 |

```mermaid
flowchart LR
  O[(주문 DB)] -->|요청 시점에 GROUP BY| S[통계 API]
  S --> D[대시보드 화면]
```

별도 집계 테이블이 없으니 **데이터가 바뀌면 통계도 항상 최신 상태**입니다. 대신 데이터가 많이 쌓이면 쿼리 성능을 다시 봐야 할 수 있는데, 지금 단계에서는 미리 최적화하기보다 필요해지면 그때 인덱스나 집계 테이블을 추가하기로 했습니다.

## 배운 점
"나중에 느려질 수도 있으니 미리 집계 테이블을 만든다"는 선택은 지금 단계에서는 과한 설계였습니다. 데이터 규모가 작을 때는 **단순한 쿼리로 정확성을 보장**하는 쪽이 복잡한 캐시 구조를 유지하는 비용보다 쌌습니다.

## 🇺🇸 Summary
The delivery screen supports list/table views and date-based bulk status changes, since a busy day means handling many orders at once rather than one at a time. The stats dashboard (by variety, box size, delivery status, time trend, region, top-10 repeat customers) deliberately skips a precomputed aggregation table in favor of computing with `GROUP BY` on request — always accurate, simple, and good enough at the current data scale; a dedicated aggregation layer is deferred until it's actually needed.
