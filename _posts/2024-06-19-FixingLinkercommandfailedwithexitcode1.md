---
title: "Linker command failed with exit code 1 오류 해결하기"
description: ""
coverImage: "/assets/img/2024-06-19-FixingLinkercommandfailedwithexitcode1_0.png"
date: 2024-06-19 10:58
ogImage: 
  url: /assets/img/2024-06-19-FixingLinkercommandfailedwithexitcode1_0.png
tag: Tech
originalTitle: "Fixing “Linker command failed with exit code 1”"
link: "https://medium.com/@amricodes/fixing-linker-command-failed-with-exit-code-1-73e864cd5257"
---


<img src="/assets/img/2024-06-19-FixingLinkercommandfailedwithexitcode1_0.png" />

iOS 개발 중 에러를 만나면 “Linker command failed with exit code 1”과 같은 알 수 없는 메시지가 표시되면 특히 답답할 수 있습니다. 그러나 걱정하지 마세요! 이 블로그 포스트에서는 이 오류를 해결하고 iOS 프로젝트를 다시 가동할 수 있는 몇 가지 간단한 단계를 안내해 드릴 거에요.

이 오류가 의미하는 바:

“Linker command failed with exit code 1” 오류는 주로 Xcode가 프로젝트 파일을 함께 연결할 수 없을 때 발생합니다. Xcode가 파일을 읽는 데 어려움을 겪는 경우는 파일을 삭제했지만 제대로 삭제되지 않은 복사본이나 종속성이 제대로 읽을 수 없는 경우입니다.

<div class="content-ad"></div>

아래는 이 오류를 해결하는 데 따를 수 있는 몇 가지 단계입니다: 

- 팟 다시 설치

   1. 먼저 맥에서 터미널을 엽니다.
   2. 아래 명령어를 따릅니다:
      ```
      cd 당신의_프로젝트_경로
      pod deintegrate
      pod update
      ```

만약 문제가 계속되면 다음을 시도해보세요:

<div class="content-ad"></div>

- pod deintegrate
- pod clean
- rm Podfile
- pod init
- pod install

2. Go to your Project navigator - Target - Build settings - Set BUILD ACTIVE ARCHITECTURE ONLY to NO 
3. Add archicture arm64 and x86_64 in Project navigator - Target - Build settings - EXCLUDED ARCHECTURES

4. Reinstall swift packages

- Remove swift packages and add them again in your project. Don’t remove all the packages at once, start by removing and immediately adding the package that you installed last.
- Reinstall one package and build your project. If the error still persists, remove another package and reinstall it. Keep doing it with all the packages you have in your project.
- Don’t forget to press cmd+shift+k to clean build folder before building the project.
- Also, clean derived data from your project and run Reset package content in your project

<div class="content-ad"></div>

결론:
그런 걸로 끝났어요! 이 간단한 단계를 따라하면 iOS 프로젝트에서 "Linker command failed with exit code 1" 오류를 해결할 수 있을 겁니다. 기억하세요, 에러 해결은 iOS 개발의 필수적인 부분이에요. 조금의 인내와 적절한 도구가 있다면 모든 어려움을 극복할 수 있습니다.

즐거운 코딩 되세요!