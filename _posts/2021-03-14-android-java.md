---
layout: posts
title: "👾Android Java Snippet"
date: 2021-03-14 00:00:00 +0900
categories: android architecture
tags: android, java
permalink: /develope/
---
---
## Android SKILL

### Intent
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


---
## CONVERT
---
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