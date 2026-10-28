---
title: "[Data Collector #3] Flutter Web 빌드 오류(조건부 import)와 Firebase Hosting 배포"
date: 2026-02-10 19:30:00 +0900
categories: [Project, Mobile]
tags: [flutter, flutter-web, firebase-hosting, drift, troubleshooting]
series: datacollector
series_order: 3
description: sqlite3가 웹 빌드를 깨뜨린 문제를 조건부 import로 풀고 Hosting까지 배포한 기록, 그리고 나중에 겪은 배포 설정 오류
---

{% include series-nav.html series="datacollector" %}

{% include project-progress.html project="datacollector" %}

## 🇰🇷 문제: 웹 빌드가 터진다
```bash
flutter build web --release
```
`sqlite3` 패키지 내부에서 `Only JS interop members may be 'external'` 같은 오류가 대량으로 나왔습니다.

### 원인
Flutter Web 컴파일러(dart2js)는 FFI(C 라이브러리 연동)를 지원하지 않습니다. 그런데 네이티브용 DB 연결 파일(`connection_native.dart`)이 웹 빌드 트리에 포함돼 있었습니다.

### 해결: 조건부 import
DB 연결 코드를 3개로 나눴습니다.

| 파일 | 역할 |
|---|---|
| `connection_stub.dart` | 껍데기(공통 인터페이스) |
| `connection_native.dart` | `dart:io` 환경용 (sqlite3 사용) |
| `connection_web.dart` | 웹용 (빈 `LazyDatabase`를 반환해 오류 방지) |

진입점에서 조건을 걸어 환경에 맞는 파일만 가져옵니다.

```dart
import 'connection_stub.dart'
    if (dart.library.io) 'connection_native.dart'
    if (dart.library.html) 'connection_web.dart';
```

여기서 놓치기 쉬운 점이 하나 있습니다. **프로젝트 어디에서든 `connection_native.dart`나 `package:drift/native.dart`를 직접 import하면 조건부 import가 소용없습니다.** 그래서 직접 참조를 전부 찾아 지우고 통일된 진입점만 쓰게 했습니다. 그 뒤 웹 빌드가 정상 통과했습니다.

## Firebase Hosting 배포
1. `flutter build web --release` → `build/web`에 정적 파일 생성
2. `firebase.json`에 `hosting` 설정을 기존 FlutterFire 설정과 병합
   - `public: "build/web"`
   - `rewrites`: 모든 경로(`**`)를 `/index.html`로 보내 GoRouter(SPA)의 새로고침 404 방지
3. `firebase deploy --only hosting`

배포된 주소에서 앱이 뜨고, Firestore 연동과 라우팅도 동작했습니다.

## 나중에 겪은 배포 오류 (2월 25일)
`firebase deploy --only hosting`에서 `Failed to get Firebase project ...` 오류가 났습니다.

- **원인:** `.firebaserc`의 프로젝트 ID에 **Firestore 데이터베이스 이름**을 넣어 뒀습니다. 프로젝트 ID와 DB ID는 다른 값입니다.
- **해결:** `.firebaserc`의 `default`를 콘솔의 실제 프로젝트 ID로 고치고, `firebase logout` → `login`으로 토큰을 갱신한 뒤 `--project`를 명시해 배포했습니다.
- **예방:** Flutter Web은 `public`이 항상 `build/web`이어야 합니다.

덧붙여 Firestore에서 이름이 있는 데이터베이스(Named DB)를 쓸 때는 프로젝트와 DB 이름을 헷갈리기 쉬우니 설정 파일에 주석으로 구분해 두는 것이 좋습니다.

## 🇺🇸 Summary
`flutter build web` failed because the native sqlite3 connection file was pulled into the web build (dart2js has no FFI). Splitting the DB connection into stub/native/web files with conditional imports, and removing every direct import of the native file, fixed it. I then deployed to Firebase Hosting with an SPA rewrite to `/index.html`. A later deploy error came from using the Firestore database name as the Firebase project ID in `.firebaserc`.
