---
title: "[Data Collector #4] 이미지 업로드와 입력 UX — 위젯 3개로 입력을 단순하게"
date: 2026-02-19 18:30:00 +0900
categories: [Project, Mobile]
tags: [flutter, firebase-storage, image-picker, permission, ux]
series: datacollector
series_order: 4
description: 이미지 업로드(권한·압축), 태그·속성 입력 위젯, 그리고 화면에 통합하기까지 하루 동안의 개선
---

{% include series-nav.html series="datacollector" %}

{% include project-progress.html project="datacollector" %}

## 🇰🇷 배경
인물과 뉴스를 입력할 때 불편한 점이 세 가지였습니다.

1. 사진을 **URL로 직접 붙여 넣어야** 한다
2. 속성을 `키:값` 문자열로 입력해야 해서 오타가 잦다
3. 태그를 콤마로 구분해 입력해야 한다

2월 19일 하루에 이 셋을 위젯으로 정리했습니다.

## 1. 이미지 업로드
### 1차: 선택기 + Firebase Storage
- `image_picker`로 갤러리에서 선택, `firebase_storage`에 업로드
- 모바일은 `File`, 웹은 `readAsBytes()`로 **`kIsWeb`에서 분기**해 업로드
- 업로드 중 로딩 인디케이터 표시

### 2차: 안정화
- **권한 문제:** Android 13 이상에서 갤러리 접근 시 권한 팝업이 뜨지 않았습니다.
  - Android는 `AndroidManifest.xml`에 `READ_MEDIA_IMAGES`(13+)와 `READ_EXTERNAL_STORAGE` 추가
  - iOS는 `Info.plist`에 `NSPhotoLibraryUsageDescription` 추가
  - `permission_handler`로 접근 전에 명시적으로 요청하고, 거부되면 설정 화면으로 안내
- **하이브리드 `CustomImagePicker`:** URL 입력과 파일 업로드를 한 위젯으로 통합
  - URL 모드: 붙여 넣은 주소에 `HEAD` 요청을 보내 파일 크기를 미리 확인
  - 업로드 모드: `flutter_image_compress`로 압축한 뒤 저장(2MB 제한, 자동 리사이즈)
- 선택·로딩·미리보기·삭제를 원형(`CircleAvatar`) UI 하나로 통합

## 2. 태그와 속성 입력 위젯
### `TagInputWidget`
텍스트를 입력하고 `Enter`를 누르면 아래에 `Chip`으로 추가되고, `X`로 지웁니다.

### `AttributeInputWidget`
`Key`와 `Value` 입력창을 나란히 두고 키보드만으로 연속 입력되게 했습니다.

```
[Key 입력] → Enter/Next → [Value 포커스] → [Value 입력] → Enter
   → 속성 추가 + 입력창 초기화 + Key 포커스 복귀
```
추가한 속성은 아래에 목록으로 쌓이고 개별 삭제됩니다.

### 효과
- 사용자가 `:`나 `,` 같은 형식을 신경 쓰지 않아도 되어 **파싱 오류가 원천적으로 사라짐**
- 화면 코드에서 문자열 처리가 빠지고 `Map`/`List` 모델을 직접 다루게 되어 간결해짐

## 3. 화면에 통합
| 화면 | 적용 |
|---|---|
| 인물 목록 | `CustomImagePicker(isCircle: true)` + `AttributeInputWidget` |
| 뉴스 목록 | `CustomImagePicker(isCircle: false)`(사각 썸네일) + `TagInputWidget` |

하드코딩돼 있던 입력 부분을 모두 공통 위젯으로 바꿨습니다.

## 배운 점
- **입력 형식을 사람이 맞추게 하지 말고 UI가 구조를 강제하게 한다.** 문자열 파싱 로직은 버그의 온상이었다.
- 갤러리 권한은 OS 버전마다 이름이 다르다. Android 13부터는 `READ_MEDIA_IMAGES`가 필요하다.
- 공통 위젯은 `core/widgets/`에 두고 기능 화면은 가져다 쓰기만 하는 구조가 유지보수에 유리하다.

## 🇺🇸 Summary
Entering data was clumsy, so I added three reusable widgets in one day: a hybrid image picker (gallery upload to Firebase Storage with compression and permission handling for Android 13+/iOS, or URL with a HEAD size check), a chip-based tag input, and a key/value attribute input with keyboard flow. Using widgets that enforce structure removed string-parsing bugs and simplified the screens.
