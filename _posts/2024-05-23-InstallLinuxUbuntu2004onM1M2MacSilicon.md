---
title: "M1 M2 Mac 실리콘에 LinuxUbuntu 2004 설치하기"
description: ""
coverImage: "/assets/img/2024-05-23-InstallLinuxUbuntu2004onM1M2MacSilicon_0.png"
date: 2024-05-23 17:09
ogImage:
  url: /assets/img/2024-05-23-InstallLinuxUbuntu2004onM1M2MacSilicon_0.png
tag: Tech
originalTitle: "Install Linux (Ubuntu 20.04) on M1 M2 Mac Silicon"
link: "https://medium.com/@shubhjain10102003/install-linux-ubuntu-20-04-on-m1-m2-mac-silicon-de1992d5fa26"
---

<img src="/assets/img/2024-05-23-InstallLinuxUbuntu2004onM1M2MacSilicon_0.png" />

안녕하세요, 로봇 공학 애호가 및 리눅스 사용자 여러분! 여러분께서는 강력한 M1 또는 M2 맥을 소유하셨지만 리눅스에서만 실행되는 프로젝트를 실행하고 싶으시죠? 다행히도 가상화 기술을 활용하여 M1/M2 맥에서 리눅스를 실행할 수 있습니다. 이 안내서에서는 UTM 가상 머신을 사용하여 M1/M2 맥에 리눅스를 설정하는 방법을 살펴보겠습니다.

UTM이란 무엇인가요?

UTM은 "Universal Terminal Machine"의 약자로, macOS 및 iOS용으로 설계된 오픈 소스 가상화 도구입니다. 이 도구를 사용하면 M1 및 M2 맥을 비롯한 Apple Silicon 기기에서 가상 머신을 실행할 수 있습니다. UTM을 사용하면 리눅스 배포본, Windows 등 다양한 운영 체제를 에뮬레이트할 수 있습니다. Mac 용 UTM 앱은 공식 UTM 다운로드 링크에서 다운로드할 수 있습니다.

<!-- ui-station 사각형 -->

<ins class="adsbygoogle"
style="display:block"
data-ad-client="ca-pub-4877378276818686"
data-ad-slot="7249294152"
data-ad-format="auto"
data-full-width-responsive="true"></ins>

<script>
(adsbygoogle = window.adsbygoogle || []).push({});
</script>

M1/M2 Mac에 Linux 설치하기:

UTM을 이용하여 Linux를 설치하는 방법에 대한 단계별 안내서입니다. 모든 단계에 대한 사진을 포함하려고 해서 길어 보이지만 이 안내서를 따라 M1/M2 Mac에 Linux를 설치할 수 있습니다:

- UTM 다운로드 및 설치:

- 위의 링크를 통해 UTM 앱을 다운로드하거나 Google에서 'Mac용 UTM'을 검색하여 다운로드할 수 있습니다.
- 다운로드 후 DMG 파일을 열고 UTM 애플리케이션을 드래그하여 설치하기 위해 Applications 폴더로 이동하세요.

<!-- ui-station 사각형 -->

<ins class="adsbygoogle"
style="display:block"
data-ad-client="ca-pub-4877378276818686"
data-ad-slot="7249294152"
data-ad-format="auto"
data-full-width-responsive="true"></ins>

<script>
(adsbygoogle = window.adsbygoogle || []).push({});
</script>

2. 리눅스 디스크 이미지 얻기:

- 설치하려는 리눅스 배포판을 선택합니다. 인기있는 선택지로는 우분투, 페도라, 데비안 등이 있습니다. 이 튜토리얼에서는 우분투 20.04를 설치할 것입니다.
- 우분투 20.04 데스크톱 버전이 ARM 64용으로 공식 웹사이트에서 제거되었기 때문에, 우분투 20.04 데스크톱을 설치하기 위해 우회 방법을 사용할 것입니다.
- 여기서 우분투 20.04 (ARM 64 버전) 서버 디스크 이미지를 다운로드합니다. 이 이미지는 서버 이미지이므로 터미널 환경만 제공됩니다. 다음 단계에서 전체 데스크톱 버전을 설치할 것입니다.

3. UTM에서 새 가상 머신 생성하기:

- 응용 프로그램 폴더에서 UTM을 엽니다.
- 새 가상 머신을 생성하려면 “+” 버튼을 클릭합니다.

<!-- ui-station 사각형 -->

<ins class="adsbygoogle"
style="display:block"
data-ad-client="ca-pub-4877378276818686"
data-ad-slot="7249294152"
data-ad-format="auto"
data-full-width-responsive="true"></ins>

<script>
(adsbygoogle = window.adsbygoogle || []).push({});
</script>

<img src="/assets/img/2024-05-23-InstallLinuxUbuntu2004onM1M2MacSilicon_1.png" />

