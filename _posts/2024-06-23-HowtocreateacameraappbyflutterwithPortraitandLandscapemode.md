---
title: "Flutter로 인물 및 가로 모드가 가능한 카메라 앱 만드는 방법"
description: ""
coverImage: "/assets/img/2024-06-23-HowtocreateacameraappbyflutterwithPortraitandLandscapemode_0.png"
date: 2024-06-23 21:39
ogImage: 
  url: /assets/img/2024-06-23-HowtocreateacameraappbyflutterwithPortraitandLandscapemode_0.png
tag: Tech
originalTitle: "How to create a camera app by flutter with Portrait and Landscape mode"
link: "https://medium.com/@smueez/how-to-create-a-camera-app-by-flutter-with-portrait-and-landscape-mode-ec1d05d31b85"
---


![이미지](/assets/img/2024-06-23-HowtocreateacameraappbyflutterwithPortraitandLandscapemode_0.png)

현재 모바일 애플리케이션 개발에서 가장 흔한 단어 중 하나인 Flutter 입니다. Flutter는 요즘 가장 편리하고 강력한 크로스 플랫폼 프레임워크 중 하나입니다. 놀라운 커뮤니티, 도구 및 라이브러리를 갖추고 있어 여러분이 원하는 모바일 애플리케이션을 만들 수 있습니다.

지금은 Flutter 애플리케이션에 카메라와 같은 기능이 있는 것이 매우 흔합니다. 여러분의 애플리케이션이 CRM이든 E-commerce이든 소셜 플랫폼이든, 카메라 기능은 매우 일반적이고 유용합니다. 모든 이런 미리 튜토리얼 단어들로 지루하게 만드는 것은 하지 않겠습니다. 바로 개발에 들어가 봅시다...

사용할 패키지:

<div class="content-ad"></div>

우린 flutter.dev의 Camera 패키지를 사용할 거야.

패키지 설치 방법:

이것을 당신의 패키지의 pubspec.yaml 파일에 다음과 같은 줄을 추가할 거야 (그리고 암시적으로 flutter pub get을 실행할 거야):

```js
dependencies:
  camera: ^0.10.5+2
```

<div class="content-ad"></div>

# 가져오기

이제 당신의 Dart 코드에서 다음을 사용할 수 있습니다:

```js
import 'package:camera/camera.dart';
```

또한 이 링크를 클릭하여 패키지 설치를 확인할 수도 있습니다.

<div class="content-ad"></div>

# 어떻게 구현하나요?

나는 코드를 깔끔하게 작성하는 것을 선호해요. 모든 기능을 분리하고 모든 기능이나 기능이 재사용 가능하도록 만들려고 해요. 그래서 전체 카메라 기능을 분리하는 걸 좋아해요.
아래에서 어떻게 할 수 있는지 알려드릴게요.
단계 1: Stateful 위젯을 만드세요 (저는 CameraScreen이라고 이름 붙였어요).

```js
class CameraScreen extends StatefulWidget 
```

이제 List`CameraDescription` 유형의 변수를 취하세요. 메인 함수에서 초기화하려면 전역 변수로 만들 수도 있어요. 카메라 화면으로 이동하기 전에 사용 가능한 카메라 목록을 가져오는 것이 좋아요. 따라서 사용 가능한 카메라 목록을 가져오는 방법입니다. 메인 함수에서 사용 가능한 카메라 목록을 가져올 수도 있고, 카메라 화면으로 이동하기 전에 가져올 수도 있고, 카메라 컨트롤러를 초기화하기 전에 가져올 수도 있어요. 하지만 이렇게하여 사용 가능한 카메라를 가져올 수 있어요.

<div class="content-ad"></div>

```dart
// 여기에 코드를 넣어 주세요.
List<CameraDescription> cameras = [];
void getAvalaibleCamera() async {
  try {
    cameras = await availableCameras();
  } catch (error, stackTrace) {
    // 무언가를 처리합니다.
  }
}
// 여기에 코드를 넣어 주세요.
```

단계 2: 이제 Init 함수를 정의합시다. Init 함수에서는 카메라에 필요한 모든 컨트롤러 및 기타 필요한 것들을 초기화할 것입니다. 다음과 같이 진행할 거에요.

```dart
late CameraController _cameraController;
late CameraDescription backCamera, frontCamera;

@override
void initState() {
  getAvailableCamera();

  super.initState();
}

@override
void dispose() {
  _cameraController.dispose();
  super.dispose();
}

void getAvailableCamera() async {
  for (var i = 0; i < cameras.length; i++) {
    var camera = cameras[i];
    if (camera.lensDirection == CameraLensDirection.back) {
      backCamera = camera;
    }
    if (camera.lensDirection == CameraLensDirection.front) {
      frontCamera = camera;
    }
  }
  if (backCamera == null) {
    backCamera = cameras.first;
  }
  if (frontCamera == null) {
    frontCamera = cameras.last;
  }
  _cameraController = CameraController(backCamera, ResolutionPreset.medium);

  _cameraController.initialize().then((_) {
    if (!mounted) {
      return;
    }
    setState(() {});
  });
}
```

