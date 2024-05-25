---
title: "윈도우 프로세스 여행 - sppsvcexe 마이크로소프트 소프트웨어 보호 플랫폼 서비스"
description: ""
coverImage: "/assets/img/2024-05-23-TheWindowsProcessJourneysppsvcexeMicrosoftSoftwareProtectionPlatformService_0.png"
date: 2024-05-23 15:28
ogImage: 
  url: /assets/img/2024-05-23-TheWindowsProcessJourneysppsvcexeMicrosoftSoftwareProtectionPlatformService_0.png
tag: Tech
originalTitle: "The Windows Process Journey — “sppsvc.exe” (Microsoft Software Protection Platform Service)"
link: "https://medium.com/@boutnaru/the-windows-process-journey-sppsvc-exe-microsoft-software-protection-platform-service-a42f3abce8ca"
---


"sppsvc.exe" (Microsoft Software Protection Platform Service)은 "%windir%\System32\sppsvc.exe"에 위치한 PE 이진 파일입니다. Windows의 64비트 버전에서는 "cmd.exe"와 같은 다른 이진 파일과 달리 실행 파일의 32비트 버전이 없습니다 (https://medium.com/@boutnaru/the-windows-process-journey-cmd-exe-windows-command-processor-501be17ba81b). 또한, "sppsvc.exe" 이진 파일은 Microsoft에 의해 디지털 서명되었습니다.

전반적으로 "sppsvc.exe"는 "Software Protection" 서비스 (aka sppsvc)의 주요 이미지입니다. 서비스 설명에는 다음과 같이 명시되어 있습니다: "Windows 및 Windows 애플리케이션의 디지털 라이선스 다운로드, 설치 및 강제 적용을 가능하게 합니다. 서비스가 비활성화되면 운영 체제 및 라이센스가 부여된 애플리케이션이 알림 모드에서 실행될 수 있습니다. 소프트웨어 보호 서비스를 비활성화하지 않는 것이 강력히 권장되며”. 이 서비스는 "Network Service" (https://medium.com/@boutnaru/the-windows-security-jorueny-network-service-nt-authority-network-service-e8706688e383) 사용자의 권한/권한으로 실행됩니다 — 아래 스크린 샷에서 확인할 수 있습니다.

따라서, "sppsvc.exe"가 다음과 같은 기능을 수행한다고 말할 수 있습니다. Windows 운영 체제가 정품이고 제대로 활성화되었는지를 보장합니다. 주기적으로 Windows 라이선스가 여전히 유효한지 (그리고 취소되지 않았는지) 확인합니다. 또한, 새 Windows 사본을 설치하거나 컴퓨터에 중요한 하드웨어 변경을 수행할 때 활성화 프로세스를 처리합니다. 또한, 시스템의 활성 상태에 대한 익명 데이터를 Microsoft에 수집하고 전송할 수도 있다는 것을 알아야 합니다 (https://malwaretips.com/blogs/microsoft-software-protection-platform-service/).

마지막으로, 활성화 토큰을 보관하는 "“%windir%\System32\spp\” 디렉토리가 있습니다 (https://community.spiceworks.com/t/windows-10-repeatedly-deactivates/681310). 이 디렉토리에서 파일을 백업하여 Office와 같은 다른 소프트웨어 제공을 다시 활성화할 수 있습니다 (https://community.citrix.com/forums/topic/230472-layered-image-office-2016-will-not-activate-on-first-boot/).

<div class="content-ad"></div>

다음 글에서 뵙겠습니다 ;-) 트위터에서 저를 팔로우할 수 있어요 — @boutnaru (https://twitter.com/boutnaru). 또한, 저의 다른 글들은 미디엄에서 읽을 수 있어요 — https://medium.com/@boutnaru. 무료 eBook은 https://TheLearningJourneyEbooks.com에서 찾을 수 있어요.

![image](/assets/img/2024-05-23-TheWindowsProcessJourneysppsvcexeMicrosoftSoftwareProtectionPlatformService_0.png)