---
layout: posts
title: "🍐Pear Comments Zip"
date: 2021-03-15 00:00:00 +0900
categories: android project
tags: android, java
permalink: /develope/pear
published: true
---

## 목차

1. [메인](#MainActivity.class)
   > []() > []()
2. [로그인](#LoginActivity.class)
   > []() > []()

---

#### MainActivity.class

```java
ActivityCompat.finishAffinity(MainActivity.this); // 해당 액티비티 종료
System.exit(0); //앱 완전 종료
// .... 각 화면 이동 가이드 주석만 존재함.(제거안함)

// come back main screen
    @Override
    protected void onResume() {
        super.onResume();
        Log.d(LOG_TAG, "onResume start");
    }
```

#### LoginActivity.class

```java
 // request에서 default_web_client_id 이부분 직접 실행해야 나옴.
final GoogleSignInOptions googleOpt = new GoogleSignInOptions.Builder(GoogleSignInOptions.DEFAULT_SIGN_IN)
        .requestIdToken(getString(R.string.default_web_client_id))
        .requestEmail()
        .build();
client = GoogleSignIn.getClient(this, googleOpt);
```

구글 api를 쓰다보면, 이 <span style="color:red">default_web_client_id</span> 부분이 빨갛게 된다.
당황하지말고, 실행시키고 한번 수신을 하게 되면 자연적으로 입력이 된다.

```java
public void signOut(View v){
    //구글 로그인 계정 아예 제거하는 메서드임
    client.signOut()
            .addOnCompleteListener(this, new OnCompleteListener<Void>() {
                @Override
                public void onComplete(@NonNull Task<Void> task) {
                    Toast.makeText(getApplicationContext(), "로그인 정보 초기화 완료 \n 로그인 버튼을 눌러주세요.", Toast.LENGTH_SHORT).show();
                    Log.d(LOG_TAG, "다른 계정으로 로그인가능.");
                }
            });
}
```
