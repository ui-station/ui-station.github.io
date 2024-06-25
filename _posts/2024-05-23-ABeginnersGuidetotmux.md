---
title: "tmux 초보자를 위한 가이드"
description: ""
coverImage: "/assets/img/2024-05-23-ABeginnersGuidetotmux_0.png"
date: 2024-05-23 15:12
ogImage:
  url: /assets/img/2024-05-23-ABeginnersGuidetotmux_0.png
tag: Tech
originalTitle: "A Beginner’s Guide to tmux"
link: "https://medium.com/pragmatic-programmers/a-beginners-guide-to-tmux-7e6daa5c0154"
---

<img src="/assets/img/2024-05-23-ABeginnersGuidetotmux_0.png" />

<img src="/assets/img/2024-05-23-ABeginnersGuidetotmux_1.png" />

```js
이 기사에서는:
- tmux 설치
- tmux 시작하기
- 기본 tmux 단축키
- 마우스 사용하기
- tmux 구성
- 상태 표시줄 사용자 정의
- 다음 단계
```

Tmux는 터미널 멀티플렉서로, 하나의 터미널에서 여러 "가상 터미널"을 생성할 수 있습니다. 이는 단일 연결로 여러 프로그램을 실행할 때 매우 유용합니다. 특히 SSH를 사용하여 원격으로 컴퓨터에 연결할 때 유용합니다.

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

Tmux은 또한 프로그램을 주요 터미널에서 분리하여 우연한 연결 해제로부터 보호합니다. 현재 터미널에서 tmux를 분리할 수 있으며, 모든 프로그램이 안전하게 백그라운드에서 실행되어 계속됩니다. 나중에는 동일한 또는 다른 터미널에 tmux를 다시 연결할 수 있습니다.

원격 연결의 이점 외에도 tmux의 속도와 유연성으로 인해 로컬 장치에서 여러 터미널을 관리하기 위한 훌륭한 도구로 사용할 수 있습니다. 저는 내 노트북에서 8년 이상 tmux를 사용해왔습니다. 제 생산성을 높이고 도와주는 tmux의 몇 가지 기능은 다음과 같습니다:

- 완전히 사용자 지정 가능한 상태 표시줄
- 여러 창 관리
- 여러 패널로 창 분할
- 자동 레이아웃
- 패널 동기화
- 사용자 정의 tmux 세션 만들기가 가능한 스크립팅 기능

다음은 사용자 지정 tmux 세션의 예시입니다:

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

![tmux](/assets/img/2024-05-23-ABeginnersGuidetotmux_2.png)

Tmux은 Screen에서 발견되는 일부 기능과 동일한 기능을 제공하며 일부 Linux 배포판에서는 더 이상 사용되지 않습니다. Tmux는 Screen보다 더 현대적인 코드 베이스를 갖추었으며 추가적인 사용자 정의 기능을 제공합니다.

이제 몇 가지 tmux의 이점을 알았으니, 설치 및 사용 방법을 보여드리겠습니다.

# tmux 설치

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

티먹스는 Fedora 및 Red Hat Enterprise Linux (RHEL) 표준 저장소에서 RHEL 8부터 사용할 수 있습니다. 다음 명령어로 설치할 수 있어요.

```js
$ sudo dnf -y install tmux
```

다른 많은 리눅스 배포판에서도 사용할 수 있으며, 선호하는 배포판 패키지 관리자를 사용하여 설치할 수 있을 거에요. 다른 운영 체제의 경우, tmux 설치 가이드를 참고하세요.

# 티먹스 시작하기

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

tmux를 사용하려면 터미널에 tmux를 입력하세요. 이 명령은 tmux 서버를 시작하고 기본 세션(번호 0)과 하나의 창을 생성한 후 해당 세션에 연결합니다.

```js
$ tmux
```

<img src="/assets/img/2024-05-23-ABeginnersGuidetotmux_3.png" />

tmux에 연결되었으므로 일반적으로 명령이나 프로그램을 실행할 수 있습니다. 예를 들어, 긴 시간이 걸리는 프로세스를 시뮬레이션하려면:

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

```js
$ c=1

$ while true; do echo "Hello $c"; let c=c+1; sleep 1; done
Hello 1
Hello 2
Hello 3
```

