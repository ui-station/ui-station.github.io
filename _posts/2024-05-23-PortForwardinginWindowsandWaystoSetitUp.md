---
title: "윈도우에서의 포트 포워딩 및 설정 방법들"
description: ""
coverImage: "/assets/img/2024-05-23-PortForwardinginWindowsandWaystoSetitUp_0.png"
date: 2024-05-23 15:26
ogImage:
  url: /assets/img/2024-05-23-PortForwardinginWindowsandWaystoSetitUp_0.png
tag: Tech
originalTitle: "Port Forwarding in Windows and Ways to Set it Up"
link: "https://medium.com/@redfishiaven/port-forwarding-in-windows-and-ways-to-set-it-up-c337e171086f"
---

Windows에서 원격 액세스 또는 서버 호스팅을 위해 포트 포워딩 설정하는 방법을 배워보세요. 지금 당신의 장치에서 이를 활성화하고 구성하는 방법에 대한 가이드를 따르세요.

![포트 포워딩 이미지](/assets/img/2024-05-23-PortForwardinginWindowsandWaystoSetitUp_0.png)

본문에서 읽을 내용 목록:

1. Windows에서의 포트 포워딩이란?
2. 포트 포워딩이 작동하는 방식은?
3. 포트 포워딩에 대한 명령 프롬프트 사용
4. Windows 방화벽을 위한 포트 포워딩 구성
5. Hyper-V 가상 스위치에서 NAT 규칙을 사용한 포트 포워딩
6. Windows에서 Netsh 포트 포워딩 규칙 관리
7. 결론

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

포트 포워딩은 네트워크의 보안 및 기능성을 제고하는 데 사용되는 기본적인 기술 중 하나입니다. 이는 네트워크 라우터를 구성하여 특정 포트로부터 오는 들어오는 트래픽을 네트워크 내의 지정된 장치로 전달하는 과정을 말합니다. Windows 환경에서 포트 포워딩을 설정하는 것은 기술적 지식이 제한된 사람들에게는 도전적일 수 있습니다. 하지만 올바른 지식과 도구를 활용하면 누구나 빠르게 포트 포워딩을 설정할 수 있습니다. 이 가이드에서는 Windows에서 포트 포워딩을 설정하는 단계와 그에 필요한 도구 및 기술에 대해 살펴보겠습니다.

# Windows에서 포트 포워딩이란?

포트 포워딩은 컴퓨터 및 기타 네트워크 장치가 인터넷과 통신할 수 있게 해주는 필수적인 네트워킹 기술입니다. 이는 로컬 네트워크 외부에서 인터넷에 접근해야 하는 장치, 서비스 또는 프로그램에 필요한 과정입니다. 포트 포워딩은 네트워크 외부의 특정 포트에서 네트워크 내의 특정 포트로 네트워크 트래픽을 라우팅함으로써 작동합니다. 기본적으로, 네트워크 라우터는 인터넷으로부터 들어오는 트래픽을 차단하도록 설계되어 있어 서버, 게임 콘솔 및 기타 네트워크 장치에서 문제가 발생할 수 있습니다.

사용자가 네트워크의 장치에 연결을 시작하면 라우터가 요청을 받아 해당 장치로 전달합니다. 그러나 포트 포워딩이 없으면 라우터는 어떤 장치가 트래픽을 받아야 하는지 알 수 없어 연결이 실패합니다. 따라서 포트 포워딩은 특정 서비스, 장치 및 애플리케이션이 올바르게 작동하고 로컬 네트워크 외부의 인터넷에 접근할 수 있도록 하는 데 중요합니다.

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

# 포트 포워딩은 어떻게 작동하나요?

포트 포워딩은 네트워크 외부의 특정 포트로부터 들어오는 트래픽을 네트워크 내의 특정 장치나 서비스로 리디렉션하는 방식으로 작동합니다. 이 과정은 라우터를 구성하여 네트워크 내의 특정 장치나 서비스로 특정 포트를 포워딩하는 것을 포함합니다. 외부 네트워크에서 연결 요청이 발생하면, 라우터가 해당 요청을 수신하고 요청에서 사용된 특정 포트 번호를 확인합니다. 그런 다음, 라우터는 포트 포워딩 규칙을 확인하여 요청을 수신할 장치나 서비스를 결정합니다. 규칙이 발견되면, 라우터는 포트 포워딩 구성에 따라 들어오는 트래픽을 지정된 장치나 서비스로 전달합니다.