단계 3: 예스, 초기화하는 것에 관해 충분히 알겠죠. 이제 UI로 넘어갑시다.

<div class="content-ad"></div>

UI를 만들 때는 화면을 최대한 간단하게 유지하려고 노력하고 있어요. CameraView와 두 개의 버튼(한 개는 사진 촬영을 위한 것이고, 다른 한 개는 카메라 설정 변경을 위한 것)만 있는 정적 UI를 만들 계획이에요.

여기서 빌드 함수를 확인해 볼게요.

```js
 @override
  Widget build(BuildContext context) {
    if (!_cameraController.value.isInitialized) {
      return Container();
    }
    return Scaffold(
      backgroundColor: Colors.transparent,
      body: CameraPreview(_cameraController),
      floatingActionButtonLocation: FloatingActionButtonLocation.centerFloat,
      floatingActionButton: Stack(
        children: [
          Align(
            alignment: Alignment.bottomCenter,
            child: FloatingActionButton(
              backgroundColor: Colors.orange,
              child: const Icon(Icons.camera_alt),
              onPressed: () async {
                XFile? image = await _cameraController.takePicture();
                if(image != null){
                  // todo: 여기에 코드를 추가해 주세요
                }
              },
            ),
          ),
          Align(
            alignment: Alignment.bottomRight,
            child: Padding(
              padding: EdgeInsets.only(right: 20),
              child: FloatingActionButton(
                backgroundColor: Colors.deepOrange,
                child: const Icon(Icons.flip_camera_android),
                onPressed: () {
                  if (_cameraController.description.lensDirection == CameraLensDirection.back) {
                    _cameraController = CameraController(frontCamera, ResolutionPreset.medium);
                  } else {
                    _cameraController = CameraController(backCamera, ResolutionPreset.medium);
                  }

                  _cameraController.initialize().then((_) {
                    if (!mounted) {
                      return;
                    }
                    setState(() {});
                  });
                },
              ),
            ),
          ),
        ],
      ),
    );
  }
```

여기서 todo 섹션에서 작업할 거예요.

<div class="content-ad"></div>

이 UI가 될 것입니다. 하지만 UI에 대해서는 걱정하지 마세요. 마음대로 UI를 만들면 됩니다.

![이미지](/assets/img/2024-06-23-HowtocreateacameraappbyflutterwithPortraitandLandscapemode_1.png)

단계 4: 여기까지는 모든 것이 카메라 패키지 설명서에 따라 괜찮아 보입니다. 하지만 이제 실제 문제가 발생합니다.

"자동 회전" 모드를 끄고 이 사진처럼 가로 모드에서 사진을 찍고 싶을 때, 이미지가 90도 회전됩니다. 이 문제는 플러터 앱을 기본적으로 세로 모드로 설정하면서 발생합니다. 따라서 기기의 방향을 변경해도 앱이 방향에 따라 구성을 변경하지 않습니다.

<div class="content-ad"></div>

응, 알았어. 이 문제는 단순히 기기의 자동 회전 모드를 켜는 것으로 쉽게 해결할 수 있어. 그런데 이미지나 비디오 파일을 편평화하기 위해 자동 회전 모드가 필요한 카메라 앱을 본 적이 없어. 이제 그런 문제가 생기면 어떻게 해야 할까?

첫 번째 할 일은, 기기가 어느 방향을 향하고 있는지 감지하는 것인데, 자동 회전 모드가 켜져 있던 말던 상관없이 이 작업을 수행해야 해.

기기의 자동 회전을 끈 채로 OrientationBuilder를 사용하여 이를 감지할 수 없어.

이를 확인하려면 자이로 센서를 사용해야 해. 인터넷에 센서에 대한 많은 정보가 있으니 찾아보길 추천해. 기기의 다양한 움직임 등을 감지하는 다른 센서들도 있어. 많은 게임이 이러한 센서들을 사용하여 사용자에게 깨끗한 게임 경험을 제공해.

<div class="content-ad"></div>

자이로 센서를 앱에 설치하려면 sensor_plus라는 이 패키지를 추가해야 합니다.

pubspec.ymal 파일에 패키지를 설치하고 사용하려는 페이지에 가져와야 합니다.

```js
dependencies:
  sensors_plus: ^3.0.3
```

제가 밝혔듯이 OrientationBuilder는 기기의 자동 회전 모드가 꺼져 있거나 강제로 앱을 특정 방향으로 설정한 경우에는 작동하지 않습니다. 그래서 기기의 방향을 알기 위해 직접 OrientationBuilder를 작성해야 합니다.

