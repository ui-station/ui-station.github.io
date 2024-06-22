---
title: "iOS 18 Early에서 새로운 Siri UI애플 인텔리전스 UI를 만나보세요"
description: ""
coverImage: "/assets/img/2024-06-19-ExperiencetheNewSiriUIEnableAppleIntelligenceUIiniOS18Early_0.png"
date: 2024-06-19 14:04
ogImage: 
  url: /assets/img/2024-06-19-ExperiencetheNewSiriUIEnableAppleIntelligenceUIiniOS18Early_0.png
tag: Tech
originalTitle: "Experience the New Siri UI (Enable Apple Intelligence UI) in iOS 18 Early"
link: "https://medium.com/macoclock/experience-the-new-siri-ui-enable-apple-intelligence-ui-in-ios-18-early-73045d70492c"
---



![이미지](/assets/img/2024-06-19-ExperiencetheNewSiriUIEnableAppleIntelligenceUIiniOS18Early_0.png)

여러 플랫폼에서 iOS 18에서 새로운 Siri UI 또는 애플이 애플 인텔리전스 UI를 활성화하지 않은 것에 대한 게시물을 본 적이 있을 것입니다. 그러나 iOS 18 Developer Beta 1 (빌드 109)에는 아직 새로운 Siri 관련 기능이 포함되어 있지 않습니다.

하지만 좋은 소식이 있습니다! 다음 단계를 따라 새 Siri 인터페이스의 미리보기를 볼 수 있습니다.

# 중요 사항:


<div class="content-ad"></div>

이 새로운 Siri UI를 활성화하면 사용자 인터페이스만 변경됩니다. Apple Intelligence 기능은 아직 제공되지 않습니다.

새로운 Siri UI는 iOS 18/iPadOS 18 개발자 베타 1을 실행하는 모든 iPhone 및 iPad과 호환됩니다. 그러나 출시되면 M1 또는 A17 Pro 칩 및 그 이후의 장치만 Apple Intelligence 기능을 지원할 것입니다.

## 시작하기 전에

- 장치 백업: 진행하기 전에 장치를 백업해야 합니다. 데이터는 중요하며 보호해야 합니다.
- Find My iPhone/iPad 비활성화: 설정에서 ` [당신의 Apple ID] ` Find My ` Find My iPhone/iPad로 이동하여 일시적으로 비활성화하세요.
- 필수 조건: 이 프로세스에는 Mac 또는 Windows PC가 필요합니다.

<div class="content-ad"></div>

<img src="/assets/img/2024-06-19-ExperiencetheNewSiriUIEnableAppleIntelligenceUIiniOS18Early_1.png" />

# 새 Siri UI를 활성화하는 단계

## Mac 사용자를 위한 방법

- Cowabunga Lite 다운로드 및 설치:
이 Github 링크로 이동하여 macOS 12 버전을 다운로드하십시오.

<div class="content-ad"></div>


![Step 2: Connect Your Device](/assets/img/2024-06-19-ExperiencetheNewSiriUIEnableAppleIntelligenceUIiniOS18Early_2.png)

2. Connect Your Device: Use a USB cable to connect your iPhone or iPad to your Mac.

![Step 3: Open Cowabunga Lite](/assets/img/2024-06-19-ExperiencetheNewSiriUIEnableAppleIntelligenceUIiniOS18Early_3.png)

3. Open Cowabunga Lite. You’ll be greeted with this screen.


<div class="content-ad"></div>


![Image 1](/assets/img/2024-06-19-ExperiencetheNewSiriUIEnableAppleIntelligenceUIiniOS18Early_4.png)

4. Navigate to the Custom Operation tab on the left.

![Image 2](/assets/img/2024-06-19-ExperiencetheNewSiriUIEnableAppleIntelligenceUIiniOS18Early_5.png)

5. Import the Configuration File: Click on Import .cowperation, then select the iOS_18_Siri.cowperation


<div class="content-ad"></div>



![이미지](/assets/img/2024-06-19-ExperiencetheNewSiriUIEnableAppleIntelligenceUIiniOS18Early_6.png)

6. 새 Siri UI 활성화: 편집을 클릭하고 "활성화" 옵션이 선택되었는지 확인하세요.

![이미지](/assets/img/2024-06-19-ExperiencetheNewSiriUIEnableAppleIntelligenceUIiniOS18Early_7.png)

7. 조정 적용: 왼쪽에있는 "적용" 탭으로 이동하여 조정 적용 버튼을 클릭하세요. 이 과정은 시간이 걸릴 수 있습니다. 기기가 다시 시작되고 설정 화면이 표시됩니다.


<div class="content-ad"></div>

경고: "iPhone 부분 설정됨"이라는 화면을 만나면 큰 파란 버튼을 누르지 마세요. 대신 데이터를 보호하려면 "부분 설정으로 계속"을 눌러주세요.

![이미지](/assets/img/2024-06-19-ExperiencetheNewSiriUIEnableAppleIntelligenceUIiniOS18Early_8.png)

직접 다운로드하기: Cowabunga Configuration

## Windows 사용자를 위한

<div class="content-ad"></div>

- Cowabunga Lite 다운로드 및 설치: 구글에서 소프트웨어를 검색합니다.
- 기기 연결: USB 케이블을 사용하여 iPhone 또는 iPad을 PC에 연결합니다.
- 설정 옵션: Cowabunga Lite에서 설정 옵션 탭에서 SkipSetup 옵션이 선택되어 있는지 확인합니다.
- 구성 파일 추가: 파일 탐색기에서 %APPDATA%/CowabungaLite/Workspace로 이동합니다. UUID를 찾은 다음 SkipSetup/ManagedPreferencesDomain/mobile로 이동하여 plist 파일을 추가합니다 (다운로드 가능).
- 트윅 적용: Cowabunga Lite에서 왼쪽의 Apply 탭으로 이동하여 Apply Tweaks 버튼을 클릭합니다. 이 프로세스는 시간이 소요됩니다. 기기가 다시 시작되고 설정 화면이 나타납니다.경고: "iPhone Partially Set Up"이라는 화면이 표시되면 큰 파란 버튼을 탭하면 안 됩니다. 대신 "부분 설정으로 계속"을 탭하여 데이터를 보존합니다.