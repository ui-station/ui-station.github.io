---
title: "라즈베리 파이 5에서 VSCode 서버 다시 시도하기"
description: ""
coverImage: "/assets/img/2024-05-18-TryingtheVSCodeServeragainontheRaspberryPI5_0.png"
date: 2024-05-18 19:13
ogImage:
  url: /assets/img/2024-05-18-TryingtheVSCodeServeragainontheRaspberryPI5_0.png
tag: Tech
originalTitle: "Trying the VSCode Server again on the Raspberry PI 5"
link: "https://medium.com/@raduzaharia/trying-the-vscode-server-again-on-the-raspberry-pi-5-9641e02507f4"
---

<img src="/assets/img/2024-05-18-TryingtheVSCodeServeragainontheRaspberryPI5_0.png" />

지난 번에 우리가 이에 대해 이야기했을 때는 Raspberry PI 5가 없었고, VSCode Server의 버전은 4.0.2였습니다. 전체 경험은 성공적이지 못했고, 실제 작업에 적합하지 않았습니다. VSCode가 느리게 실행되었고, 빌드 시간도 더욱 더 걸렸습니다. 특히 러스트와 같은 언어에 대해서는 그러했습니다. 따라서 우리는 더 나은 하드웨어를 기다리는 실험을 종료했습니다. 운좋게도, 오늘 기다리던 하드웨어가 마침내 출시되었습니다: Raspberry PI 5.

Raspberry PI 4보다 최대 세 배 빠른 속도로 벤치마킹된 새로운 Raspberry PI는 개인용 코딩 및 빌딩 워크스테이션으로 강력한 경쟁자가 되리라 약속합니다. 이에 더해 더 많은 RAM, 더 높은 I/O 대역폭 및 더 나은 GPU를 제공하여 모든 것이 성공을 향해 나아가는 것으로 보입니다. 우리는 마침내 우리의 홈 VSCode Server를 가질 수 있을까요? 알아보겠습니다!

## VSCode Server 설치 및 구성하기

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

![Trying the VSCode Server again on the Raspberry Pi](/assets/img/2024-05-18-TryingtheVSCodeServeragainontheRaspberryPI5_1.png)

VSCode Server도 업데이트되었습니다. 현재 버전 4.20.0에 도달했습니다. 이번에는 지난 시험한 버전보다 많은 개선 사항을 갖춘 VSCode 1.85.1이 실행됩니다. 그래서 이번에는 이전과 마찬가지로 curl을 사용하여 공식 페이지에서 그것을 받아봅시다. 이번에는 여러분도 이미 알다시피 Fedora가 아직 Raspberry PI 5를 지원하지 않는 커널을 사용하고 있기 때문에 Ubuntu를 위한 Debian 패키지를 사용하겠습니다:

```bash
#curl -fOL https://github.com/coder/code-server/releases/download/v4.20.0/code-server_4.20.0_arm64.deb
#sudo apt install ./code-server_4.20.0_arm64.deb
```

그럼 시작해봅시다!

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

<img src="/assets/img/2024-05-18-TryingtheVSCodeServeragainontheRaspberryPI5_2.png" />

설치 후에 우리가 해야 할 일은 잘 알려진 아래 명령어를 실행하여 VSCode 서버를 활성화하는 것뿐입니다:

```js
#sudo systemctl start code-server@ubuntu
#sudo systemctl enable code-server@ubuntu
```

@ubuntu 부분은 서버를 실행할 사용자를 가리킵니다. 이 경우에는 ubuntu인데요, 라즈베리 파이 서버 사용자를 반영하도록 변경해도 상관 없습니다. 서버는 이제 기본 포트 8080에서 실행되지만, ~/config/code-server/config.yaml 파일을 편집하여 해당 포트 및 다른 설정을 변경할 수 있습니다. 예를 들어, 저는 포트를 변경하고 로그인 암호를 제거했습니다:

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

아래에서 볼 수 있듯이, 우리는 IP 주소를 변경하여 네트워크의 모든 클라이언트에서 10000번 포트로의 연결을 허용하도록 했습니다. 또한 인증을 비활성화하여 기본 비밀번호에서 변경했습니다. 비밀번호를 추가하려면 다음을 사용합니다:

```js
bind-addr: 0.0.0.0:10000
auth: password
password: password-hash
cert: false
```

비밀번호 해시는 mkpasswd를 사용해서 얻을 수 있습니다. 비밀번호를 입력하라고 요청하며, 해시를 복사하여 위의 비밀번호 필드에 붙여넣습니다. 이것이 모든 구성 단계입니다. 이제 브라우저로 넘어가봅시다!

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