포트 포워딩은 복잡할 수 있지만, 적절한 지식과 도구를 활용하면 빠르고 쉽게 수행할 수 있습니다. 라우터에서 포트 포워딩을 구성하려면 라우터의 구성 인터페이스에 대한 지식, 각 서비스에 필요한 특정 포트 번호, 그리고 인터넷을 통해 접근해야 하는 네트워크 내 장치의 IP 주소에 대한 지식이 필요합니다.

# 명령 프롬프트를 사용한 포트 포워딩

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

윈도우에서 포트 포워딩을 위해 명령 프롬프트를 사용하는 방법에 대한 단계별 안내서입니다:

## 단계 1: 명령 프롬프트 열기

시작 메뉴를 클릭하고 검색 필드에 “cmd”를 입력한 후 Enter 키를 눌러주세요. 그러면 명령 프롬프트 창이 열립니다.

## 단계 2: 장치의 IP 주소 얻기

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

명령 프롬프트 창에 "ipconfig"을 입력하고 Enter 키를 누르세요. 네트워크 어댑터 아래의 "IPv4 주소"를 찾아 IP 주소를 메모해 두세요.

## 단계 3: 포트 포워딩 규칙 생성

다음 명령을 입력하고 Enter 키를 누르세요:

netsh interface portproxy add v4tov4 listenport=8080 listenaddress=192.168.1.10 connectport=8080 connectaddress=192.168.1.10

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

## 단계 4: 포트 포워딩 규칙 확인하기

다음 명령을 입력하고 Enter 키를 눌러주세요:

netsh interface portproxy show all

이 명령은 현재 네트워크에서 활성화된 모든 포트 포워딩 규칙의 목록을 표시합니다. 만들었던 규칙이 나열되어 있는지 확인해보세요.

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

## 단계 5: 명령 프롬프트 닫기

"exit"을 입력하고 Enter 키를 눌러 명령 프롬프트 창을 닫습니다.

그것이죠! 이제 Windows에서 포트 포워딩을 위해 명령 프롬프트를 성공적으로 사용했습니다.

# 포트 포워딩을 위한 Windows 방화벽 구성

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

다음은 포트 포워딩을 위한 Windows 방화벽 구성 방법에 대한 단계별 가이드입니다:

## 단계 1: Windows 방화벽 설정 열기

시작 메뉴를 클릭하고 검색란에 "방화벽"을 입력한 후 "Windows Defender 방화벽"을 선택합니다.

## 단계 2: "고급 설정"을 클릭하세요

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

치트 시트 태그를 마크다운 형식으로 수정해주세요.

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

"Port"을 선택하고 "다음"을 클릭하세요.

## 단계 5: 포트 구성

구성해야 할 포트 유형을 선택하세요: TCP 또는 UDP. "구체적 로컬 포트" 필드에 전달할 포트 번호를 입력하세요.

## 단계 6: 작업 선택

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

“연결 허용”을 선택하고 “다음”을 클릭하세요.

## 단계 7: 프로필 선택

규칙을 적용할 프로필을 선택하고(Domain, Private, Public) “다음”을 클릭하세요.

## 단계 8: 규칙의 이름 지정 및 저장

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

**단계 9: 규칙 확인**

규칙에 이름을 지정하고 "완료"를 클릭하세요.

# Hyper-V 가상 스위치에서 포트 포워딩을 위한 NAT 규칙 사용하기

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

안녕하세요! Hyper-V Virtual Switch에서 NAT 규칙으로 포트 포워딩하는 방법에 대한 단계별 안내서입니다:

## 단계 1: Hyper-V 매니저 열기

시작 메뉴를 클릭하고 검색 필드에 "Hyper-V 매니저"를 입력한 후 결과에서 "Hyper-V 매니저"를 선택합니다.

## 단계 2: 가상 스위치 생성

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

하이퍼-V 관리자 창의 오른쪽 사이드바에서 "가상 스위치 관리자"를 클릭하세요. "새 가상 네트워크 스위치"를 클릭하고 가상 스위치 유형으로 "내부" 또는 "개인"을 선택하세요.

## 단계 3: 가상 스위치 구성

가상 스위치에 이름을 지어 네트워크 어댑터 설정을 구성하세요. "관리 운영 체제가이 네트워크 어댑터를 공유하도록 허용" 옵션이 선택 해제되어 있는지 확인하세요.

## 단계 4: 새 가상 머신을 만들거나 기존 머신을 선택하세요

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

하이퍼-V 관리자 창에서 가상 머신을 마우스 오른쪽 단추로 클릭하고 "설정"을 선택하세요.

## 단계 5: 네트워크 어댑터 추가

"Add Hardware"를 클릭하고 "네트워크 어댑터"를 선택하여 가상 머신에 네트워크 어댑터를 추가하세요.

## 단계 6: 가상 스위치에 연결

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

