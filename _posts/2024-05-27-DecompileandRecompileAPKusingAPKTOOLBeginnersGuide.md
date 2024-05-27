---
title: "APKTOOL을 사용하여 APK를 디컴파일하고 재컴파일하는 방법 초보자 안내"
description: ""
coverImage: "/assets/img/2024-05-27-DecompileandRecompileAPKusingAPKTOOLBeginnersGuide_0.png"
date: 2024-05-27 16:15
ogImage: 
  url: /assets/img/2024-05-27-DecompileandRecompileAPKusingAPKTOOLBeginnersGuide_0.png
tag: Tech
originalTitle: "Decompile and Recompile APK using APKTOOL : Beginners Guide"
link: "https://medium.com/@ps.sujith/decompile-and-recompile-apk-using-apktool-beginners-guide-4ad03c2c5b8f"
---


개인 실험을 위한 앱의 디컴파일 및 다시 컴파일하는 과정을 찾다가 많은 기사와 블로그를 만나게 되었습니다. 그 중 일부는 몇 단계를 놓치거나 다른 도구를 사용했습니다. 그래서 저는 apktool을 사용하여 앱을 디컴파일하고 다시 컴파일하는 방법에 대한 기사를 쓰기로 생각했습니다. 제가 수집한 모든 정보를 통합하고 샘플 표현과 함께 제시하겠습니다.

![이미지](/assets/img/2024-05-27-DecompileandRecompileAPKusingAPKTOOLBeginnersGuide_0.png)

## 기본 사항

- Apktool: 안드로이드 APK 파일에 대한 역공학 도구입니다.
- Keytool: 디컴파일된 APK에 서명하기 위한 새 키스토어 파일을 생성하는 데 사용됩니다.
- Apksigner: APK에 서명하는 데 사용됩니다.
- Zipalign: 디컴파일된 파일들을 정렬하는 데 도움이 됩니다.

<div class="content-ad"></div>

추가 도구
JD-GUI: Java 디컴파일러
dex2jar: 안드로이드 .dex 및 java .class 파일과 작업하는 도구

## 전제 조건

Mac과 Linux에서 APK를 쉽게 디컴파일할 수 있습니다. Windows의 경우 일부 조정이 필요하지만, 아직 시도해보지 않았습니다. 디컴파일을 시작하려면 시스템에 JDK와 Android SDK를 설치해야 합니다.

Mac에서 Brew를 사용하여 Apktool 유틸리티를 설치하는 것은 매우 쉽습니다.

<div class="content-ad"></div>

```js
brew install apktool
```

이 링크에서 자세한 설치 가이드를 찾을 수 있어요

## 실험

화면에 "Original App"이라는 텍스트를 표시하는 앱의 프로가드가 활성화된 — 서명된 APK가 있어요
우리 실험의 목표는
* 이 APK를 디컴파일하기
* 배경색과 텍스트 색상 변경하기
* 텍스트 "Original App"을 "Recompiled App"으로 바꾸기
* 성공적으로 재컴파일하고 앱에 서명하기

<div class="content-ad"></div>

<img src="/assets/img/2024-05-27-DecompileandRecompileAPKusingAPKTOOLBeginnersGuide_1.png" />

## 연구실로 들어가봅시다

단계 1: 디컴파일

ApkMirror.com 또는 다른 사이트에서 어떤 앱의 APK를 다운로드할 수 있습니다. 여기서는 샘플 앱 "experiment_app.apk"의 서명된 APK를 사용하겠습니다.
먼저, 이 APK를 디컴파일해야 합니다. apktool을 사용하여 APK를 디컴파일하는 다음 명령을 사용할 수 있습니다.

<div class="content-ad"></div>

```js
apktool d [apk 위치] -o [디컴파일된 파일이 저장될 출력 폴더 위치]
```

![APKTOOL 사용하여 APK 디컴파일 및 재컴파일 시작 가이드](/assets/img/2024-05-27-DecompileandRecompileAPKusingAPKTOOLBeginnersGuide_2.png)

위 명령어를 실행한 후, apktool이 내 문서 디렉토리에 "experimentapp_decompiled"라는 새 폴더를 생성했습니다.

![APKTOOL 사용하여 APK 디컴파일 및 재컴파일 시작 가이드](/assets/img/2024-05-27-DecompileandRecompileAPKusingAPKTOOLBeginnersGuide_3.png)

<div class="content-ad"></div>

만약 APK 파일에서 리소스 파일을 디컴파일하고 싶지 않다면, 대신 이 명령을 사용하세요

```js
apktool d -r -s [apk 위치]  -o [디컴파일된 파일을 저장할 출력 폴더 위치]
```

Stage 2 : 파일 수정

저는 디컴파일된 파일 폴더에서 strings.xml과 colours.xml을 찾았어요

<div class="content-ad"></div>

