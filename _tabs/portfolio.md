---
icon: fas fa-briefcase
order: 5
---

대표 프로젝트 모음입니다. A curated list of featured projects.

## 🛰️ Portable LiDAR Mapper — 진행 중

백팩 어깨끈에 라이다+IMU를 달고 걸으며 실내 2D 지도를 만드는 프로젝트
(ROS2 · SLAM · Flutter · Orange Pi · Docker). 현재 필드 테스트로 정확도를 올리는 단계입니다.
*A backpack-mounted LiDAR + IMU rig that builds indoor 2D maps while walking — work in progress.*

{% include project-progress.html project="lidarmapper" %}

### 개발 타임라인
{% include series-timeline.html series="lidarmapper" %}

- 릴리즈(앱 배포): [PortableLiDARMapper-Release](https://github.com/greenhelix/PortableLiDARMapper-Release)

## 🍐 Pear — 배 농장 배송관리 앱 (사이드로드 배포 중)

주문 접수·배송 현황·통계를 돕는 모바일/웹 앱. 사진 AI입력으로 제각각의 주문서를 자동 입력합니다 (Flutter · FastAPI · PostgreSQL · Cloudflare Tunnel). 현재 v0.1.14, 플레이스토어 정식 출시를 준비 중입니다.
*A mobile/web app for a pear farm's order and delivery workflow, with camera-based AI order entry — sideloaded pre-release, heading toward a Play Store launch.*

{% include project-progress.html project="pear" %}

{% include series-timeline.html series="pear" %}

## 🗂️ Data Collector — 인물·뉴스·지도 기록 앱

인물 위키, 뉴스 아카이브, GPS 경로 기록을 한 앱에 쌓는 개인 프로젝트 (Flutter · Riverpod · Drift · Firebase). 웹 배포까지 마쳤고 모바일 실기기 검증이 남아 있습니다.
*A personal Flutter app for people, news and GPS tracks — web deployed, device testing pending.*

{% include project-progress.html project="datacollector" %}

{% include series-timeline.html series="datacollector" %}

## 🧩 코드 구조 파악 도구 만들기

방대한 코드를 혼자 읽어야 하는 문제를 줄이려고 만든 작은 도구 3가지 — Maven→Android.bp 변환기, PlantUML 다이어그램 생성기, 핵심 파일 점수화 웹 도구(Kani Diagram).
*Three small tools I built to make reading a large codebase alone less painful.*

{% include series-nav.html series="codetools" %}

## 📚 알고리즘 스터디

2020년부터 쌓은 풀이를 주제별(힙·정렬·DFS/BFS·다익스트라·백트래킹·DP·그리디·C 자료구조)로 다시 정리했습니다.
*Topic-by-topic write-ups of my algorithm practice since 2020.*

{% include series-nav.html series="algorithm" %}