## 브라우저에서 Visual Studio Code 실행하기

![이미지](/assets/img/2024-05-18-TryingtheVSCodeServeragainontheRaspberryPI5_4.png)

위의 스크린샷에서 볼 수 있듯이, 우리가 해야 할 일은 네트워크 내의 어떤 브라우저에서라도 Raspberry PI에 있는 IP 주소로 접근하고, VSCode Server가 실행 중인 올바른 포트인 경우(우리의 경우 10000)를 제공해주면 됩니다. 이제 즉시 브라우저 창을 새로고침하면, Raspberry PI 4에서 VSCode가 훨씬 빠르게로드됩니다. 심지어 이미 LDAP 서버를 포함한 많은 네트워크 서비스가 실행 중이지만요. 터미널을 열고 rust를 설치해봅시다:

![이미지](/assets/img/2024-05-18-TryingtheVSCodeServeragainontheRaspberryPI5_5.png)

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

위의 스크린샷에 나오는 명령어는 rustup을 설치하는 기본 명령어입니다: curl --proto `=https` --tlsv1.2 -sSf https://sh.rustup.rs | sh. 이 명령어는 라즈베리 파이 5 VSCode 서버에 rust를 설정해줍니다:

![라즈베리 파이 5 VSCode 서버 재시도](/assets/img/2024-05-18-TryingtheVSCodeServeragainontheRaspberryPI5_6.png)

라즈베리 파이 5의 와이파이 속도가 향상된 것을 알립니다. Rust를 다운로드하는 과정이 로컬 머신에서 실행하는 것과 더 닮았습니다. 그럼에도 불구하고, 설치 과정은 기대보다 느립니다. 물론, 제 최신 세대 인텔 i7 데스크탑과 비교하면 공평하지 않지만, 이 작은 라즈베리 파이 5도 아직 데스크톱 속도에 도달하기 위해 많은 것을 해야 한다는 것을 보여줍니다. 그럼에도 불구하고, 경험은 실제로 라즈베리 파이보다 빨랐습니다. 3배 빠른가요? 정말 그렇지는 않았지만, 라즈베리 파이 4에서 약 10분 정도 걸릴 작업이 라즈베리 파이 5에서는 약 4분 정도 소요되었습니다. 확실한 향상이었습니다.

하지만, 일반적이고 작은 웹 서버와 같은 몇 가지 종속성을 사용하여 rust 프로젝트를 작성하고 빌드하는 작업을 해보죠. 그러기 위해 우리는 projects라는 새 폴더를 만들어서 브라우저에서 VSCode를 열고 터미널을 실행하여 cargo new web-test --bin을 실행하세요:

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

<table>
  <tr>
    <td><img src="/assets/img/2024-05-18-TryingtheVSCodeServeragainontheRaspberryPI5_7.png" /></td>
  </tr>
</table>

이제 해당 폴더를 열어 봅시다:

<table>
  <tr>
    <td><img src="/assets/img/2024-05-18-TryingtheVSCodeServeragainontheRaspberryPI5_8.png" /></td>
  </tr>
</table>

프로젝트를 컴파일해 보겠습니다. 먼저 rust-analyzer 확장 프로그램을 설치하여 VSCode에서 rust 언어를 완전히 지원받을 수 있습니다.

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

![이미지](/assets/img/2024-05-18-TryingtheVSCodeServeragainontheRaspberryPI5_9.png)

LLDB 디버거도 설치할 겁니다. 이를 통해 러스트 프로그램을 디버깅할 수 있어요:

![이미지](/assets/img/2024-05-18-TryingtheVSCodeServeragainontheRaspberryPI5_10.png)

사실, Raspberry PI 4에 비해 VSCode 익스텐션 설치가 훨씬 빠른 것 같아요. 그것들은 로컬 데스크톱에서 설치하는 것과 똑같아요. 전체 경험은 로컬에서 VSCode를 실행하는 것 같아요. 다시 F5를 누르면 다음으로 linker cc를 찾을 수 없다는 에러가 나올 거에요. 그래서 sudo apt install build-essential을 사용해서 build-essential 패키지를 설치해볼까요:

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

![다운로드 속도가 빠르고 설치 속도는 조금 느립니다. 하지만 라즈베리 파이 5는 여전히 1분 이내에 모든 것을 설치하는 데 성공합니다. 이번에는 F5를 누르면 정말로 프로젝트를 컴파일하고 디버그합니다. 마침내. 러스트 서버를 실행해 봅시다!](/assets/img/2024-05-18-TryingtheVSCodeServeragainontheRaspberryPI5_11.png)

