---
layout: posts
title: "👾Android Snippet"
date: 2021-03-14 00:00:00 +0900
categories: android Java Kotlint
tags: android, java, kotlin
permalink: /develope/snippet
---

---
## 목차
1. [자주 사용 객체](#Android-SKILL)
    - [인텐트](#Intent-(데이터-이동))
    - [팝업창](#AlertDialog-(팝업창))

2. [자주 쓰는 자바 형 변화 / 코틀린도 포함하자](#CONVERT)
    - [List, Array](#List,-Array)

---

## Android SKILL
---

### Intent (데이터 이동)
- adapter에서 activity 로 arraylist 보내고 받기 

```java
// arraylist 그리고 adapter에서activity로 데이터 보내는 방법
List<String> deliver_addr = new ArrayList<>();
Intent test = new Intent(v.getContext(), 도착할액티비티.class);
test.putExtra("key이름", (Serializable) deliver_addr);
v.getContext().startActivity(test);

//가져오기
receive = getIntent().getExtras().getArrayList("key이름");
```
- 일반 ArrayList 보내기 (직접사용은 아직안해봄)~

```java
// ArrayList 보내기 - 라고 하는데 해보지는 않음
Intent intent = new Intent(context ,TargetActivity.class);
intent.putParcelableArrayListExtra("Object_list", personArrayList);
context.startActivity(intent);
//가져오기
ArrayList<ListItem> items = getIntent().getParcelableArrayListExtra("Object_list");
```

### AlertDialog (팝업창)
- 화면에서 팝업창을 띄우는 방법 

```java
// builder를 만들어준다.
AlertDialog.Builder builder = new AlertDialog.Builder(위치한액티비티명.this);
// 원하는 모양/문구로 커스텀 
builder.setTitle("알림");
builder.setMessage("정말 종료하시겠습니까?");

// T/F 사용자 응답 선택지 제공 
// setPostiveButton true를 원할 경우, ('원하는 멘트', new DialogInterface.OnClickListener)를 옵션으로 자동 완성되게 한다.
builder.setPositiveButton("예", new DialogInterface.OnClickListener() { 
        @Override
        public void onClick(DialogInterface dialog, int which) {
            Log.d(LOG_TAG,"예");
            ActivityCompat.finishAffinity(MainActivity.this); // 해당 앱 액티비티 종료
            System.exit(0);  // 앱 완전 종료 
            }
        }).setNegativeButton("아니요", new DialogInterface.OnClickListener() {
            @Override
            public void onClick(DialogInterface dialog, int which) {
                Log.d(LOG_TAG,"유지합니다.");
            }
        }).show(); // 마지막으로 보여주는 부분 까먹으면 안된다.
```


---
## CONVERT
---
### List, Array

- String[] ⏩ List<String>

```java
String[] B = new String[3]; 
String delimiter = " ";
List<String> split = Arrays.asList(str.split(delimiter));
// or String[] B = new String[]{"a","b","c"}; or String[] B = {"a","b","c"};
```
    
- List<String> ⏩ ArrayList<String>

```java
String delimiter = " ";
List<String> split = Arrays.asList(str.split(delimiter));
ArrayList<String> arrayList = new ArrayList<String>();
arrayList.addAll(split);
```