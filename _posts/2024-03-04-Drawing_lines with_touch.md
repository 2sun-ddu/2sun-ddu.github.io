---
layout: post
title:  "Drawing lines with screen touch"
tags: [flutter, CustomPainter, touch, draw, line]
categories: [flutter]
---



<br>
<br>


<div style="font-size:18px; font-weight:bold;display:inline-block;border:1px solid #d9d9d9; padding:0 4px 0 4px;">대략요약</div>

- GestureDetector를 이용해서 좌표(offset) 구하기
	- List<Offset>에 계속해서 add offset...
- 좌표로 CustomPainter를 이용해서  drawLine하기

```dart
canvas.drawLine(startOffset, endOffset, paint);
```

<hr/>

<div style="font-size:18px; font-weight:bold;display:inline-block;border:1px solid #d9d9d9; padding:0 4px 0 4px;">추가</div>

-  좌표를 구할때, List<Offset?>으로 null값을 넣어서 CustomPainter로 그릴때 null 값일때 안 그리면, 여러 라인들을 그릴수 있다. 
```dart
for (var i = 0; i < offsets.length - 1; ++i) {  
  if (offsets[i] != null && offsets[i + 1] != null) {  
    canvas.drawLine(offsets[i]!, offsets[i + 1]!, paintMountains);  
  }  
}
```
	RepaintBoundary알아보기
