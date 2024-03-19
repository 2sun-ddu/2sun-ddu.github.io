---
layout: post
title:  "Flutter에서의 debounce와 throttle"
tags: [flutter, debounce, throttle]
categories: [flutter]
author: frnd
---

# Flutter에서의 debounce와 throttle

## debounce

debounce는 이벤트가 발생한 후 일정 시간 동안 함수 호출을 지연시키고, 그 시간 동안 다른 이벤트가 발생하면 지연 시간을 다시 초기화합니다. 플러터에서는 `Timer` 클래스를 사용하여 debounce를 구현할 수 있습니다.

dart

```dart
import 'dart:async';

class Debouncer {
  final Duration delay;
  Timer? _timer;

  Debouncer({required this.delay});

  void call(Function() action) {
    _timer?.cancel();
    _timer = Timer(delay, action);
  }
}

// 사용 예시
Debouncer debouncer = Debouncer(delay: Duration(milliseconds: 250));

// 텍스트 필드에 debounce 적용
TextField(
  onChanged: (value) {
    debouncer.call(() {
      // 텍스트 필드 입력 처리 로직
    });
  },
);
```





## throttle

throttle은 일정 시간 동안 함수 호출을 제한합니다. 플러터에서는 `Timer`와 `DateTime` 클래스를 사용하여 throttle을 구현할 수 있습니다.

```dart
import 'dart:async';

class Throttler {
  final Duration delay;
  Timer? _timer;
  DateTime? _lastExecutionTime;

  Throttler({required this.delay});

  void call(Function() action) {
    if (_lastExecutionTime == null ||
        DateTime.now().difference(_lastExecutionTime!) > delay) {
      _lastExecutionTime = DateTime.now();
      action();
    } else if (_timer == null) {
      _timer = Timer(delay, () {
        _lastExecutionTime = DateTime.now();
        action();
        _timer = null;
      });
    }
  }
}

// 사용 예시
Throttler throttler = Throttler(delay: Duration(milliseconds: 250));

// 스크롤 뷰에 throttle 적용
ListView.builder(
  itemCount: 100,
  itemBuilder: (context, index) {
    return ListTile(
      title: Text('Item $index'),
    );
  },
  controller: ScrollController()
    ..addListener(() {
      throttler.call(() {
        // 스크롤 이벤트 핸들러 로직
      });
    }),
);
```

## 언제 사용해야 하는가?

플러터에서도 debounce와 throttle은 주로 이벤트 핸들러에서 사용됩니다. 예를 들면 다음과 같은 경우에 유용합니다:

- **텍스트 필드 입력 이벤트**: 사용자가 입력을 완료할 때까지 기다린 후 한 번만 함수를 실행하려면 debounce를 사용합니다.
- **스크롤 이벤트**: 스크롤 이벤트는 매우 자주 발생하므로, throttle을 사용하여 불필요한 함수 호출을 줄일 수 있습니다.
- **애니메이션 이벤트**: 애니메이션 중에 불필요한 리빌드를 방지하기 위해 debounce나 throttle을 사용할 수 있습니다.

이렇게 debounce와 throttle은 플러터 앱의 성능을 최적화하고 불필요한 리소스 낭비를 줄이는 데 도움이 됩니다.


> ddu에서는 [EASY_DEBOUNCE](https://pub.dev/packages/easy_debounce)를 사용하고있습니다. 🫣