- "Virtualize" 옵션을 선택하세요. "Virtualize"와 "Emulate"의 차이점은 Virtualize는 Apple 실리콘, 즉 ARM 64 칩셋을 네이티브로 지원하는 기계를 가상화한다는 것입니다. 그래서 빠릅니다. 반면에 Emulate는 필요한 CPU 아키텍처를 가상화하여 다른 CPU 아키텍처를 실행할 수 있습니다. (진실을 말하면, Emulate를 시도해 봤는데, 너무 느려요. 만약 단순한 명령줄만 실행되는 매우 가벼운 OS가 아니라면 절대 Emulate 옵션 선택하지 말기를 권장합니다.)

<img src="/assets/img/2024-05-23-InstallLinuxUbuntu2004onM1M2MacSilicon_2.png" />

- 당연히 "Linux"를 선택하세요.

<!-- ui-station 사각형 -->

<ins class="adsbygoogle"
style="display:block"
data-ad-client="ca-pub-4877378276818686"
data-ad-slot="7249294152"
data-ad-format="auto"
data-full-width-responsive="true"></ins>

<script>
(adsbygoogle = window.adsbygoogle || []).push({});
</script>

![Install Linux Ubuntu 20.04 on M1/M2 Mac Silicon Step 3](/assets/img/2024-05-23-InstallLinuxUbuntu2004onM1M2MacSilicon_3.png)

- ISO 이미지에서 부팅을 선택하고 Step 2에서 다운로드한 서버 이미지를 찾아 선택하세요.

![Install Linux Ubuntu 20.04 on M1/M2 Mac Silicon Step 4](/assets/img/2024-05-23-InstallLinuxUbuntu2004onM1M2MacSilicon_4.png)

- 이제 가상 머신에 필요한 자원을 할당하세요. 최상의 성능을 위해 시스템 자원의 절반을 가상 머신에 할당하는 것을 강력히 권장합니다. 가상 OS에서 작업하는 동안 다른 응용 프로그램을 실행하지 않고 자원을 절약하고 성능을 향상시키기 위해 노력해주세요. 그렇지 않으면 시스템이 다운될 수 있습니다.

<!-- ui-station 사각형 -->

<ins class="adsbygoogle"
style="display:block"
data-ad-client="ca-pub-4877378276818686"
data-ad-slot="7249294152"
data-ad-format="auto"
data-full-width-responsive="true"></ins>

<script>
(adsbygoogle = window.adsbygoogle || []).push({});
</script>

![Image 5](/assets/img/2024-05-23-InstallLinuxUbuntu2004onM1M2MacSilicon_5.png)

![Image 6](/assets/img/2024-05-23-InstallLinuxUbuntu2004onM1M2MacSilicon_6.png)

![Image 7](/assets/img/2024-05-23-InstallLinuxUbuntu2004onM1M2MacSilicon_7.png)

![Image 8](/assets/img/2024-05-23-InstallLinuxUbuntu2004onM1M2MacSilicon_8.png)

<!-- ui-station 사각형 -->

<ins class="adsbygoogle"
style="display:block"
data-ad-client="ca-pub-4877378276818686"
data-ad-slot="7249294152"
data-ad-format="auto"
data-full-width-responsive="true"></ins>

<script>
(adsbygoogle = window.adsbygoogle || []).push({});
</script>

4. 가상 머신 시작하기:

- 구성이 완료되면 "만들기"를 클릭하여 가상 머신을 생성합니다.
- 목록에서 새로 생성된 가상 머신을 선택하고 "시작"을 클릭하여 실행합니다.

5. Linux 설치하기:

- 가상 머신은 Linux ISO 이미지에서 부팅됩니다.
- 화면 안내에 따라 가상 디스크에 Linux를 설치합니다.

<!-- ui-station 사각형 -->

<ins class="adsbygoogle"
style="display:block"
data-ad-client="ca-pub-4877378276818686"
data-ad-slot="7249294152"
data-ad-format="auto"
data-full-width-responsive="true"></ins>

<script>
(adsbygoogle = window.adsbygoogle || []).push({});
</script>

![Screenshot 9](/assets/img/2024-05-23-InstallLinuxUbuntu2004onM1M2MacSilicon_9.png)

![Screenshot 10](/assets/img/2024-05-23-InstallLinuxUbuntu2004onM1M2MacSilicon_10.png)

![Screenshot 11](/assets/img/2024-05-23-InstallLinuxUbuntu2004onM1M2MacSilicon_11.png)

Select the keyboard configuration and the network connection configuration.

<!-- ui-station 사각형 -->

<ins class="adsbygoogle"
style="display:block"
data-ad-client="ca-pub-4877378276818686"
data-ad-slot="7249294152"
data-ad-format="auto"
data-full-width-responsive="true"></ins>

<script>
(adsbygoogle = window.adsbygoogle || []).push({});
</script>

![Screenshot 1](/assets/img/2024-05-23-InstallLinuxUbuntu2004onM1M2MacSilicon_12.png)

만약 외부 세계 "content😗"에 액세스하기 위해 어떤 HTTP 프록시도 사용하고 싶지 않다면, 프록시 주소 필드를 비워 두십시오.

