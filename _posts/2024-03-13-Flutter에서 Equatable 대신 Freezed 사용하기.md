---
layout: post
title:  "Flutter에서 Equatable 대신 Freezed 사용하기"
tags: [flutter, freezed, equatable]
categories: [flutter]
author: frnd
---


Flutter 개발 시 상태 관리를 위해 모델 클래스를 자주 사용합니다. 
이때 모델 클래스의 인스턴스를 비교하거나 불변성(immutability)을 보장하기 위해 `Equatable` 패키지를 사용하는 경우가 많습니다. 그러나 `freezed` 패키지는 `Equatable`보다 더 강력한 기능을 제공하며, 모델 클래스 작성을 더욱 편리하게 해줍니다. 이 블로그 포스트에서는 `Equatable` 대신 `freezed`를 사용하는 것이 좋은 이유에 대해 알아보겠습니다.

## Freezed 소개

[Freezed](https://pub.dev/packages/freezed)는 Flutter와 Dart에서 불변 데이터 클래스를 생성하는 데 사용되는 코드 생성기입니다. 이 패키지는 개발자가 정의한 모델 클래스에 대해 다양한 유틸리티 메서드와 함수를 자동으로 생성해줍니다. 이를 통해 코드 작성을 줄이고, 버그 발생 가능성을 낮출 수 있습니다.

## Equatable 대신 Freezed를 사용하는 이유

### 1. 불변성(Immutability) 보장

`Equatable`을 사용할 때는 개발자가 직접 불변성을 보장해야 합니다. 반면, `freezed`는 생성된 모델 클래스가 자동으로 불변이 됩니다. 이를 통해 상태 관리 시 발생할 수 있는 버그를 방지할 수 있습니다.

### 2. 값 비교 및 해시 코드 생성

`Equatable`을 사용하면 값 비교를 위해 `==` 연산자를 오버라이드해야 합니다. `freezed`는 이를 자동으로 처리해주며, 해시 코드 생성 기능도 제공합니다.

### 3. 코드 생성기 기능

`freezed`는 단순히 불변 데이터 클래스를 생성하는 것 이상의 기능을 제공합니다. 개발자가 정의한 모델 클래스에 대해 다양한 유틸리티 메서드와 함수를 자동으로 생성해줍니다. 예를 들어, `copyWith` 메서드를 통해 기존 인스턴스의 일부 값만 변경한 새로운 인스턴스를 쉽게 만들 수 있습니다.

### 4. 유니온 타입 지원

`freezed`는 유니온 타입(Union Types)을 지원합니다. 이를 통해 여러 가지 상태를 하나의 클래스로 표현할 수 있어, 상태 관리가 더욱 간편해집니다.
```dart
@freezed
sealed class Union with _$Union {
  const factory Union.data(int value) = Data;
  const factory Union.loading() = Loading;
  const factory Union.error([String? message]) = Error;
}
```


```dart
var union = Union(42);

print(
  union.when(
    (int value) => 'Data $value',
    loading: () => 'loading',
    error: (String? message) => 'Error: $message',
  ),
);
```


### 5. 코드 생성 및 문법 지원

`freezed`는 코드 생성기 기반으로 동작하므로, 개발 환경에서 코드 자동 완성 및 문법 강조 기능을 제공합니다. 이를 통해 개발 생산성을 높일 수 있습니다.

## 결론

`freezed`는 `Equatable`보다 더 강력한 기능을 제공하며, 모델 클래스 작성을 더욱 편리하게 해줍니다. 불변성 보장, 값 비교, 해시 코드 생성, 코드 생성기 기능, 유니온 타입 지원 등의 이점이 있습니다. 따라서 Flutter 개발 시 `Equatable` 대신 `freezed`를 사용하는 것이 좋습니다. 코드의 안전성과 생산성을 높일 수 있을 것입니다.

