---
title: "RootBeer 루트 감지 우회 방법  irsyadsec"
description: ""
coverImage: "/assets/img/2024-06-22-RootBeerRootDetectionBypassirsyadsec_0.png"
date: 2024-06-22 22:20
ogImage: 
  url: /assets/img/2024-06-22-RootBeerRootDetectionBypassirsyadsec_0.png
tag: Tech
originalTitle: "RootBeer Root Detection Bypass | irsyadsec"
link: "https://medium.com/@irsyadsec/rootbeer-root-detection-bypass-irsyadsec-8f183b67c6f0"
---



![RootBeer](/assets/img/2024-06-22-RootBeerRootDetectionBypassirsyadsec_0.png)

몇 달 전에 어떤 애플리케이션의 침투 테스트를 진행했는데, 해당 애플리케이션이 루팅된 기기를 감지하기 위해 사용되는 RootBeer 라이브러리로 보호되어 있음을 발견했습니다. 이 기사에서는 RootBeer 라이브러리를 우회하는 방법을 보여드리겠습니다. 이때 사용할 것은 샘플 RootBeer 애플리케이션입니다.

시작하기 전에, 이미 Frida Server 및 ADB 사용에 익숙하다고 가정합니다.

# RootBeer 라이브러리 우회 단계별 안내


<div class="content-ad"></div>

## 1. 환경 설정

아래 도구들이 설치되어 있는지 확인해주세요:

- ADB (Android Debug Bridge): 안드로이드 장치와 통신하는 데 사용됩니다.
- Frida Server: 개발자, 역공학자 및 보안 연구자를 위한 동적 인스트루먼테이션 툴킷입니다.
- 에뮬레이터: 이 경우, ldplayer를 사용하고 있습니다.

## 2. 장치 준비

<div class="content-ad"></div>

Frida 릴리스 페이지에서 기기 아키텍처에 맞는 적절한 Frida Server 이진 파일을 다운로드하세요.

에뮬레이터에 연결하세요

```js
adb devices -l
```

<img src="/assets/img/2024-06-22-RootBeerRootDetectionBypassirsyadsec_1.png" />

<div class="content-ad"></div>

```js
adb root
```

<img src="/assets/img/2024-06-22-RootBeerRootDetectionBypassirsyadsec_2.png" />

ADB를 사용하여 바이너리를 장치로 푸시합니다:

```js
adb push frida-server-16.0.10-android-x86_64 /data/local/tmp
```

<div class="content-ad"></div>

파일에 대한 권한을 주십시오.

```js
adb shell
cd /data/local/tmp
chmod +x frida-server-16.0.10-android-x86_64
```

![이미지](/assets/img/2024-06-22-RootBeerRootDetectionBypassirsyadsec_3.png)

프리다 서버를 시작하세요.

<div class="content-ad"></div>

```js
./frida-server-16.0.10-android-x86_64 -D
```

![Root Detection Bypass](/assets/img/2024-06-22-RootBeerRootDetectionBypassirsyadsec_4.png)

## 3. 루트 우회

여기서는 GitHub에서 이미 사용 가능한 exploit 파일을 사용하여 가장 빠른 방법을 사용할 것입니다. Pich4ya의 exploit을 사용하고 있으며, 해당 사이트에서 다운로드할 수 있습니다. 그에게 큰 박수를 보내요👏👏👏👏

<div class="content-ad"></div>

이미지 태그를 Markdown 형식으로 변경하세요.

![이미지](/assets/img/2024-06-22-RootBeerRootDetectionBypassirsyadsec_5.png)

파일을 다운로드한 후 저장한 폴더로 이동하여 명령 프롬프트를 엽니다.

애플리케이션 패키지 이름을 찾습니다.

```js
frida-ps -Uai
```

<div class="content-ad"></div>

<img src="/assets/img/2024-06-22-RootBeerRootDetectionBypassirsyadsec_6.png" />

Rootbeer 샘플 애플리케이션을 사용 중이기 때문에 패키지 이름은 com.scottyab.rootbeer.sample 입니다.

그 다음에 다음 명령어를 실행하세요

```js
frida -l root.js -U -f com.example.app --pause
```

<div class="content-ad"></div>

엔터를 누른 후 다시 타이핑하세요

```js
%이력서
```

![이미지](/assets/img/2024-06-22-RootBeerRootDetectionBypassirsyadsec_7.png)

그리고 Voilaaaa 당신의 apk가 이미 우회됐어야 합니다

<div class="content-ad"></div>

<표>를 마크다운 형식으로 변경해보세요.

그리고 와! APK가 바이패스되어야 합니다.

![이미지](/assets/img/2024-06-22-RootBeerRootDetectionBypassirsyadsec_8.png)

이렇게 도움이 되는 기사들을 더 보시고 싶으세요? 귀하의 지원이 저에게 가치 있는 콘텐츠를 만들 수 있게 합니다. 더 많은 무료 프롬프트를 만들기 위해 커피 한 잔 사주시겠어요? 귀하의 기부에 감사드립니다! ❤️❤️❤️