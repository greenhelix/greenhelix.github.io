# greenhelix.github.io — kani's dev log (Jekyll + Chirpy)

Owner: kani (system + app engineer; Android, Flutter, ROS, AOSP, Linux).
Site: https://greenhelix.github.io · Deploy: push to `main` → GitHub Actions (`.github/workflows/pages-deploy.yml`).

## 글 작성 규칙
- 위치: `_posts/YYYY-MM-DD-slug.md`. 템플릿: `templates/work-log.md`, `templates/project.md`.
- 한국어 본문 + 글 끝에 `## 🇺🇸 Summary` 영어 요약(필수).
- 카테고리: `[Work Log, 분야]` 또는 `[Project, 분야]`, 그 외 `TIL`, `Troubleshooting`.
- 이미지: `assets/img/posts/<slug>/NN-설명.png`, 가로 1200px 이하, 항상 alt 텍스트. 구조는 Mermaid.
- 개인 프로젝트 글은 구체적으로(스택, 구조도, 스크린샷, 문제-해결, 링크). 업무 글은 짧게(문제→접근→결과→배운 점).

## 업무 글 비식별 규칙 (반드시 준수)
- 회사/팀/고객사/내부 서비스·프로젝트 코드명, 사내 URL·IP·계정·토큰·경로, 직원 이름 금지.
- 소스코드 원문 금지 — 일반화한 예제만. 사내 화면 캡처 금지(재현 화면 또는 모자이크).
- 실제 수치는 비율/대략 표현으로. 불확실하면 빼거나 사용자에게 확인.
- 업무 글은 초안을 사용자에게 보여주고 승인 후에만 커밋/푸시.

## 프로젝트 시리즈 기록 (pear, lidarmapper 등)
- 시리즈 메타: `_data/series.yml`(title/source/synced_through), 진행률: `_data/progress.yml`. 글은 front matter에 `series: <키>`, `series_order: N`.
- 목록/진행률: `{% include series-nav.html series="키" %}`, `{% include project-progress.html project="키" %}`, `{% include series-timeline.html series="키" %}`.
- 업데이트 절차: 소스(세션 transcript 또는 repo devlog)에서 `synced_through` 이후만 읽는다 → **굵직한 맥락 단위**(결정/전환/해결한 문제)로 글 1편씩 묶는다 → 진행률·`synced_through` 갱신 → 빌드/링크 검사 후 푸시.
- 글 날짜는 사건이 일어난 날짜로 쓴다(번호순 = 시간순).
- pear: 고객 개인정보(이름·주소·연락처), 서버 주소/터널 URL, 비밀번호, 회사/기관명 금지. 실제 운영 데이터 스크린샷 금지(더미 데이터만).