Tmux 세션에서 분리하려면 Ctrl+B를 누르고 D를 누르세요. Tmux는 "접두사" 조합을 눌러 트리거되는 일련의 키 바인딩(키보드 단축키)를 사용하여 작동합니다. 기본적으로 "접두사"는 Ctrl+B입니다. 그 다음에 현재 세션에서 분리하려면 D를 누르세요.

```js
[session 0에서 분리됨]
```

더 이상 세션에 연결되어 있지 않지만 오랫동안 실행되는 명령이 안전하게 백그라운드에서 실행됩니다. 활성 tmux 세션을 tmux ls로 나열할 수 있습니다:

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

```plaintext
$ tmux ls

0: 1개의 창 (2022년 8월 27일 토요일 생성)
```

여기서 SSH 연결을 해제할 수 있고 명령은 계속 실행됩니다. 준비가 되면 서버에 다시 연결하여 기존 tmux 세션에 다시 연결하여 이전에 중단한 곳에서 계속할 수 있습니다:

```plaintext
$ tmux attach -t 0
안녕 72
안녕 73
안녕 74
안녕 75
안녕 76
^C
```

메시지가 계속 출력되는 것을 확인할 수 있습니다. 취소하려면 Ctrl+C를 입력하실 수 있습니다.

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

모든 tmux 명령어는 약어로도 사용할 수 있습니다. 예를 들어, tmux attach 대신에 tmux a를 입력해도 작동합니다.

이 기능 자체만으로도 tmux는 훌륭한 도구입니다. 더욱이 기본 키바인딩을 포함하여 더 많은 기능을 제공합니다.

# 기본 tmux 키바인딩

Tmux는 tmux 세션에서 빠르게 명령어를 실행할 수 있게 해주는 여러 키바인딩을 제공합니다. 가장 유용한 몇 가지를 소개합니다.

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

별도의 tmux 세션을 생성하려면 이미 tmux 세션에 들어가 있지 않은 경우 새로운 tmux 세션을 생성하세요. 새 세션을 만들 때 tmux new 명령을 사용하고 -s '이름' 매개변수를 전달하여 세션의 이름을 지정할 수 있습니다:

```js
$ tmux new -s Session1
```

```js
+=========================+=============================================+
|       Keybinding        |                 Description                 |
+=========================+=============================================+
| Ctrl+B D                | 현재 세션에서 분리하기                       |
+-------------------------+---------------------------------------------+
| Ctrl+B %                | 창을 수평으로 2개 패널로 분할하기            |
+-------------------------+---------------------------------------------+
| Ctrl+B "                | 창을 수직으로 2개 패널로 분할하기            |
+-------------------------+---------------------------------------------+
| Ctrl+B 화살표 키       | 패널 간 이동                               |
|  (왼쪽,오른쪽,위,아래) |                                             |
+-------------------------+---------------------------------------------+
| Ctrl+B X                | 패널 닫기                                  |
+-------------------------+---------------------------------------------+
| Ctrl+B C                | 새 창 만들기                               |
+-------------------------+---------------------------------------------+
| Ctrl+B N (또는 P)       | 다음 창 또는 이전 창으로 이동               |
+-------------------------+---------------------------------------------+
| Ctrl+B 0 (1,2...)       | 특정 번호의 창으로 이동                    |
+-------------------------+---------------------------------------------+
| Ctrl+B :                | 명령을 입력할 명령 줄로 이동합니다.         |
|                         | 탭 자동 완성 사용 가능                      |
+-------------------------+---------------------------------------------+
| Ctrl+B ?                | 모든 키 바인딩 보기. "Q"를 눌러 종료        |
+-------------------------+---------------------------------------------+
| Ctrl+B W                | 여러 세션의 창 간 이동을 위한 패널 열기    |
+-------------------------+---------------------------------------------
```

추가 키 바인딩은 tmux 매뉴얼 페이지를 참조하세요.

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

# 마우스 사용하기

티먹스는 주로 키보드와 함께 사용되며, 여러 키 바인딩을 제공하여 명령을 실행하고 새 창을 만들고 크기를 조정하는 것을 더 쉽게 할 수 있습니다. 마우스를 선호한다면, 티먹스도 그것을 허용합니다. 그러나 기본적으로 마우스는 비활성화되어 있습니다. 활성화하려면 먼저 Ctrl+B :를 입력하여 명령 모드로 진입한 후 set -g mouse 명령을 사용하여 마우스를 켜거나 끌 수 있습니다.

