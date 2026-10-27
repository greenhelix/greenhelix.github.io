---
title: "[Pear #1] 배 농장 배송관리 앱 — 킥오프와 요구사항 정리"
date: 2026-10-23 10:00:00 +0900
categories: [Project, Mobile]
tags: [flutter, fastapi, postgresql, ocr, planning]
series: pear
series_order: 1
description: 배 농장의 주문·배송 업무를 돕는 앱을 시작하며, 현장 상황에서 출발한 요구사항을 정리한 기록
---

{% include series-nav.html series="pear" %}

## 🇰🇷 무엇을, 왜

배 농장의 **주문 접수 → 배송 현황 → 통계**까지를 돕는 모바일/웹 앱을 만들기 시작했습니다. 규모가 커질 수 있는 프로젝트라, 의사결정이 쌓이는 시점부터 기록을 남깁니다.

> 현재는 **킥오프 단계**입니다. 코드를 쓰기 전에 요구사항과 아키텍처를 먼저 정리하고 있습니다.
{: .prompt-info }

{% include project-progress.html project="pear" %}

## 현장에서 출발한 요구사항

사용자는 농부이고, 일하는 환경이 일반 앱 사용 환경과 다릅니다. 그래서 기능보다 **상황**을 먼저 정리했습니다.

| 현장 상황 | 설계 방향 |
|---|---|
| 주문이 손글씨, 엑셀, 문자 등 **제각각의 형태**로 들어옴 | 폰 카메라 촬영 → **OCR**로 자동 인식·입력 |
| 주소 오타가 많고, 틀리면 배송 사고로 이어짐 | OCR 결과는 **정확도 우선**, 주소는 검토 단계를 둠 |
| 장갑을 끼거나 통화 중에도 주문을 받음 | 타이핑 대신 **터치/선택 기반** 입력, 음성·대체 입력 방안 검토 |
| 사용자가 앱에 익숙하지 않음 | 단순한 흐름, 폰트 크기·밝기 조절, 태블릿 대응 반응형 UI |
| 피드백을 받을 창구가 필요 | 앱 안에서 텍스트/음성으로 개발자에게 전송 |

## 핵심 기능 범위
- 주문 접수 / 배송 현황 관리 / 주문 통계 대시보드
- 엑셀(지정 양식)·CSV 내보내기, 송장 프린터 연동 확장 고려
- 배 정보 위키 (DB 기반, 추후 애니메이션 UI)
- 배송 시간, 택배차 정보(크기, 1회 적재량) 기록
- 최후순위: 고객이 직접 주문하는 화면(관리자/소비자 화면 분리, 등록된 폰만 관리자 권한)
- 목표: 구글 플레이 스토어 배포(정책 위반 없는 설계)

## 개인정보는 처음부터 설계에 넣기
고객 정보는 **법 범위 내 최소 수집**, 외부 배포 금지, 보관 기간(3년 또는 장기 보관 시 파일화) 정책을 요구사항에 포함했습니다. 나중에 덧붙이기 가장 어려운 부분이라 먼저 정했습니다.

## 개발 원칙
- 확실하지 않은 부분은 **추측하지 않고 확인 후 진행**합니다.
- 기능이 계속 바뀔 것을 전제로 분기·확장이 쉬운 구조로 작성합니다.
- 테스트 코드와 최소한의 로그를 함께 남깁니다.

## 🇺🇸 Summary

Kick-off of a mobile/web app that supports a pear farm's order intake, delivery tracking and statistics. Requirements started from field conditions: orders arrive as handwriting, spreadsheets and text messages (camera + OCR with accuracy-first address review), users often wear gloves or are on the phone (touch-based input), and users are non-technical (simple UI, adjustable fonts, tablet-responsive). Privacy rules (minimal collection, retention policy) were put into the requirements from day one. Status: planning and architecture only.
