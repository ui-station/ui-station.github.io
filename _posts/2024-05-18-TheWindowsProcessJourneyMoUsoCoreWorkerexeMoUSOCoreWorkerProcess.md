---
title: "윈도우 프로세스 여정  MoUsoCoreWorkerexe MoUSO 코어 워커 프로세스"
description: ""
coverImage: "/assets/img/2024-05-18-TheWindowsProcessJourneyMoUsoCoreWorkerexeMoUSOCoreWorkerProcess_0.png"
date: 2024-05-18 17:42
ogImage: 
  url: /assets/img/2024-05-18-TheWindowsProcessJourneyMoUsoCoreWorkerexeMoUSOCoreWorkerProcess_0.png
tag: Tech
originalTitle: "The Windows Process Journey — “MoUsoCoreWorker.exe” (MoUSO Core Worker Process)"
link: "https://medium.com/@boutnaru/the-windows-process-journey-mousocoreworker-exe-mouso-core-worker-process-c39934971fbc"
---


“MoUsoCoreWorker.exe”는 Windows 업데이트를 수행하는 책임이 있는 실행 파일입니다. 이 파일은 "wuauclt.exe"에 의해 수행되는 일부 작업을 대체하며, Windows 10/11 환경 업데이트를 지원합니다. 이 파일은 “통합 업데이트 플랫폼” 또는 UUP로 이동함에 따라 이러한 업데이트를 수행합니다.

따라서 Windows 10이 출시되면서 Microsoft는 UUP로 이동하여 모든 유형의 OS 업데이트(월간 및 새로운 기능 업데이트)를 대상으로 하는 클라이언트 장치에 대한 단일 발행, 호스팅, 스캔 및 다운로드를 허용합니다.

또한, 해당 실행 파일은 “%windir%\System32\MoUsoCoreWorker.exe”에 위치해 있습니다. 64비트 시스템에서는 64비트 버전만 있으며 32비트 버전은 없습니다. 이 파일은 “svchost.exe”에 의해 시작되며 “로컬 시스템”의 권한으로 실행됩니다.

그리고, USO는 “Update Session Orchestrator”의 약자입니다. “MoUsoCoreWorker.exe”는 Windows에서 업데이트를 다운로드하고 설치하는 순서를 제어하는 중요한 구성 요소입니다.

<div class="content-ad"></div>

마지막으로, Windows가 업데이트를 찾을 때마다 "MoUsoCoreWorker.exe"가 시작됩니다. 해당 내용은 아래 스크린샷에서 확인할 수 있습니다. 해당 스크린샷은 Sysinternals의 ProcMon을 사용하여 촬영되었고, "업데이트 확인" 버튼을 누른 후에 촬영되었습니다. 우리는 "usoapi.dll"(업데이트 세션 오케스트레이터 API)가 "SystemSettings.exe"에 의해 로드되고, 그 후에 "MoUsoCoreWorker.exe"가 "svchost.exe"에 의해 시작되는 것을 볼 수 있습니다.

다음 글에서 다시 만나요 ;-) 제 트위터 계정을 팔로우할 수 있습니다 - @boutnaru. 또한, 다른 글은 중에서 읽어볼 수 있습니다 - https://medium.com/@boutnaru. 무료 eBook은 https://TheLearningJourneyEbooks.com에서 확인할 수 있습니다.

![이미지](/assets/img/2024-05-18-TheWindowsProcessJourneyMoUsoCoreWorkerexeMoUSOCoreWorkerProcess_0.png)