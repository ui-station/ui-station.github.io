---
title: "라라벨  플러터 플러터 앱을 라라벨 인증 API에 연결하기"
description: ""
coverImage: "/assets/img/2024-05-27-LaravelFlutterConnectingFlutterApptoLaravelAuthAPI_0.png"
date: 2024-05-27 16:35
ogImage: 
  url: /assets/img/2024-05-27-LaravelFlutterConnectingFlutterApptoLaravelAuthAPI_0.png
tag: Tech
originalTitle: "Laravel + Flutter: Connecting Flutter App to Laravel Auth API"
link: "https://medium.com/@mohamad.razzi.my/laravel-flutter-connecting-flutter-app-to-laravel-auth-api-bbbde371d730"
---


![Screenshot](/assets/img/2024-05-27-LaravelFlutterConnectingFlutterApptoLaravelAuthAPI_0.png)

플러터(Flutter)는 네이티브 컴파일된 애플리케이션을 구축하기 위한 인기 있는 오픈 소스 프레임워크로, 라라벨(Laravel)은 안전하고 확장 가능한 백엔드 솔루션을 제공하는 강력한 PHP 기반 웹 프레임워크입니다.

이 글에서는 플러터 애플리케이션과 라라벨 REST API를 통합하는 과정을 탐색하고, 안전한 사용자 인증 구현을 포함할 것입니다.

# [0] 인증 API가 포함된 라라벨 프로젝트 생성하기

<div class="content-ad"></div>

이전 글을 따라하거나 빠른 시작 프로젝트를 다운로드하세요.

# [1] Flutter 프로젝트 생성하기

## [1.1] 기본 Flutter 프로젝트 생성

...다음 기본 설정으로.

<div class="content-ad"></div>

(파일 → pubspec.yaml)

```yaml
version: 1.0.0+1
environment:
  sdk: '>=2.18.2 <3.0.0'
dependencies:
  flutter:
    sdk: flutter
  cupertino_icons: ^1.0.2
  dio: ^4.0.0
dev_dependencies:
  flutter_test:
    sdk: flutter
  flutter_lints: ^2.0.0
flutter:
  uses-material-design: true
```

## [1.2] dio 패키지 추가

dio.dart 패키지는 Dart용 인기있는 HTTP 클라이언트 라이브러리로, Flutter 애플리케이션에서 일반적으로 사용됩니다. 이는 HTTP 요청을 만들고 응답을 처리하며, 가로채기, 헤더 및 기타 고급 기능을 관리하기 위한 간단하고 직관적인 API를 제공하는 유연한 HTTP 클라이언트입니다.

<div class="content-ad"></div>

pubspec.yaml 파일을 업데이트해주세요:

```yaml
dependencies:
  dio: ^4.0.6
```

## [1.3] Auth 서비스 생성

(파일 →lib/services/auth_service.dart)

<div class="content-ad"></div>

```js
import 'package:dio/dio.dart';

class AuthService {
  final _dio = Dio(
    BaseOptions(
      baseUrl: 'https://demo.razzi.my/lara11breeze/public/api',
      headers: {
        'Accept': 'application/json',
      },
    ),
  );

  Future<Map<String, dynamic>> login(String email, String password) async {
    final response = await _dio.post('/login', data: {
      'email': email,
      'password': password,
    });
    return response.data;
  }

  Future<Map<String, dynamic>> register(
    String name,
    String email,
    String password,
  ) async {
    final response = await _dio.post('/register', data: {
      'name': name,
      'email': email,
      'password': password,
    });
    return response.data;
  }

  Future<Map<String, dynamic>> logout() async {
    final response = await _dio.post('/logout');
    return response.data;
  }
}
```

코드 설명:

1] dio/dio.dart 패키지 가져오기: 이 import 문은 dio.dart 패키지에서 HTTP 요청을 수행하는 데 사용되는 주요 클래스인 Dio 클래스를 가져옵니다.

2] Dio 인스턴스 초기화: 클래스에는 Dio 인스턴스로 초기화된 private _dio 필드가 있습니다. BaseOptions 매개변수를 사용하여 HTTP 요청의 기본 URL 및 기본 헤더를 구성합니다.
```

<div class="content-ad"></div>

- baseUrl: `https://demo.razzi.my/lara11breeze/public/api`: 이 설정은 API의 기본 URL을 설정합니다. 이 경우에는 지정된 URL에서 호스팅되는 데모 라라벨 API입니다.
- headers: ' `Accept`: `application/json` ': 이 설정은 기본 `Accept` 헤더를 `application/json`으로 설정합니다. 이는 API가 JSON 응답을 반환해야 함을 나타냅니다.

3] 로그인 메서드: 로그인 메서드는 이메일과 비밀번호를 인수로 취하며, `_dio.post` 메서드를 사용하여 /login 엔드포인트로 POST 요청을 보냅니다. 요청 데이터는 데이터 매개변수를 통해 맵으로 전달됩니다.

4] 등록 메서드: 등록 메서드는 이름, 이메일 및 비밀번호를 인수로 취하며, `_dio.post` 메서드를 사용하여 /register 엔드포인트로 POST 요청을 보냅니다. 요청 데이터는 데이터 매개변수를 통해 맵으로 전달됩니다.

5] 로그아웃 메서드: 로그아웃 메서드는 `_dio.post` 메서드를 사용하여 /logout 엔드포인트로 POST 요청을 보냅니다.

<div class="content-ad"></div>

## [1.4] 로그인 화면 생성

(파일 → lib/login_screen.dart)

