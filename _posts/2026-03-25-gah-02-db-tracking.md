---
title: "[GAH #2] 결과를 DB에 쌓기 시작하다"
date: 2026-03-25 10:00:00 +0900
categories: [Work Log, Android]
tags: [database, automation, xml-parsing]
series: gah
series_order: 2
description: 일회성으로 보고 끝나던 테스트 결과를, 실패 패턴을 추적할 수 있는 구조화된 저장소로 바꾼 과정
---

{% include series-nav.html series="gah" %}

> 이 글이 다루는 실제 작업 기간: **2026-03-25 ~ 2026-04-24**
{: .prompt-tip }

## 🇰🇷 문제
테스트를 실행하는 것과, 그 결과를 추적 가능한 데이터로 남기는 것은 전혀 다른 문제였습니다. 처음엔 결과 XML을 그때그때 보고 끝났지만, 반복되는 실패 패턴을 추적하려면 구조화된 저장소가 필요했습니다.

## 접근
세션/디바이스/모듈/케이스/해결방안 테이블로 DB 스키마를 처음 설계하고, 결과 XML을 파싱해 실패 케이스부터 업로드하는 파이프라인을 만들었습니다. 이어서 **해결방안(Solution)** 기능을 추가해, 실패 케이스마다 과거에 어떻게 해결했는지 기록·조회할 수 있게 했습니다 — 이후 계속 확장되는 핵심 기능의 시작점입니다.

동시에 펌웨어 리포트와 이슈 트래커 연동을 붙여, 테스트 결과를 이메일/이슈트래커로 공유하는 흐름까지 만들었습니다. 기기 제어·파일 탐색기·빠른 명령 실행 기능도 이 시기에 네이티브로 통합됐습니다. 원래 별도 도구였던 것을 앱 안으로 가져온 것입니다.

## 결과와 배운 점
사소하지만 반복된 교훈도 있었습니다. 버전 번호를 안 올리고 재배포하면 자동 업데이트 루프가 발생한다는 걸 직접 겪고 배웠고, 이후 "배포 전 반드시 버전 확인"이 체크리스트 규칙으로 고정됐습니다.

## 🇺🇸 Summary
Moved from eyeballing test result XML each run to a structured database (session/device/module/case/solution tables), with a parsing pipeline that uploads failing cases first. Added a "Solution" feature so each failing case could record how it was fixed before — the seed of a feature that kept growing later. Also folded a previously separate device-control tool into the app, and learned the hard way that skipping a version bump before redeploying causes an auto-update loop.
