---
title: "2024년을 위한 데이터 과학을 위한 MacBook 설정 방법"
description: ""
coverImage: "/assets/img/2024-05-18-HowtoSetupYourMacbookforDataSciencein2024_0.png"
date: 2024-05-18 17:38
ogImage: 
  url: /assets/img/2024-05-18-HowtoSetupYourMacbookforDataSciencein2024_0.png
tag: Tech
originalTitle: "How to Setup Your Macbook for Data Science in 2024"
link: "https://medium.com/@tomergabay/how-to-setup-your-macbook-for-data-science-in-2024-43903ac40d4a"
---


<img src="/assets/img/2024-05-18-HowtoSetupYourMacbookforDataSciencein2024_0.png" />

최근에 새 맥북을 샀어요. 맥북을 제 마음대로 설정하기 위해 이전 맥북 경험과 인터넷 조사를 토대로 많은 시간을 보냈어요. 데이터 과학자를 위한 앱과 도구를 갖춘 매우 쾌적한 환경을 만들기 위해 취한 단계를 문서화했어요. 이 기사는 세 가지 섹션으로 나누어져 있어요:

- 브라우저 설정
- 설치할 앱
- 터미널 설정 (파이썬 설정 포함)

# 브라우저

<div class="content-ad"></div>

먼저 브라우저부터 살펴보겠습니다. 나는 Google Chrome 또는 Arc와 같은 Chromium 기반 브라우저를 Safari보다 선호합니다. Chromium 기반 브라우저가 가지고 있는 확장 기능 라이브러리 때문에 그런데요. 나는 Chrome보다는 더 깔끔하고 우아한 느낌의 Arc를 사용합니다.

나의 브라우저에 사용하는 익스텐션은 다음과 같습니다:

