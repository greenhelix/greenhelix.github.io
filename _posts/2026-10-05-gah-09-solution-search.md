---
title: "[GAH #9] 해결방안 검색 고도화 — 설계 결함을 히스토리로 추적하다"
date: 2026-10-05 10:00:00 +0900
categories: [Work Log, Android]
tags: [database, git-archaeology, parser-design]
series: gah
series_order: 9
description: 초기 설계부터 있던 식별 구조 결함을 git 히스토리로 추적해 하위호환을 지키며 고친 과정
---

{% include series-nav.html series="gah" %}

## 🇰🇷 문제
2편에서 소개한 해결방안 기능이 화면에 안 보인다는 제보가 들어왔습니다. 추적해 보니, 해결방안 테이블이 **프로젝트 초기 설계 시점부터** 모듈 정보 없이 스위트명+클래스명+테스트명만으로 케이스를 식별하고 있었다는 구조적 결함이 git 히스토리로 확인됐습니다. 같은 이름의 테스트 클래스가 다른 모듈에서 재사용되면 매칭이 뒤섞일 수 있는 구조였습니다.

## 접근
모듈 컬럼을 DB에 추가한 뒤, 아래 규칙으로 하위호환을 유지하며 고쳤습니다.

- 모듈이 지정된 해결방안은 모듈까지 일치해야 매칭
- 모듈 없는 기존(레거시) 데이터는 모듈 무관하게 매칭

기존 데이터를 깨지 않으면서 새 데이터부터 정밀도를 높이는 방식입니다.

## 멀티 케이스 붙여넣기 검색
이어서 "여러 케이스를 한 번에 붙여넣어 검색"하는 기능을 추가했습니다. 실제 사용자가 붙여넣는 텍스트 포맷이 생각보다 다양했습니다(헤더가 한 줄/세 줄로 나뉨, 모듈당 테스트케이스가 1건/여러 건). 포맷의 "내용"이 아니라 **"바로 다음에 헤더가 이어지는가"**라는 구조적 특징으로 블록 경계를 판별하도록 파서를 설계해, 포맷이 조금씩 달라져도 안정적으로 동작하게 만들었습니다.

## 배운 점
오래된 버그는 때로 최근 변경이 아니라 **설계 시점의 가정**에 있습니다. git 히스토리로 "언제부터 이랬는지"를 먼저 확인하는 것이 원인 추적의 지름길이었습니다. 또한 입력 포맷이 다양할 때는 내용을 패턴 매칭하기보다 구조적 특징으로 경계를 판별하는 쪽이 더 견고했습니다.

## 🇺🇸 Summary
A "solutions not showing up" report led back to a structural flaw present since the feature's original design: cases were identified only by suite+class+test name, with no module column, so identically-named test classes in different modules could collide. Added a module column and kept backward compatibility by requiring a module match only when one was recorded, leaving legacy rows matched as before. Also built a paste-many-cases search whose parser finds block boundaries by a structural signal (whether a header immediately follows) rather than matching specific text formats, so it tolerates the format variety users actually paste.

---

이것으로 GAH 개발기 9편을 마칩니다. 각 편의 내용은 비식별화를 거쳐 정리했습니다.
