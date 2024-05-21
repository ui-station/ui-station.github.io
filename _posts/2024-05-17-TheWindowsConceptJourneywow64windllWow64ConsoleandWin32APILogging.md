---
title: "윈도우 컨셉트 저니 - wow64windll Wow64 콘솔 및 Win32 API 기록"
description: ""
coverImage: "/assets/img/2024-05-17-TheWindowsConceptJourneywow64windllWow64ConsoleandWin32APILogging_0.png"
date: 2024-05-17 18:57
ogImage: 
  url: /assets/img/2024-05-17-TheWindowsConceptJourneywow64windllWow64ConsoleandWin32APILogging_0.png
tag: Tech
originalTitle: "The Windows Concept Journey — “wow64win.dll” (Wow64 Console and Win32 API Logging)"
link: "https://medium.com/@boutnaru/the-windows-concept-journey-wow64win-dll-wow64-console-and-win32-api-logging-eb7bae974598"
---


일반적으로 "wow64.dll"(Win32 콘솔 및 Win32 API 로깅)은 WOW64에서 사용되는 동적 링크 라이브러리입니다. "%windir%\system32\wow64win.dll"에 위치한 64비트 이진 파일입니다. 이 DLL 파일은 Microsoft에 의해 디지털로 서명되었습니다.

요약하면, "wow64win.dll"은 "win32k.sys" 진입점 함수들을 제공합니다(https://learn.microsoft.com/en-us/windows/win32/winprog64/wow64-implementation-details). 따라서 이는 "win32k.sys"에서 노출된 관련 시스콜을 호출하기 위해 사용됩니다("NtGdi*" 또는 "NtUser*"로 시작되는 것과 같은 것들) — 아래 스크린샷에서 보여지는 대로(무료 버전의 IDA를 사용하여 캡처했습니다).

마지막으로, DLL의 특성을 고려하면 원래 파일 이름은 "wow64lg2.dll"임을 알 수 있습니다 — 아래 스크린샷에서도 확인할 수 있습니다. 또한, "wow64.dll" (https://medium.com/@boutnaru/the-windows-concept-journey-wow64-dll-win32-emulation-on-nt64-8ff99ec32c43)은 "wow64win.dll"에 종속되어 있습니다. 따라서 일반적으로 user32.dll을 로드하지 않는 "비 Windows 서브시스템" 프로세스에서도로드될 수 있습니다(https://wbenny.github.io/2018/11/04/wow64-internals.html).

다음 글에서 뵙겠습니다;-) 트위터에서 팔로우할 수 있으세요 — @boutnaru (https://twitter.com/boutnaru). 또한 다른 글을 읽고 싶으시면 미디엄에서 확인 가능합니다 — https://medium.com/@boutnaru. 무료 eBook은 https://TheLearningJourneyEbooks.com에서 찾을 수 있습니다.

<div class="content-ad"></div>


![2024-05-17-TheWindowsConceptJourneywow64windllWow64ConsoleandWin32APILogging_0.png](/assets/img/2024-05-17-TheWindowsConceptJourneywow64windllWow64ConsoleandWin32APILogging_0.png)