```dart
import 'package:flutter/material.dart';
import 'services/auth_service.dart';

class LoginScreen extends StatefulWidget {
  @override
  _LoginScreenState createState() => _LoginScreenState();
}

class _LoginScreenState extends State<LoginScreen> {
  final _authService = AuthService();
  final _emailController = TextEditingController();
  final _passwordController = TextEditingController();

  Future<void> _login() async {
    final email = _emailController.text;
    final password = _passwordController.text;

    try {
      final response = await _authService.login(email, password);
      // 응답 처리, 예를 들어 액세스 토큰 및 사용자 데이터 저장
      print(response);
    } catch (e) {
      // 오류 처리
      print(e);
    }
  }

  @override
  Widget build(BuildContext context) {
    return Scaffold(
      appBar: AppBar(
        title: Text('Login'),
      ),
      body: Padding(
        padding: EdgeInsets.all(16.0),
        child: Column(
          children: [
            TextField(
              controller: _emailController,
              decoration: InputDecoration(
                hintText: 'Email',
              ),
            ),
            SizedBox(height: 16.0),
            TextField(
              controller: _passwordController,
              obscureText: true,
              decoration: InputDecoration(
                hintText: 'Password',
              ),
            ),
            SizedBox(height: 16.0),
            ElevatedButton(
              onPressed: _login,
              child: Text('Login'),
            ),
          ],
        ),
      ),
    );
  }
}
```

코드 설명:

<div class="content-ad"></div>

1] 의존성 가져오기: 이 코드는 핵심 Flutter UI 위젯을 제공하는 flutter/material.dart 패키지와 AuthService 클래스를 포함하는 auth_service.dart 파일을 가져옵니다.

2] LoginScreen 위젯: LoginScreen은 StatefulWidget으로 정의되어 있어 변경 가능한 상태를 가질 수 있습니다.

3] _LoginScreenState 클래스: _LoginScreenState 클래스는 LoginScreen 위젯의 내부 상태 클래스입니다. 다음을 포함합니다:

- _authService: 로그인 작업을 수행하는 데 사용되는 AuthService 클래스의 인스턴스입니다.
- _emailController 및 _passwordController: 이메일 및 비밀번호 필드의 입력 값을 관리하는 두 TextEditingController 인스턴스입니다.

<div class="content-ad"></div>

4] \_login 메서드: \_login 메서드는 사용자가 "로그인" 버튼을 탭했을 때 호출되는 비동기 함수입니다. 다음을 수행합니다:

- 각각의 TextEditingController 인스턴스에서 이메일과 비밀번호 값을 가져옵니다.
- \_authService 인스턴스의 login 메서드를 호출하여 이메일과 비밀번호를 전달합니다.
- login 메서드로부터 예상되는 데이터 맵(예: 사용자의 액세스 토큰 및 기타 데이터)를 처리합니다.
- 오류가 발생하면 콘솔에 오류를 출력합니다.

5] build 메서드: build 메서드는 LoginScreen 위젯의 UI를 정의합니다. 앱 바와 다음을 포함하는 본문이 있는 Scaffold 위젯을 생성합니다:

- 이메일과 비밀번호 입력을 위한 두 개의 TextField 위젯이 있으며, 각각의 TextEditingController 인스턴스가 연결되어 있습니다.
- 텍스트 필드와 로그인 버튼 사이에 간격을 추가하기 위한 SizedBox가 있습니다.
- 눌렸을 때 \_login 메서드를 호출하는 ElevatedButton 위젯이 있습니다.

<div class="content-ad"></div>

## [1.5] 메인 업데이트

(파일 → lib/main.dart)

```dart
import 'package:flutter/material.dart';
import 'login_screen.dart';

void main() {
  runApp(const MyApp());
}

class MyApp extends StatelessWidget {
  const MyApp({key}) : super(key: key);

  @override
  Widget build(BuildContext context) {
    return MaterialApp(
      title: '플러터 앱!!',
      theme: ThemeData(
        colorScheme: ColorScheme.fromSwatch(primarySwatch: Colors.indigo),
        useMaterial3: true,
        brightness: Brightness.light,
      ),
      darkTheme: ThemeData(
        colorScheme: ColorScheme.fromSwatch(primarySwatch: Colors.blue),
        useMaterial3: true,
        brightness: Brightness.dark,
      ),
      home: LoginScreen(),
      debugShowCheckedModeBanner: false,
    );
  }
}
```

이제 플러터 앱을 실행하면 MyHomePage 위젯이 아닌 LoginScreen이 표시됩니다.

<div class="content-ad"></div>

인증 기능을 더 강화하기 위해 더 많은 화면을 추가할 수 있습니다. 예를 들어, 등록 화면, 비밀번호 재설정 화면, 그리고 로그인에 성공한 후에 표시되는 홈 화면 등을 추가할 수 있습니다. 또한 사용자 인증 상태와 세션 관리를 다루는 로직을 추가할 수도 있습니다.

# [2] 테스트

Flutter 앱을 실행하세요.

1] 로그인 화면에서 Laravel 사용자의 이메일과 비밀번호를 입력하세요. 로그인 버튼을 누르세요.

<div class="content-ad"></div>

2] 로그를 확인하세요. 모든 것이 잘 작동되면 아래와 같이 응답이 출력됩니다. 응답에는 사용자 토큰이 포함되어 있는데, 이를 이후 요청에 사용할 수 있습니다.

![이미지](/assets/img/2024-05-27-LaravelFlutterConnectingFlutterApptoLaravelAuthAPI_1.png)

# 다운로드

[링크](https://archive.org/download/laravelprojects/lara11breeze_userapi_flutter_20240409.zip)