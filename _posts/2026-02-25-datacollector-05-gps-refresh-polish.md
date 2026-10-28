---
title: "[Data Collector #5] GPS 트래킹 완성과 다듬기 — Pull-to-Refresh, 반응형, 안전한 이미지 위젯"
date: 2026-02-25 18:30:00 +0900
categories: [Project, Mobile]
tags: [flutter, gps, google-maps, riverpod, refresh-indicator, troubleshooting]
series: datacollector
series_order: 5
description: 지도 트래커를 마무리하고, Riverpod StreamProvider가 갱신되지 않던 문제와 이미지 로딩 문제를 정리
---

{% include series-nav.html series="datacollector" %}

{% include project-progress.html project="datacollector" %}

## 🇰🇷 1. GPS 트래킹 (2월 19일 ~ 25일)
### 웹에서 지도가 안 보이던 문제
웹 배포 후 지도가 렌더링되지 않았습니다. Google Maps JavaScript API 스크립트를 `index.html`에 추가하고 null 타입 처리 하나를 고치자 지도와 위치 추적 버튼이 웹에서도 동작했습니다.

### 흐름
**Start → 경로 수집 → Stop → `TrackRecord` 하나로 저장**

```
features/maps/presentation/
├── map_tracker_screen.dart       # 기록 시작·종료, 저장 로직
├── tracking_history_screen.dart  # 기록 목록
└── tracking_detail_screen.dart   # 경로 지도 + 메모 수정
```

- `trackRecordRepositoryProvider`, `trackRecordStreamProvider`를 기존 방식대로 `providers.dart`에 등록
- 라우터에 `/maps/history`, `/maps/history/detail` 추가
- 상세 화면: 전체 경로가 화면에 들어오도록 **자동 줌**, 시작·종료 시각으로 **소요 시간 계산**, **메모 수정**, 확인 대화상자가 있는 **삭제**

## 2. 문제: 추가·삭제해도 목록이 바뀌지 않는다
### 증상
- 인물·뉴스·지도 화면에서 CRUD 후 목록에 바로 반영되지 않음
- 당겨서 새로고침이 없고, 목록이 **비어 있으면 당기는 제스처 자체가 동작하지 않음**

### 원인
- Riverpod `StreamProvider` 캐시가 즉시 갱신되지 않아 이전 데이터를 보여줌
- `RefreshIndicator`는 자식이 **스크롤 가능**해야 하는데, 비어 있을 때 `Center`/`Column`(스크롤 불가)을 반환하고 있었음

### 해결
1. 목록 화면 3곳(`PersonListScreen`, `NewsListScreen`, `TrackingHistoryScreen`)을 `RefreshIndicator`로 감쌈
2. 저장·수정·삭제 직후와 `onRefresh`에서 `ref.invalidate()`로 스트림을 다시 구독
3. 비어 있을 때도 빈 `ListView`를 반환
4. 모든 `ListView`에 `physics: AlwaysScrollableScrollPhysics()` 지정

```dart
RefreshIndicator(
  onRefresh: () async => ref.invalidate(personListProvider),
  child: ListView(
    physics: const AlwaysScrollableScrollPhysics(),
    children: items,
  ),
)
```

**규칙으로 정립:** 스트림 기반 데이터를 변경한 뒤에는 `ref.invalidate()`, `RefreshIndicator`의 자식은 항상 `AlwaysScrollableScrollPhysics`.

## 3. 뉴스 기능 확장 (2월 26일)
- 관련 기사 링크(제목+URL) 입력과 표시, 웹에서 새 탭으로 열기(`dart:html`은 조건부 import로 분리)
- 타임라인 최신순/오래된순 토글
- 본문에서 빈도 상위 4개 단어로 **태그 자동 추출** 버튼
- 이미지 업로드 UI를 PC(중앙 다이얼로그)와 모바일(바텀시트)로 분리

## 4. 안정화 (3월 3일)
### 반응형 홈 화면
넓은 화면에서 카드가 지나치게 커졌습니다. `LayoutBuilder`로 `constraints.maxWidth`를 읽어 `GridView`의 열 수를 모바일 1~2열, PC 3열로 바꿨습니다.

### `CustomNetworkImage`
외부 이미지 링크(WebP, JPG, CORS 제한 등)가 깨져 엑스박스나 흰 화면이 보였습니다. 로딩 표시(`loadingBuilder`)와 에러 아이콘(`errorBuilder`)을 내장한 위젯을 만들어 인물·뉴스의 상세·목록·미리보기 5곳을 모두 교체했습니다.

### 기본 위젯 테스트
Flutter 기본 카운터 앱 기준이던 `widget_test.dart`가 앱 구조와 맞지 않아 실패했습니다. `ProviderScope`로 감싼 앱의 **홈 화면 렌더링 여부를 확인하는 스모크 테스트**로 바꿨습니다.

## 정리와 다음 단계
| 항목 | 상태 |
|---|---|
| 인물 위키 · 뉴스 아카이브 · GPS 트래커 | 동작 |
| Firebase 연동 · 웹 배포 | 동작 |
| 실제 모바일 기기 검증(권한, 업로드, 백그라운드 GPS) | **남음** |
| Storage 보안 규칙 검토 | **남음** |
| 테스트 확충 | **남음** |

이 프로젝트에서 가장 도움이 된 것은 "교체 지점을 모은다"(Repository), "구조를 UI가 강제한다"(입력 위젯), "규칙을 문서에 남긴다"(invalidate 규칙)였습니다.

## 🇺🇸 Summary
I finished the GPS tracker (start → collect path → stop → save a TrackRecord, with history and detail screens) and fixed lists not updating after CRUD: Riverpod stream caches needed `ref.invalidate()`, and `RefreshIndicator` needed an always-scrollable child even when empty. I also added news links, sorting and auto-tags, made the home grid responsive, replaced broken external images with a safe image widget and fixed the default widget test. Remaining: real-device checks, Storage rules and more tests.