다음 단계에서는 미러 주소를 기본 설정으로 유지하고 "완료"를 클릭하세요.
다음 단계에서는 전체 디스크 옵션을 선택해 주세요.

![Screenshot 2](/assets/img/2024-05-23-InstallLinuxUbuntu2004onM1M2MacSilicon_13.png)

<!-- ui-station 사각형 -->

<ins class="adsbygoogle"
style="display:block"
data-ad-client="ca-pub-4877378276818686"
data-ad-slot="7249294152"
data-ad-format="auto"
data-full-width-responsive="true"></ins>

<script>
(adsbygoogle = window.adsbygoogle || []).push({});
</script>

<img src="/assets/img/2024-05-23-InstallLinuxUbuntu2004onM1M2MacSilicon_14.png" />

파일 시스템은 주어진 그대로 선택하고 계속 진행하세요.

이어진 단계에서 우분투 계정 프로필을 만들어 시스템에 로그인합니다.

<img src="/assets/img/2024-05-23-InstallLinuxUbuntu2004onM1M2MacSilicon_15.png" />

원한다면 다음 단계에서 오픈 SSH 서버를 사용할 수 있습니다!

다음 창에서 프로젝트나 업무에 유용하거나 사용할 서버 스냅을 선택하세요. 원한다면 나중에도 설치 가능합니다!!

<!-- ui-station 사각형 -->

<ins class="adsbygoogle"
style="display:block"
data-ad-client="ca-pub-4877378276818686"
data-ad-slot="7249294152"
data-ad-format="auto"
data-full-width-responsive="true"></ins>

<script>
(adsbygoogle = window.adsbygoogle || []).push({});
</script>

![Image 1](/assets/img/2024-05-23-InstallLinuxUbuntu2004onM1M2MacSilicon_16.png)

이제 설치가 시작됩니다 - →

![Image 2](/assets/img/2024-05-23-InstallLinuxUbuntu2004onM1M2MacSilicon_17.png)

완료될 때까지 기다리세요. 완료되면 기계를 다시 부팅하라는 메시지가 표시됩니다.

<!-- ui-station 사각형 -->

<ins class="adsbygoogle"
style="display:block"
data-ad-client="ca-pub-4877378276818686"
data-ad-slot="7249294152"
data-ad-format="auto"
data-full-width-responsive="true"></ins>

<script>
(adsbygoogle = window.adsbygoogle || []).push({});
</script>

기계를 다시 시작한 후 설치된 미디어, 즉 VM 설정에서 iso 파일을 제거하고 기계를 다시 시작하세요.

<img src="/assets/img/2024-05-23-InstallLinuxUbuntu2004onM1M2MacSilicon_18.png" />

이제 Ubuntu 서버에 로그인하고 좋아하는 데스크톱 환경을 설치해 봅시다.

6. 데스크톱 환경 설치:

<!-- ui-station 사각형 -->

<ins class="adsbygoogle"
style="display:block"
data-ad-client="ca-pub-4877378276818686"
data-ad-slot="7249294152"
data-ad-format="auto"
data-full-width-responsive="true"></ins>

<script>
(adsbygoogle = window.adsbygoogle || []).push({});
</script>

- 우분투 데스크톱 시스템(gnome 3)을 설치하려면 다음을 실행하세요:
  $ sudo apt install ubuntu-desktop
  또는 Gnome 3 데스크톱을 위한 전환 패키지 이름을 사용해보세요:
  $ sudo apt install ubuntu-gnome-desktop
  Kubuntu Plasma 데스크톱/넷북 시스템(KDE)을 설치하려면 다음을 실행하세요 (가벼우면서 부드러운 터치를 선호합니다) :
  $ sudo apt install kubuntu-desktop
  Lubuntu 데스크톱 환경을 원하시나요? 다음을 실행하세요:
  $ sudo apt install lubuntu-desktop
  Xubuntu 데스크톱 시스템을 설치하려면 다음을 실행하세요:
  $ sudo apt install xubuntu-desktop

- 데스크톱 설치 과정이 완료되면 시스템을 한 번 더 재부팅하면 됩니다. 그 후, 와우! 새로운 깨끗하고 새로운 우분투 데스크톱에 로그인하게 될 것입니다.

![이미지](/assets/img/2024-05-23-InstallLinuxUbuntu2004onM1M2MacSilicon_19.png)

와! 이제 우분투 20.04의 완전한 데스크톱 버전을 성공적으로 설치했습니다. 이제 선호하는 우분투 패키지와 소프트웨어를 설치하고 작업에 원활히 참여할 준비가 모두 되었습니다.
이 설명서가 맥 실리콘을 위한 우분투 20.04 설치에 도움이 되었길 바랍니다.

만약 설치 중 궁금한 점이나 오류가 발생하면 shubhjain10102003@gmail.com으로 이메일을 보내주시면 감사하겠습니다! 😇.