<div class="content-ad"></div>

Builder 위젯을 만들어 봅시다.

```js
import 'dart:async';

import 'package:flutter/material.dart';
import 'package:flutter/services.dart';
import 'package:sensors_plus/sensors_plus.dart';

class CustomOrientationBuilder extends StatefulWidget {
  const CustomOrientationBuilder({required this.builder, required this.isVideosRecording, super.key});
  /// 이 빌더 함수는 콘텍스트와 방향(열거형)을 제공합니다.
  final Widget Function(BuildContext, CustomOrientation) builder;
  /// 이것은 비디오 녹화 중인지 여부를 나타내는 플래그입니다.
  /// 녹화 중인 경우 방향 변경이 인식되지 않습니다. 이 논리는 제가 만들었지만 여러분은 변경할 수 있습니다.
  final bool isVideosRecording;
  @override
  State<CustomOrientationBuilder> createState() => _CustomOrientationBuilderState();
}

class _CustomOrientationBuilderState extends State<CustomOrientationBuilder> {
  late StreamSubscription<AccelerometerEvent> accelerometerSubscription;
  CustomOrientation cameraOrientation = CustomOrientation.portrait;

  @override
  void initState() {
    super.initState();
    orientation();
  }
  @override
  Widget build(BuildContext context) {
    return widget.builder(context, cameraOrientation);
  }

  orientation(){
    try {
      accelerometerSubscription = accelerometerEvents.listen((AccelerometerEvent event) {
        if(!widget.isVideosRecording){
          setState(() {
            if (event.z < -8.0) {

              cameraOrientation = CustomOrientation.portrait;
            } else if (event.x > 5.0) {

              cameraOrientation = CustomOrientation.rightLandScape;
            } else if (event.x < -5.0) {

              cameraOrientation = CustomOrientation.leftLandScape;
            }
            else{

              cameraOrientation = CustomOrientation.portrait;
            }
           
          });

        }



      });
    } on PlatformException catch (e) {
      print("Error: $e");
    }
  }
  @override
  void dispose() {
    super.dispose();
    /// 듣기자나 컨트롤러나 스트림 등의 청취자나 컨트롤러나 스트림을 해제하거나 취소하거나 제거하지 않도록 유의하세요.
    accelerometerSubscription.cancel();
  }
}

/// 방향 열거형
enum CustomOrientation {portrait, leftLandScape, rightLandScape}
```

또는 사진을 찍기 전에 자이로 센서로 방향을 확인할 수 있습니다. 다음과 같이요.

```js
late StreamSubscription<AccelerometerEvent> accelerometerSubscription;
  
CustomOrientation getOrientation(){
  CustomOrientation cameraOrientation = CustomOrientation.portrait;
    try {
      accelerometerSubscription = accelerometerEvents.listen((AccelerometerEvent event) {
        if(!widget.isVideosRecording){
          if (event.z < -8.0) {

              cameraOrientation = CustomOrientation.portrait;
            } else if (event.x > 5.0) {

              cameraOrientation = CustomOrientation.rightLandScape;
            } else if (event.x < -5.0) {

              cameraOrientation = CustomOrientation.leftLandScape;
            }
            else{

              cameraOrientation = CustomOrientation.portrait;
            }

        }

      });
    } on PlatformException catch (e) {
      print("Error: $e");
    }
    return cameraOrientation;
}

/// 여러분의 코드
takePhoto()async{
  CustomOrientation orientation = getOrientation();
  XFile? image =  await _cameraController.takePicture();

  /// 여러분의 코드

}
/// 여러분의 코드
```

<div class="content-ad"></div>

지금은 당신의 선택입니다. 방향을 어떻게 설정할지 결정하세요.

이제 이 빌더를 카메라 화면에 추가해 보겠습니다. 다음과 같이 보일 것입니다.

