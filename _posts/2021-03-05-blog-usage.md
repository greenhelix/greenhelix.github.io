---
layout: posts
title: "🌋🧗🏻‍♂️github.io 편하게 사용하기 위한 여정🌋🧗🏻‍♂️"
date: 2021-03-05 18:19:07 +0900
categories: blog control
tags: blog usages
---

---

## 🎃 복잡한건 싫다. 그냥 이것만 보자 🎃

---

1. 일단 이 테마 관련 사항은 설명을 참고 해야한다.

   > [여기로 가보자](https://mmistakes.github.io/minimal-mistakes/docs/configuration/)<br>별로 도움은 안된다.

2. jekyll 사용법 / bundle 사용법

   > jekyll -v : 이렇게 git bash나 해당 폴더 cmd에서 치면 버전 나옴.

3. 카테고리 만들기
   > 1. 카테고리를 만드려면 먼저,\_data 폴더로 이동해야한다.
   > 2. \_data폴더에서 navigation.yml 파일로 들어간다.
   > 3. 이런식으로 적으면 형성된다.
   >
   > ```yml
   > # main links
   > main:
   >    - title: "About"
   >    url: /about/
   >    - title: "App Develope"
   >    url: /app-develope/
   >    - title: "Android"
   >    url: /android/
   >    - title: "Stock"
   >    url: /stock/
   > ```
4. 파비콘 만들기

   > 파비콘을 만드려면, 정말 알찬 이 [블로그](https://danggai.github.io/github.io/Github.io-%ED%8C%8C%EB%B9%84%EC%BD%98-%EC%88%98%EC%A0%95%ED%95%98%EA%B8%B0/)를 가서 본다.
   >
   > 1. [사이트](https://www.favicon-generator.org/)에 들어가서 원하는 이미지를 넣는다. (**70~240px 사이즈**면 다 되는듯)
   > 2. create favicon을 누르면 다운로드 압축파일과 html 코드가 보인다.
   > 3. 압축파일을 다운로드- 압축해제후 해당 폴더 명 맨 끝에 **.ico** 를 붙인다.
   > 4. 이 파일을 **/assets/**폴더 안에 통째로 복사나 이동시킨다.
   > 5. **\_includes/head/**폴더 안으로 들어가서 custom.html파일로 들어간다.
   > 6. 아까 파비콘 압축파일을 만든 사이트에 있던 html코드를 붙여넣어준다. (통째로)
   > 7. href 위치에 아까 넣어준 폴더명경로를 추가해준다. 아래와 같이 한다.
   >
   > ```html
   > <!-- start custom head snippets -->
   > <link
   >   rel="apple-touch-icon"
   >   sizes="57x57"
   >   href="/assets/favicon_greentornado.ico/apple-icon-57x57.png"
   > />
   > <!-- ... 중간 생략 ... -->
   > <link
   >   rel="manifest"
   >   href="/assets/favicon_greentornado.ico/manifest.json"
   > />
   > <meta name="msapplication-TileColor" content="#ffffff" />
   > <meta
   >   name="msapplication-TileImage"
   >   content="/assets/favicon_greentornado.ico/ms-icon-144x144.png"
   > />
   > <meta name="theme-color" content="#ffffff" />
   > <!-- end custom head snippets -->
   > ```

5. 깃허브 기본 아이콘 사용

   > **깃허브 기본 아이콘을 사용하면 간편하다.**
   >
   > 1. [원하는 아이콘을 찾는다.](https://fontawesome.com/v4.7.0/icons/)
   > 2. 해당 코드를 복사한다. (ex:<i class="fa fa-bandcamp" aria-hidden="true"></i> 이중 fa fa-bandcamp 만 복사 )
   > 3. 이 복사를 config.yml에서 icon 부분에 붙여주면 적용이 된다.

   > (fab부분을 빼면 안되는지는 모른다. 일단 넣는다.)
   >
   > ```yml
   > icon: "fab fa-fw fa-bitbucket"
   > ```

6. 이메일 주소 클릭시 나에게 메일 전송 화면 뜨게 하기

   > 이메일 버튼을 누르면 gmail화면이 뜨면서 나의 이메일 주소가 연동되구, <br>바로 적을 수 있게 해주는 기능을 원했다.<br> [여기를 들어가서 보니 다양한 기능이 존재하고 있었다.](https://www.makeuseof.com/tag/instantly-compose-new-emails-gmail-bookmark-trick/)

   > 핵심은 아래의 링크를 그냥 먼저 적는다.<br> **https://mail.google.com/mail/?view=cm&fs=1&tf=1**

   > 다음은 옵션 별로 원하는 것을 붙여 쓰면된다.
   >
   > - &to=보내는곳 : 내 이메일 (사용자가 버튼을 누르면 본인이 보내는 사람이고 나에게 보내는 것이니 to가 본인 계정)
   > - &cc=참조 : 또 받으면 좋을 사람이나 기타 등...
   > - &bcc=숨은참조
   > - &su=제목
   > - &body=내용