## 작은 러스트 웹 서버 만들기

![다운로드 속도가 빠르고 설치 속도는 조금 느립니다. 하지만 라즈베리 파이 5는 여전히 1분 이내에 모든 것을 설치하는 데 성공합니다. 이번에는 F5를 누르면 정말로 프로젝트를 컴파일하고 디버그합니다. 마침내. 러스트 서버를 실행해 봅시다!](/assets/img/2024-05-18-TryingtheVSCodeServeragainontheRaspberryPI5_12.png)

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

지금까지 우리는 안정적으로 1%에서 3%의 CPU 사용량과 겨우 1.2GB의 RAM 사용량을 유지하고 있어요. 이라고 느껴질 수 있지만, 라즈베리 파이 5는 8GB의 사용 가능한 RAM을 가지고 있어요. 라즈베리 파이 4에서는 이미 이것이 고난일이었죠:

![이미지1](/assets/img/2024-05-18-TryingtheVSCodeServeragainontheRaspberryPI5_13.png)

그럼, 정적 파일을 제공하는 간단한 웹 서버를 준비해뒀어요:

![이미지2](/assets/img/2024-05-18-TryingtheVSCodeServeragainontheRaspberryPI5_14.png)

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

rust-analyzer가 작업을 수행하는 동안 actix 종속성을 다운로드하고 컴파일하며 소스 코드를 색인화하는 과정에서 4코어 ARM CPU에서 발생하는 전형적인 노동의 결과를 확인할 수 있습니다. 하지만 이는 라즈베리 파이 5가 쉽게 처리할 수 없는 것은 없습니다. 웹 경험은 여전히 부드럽고 자동 완성은 여전히 즉각적으로 응답합니다:

![이미지1](/assets/img/2024-05-18-TryingtheVSCodeServeragainontheRaspberryPI5_15.png)

또한 전형적인 index.html 파일을 준비했습니다:

![이미지2](/assets/img/2024-05-18-TryingtheVSCodeServeragainontheRaspberryPI5_16.png)

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

이제 웹 서버를 구축하고 실행해 보려고 합니다. 우리 손가락을 교차하고 cargo build를 실행해 보세요. actix web 라이브러리는 이미 인상적인 수의 종속성을 필요로 하지만, Raspberry PI 5는 이 모든 것을 빠르게 처리합니다:

![이미지](/assets/img/2024-05-18-TryingtheVSCodeServeragainontheRaspberryPI5_17.png)

지금 세 분은 꽤 오랜 시간으로 느껴질 수 있지만, 말해 드릴게요. 저는 웹 프로젝트를 만들 때 Raspberry PI 4에서 러스트로 빌드했을 때 쉽게 10분씩 썼습니다. 저에게는 이미 이는 압도적인 승리입니다. 더 구체적인 비교를 해보면, 저의 평균 AMD Ryzen 5 3000 노트북은 이 일을 완료하는 데 약 한 시간 반 정도 걸리는 반면, 동시에 많은 일을 실행 중이기는 하지만요. 하지만, 합당하게 말해서, Raspberry PI 5는 상황을 감안할 때 충분히 좋은 일을 하고 있습니다. 그리고 Raspberry PI 4보다 훨씬 더 훌륭한 일을 합니다.

정말 인상적입니다. Raspberry PI 4는 홈 네트워크 코딩 서버로 사용하기에 적합하지 않았고, 단 몇 분만 지나도 Raspberry PI 5가 이 상황을 어떻게 처리하는지 완전히 만족스럽다고 할 수 있습니다. 대규모 자원을 사용하는 복잡한 빌드 프로세스로 알려진 러스트 프로그램을 빌드하고 실행하는 것은 매우 쉽고 로컬에서 실행하는 것 같은 느낌이 듭니다. 이게 최고의 칭찬이에요. Raspberry PI 4에서 서버가 제한적이고 중단되는 느낌이 들었던 것과는 달리, 이번에는 서버로 인해 제한받거나 방해받는 느낌이 들지 않습니다. 이 실험을 성공으로 인정하고 계속하여 실험을 진행하기 위해 VSCode Server 구동 상태로 두겠습니다.

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

이것은 대형 태블릿을 사용하여 코딩 및 이전에 거부되었던 가정 네트워크의 다른 리소스를 활용할 수 있는 훌륭한 시나리오를 열어줍니다. 이 멋진 여정을 함께 해 줘서 감사하고, 다음에 또 만나요!