```js
import 'package:camera/camera.dart';
import 'package:flutter/material.dart';
import 'package:prism_v2/global/global_variables.dart';
import 'package:sizer/sizer.dart';

class CameraScreen extends StatefulWidget {
  static const routeName = '/camera';

  const CameraScreen({Key? key}) : super(key: key);

  @override
  _CameraScreenState createState() => _CameraScreenState();
}

class _CameraScreenState extends State<CameraScreen> {
  late CameraController _cameraController;
  late CameraDescription backCamera, frontCamera;

  @override
  void initState() {
    getAvailableCamera();

    super.initState();
  }

  @override
  void dispose() {
    _cameraController.dispose();
    super.dispose();
  }

  void getAvailableCamera() async {
    for (var i = 0; i < cameras.length; i++) {
      var camera = cameras[i];
      if (camera.lensDirection == CameraLensDirection.back) {
        backCamera = camera;
      }
      if (camera.lensDirection == CameraLensDirection.front) {
        frontCamera = camera;
      }
    }
    if (backCamera == null) {
      backCamera = cameras.first;
    }
    if (frontCamera == null) {
      frontCamera = cameras.last;
    }
    _cameraController = CameraController(backCamera, ResolutionPreset.medium);

    _cameraController.initialize().then((_) {
      if (!mounted) {
        return;
      }
      setState(() {});
    });
  }

  @override
  Widget build(BuildContext context) {
    if (!_cameraController.value.isInitialized) {
      return Container();
    }
    return Scaffold(
      backgroundColor: Colors.transparent,
      body: CameraPreview(_cameraController),
      floatingActionButtonLocation: FloatingActionButtonLocation.centerFloat,
      floatingActionButton: Stack(
        children: [
          Align(
            alignment: Alignment.bottomCenter,
            /// here I have added this right on the take photo button  
            child: CustomOrientation(
              builder: (context, orientation) {
                return FloatingActionButton(
                  backgroundColor: Colors.orange,
                  child: const Icon(Icons.camera_alt),
                  onPressed: () async {
                    await _cameraController.takePicture().then((value) {
                      Navigator.pop(context, value.path);
                    });
                  },
                );
              }
            ),
          ),
          Align(
            alignment: Alignment.bottomRight,
            child: Padding(
              padding: EdgeInsets.only(right: 5.0.w),
              child: FloatingActionButton(
                backgroundColor: Colors.deepOrange,
                child: const Icon(Icons.flip_camera_android),
                onPressed: () {
                  if (_cameraController.description.lensDirection ==
                      CameraLensDirection.back) {
                    _cameraController =
                        CameraController(frontCamera, ResolutionPreset.medium);
                  } else {
                    _cameraController =
                        CameraController(backCamera, ResolutionPreset.medium);
                  }

                  _cameraController.initialize().then((_) {
                    if (!mounted) {
                      return;
                    }
                    setState(() {});
                  });
                },
              ),
            ),
          ),
        ],
      ),
    );
  }
}
```

이제 우리는 방향을 설정했습니다. 당신의 기기나 앱의 기본 방향에 대해 아무것도 할 필요가 없습니다. 문제는 기기의 방향을 변경할 때 발생할 것입니다.

<div class="content-ad"></div>

해결하기 위해서는 디바이스의 현재 방향에 따라 이미지를 회전해야 합니다. 여기서는 takephoto 함수만 작성하고 있어요. 한번 봅시다...

```dart
import 'package:image/image.dart' as img;

takePhoto(CustomOrientation orientation) async {
  
  XFile? image = await _cameraController.takePicture();
  List<int> rotatedBytes = [];
  if (orientation != AppString.portrait) {
    int angle = 90;
    if (orientation == AppString.leftLandScape) {
      angle = -90;
    }
    Uint8List byte = await File(rawImage!.path).readAsBytes();
    img.Image? image = img.decodeImage(byte);
    image = img.copyRotate(image!, angle.toInt());
    rotatedBytes = img.encodeJpg(image);
  }
  File imageFile = rotatedBytes.isEmpty ? File(rawImage!.path) : await File(rawImage!.path).writeAsBytes(rotatedBytes);
  /// 여기에 코드를 작성하세요

}
```

요렇게요. 이제 원하는 대로 직립 사진을 찍을 수 있어요. 이제 카메라 방향이 OS 카메라 기능처럼 작동할 거에요.

이제 비디오 파일을 회전시키고 싶다면! 가능해요. 그러나 해당 작업을 하려면 flutter_ffmpeg 패키지를 import해야 해요. 그걸 추천하진 않겠어요. 두 가지 이유가 있어서요.

<div class="content-ad"></div>

- 비디오 녹화를 중지한 후, 비디오 처리에 너무 오랜 시간이 걸릴 수 있습니다. 녹화된 비디오의 방향에 따라 회전 처리를 하려면 많은 시간이 소요될 수 있습니다.
- 특정 패키지를 가져오려면 minSdkVersion을 24로 증가해야 합니다. 그러면 앱 크기가 두 배가 됩니다. 그렇지 않길 바라겠네요.

그러므로 가장 좋은 방법은 비디오를 서버로 전송하여 서버에서 비디오를 처리하도록 하는 것입니다.
또는 사용자에게 비디오를 보여줄 때, RotatedBox를 사용하여 화면 방향에 따라 비디오 플레이어 위젯을 회전시킬 수 있습니다. 이렇게 하면 사용자가 비디오가 회전되었는지 여부를 이해하지 못할 것입니다.
Flutter 여행에 도움이 되기를 바랍니다.

즐거운 코딩하시고 ❤️