이제 마우스를 사용하여 창과 창 사이를 전환하고 크기를 조정할 수 있습니다. 티먹스 버전 3부터는 마우스로 오른쪽 클릭하여 콘텍스트 메뉴를 열 수도 있습니다:

![image](/assets/img/2024-05-23-ABeginnersGuidetotmux_4.png)

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

마우스 커서 아래 화면 내용에 따라 메뉴가 변합니다.

# tmux 구성하기

tmux 구성을 영구적으로 변경할 수 있습니다. tmux 구성 파일을 수정하여 변경할 수 있습니다. 기본적으로 이 파일은 $HOME/.tmux.conf에 위치합니다.

예를 들어, 기본 접두표 키 조합은 Ctrl+B이지만 때로는 이 조합을 누르기가 조금 어색하고 양손이 필요할 수도 있습니다. 구성 파일을 편집하여 다른 키로 변경할 수 있습니다. 저는 접두표 키를 Ctrl+A로 설정하는 것을 좋아합니다. 이를 위해 새 구성 파일을 만들어 다음 라인을 추가하면 됩니다:

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

```sh
$ vi $HOME/.tmux.conf

# Prefix를 Ctrl+a로 설정합니다.
set -g prefix C-a

# 이전 Prefix 설정을 제거합니다.
unbind C-b

# 두 번 눌러 애플리케이션에 Ctrl+a를 전송합니다.
bind C-a send-prefix

:wq
```

이 머신에서 tmux 세션을 시작할 때는 Ctrl+A를 눌러 위에 나열된 명령을 실행할 수 있습니다. 설정 파일을 사용하여 다른 tmux 키바인딩 및 명령을 변경하거나 추가할 수 있습니다.

# 상태 표시줄 사용자 정의

Tmux의 상태 표시줄은 완벽하게 사용자 정의할 수 있습니다. 각 섹션의 색상 및 표시 내용을 변경할 수 있습니다. 이러한 많은 옵션이 있기 때문에 다룰 내용이 또 다른 기사가 필요할 정도이지만, 기초부터 시작하겠습니다.

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

상태 표시줄 전체에 대한 표준 녹색 색상 때문에 각 섹션을 구별하기 어려울 수 있습니다. 특히 열려 있는 창의 수와 활성 창을 파악하기가 어려울 수 있습니다.

![image](/assets/img/2024-05-23-ABeginnersGuidetotmux_5.png)

상태 표시줄 색상을 업데이트하여 이를 변경할 수 있습니다. 먼저 Ctrl+B를 입력하여 명령 모드로 전환하십시오. (또는 위에서 접두어 구성 변경을 했다면 Ctrl+A를 입력하십시오.) 그런 다음 다음 명령어로 색상을 변경하십시오:

- 상태 표시줄 배경색 변경: set -g status-bg cyan
- 비활성 창 색상 변경: set -g window-status-style bg=yellow
- 활성 창 색상 변경: set -g window-status-current-style bg=red,fg=white

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

영구적 변경을 위해 해당 명령을 구성 파일에 추가해보세요.

이 구성이 적용되면 상태 표시줄이 더 멋지게 보이고 현재 활성 창을 쉽게 식별할 수 있습니다:

![tmux status bar](/assets/img/2024-05-23-ABeginnersGuidetotmux_6.png)

# 다음은 무엇인가요?

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

Tmux은 원격 연결을 보호하는 데 탁월한 도구이며 터미널을 오랜 시간 사용할 때 유용합니다. 이 기사에서는 기본 기능에 대해서만 다루고 있으며 더 많은 것을 탐험할 수 있습니다. tmux에 대한 추가 정보는 공식 위키 페이지를 참조하십시오.

더 많은 명령을 추가하고 Vim과 같은 응용 프로그램과 통합되고 상태 표시줄에 새로운 기능을 추가하는 추가 비공식 플러그인으로 tmux의 기능을 확장할 수도 있습니다. 더 많은 정보는 tmux 플러그인 프로젝트를 참조하십시오.

The Pragmatic Bookshelf에서 출판된 Ricardo의 책을 확인해 보세요.

Go 프로그래밍 언어로 직접 빠르고 신뢰할 수 있으며 크로스 플랫폼 명령 줄 도구를 작성해 보세요.

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

![2024-05-23-ABeginnersGuidetotmux_7](/assets/img/2024-05-23-ABeginnersGuidetotmux_7.png)