### 단계 7: NAT 서브 스위치 활성화

Hyper-V 관리자 창에서 단계 2에서 생성한 가상 스위치를 마우스 오른쪽 버튼으로 클릭하고 "속성"을 선택합니다. "NAT 활성화" 옵션을 선택하고 "OK"를 클릭합니다.

### 단계 8: NAT 규칙 생성

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

가상 머신에서 "Windows 키 + R"을 눌러 명령 프롬프트를 열고, 실행 대화상자에 "cmd"를 입력하여 실행하세요. 다음 명령어를 입력하여 NAT 규칙을 생성하세요:

```plaintext
netsh interface portproxy add v4tov4 listenport=80 listenaddress=0.0.0.0 connectport=8080 connectaddress=192.168.1.10
```

포워딩할 포트 번호로 "80"을, 포트를 전달할 장치의 IP 주소로 "192.168.1.10"을 대체하세요.

## 단계 9: NAT 규칙 확인

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

다음 명령을 입력하여 NAT 규칙을 확인하세요:

netsh interface portproxy show all

모든 NAT 규칙을 나열해줄 것입니다.

# 윈도우에서 Netsh 포트 포워딩 규칙 관리

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

윈도우에서 Netsh 포트 포워딩 규칙을 관리하는 단계별 가이드입니다:

## 단계 1: 명령 프롬프트 열기

시작 메뉴를 클릭하고 검색 필드에 "cmd"를 입력한 다음 "명령 프롬프트"를 선택하세요.

## 단계 2: 기존 포트 포워딩 규칙 확인

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

"netsh interface portproxy show all"을 입력하고 Enter 키를 누르세요. 이렇게 하면 현재 모든 포트 포워딩 규칙 목록이 표시됩니다.

## 단계 3: 포트 포워딩 규칙 추가

새 포트 포워딩 규칙을 추가하려면 다음을 입력하세요:

"netsh interface portproxy add v4tov4 listenport= listenaddress= connectport= connectaddress="

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

## 단계 4: 포트 포워딩 규칙 삭제

기존의 포트 포워딩 규칙을 삭제하려면 다음을 입력하세요:

`netsh interface portproxy delete v4tov4 listenport= listenaddress=`

## 단계 5: 기존의 포트 포워딩 규칙 수정

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

기존 포트 포워딩 규칙을 수정하려면 해당 규칙을 삭제하고 원하는 변경 사항이 적용된 새로운 규칙을 추가하면 됩니다.

## 단계 6: 포트 포워딩 규칙 비활성화

기존 포트 포워딩 규칙을 비활성화하려면 다음을 입력하세요:

“netsh interface portproxy delete v4tov4 listenport= listenaddress=”

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

## 단계 7: 비활성화된 포트 포워딩 규칙 활성화

비활성화된 포트 포워딩 규칙을 활성화하려면 다음을 입력하여 규칙을 다시 추가하세요:

“netsh interface portproxy add v4tov4 listenport= listenaddress= connectport= connectaddress=”

# 결론

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

- 포트 포워딩은 네트워킹 기술로, 방화벽이나 라우터를 통해 공용 네트워크에서 사설 네트워크로 트래픽을 전달하는 것을 가능하게 합니다. 이 기술을 사용하면 가정 네트워크에 원격으로 접속하여 서버를 호스팅할 수 있습니다.
- 포트 포워딩은 공용 IP 주소를 각 사설 네트워크 장치에 할당하여, 방화벽에서 차단되는 대신 특정 장치로 들어오는 트래픽을 직접 전달합니다.
- Windows 운영 체제에는 포트 포워딩 설정을 구성할 수 있는 내장 도구가 포함되어 있습니다. Windows 방화벽과 인터넷 연결 공유(ICS) 기능 등이 이에 해당합니다. 이러한 도구를 사용하면 포트 포워딩 및 수신 트래픽을 받아들일 장치나 응용 프로그램을 명시하는 규칙을 만들 수 있습니다.

#Price를 위한 더 많은 도구가 있습니다

https://t.me/redfishiaven

#업데이트 #튜토리얼 #리아뉴스 #소프트웨어 #하드웨어 #기술 #돈 #수익 #IPMC #사랑 #이벤트 #컴퓨팅 #컴퓨터 #정보기술 #학습 #인공지능 #redfishiaven #서버 #딥웹 #다크웹 #비트코인

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

구글 맵스에서 REDFISH IA VEN (https://goo.gl/maps/LVKkEYNN2LTe9C34A)을 확인해보세요.

https://www.youtube.com/channel/UC6k_cFigPCSEtRyALo1D-tA

새로운 소프트웨어에 대한 최초 정보를 받아보세요! #software
