## Flutter에서 HTTP Interceptor 활용하기

### 서론

Flutter 개발에서 HTTP 통신은 필수적인 요소입니다. 다양한 API를 호출하여 데이터를 주고받는 과정에서, 개발자들은 보안, 로그 기록, 에러 처리 등의 작업을 반복적으로 수행하게 됩니다. 이러한 반복적인 작업을 간편하고 효율적으로 처리하기 위해 **HTTP Interceptor**가 등장했습니다.

### HTTP Interceptor란 무엇일까요?

HTTP Interceptor는 HTTP 요청과 응답을 가로채서 처리할 수 있는 강력한 도구입니다. 마치 중개자 역할을 하여, 요청과 응답에 대한 다양한 작업을 수행할 수 있습니다.

**주요 기능:**

- **헤더 추가 및 수정:** 요청에 필요한 인증 토큰, 언어 설정 등의 헤더를 추가하거나 수정할 수 있습니다.
- **로그 기록:** 모든 HTTP 요청과 응답을 기록하여 디버깅 및 문제 해결에 활용할 수 있습니다.
- **에러 처리:** 예상치 못한 에러 발생 시, 사용자에게 적절한 메시지를 보여주거나 자동으로 재시도 로직을 구현할 수 있습니다.
- **요청 캐싱:** 응답 데이터를 캐싱하여 반복적인 요청을 줄이고 성능을 향상시킬 수 있습니다.

### Flutter에서 HTTP Interceptor 사용하기

Flutter에서 HTTP Interceptor를 사용하기 위해서는 다음 단계를 따르면 됩니다.

1. **http_interceptor** 패키지를 프로젝트에 추가합니다.
2. **Interceptor** 클래스를 상속받아 자신만의 Interceptor를 구현합니다.
3. **interceptRequest** 및 **interceptResponse** 메서드를 오버라이드하여 원하는 작업을 수행합니다.
4. **HttpClient** 클래스를 생성할 때 **interceptors** 옵션에 구현된 Interceptor를 추가합니다.

### 실제 코드 예시

Dart

```dart
import 'package:http_interceptor/http_interceptor.dart';

class MyInterceptor extends InterceptorContract {
  @override
  Future<Request> interceptRequest(Request request) async {
    // 요청 헤더에 토큰 추가
    request.headers['Authorization'] = 'Bearer YOUR_TOKEN';
    return request;
  }

  @override
  Future<Response> interceptResponse(Response response) async {
    // 응답 데이터 로그 기록
    print('Response: ${response.body}');
    return response;
  }
}

void main() async {
  // Interceptor 설정
  final client = HttpClient(interceptors: [MyInterceptor()]);

  // API 호출
  final response = await client.get(Uri.parse('https://api.example.com'));

  // 응답 처리
  print(response.statusCode);
  print(response.body);
}
```


### HTTP Interceptor 활용 팁

- 다양한 Interceptor를 조합하여 사용할 수 있습니다.
- 여러 Interceptor의 실행 순서를 제어할 수 있습니다.
- 특정 조건에 따라 Interceptor를 선택적으로 적용할 수 있습니다.

### 결론

HTTP Interceptor는 Flutter 개발에서 HTTP 통신을 더욱 효율적이고 안정적으로 만들어주는 강력한 도구입니다. 다양한 기능을 활용하여 개발 생산성을 높이고, 앱의 안정성을 향상시킬 수 있습니다.

> 하지만 DDU에서는 dio를 쓰지롱 😱
