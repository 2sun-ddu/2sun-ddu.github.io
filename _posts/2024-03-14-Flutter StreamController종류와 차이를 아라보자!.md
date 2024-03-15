---
layout: post
title:  "Flutter에서 SingleStreamController와 BroadcastStreamController의 차이점"
tags: [flutter, stream, broadcast]
categories: [flutter]
author: frnd
---

## Flutter에서 SingleStreamController와 BroadcastStreamController의 차이점

### 1. 개요

Flutter에서 StreamController는 데이터 스트림을 관리하는 데 사용되는 클래스입니다. StreamController에는 두 가지 주요 유형이 있습니다:

- **SingleStreamController:** 단일 구독자만 허용하는 스트림 컨트롤러입니다.
- **BroadcastStreamController:** 여러 구독자를 허용하는 스트림 컨트롤러입니다.

### 2. 차이점 비교

|구분|SingleStreamController|BroadcastStreamController|
|---|---|---|
|구독자 수|단일 구독자만 허용|여러 구독자 허용|
|데이터 전달 방식|최근 데이터만 전달|모든 구독자에게 모든 데이터 전달|
|메모리 사용량|적음|많음|
|성능|빠름|느림|
|사용 예시|간단한 데이터 전달, 예: 버튼 클릭 이벤트|실시간 데이터 업데이트, 예: 채팅 메시지, 센서 데이터|


### 3. 예시

**SingleStreamController 예시:**

Dart

```dart
final controller = SingleStreamController<int>();

// 데이터 추가
controller.add(1);
controller.add(2);

// 구독
controller.stream.listen((data) {
  print(data); // 1, 2 출력
});
```


**BroadcastStreamController 예시:**

Dart

```dart
final controller = BroadcastStreamController<int>();

// 데이터 추가
controller.add(1);
controller.add(2);

// 구독
controller.stream.listen((data) {
  print(data); // 1, 2 출력
});

// 추가 구독
controller.stream.listen((data) {
  print(data); // 1, 2 출력
});
```

### 4. 결론

SingleStreamController와 BroadcastStreamController는 각각 장단점이 있습니다. SingleStreamController는 구독자가 하나만 가능하고 최근 데이터만 전달하기 때문에 메모리 사용량이 적고 성능이 빠릅니다. BroadcastStreamController는 여러 구독자를 허용하고 모든 데이터를 전달하기 때문에 실시간 데이터 업데이트에 적합하지만 메모리 사용량이 많고 성능이 느릴 수 있습니다.

따라서, 어떤 StreamController를 사용할지는 구독자 수, 데이터 전달 방식, 메모리 사용량, 성능 등을 고려하여 선택해야 합니다.
