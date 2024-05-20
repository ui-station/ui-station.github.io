---
title: "Windows Concept 여행 - Control Panel"
description: ""
coverImage: "/assets/img/2024-05-20-TheWindowsConceptJourneyControlPanel_0.png"
date: 2024-05-20 18:20
ogImage: 
  url: /assets/img/2024-05-20-TheWindowsConceptJourneyControlPanel_0.png
tag: Tech
originalTitle: "The Windows Concept Journey — “Control Panel”"
link: "https://medium.com/@boutnaru/the-windows-concept-journey-control-panel-34bf84ca7ff0"
---


"Control Panel"의 목표는 운영 체제의 시스템 수준 기능 구성을 도와주는 것입니다. 이에는 시스템 유지 보수, 보안, 하드웨어/소프트웨어 설정 및 사용자 계정 관리 등이 포함됩니다. "Control Panel"을 언급할 때는 일반적으로 Windows 제어판 전체 기능을 의미하며, 특정 제어판은 아래 스크린샷에 표시된 것과 같이 "Control Panel Items"로 참조됩니다.

전반적으로, 제어판 항목은 "앱렛(applet)"로도 불립니다. 각 앱렛은 기본적으로 "CPlApplet" 함수를 익스포트하는 "*.CPL" 파일이며, DLL/PE 파일입니다. "앱렛"을 등록하는 몇 가지 방법이 있습니다. "*.CPL" 파일을 "%windir%\System32" 디렉터리에 배치하는 것도 그 중 하나입니다. 또한, 앱렛에 대한 정보(위치/이름의 CPL 파일)를 다음 하위키에 추가하는 것도 하나의 방법입니다: "HKLM\Software\Microsoft\Windows\CurrentVersion\Control Panel\Cpls".

마지막으로, 앱렛에 대한 CLSID(클래스 식별자, Microsoft의 "Component Object Model" 즉, COM의 일부)를 다음 위치에 추가할 수도 있습니다: "HKLM\SOFTWARE\Microsoft\Windows\CurrentVersion\Explorer\ControlPanel\NameSpace". 추가로, 앱렛에 의해 수행된 구성은 주로 로컬 컴퓨터에 관련이 있습니다. MMC 스냅인은 "mmc.exe"에 의해 호스팅되며, MS-RPC와 같은 프로토콜을 사용하여 원격 관리를 지원합니다.

다음 글에서 뵙겠습니다 ;-) 트위터에서 저를 팔로우해보세요 — @boutnaru (https://twitter.com/boutnaru). 그리고 미디엄에서 다른 글도 읽을 수 있습니다 — https://medium.com/@boutnaru. 무료 eBook은 https://TheLearningJourneyEbooks.com에서 찾아볼 수 있습니다.

<div class="content-ad"></div>

![2024-05-20-TheWindowsConceptJourneyControlPanel_0](/assets/img/2024-05-20-TheWindowsConceptJourneyControlPanel_0.png)