- Vimium C-`는 웹을 탐색할 때 마우스 대신 단축키를 사용할 수 있습니다. f를 누르면 각 버튼에 대한 단축키가 표시되며 해당 버튼을 클릭할 수 있습니다. 해당 버튼의 문자를 입력하여 마우스를 사용하지 않고 버튼을 클릭할 수 있습니다! 익스텐션의 설정에서는 단축키를 표시하거나 숨기기를 쉽게 전환하기 위해 링크 힌트에 사용된 문자에서 문자 f를 제거하는 것을 권장합니다. 또한, Vimium C를 사용하면 책갈피 및 다른 탭에 신속하게 액세스할 수 있습니다.

![이미지](/assets/img/2024-05-18-HowtoSetupYourMacbookforDataSciencein2024_1.png)

<div class="content-ad"></div>

- 비트워던 -` 다양한 비밀번호 관리자가 있지만, 비트워던은 그 중 하나입니다. 모든 기기에서 작동하며 무료 티어에서 다양한 기능을 제공하므로 제 선호도입니다.
- 원세 -` 원세는 자주 열고 싶지만 그렇게 하기 전에 한 번 더 생각해야 할 웹사이트를 구성할 수 있는 확장 프로그램입니다. 소셜 미디어 및 뉴스 웹사이트 등의 예시가 있습니다. 이러한 URL로 이동하면 원세가 일정 시간 동안 웹사이트를 차단하여 실제로 해당 웹사이트를 방문하고 싶은지 여부를 두 번 생각할 수 있습니다. 제 모든 기기에서 사용하고 있으며 소셜 미디어 및 뉴스 웹사이트에서 많은 시간을 절약하도록 도와줍니다.

![이미지](/assets/img/2024-05-18-HowtoSetupYourMacbookforDataSciencein2024_2.png)

# 앱

- 비주얼 스튜디오 코드 -` 제 선호하는 통합 개발 환경(IDE)입니다. 물론, VS code 대 PyCharm 논쟁이 있지만, 확장 라이브러리가 넓고 PyCharm보다 훨씬 가볍기 때문에 VS Code를 선호합니다.
- 도커 데스크톱 -` Docker를 사용할 수 있도록 멋진 UI가 제공됩니다.
- 비트워던 -` 크롬 확장 프로그램으로 언급한 이유와 동일합니다.
- 키클루 -` 누른 키를 두 번 눌러 누르고 두 번째로 누른 채로 최종으로 너무 많은 응용 프로그램에 대한 대부분의 단축키를 볼 수 있습니다. 사용자 정의 단축키도 추가할 수 있습니다.
- 원세 -` 앞에서 언급한 크롬 웹 확장 프로그램과 같은 아이디어입니다.
- 마그넷 -` Windows에서 온 경우이며 Windows 10부터 사용할 수 있는 화면 분할 기능이 맥에서도 사용 가능하게 해주는 손쉬운 방법이 부족한가요? Magnet은 맥에서도 동일한 기능을 제공하지만 무료는 아닙니다.

<div class="content-ad"></div>

# 터미널

MacOs의 기본 터미널은 괜찮아요. 저는 여러 해 동안 사용해 왔고, 많은 사람들이 아직도 사용하고 있어요. zsh에 플러그인을 사용하면 터미널을 사용하는 것이 이미 훨씬 더 즐거워집니다. 하지만 여기서 여러분이 터미널에서 절대로 얻을 수 있는 최대한의 이점에 대해 설명할게요.

## Oh My Zsh

Oh My Zsh는 여러분의 Zsh 구성을 관리하기 위한 오픈소스, 커뮤니티 주도의 프레임워크로, 테마와 플러그인을 제공하여 터미널 경험을 향상시킵니다.

<div class="content-ad"></div>

먼저 다음 명령어를 입력하여 터미널에서 Oh My Zsh를 설치하세요:

```js
sh -c "$(curl -fsSL https://raw.githubusercontent.com/ohmyzsh/ohmyzsh/master/tools/install.sh)"
```

## Homebrew

그런 다음 Homebrew를 설치하세요. Homebrew는 MacOS용 무료 오픈 소스 패키지 관리 시스템으로, 소프트웨어를 설치, 업데이트 및 관리하는 데 사용됩니다.

<div class="content-ad"></div>

```bash
/bin/bash -c "$(curl -fsSL https://raw.githubusercontent.com/Homebrew/install/HEAD/install.sh)"
```

## Warp

그런 다음 Warp를 설치하세요. Warp는 내장 MacOs 터미널을 대체할 거의 IDE와 같은 터미널입니다. 내장 터미널처럼 계속 사용할 수 있지만, 매우 환영 받는 추가 기능(무료 커서 배치, 텍스트 바로 가기)부터 고급 기능(패널 분할, 탭, 이전 명령어 검색) 및 기타 여러 가지를 제공합니다. 또한 명령어보다 하고자 하는 것을 입력할 수 있는 A.I. 어시스턴트를 사용할 수 있습니다. 또한, autosuggestions와 color-highlighting과 같은 인기있는 oh-my-zsh 플러그인이 Warp와 함께 내장되어 있습니다.

<img src="/assets/img/2024-05-18-HowtoSetupYourMacbookforDataSciencein2024_3.png" />


<div class="content-ad"></div>

이 튜토리얼을 따라 하면 VS Code에서 Warp를 더 원활하게 사용할 수 있습니다.

## powerlevel10k

저는 powerlevel10k를 터미널 테마로 좋아하는데, 깔끔하게 보이고 간단한 단계로 쉽게 사용자 정의할 수 있기 때문이죠.

![이미지](/assets/img/2024-05-18-HowtoSetupYourMacbookforDataSciencein2024_4.png)

<div class="content-ad"></div>

제가 추천하는 글꼴을 설치하는 방법부터 시작할게요. 워프에서 활성화하려면 설정 -` 외형 -` 터미널 글꼴로 이동하여 MesloLGS NF를 선택하세요.

그런 다음 다음과 같이 설치하세요:

```js
git clone --depth=1 https://github.com/romkatv/powerlevel10k.git ${ZSH_CUSTOM:-$HOME/.oh-my-zsh/custom}/themes/powerlevel10k
```

source ~/.zshrc로 셸을 다시 시작하고 p10k configure를 실행하여 테마를 구성하세요! 설정 -` 외형 -` 프롬프트에서 쉘 프롬프트 대신 워프 프롬프트 설정되었는지 확인해주세요.

<div class="content-ad"></div>

## z

저는 oh-my-zsh에 z 플러그인을 사용하는 것을 좋아합니다. 이를 사용하면 전체 경로를 입력하지 않고 디렉토리 간에 이동할 수 있습니다. vim ~/.zshrc을 실행한 후 플러그인 섹션에 z를 추가하고 :wq로 vim을 나가면 됩니다.

![이미지](/assets/img/2024-05-18-HowtoSetupYourMacbookforDataSciencein2024_5.png)

## Python

<div class="content-ad"></div>

대부분의 데이터 과학자들은 주 프로그래밍 언어로 Python을 사용합니다. MacBook에는 기본 설치되어 있어야 합니다. 그러나 새로 시작할 때 python --version 명령이 실패할 수 있고 python3 --version은 작동할 수 있습니다. python3 명령뿐 아니라 python 명령을 실행하려면 ~/.zshrc 파일에 다음을 추가하십시오:

```js
echo 'alias python="python3"' >> ~/.zshrc
```

다른 프로젝트에 대해 다른 Python 버전을 사용하려면 pyenv를 사용합니다:

```js
brew update
brew install pyenv
brew install xz