```markdown
![Image](/assets/img/2024-05-27-DecompileandRecompileAPKusingAPKTOOLBeginnersGuide_4.png)

Android Studio에서는 이 리소스 파일을 창으로 드래그하거나 XML 편집기를 사용하여 열 수 있습니다.
배경색을 빨간색으로, 텍스트 색상을 노란색으로 변경했습니다. 그런 다음, 텍스트를 "재컴파일된 앱"으로 변경했습니다.

Stage 3: 디컴파일된 리소스를 APK로 재컴파일하기

변경 사항을 적용한 후, 다음 명령을 사용하여 디컴파일된 파일을 APK로 재컴파일할 예정입니다:
```

<div class="content-ad"></div>

```js
apktool b [디컴파일된 파일의 루트 폴더 위치]
```

![Decompile and Recompile APK using APKTOOL Beginner's Guide](/assets/img/2024-05-27-DecompileandRecompileAPKusingAPKTOOLBeginnersGuide_5.png)

이제 Apktool은 파일들을 컴파일하고 APK를 생성합니다. 디컴파일된 파일을 저장한 루트 폴더와 동일한 위치의 새 폴더인 "dist" 안에 저장될 것입니다.

아하.. 알겠네요..

<div class="content-ad"></div>

```markdown
![image](/assets/img/2024-05-27-DecompileandRecompileAPKusingAPKTOOLBeginnersGuide_6.png)

Stage 4: Zipalign the apk for the optimal loading

Zipalign is a zip archive alignment tool that helps ensure that all uncompressed files in the archive are aligned relative to the start of the file. Zipalign tool can be found in the “Build Tools” folder within the Android SDK path.

![image](/assets/img/2024-05-27-DecompileandRecompileAPKusingAPKTOOLBeginnersGuide_7.png)
```  

<div class="content-ad"></div>

APK를 zip align하려면 다음 명령을 실행하세요:

```js
zipalign -v 4 [재컴파일 된 apk] [zip align된 apk를 저장할 위치 및 apk 이름 및 확장자]
```

![이미지](/assets/img/2024-05-27-DecompileandRecompileAPKusingAPKTOOLBeginnersGuide_8.png)

단계 5: zip align된 APK에 서명하기 위한 새 키스토어 파일 생성

<div class="content-ad"></div>

다음 명령어를 사용하여 keytool을 사용하여 키스토어 파일을 생성했어요. 이 명령을 사용하면 키스토어의 암호와 상세 정보를 입력하라는 프롬프트가 나타날 거에요.

```js
keytool -genkey -v -keystore [키스토어 이름] -alias alias_name -keyalg RSA -keysize 2048 -validity 10000
```

<img src="/assets/img/2024-05-27-DecompileandRecompileAPKusingAPKTOOLBeginnersGuide_9.png" />

단계 6: apksigner를 사용하여 앱에 서명하세요.

<div class="content-ad"></div>

Apksigner 도구는 Android SDK 빌드 도구의 24.0.3 버전 이상에서 제공되며, APK를 서명하고 해당 APK가 지원하는 Android 플랫폼 버전에서 성공적으로 확인될 것임을 확인할 수 있습니다.
Apk signer는 Android SDK 경로의 "build tools" 폴더 안에 ZipAlign과 함께 있습니다.

![image](/assets/img/2024-05-27-DecompileandRecompileAPKusingAPKTOOLBeginnersGuide_10.png)

아래 명령어를 사용하여 apksigner를 사용하여 APK를 서명하십시오.

```js
apksigner sign --ks [키스토어 이름] --v1-signing-enabled true --v2-signing-enabled true [zip align된 apk 위치]
```

<div class="content-ad"></div>

```markdown
<img src="/assets/img/2024-05-27-DecompileandRecompileAPKusingAPKTOOLBeginnersGuide_11.png" />

Stage 7: Verify the signed APK

The zip-aligned — signed APK can be verified using the same apksigner.

```js
apksigner verify [signed apk location]
``` 
```

<div class="content-ad"></div>

<img src="/assets/img/2024-05-27-DecompileandRecompileAPKusingAPKTOOLBeginnersGuide_12.png" />

단계 8: 앱 설치

adb 명령어를 사용하거나 수동으로 설치하여 확인된 apk를 설치합니다.

```js
adb install /Users/matrix/Documents/APK/experimentapp_zipaligned.apk
```

<div class="content-ad"></div>

```markdown
![앱의 배경과 텍스트 색상이 변경되었어요](/assets/img/2024-05-27-DecompileandRecompileAPKusingAPKTOOLBeginnersGuide_13.png)

와우! 앱의 배경과 텍스트 색상이 변경되었어요.

![실험 성공](/assets/img/2024-05-27-DecompileandRecompileAPKusingAPKTOOLBeginnersGuide_14.png)

실험 성공 .....
```

<div class="content-ad"></div>

```md
dex2jar [classes.dex 파일 위치 디컴파일 된 폴더]

jd-gui [classes-dex2jar.jar 파일 위치]
```