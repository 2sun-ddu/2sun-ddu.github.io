---
layout: post
title:  "Flutter에서 ExtendBodyBehindAppBar 사용하기"
tags: [flutter, ExtendBodyBehindAppBar, appbar]
categories: [flutter]
author: frnd
---

# Flutter에서 ExtendBodyBehindAppBar 사용하기

Flutter에서 `Scaffold` 위젯은 앱의 기본 레이아웃을 제공합니다. 이 레이아웃에는 `AppBar`, 본문 콘텐츠, 그리고 필요에 따라 `FloatingActionButton`과 같은 다른 UI 요소들이 포함됩니다. 기본적으로 본문 콘텐츠는 `AppBar` 아래에 렌더링됩니다. 하지만 `ExtendBodyBehindAppBar` 속성을 사용하면 본문 콘텐츠가 `AppBar` 뒤로 확장되도록 할 수 있습니다. 이를 통해 시각적으로 매력적인 레이아웃을 만들 수 있습니다.

## ExtendBodyBehindAppBar 사용 예시

다음 예제는 `ExtendBodyBehindAppBar` 속성을 사용하여 본문 콘텐츠를 `AppBar` 뒤로 확장하는 방법을 보여줍니다.

Dart
```dart
import 'package:flutter/material.dart';

class MyHomePage extends StatelessWidget {
 @override
 Widget build(BuildContext context) {
   return Scaffold(
     extendBodyBehindAppBar: true, // 본문 콘텐츠를 AppBar 뒤로 확장
     appBar: AppBar(
       title: Text('ExtendBodyBehindAppBar Example'),
       backgroundColor: Colors.transparent, // AppBar의 배경색을 투명하게 설정
       elevation: 0, // AppBar의 그림자 효과 제거
     ),
     body: Stack(
       children: [
         Image.network(
           'https://example.com/image.jpg', // 전체 화면 배경 이미지
           fit: BoxFit.cover,
           height: double.infinity,
         ),
         SingleChildScrollView(
           padding: EdgeInsets.only(top: 150), // AppBar 높이만큼 패딩 추가
           child: Column(
             children: [
               Card(
                 child: ListTile(
                   title: Text('List Item 1'),
                 ),
               ),
               Card(
                 child: ListTile(
                   title: Text('List Item 2'),
                 ),
               ),
               // 더 많은 콘텐츠 추가 가능
             ],
           ),
         ),
       ],
     ),
   );
 }
}
```

위 예제에서는 다음과 같은 단계를 따릅니다:

1. `Scaffold` 위젯의 `extendBodyBehindAppBar` 속성을 `true`로 설정합니다.
2. `AppBar`의 `backgroundColor`를 `Colors.transparent`로 설정하고 `elevation`을 `0`으로 설정하여 투명한 `AppBar`를 만듭니다.
3. `body`에 `Stack` 위젯을 사용하여 전체 화면 배경 이미지와 본문 콘텐츠를 겹치게 합니다.
4. `SingleChildScrollView`를 사용하여 본문 콘텐츠를 스크롤 가능하게 만듭니다.
5. `SingleChildScrollView`에 `padding`을 추가하여 본문 콘텐츠가 `AppBar` 아래에서 시작하도록 합니다.

이 예제의 결과는 다음과 같습니다:

- `AppBar`는 투명하게 렌더링되며, 본문 콘텐츠가 뒤에서 보입니다.
- 전체 화면 배경 이미지가 표시됩니다.
- 본문 콘텐츠는 `AppBar` 아래에서 시작하며, 스크롤 가능합니다.

## ExtendBodyBehindAppBar의 추가 고려사항

`ExtendBodyBehindAppBar`를 사용할 때는 다음 사항을 고려해야 합니다:

1. **안전 영역 고려**: 일부 기기에서는 노치(notch) 또는 카메라 컷아웃 등의 안전 영역이 있습니다. 이러한 영역을 피하기 위해 `SafeArea` 위젯을 사용해야 합니다.
2. **스크롤 동작 조정**: 본문 콘텐츠가 `AppBar` 뒤로 확장되므로, 스크롤 동작을 원활하게 하기 위해 추가 조정이 필요할 수 있습니다.
3. **AppBar 스타일링**: `ExtendBodyBehindAppBar`를 사용할 때는 `AppBar`의 스타일링을 조정해야 할 수 있습니다. 예를 들어, 배경색과 그림자 효과를 제거하거나 텍스트 색상을 변경해야 할 수 있습니다.

## 결론

Flutter의 `ExtendBodyBehindAppBar` 속성은 본문 콘텐츠를 `AppBar` 뒤로 확장하여 시각적으로 매력적인 레이아웃을 만들 수 있습니다. 이 기능을 사용할 때는 안전 영역, 스크롤 동작, 그리고 `AppBar` 스타일링을 고려해야 합니다. 적절한 예제와 설명을 통해 이 기능을 효과적으로 활용할 수 있습니다.
