---
title: "[GAH #5] DB 이전과 구조 정리 — 클라우드에서 로컬 PostgreSQL로"
date: 2026-06-28 10:00:00 +0900
categories: [Work Log, Android]
tags: [postgresql, postgrest, architecture, repository-pattern]
series: gah
series_order: 5
description: 클라우드 DB 용량 제한에 걸려 로컬 PostgreSQL로 전환하며 아키텍처도 함께 정리한 기록
---

{% include series-nav.html series="gah" %}

## 🇰🇷 문제
클라우드 DB 서비스의 용량 제한에 걸렸습니다. 전체 DB를 로컬 PostgreSQL + PostgREST로 이전해야 했습니다.

## 접근
클라우드 DB 전용 패키지를 제거하고 PostgREST 클라이언트로 10여 개 리포지토리를 교체했습니다. 다행히 PostgREST API 형태 자체는 기존 클라우드 DB와 거의 동일해서 전환 비용이 크지 않았습니다.

이 시기에 아키텍처 정리도 같이 진행했습니다. 화면(presentation) 레이어가 리포지토리를 직접 호출하던 구조를, 반드시 provider를 거치도록 강제했습니다. 4편에서 언급한 연동 중 하나도 이 구간에서 전체 제거했습니다.

## 결과와 배운 점
"한 번 붙인 기능/연동이라도 조직의 실제 사용 패턴과 안 맞으면 걷어낸다"는 원칙이 이 구간에서 가장 확실하게 드러났습니다. API 형태가 호환되는 서비스로 옮기면 마이그레이션 비용을 크게 줄일 수 있다는 것도 확인했습니다.

## 🇺🇸 Summary
Hit a storage cap on the cloud DB service and migrated the whole database to local PostgreSQL + PostgREST, swapping about a dozen repositories to the PostgREST client — made easier because its API shape closely matched the cloud service's. Used the opportunity to enforce that the presentation layer only talks to repositories through providers, and fully removed one of the earlier integrations that had fallen out of use.