echo 'export PYENV_ROOT="$HOME/.pyenv"' >> ~/.zshrc
echo '[[ -d $PYENV_ROOT/bin ]] && export PATH="$PYENV_ROOT/bin:$PATH"' >> ~/.zshrc
echo 'eval "$(pyenv init -)"' >> ~/.zshrc
```

<div class="content-ad"></div>

파이썬 버전을 확인하려면 pyenv install --list 명령을 사용하세요. 저는 일반적인 Python3 버전만 설치하고 싶어하여 pyenv install --list | grep ' 3\.' 를 사용합니다.

내 설치를 깨끗하게 유지하고 실수로 가상 환경이 아닌 전역적으로 의존성을 설치하는 것을 방지하기 위해 다음 두 가지 방법을 적용합니다:

```js
echo 'alias pip="pip3 --require-virtualenv"' >> ~/.zshrc
```

그리고 pip 설정의 전역 설정에 require-virtualenv = true를 추가합니다:
이미 pip 설정 파일이 있는지 확인하려면 cat ~/.config/pip/pip.conf를 사용하세요. 이미 설정 파일이 있다면 [global] 섹션에 require-virtualenv = true를 추가하세요. 설정 파일이 없다면 아래 명령을 사용하세요:

<div class="content-ad"></div>

```bash
mkdir -p ~/.config/pip && echo "[global]\nrequire-virtualenv = true" > ~/.config/pip/pip.conf
```

# 결론

위에서 언급한 브라우저 확장 프로그램, 앱 및 명령줄 도구를 모두 설치하면 MacBook에서 프로그래밍하는 데 훌륭한 경험을 할 수 있을 것입니다. 물론 모든 도구가 모든 사람에게 유용하거나 호감을 받는 것은 아닙니다. 키보드 단축키보다 마우스를 선호한다면 Vimium C 같은 도구를 설치할 필요가 없습니다. 또한 VS Code 대신 PyCharm을 선호한다면 쉽게 교체할 수 있습니다.

제가 사용 중인 MacBook 설정에 만족하실 것으로 기대하며 놓치고 있는 유용한 도구가 있으면 댓글로 알려주세요!```

<div class="content-ad"></div>

## MLearning.ai의 작가 / Hacking GPTs Store / 20,000개 이상의 아트 프롬프트