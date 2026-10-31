---
title: "[GAH #3] 공유 생태계 전환 — 이슈 트래커에서 메신저 중심으로"
date: 2026-05-12 10:00:00 +0900
categories: [Work Log, Android]
tags: [integration, messenger-bot, kpi]
series: gah
series_order: 3
description: 테스트 결과 공유 채널이 조직 상황에 맞춰 바뀌면서, 쓰지 않는 연동을 걷어내고 실제 쓰는 도구 중심으로 재편한 기록
---

{% include series-nav.html series="gah" %}

## 🇰🇷 문제
테스트 결과를 어디에 공유할지는 조직 상황에 따라 계속 바뀌었습니다. 처음엔 이슈 트래커 A를 붙였고, 이어서 이슈 트래커 B를 붙였지만, 결국 팀이 실제로 매일 쓰는 메신저+협업 보드 중심으로 완전히 갈아탔습니다.

## 접근
공유 메뉴를 "목적지 기준"으로 재편했습니다 — 테스트 등록 / DB / 메신저 / 이메일 / 설정. 메신저 봇으로 그룹 채팅에 상태 메시지를 직접 보내는 기능을 추가했고, KPI 집계 뷰(디바이스 효율, 재시도 낭비율 등)도 이 시기에 도입해서 "얼마나 빨라졌는지"를 숫자로 보여주기 시작했습니다.

## 결과
처음 붙였던 연동 코드는 완전히 삭제했고, 이후 두 번째 연동도 결국 전체 제거했습니다(이 과정은 별도 편에서 다룹니다).

## 배운 점
한 번 붙인 외부 연동이라도 조직의 실제 사용 패턴과 안 맞으면 과감하게 걷어내는 것이 유지보수 부담을 줄이는 길이었습니다. "일단 붙여 둔다"보다 "실제로 쓰는지 주기적으로 확인한다"가 이 구간의 교훈입니다.

## 🇺🇸 Summary
Result-sharing destinations kept shifting with the team's actual workflow — from one issue tracker, to another, and finally to the team's day-to-day messenger/collaboration board. Reorganized the share menu by destination, added a bot that posts status to group chats, and introduced a KPI view (device efficiency, retry waste). The earlier integrations were fully removed rather than left dormant — a reminder to periodically check whether an integration is actually used.
