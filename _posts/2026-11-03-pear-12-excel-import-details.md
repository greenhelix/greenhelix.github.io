---
title: "[Pear #12] 엑셀 가져오기 보완 — 발송인 자동 분리, 주소 휴리스틱, file_picker 업그레이드"
date: 2026-11-03 11:00:00 +0900
categories: [Project, Mobile]
tags: [flutter, excel, regex, troubleshooting]
series: pear
series_order: 12
description: 엑셀 가져오기 경로를 실제로 써보며 다듬은 세 가지 디테일 — 발송인별 자동 분리, 주소 정규식 휴리스틱, file_picker 메이저 업그레이드 트러블슈팅
---

{% include series-nav.html series="pear" %}

{% include project-progress.html project="pear" %}

## 🇰🇷 발송인이 여러 명 섞인 엑셀 자동 분리
택배사 양식 엑셀 한 장에 "보내는 분"이 여러 명 섞여 있는 경우가 실제로 있었습니다. 이 경우를 그냥 한 묶음(OrderBatch)으로 처리하면 나중에 통계나 환불 처리에서 뒤섞입니다. 그래서 **보내는 분이 바뀌는 지점마다 자동으로 새 주문묶음을 생성**하도록 바꿨습니다. 사용자는 엑셀 구조를 신경 쓸 필요 없이 그냥 업로드만 하면 됩니다.

리뷰 화면은 새로 만들지 않고 **사진 AI입력과 완전히 같은 화면을 재사용**했습니다. 결과적으로 들어오는 데이터 구조가 같으니 검증·수정 UI도 같아야 한다는 게 자연스러웠고, 이건 사용자 본인 아이디어였습니다.

## 주소 분리 휴리스틱
엑셀의 주소 컬럼은 "도로명주소 + 상세주소"가 한 칸에 합쳐 들어오는 경우가 많습니다. **건물번호 뒤 첫 공백**을 기준으로 도로명주소와 상세주소를 정규식으로 분리하는 휴리스틱을 넣었습니다. 다만 이 기준이 애매한 경우(건물번호를 못 찾는 등)에는 억지로 분리하지 않고 **전체를 도로명주소로만 두는** 쪽을 택했습니다. 잘못 자른 상세주소보다, 안 잘리고 통째로 들어간 도로명주소가 배송 사고 측면에서 더 안전합니다.

## 숫자서식 때문에 사라지는 010의 앞자리 0
엑셀에서 전화번호 열을 숫자 서식으로 입력하면 `010...`의 앞자리 `0`이 사라지는 경우가 있습니다(엑셀이 숫자로 인식해 선행 0을 버림). 가져오기 단계에서 길이가 10자리인 숫자 문자열이면 앞에 `0`을 복원하는 보정을 넣었습니다.

## file_picker 8.x → 13.x 업그레이드
파일 선택 패키지를 메이저 버전업하며 API가 꽤 바뀌어 있었습니다.
- 인스턴스 메서드 중 일부가 **정적(static) 메서드**로 전환됨
- 결과 객체에서 `bytes` 필드가 삭제되어, 플랫폼별로 스트림이나 경로를 통해 직접 읽어오는 방식으로 변경 필요

버전을 고정하지 않고 미뤄뒀다가 한 번에 올리면서 변경점을 몰아서 처리해야 했는데, 작은 패키지라도 메이저 업그레이드는 변경 로그를 먼저 읽는 습관이 필요하다는 걸 다시 느꼈습니다.

## 배운 점
"엑셀을 그대로 불러온다"는 기능은 겉보기엔 단순해 보이지만, 실제 사용자가 만드는 엑셀은 양식을 벗어난 경우가 생각보다 많았습니다. 완벽하게 모든 변형을 처리하기보다, **애매한 경우는 보수적으로(분리하지 않는 쪽으로) 처리하고 사람이 리뷰 화면에서 고치게** 하는 게 더 안전한 기본값이었습니다.

## 🇺🇸 Summary
Real-world Excel order sheets sometimes mix multiple senders in one file; the importer now auto-splits into separate order batches at each sender boundary, and reuses the exact same review screen as photo-based AI input since the resulting data shape is identical. Address parsing splits road-name from detail-address using a regex heuristic anchored on the first space after a building number, falling back to leaving the whole string as road-name-only when the split point is ambiguous — a safer default than a wrongly-cut detail address. A fix restores the leading zero that Excel's numeric formatting strips from `010...` phone numbers. Upgrading file_picker from 8.x to 13.x required adapting to several instance-to-static method changes and the removal of the `bytes` field from results.
