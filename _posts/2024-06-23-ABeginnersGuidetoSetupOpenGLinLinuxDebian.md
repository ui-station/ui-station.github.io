---
title: "초보자를 위한 Linux Debian에서 OpenGL 설정하는 방법"
description: ""
coverImage: "/assets/img/2024-06-23-ABeginnersGuidetoSetupOpenGLinLinuxDebian_0.png"
date: 2024-06-23 00:10
ogImage: 
  url: /assets/img/2024-06-23-ABeginnersGuidetoSetupOpenGLinLinuxDebian_0.png
tag: Tech
originalTitle: "A Beginner’s Guide to Setup OpenGL in Linux (Debian)"
link: "https://medium.com/geekculture/a-beginners-guide-to-setup-opengl-in-linux-debian-2bfe02ccd1e"
---


## OpenGL 설치를 위한 우분투에서의 단계별 가이드: 필요한 라이브러리인 GLFW 및 GLAD 설치와 함께.

![이미지](/assets/img/2024-06-23-ABeginnersGuidetoSetupOpenGLinLinuxDebian_0.png)

리눅스에서 OpenGL을 시작하려고 하지만 설정하는 데 충분한 자료를 찾지 못했나요? 걱정 마세요, 제가 도와드릴게요!
이 문서에서는 VS Code를 사용하지 않고 우분투 및 다른 데비안 기반 리눅스 배포판에서 OpenGL 환경을 설정하는 간단한 지침을 제공할 것입니다. 우리는 또한 필수 라이브러리인 GLFW 및 GLAD를 설치할 것입니다.

OpenGL 사용 방법에 대한 튜토리얼은 매우 기초부터 이해하기 쉬운 예제를 사용하여 가르치는 이 웹사이트를 추천합니다. 비디오 튜토리얼을 선호하는 경우 이를 확인하는 것이 좋습니다.

<div class="content-ad"></div>

# 소개

OpenGL은 크로스 플랫폼 및 크로스 언어 API로, 2D 및 3D 벡터 그래픽을 렌더링하는 데 사용할 수 있는 많은 함수 집합을 제공합니다. 이 API는 일반적으로 GPU와 상호 작용하여 하드웨어 가속 렌더링을 달성하는 데 사용됩니다.

그러나 OpenGL 자체는 API가 아니라 단지 명세입니다. 각 함수의 결과물이 정확히 무엇이어야 하는지 및 어떻게 작동해야 하는지에 대한 설명일 뿐입니다. 제조업체가 명세를 따라 드라이버 내에 이미 구현했기 때문에 OpenGL을 별도로 "설치"하는 것은 없습니다. 그러나 운영 체제에 상호 작용하여 구현에 액세스하고 창 시스템 및 OpenGL 컨텍스트를 설정하는 ​​데 도움이 되는 라이브러리를 설치해야 합니다.

# OpenGL 설정: 종속성 설치

<div class="content-ad"></div>

필요한 라이브러리를 설치하기 전에 몇 가지 종속성을 먼저 설치해야 합니다. 터미널을 열고 다음 명령어를 실행해주세요:

```js
sudo apt-get update
sudo apt-get install cmake pkg-config
sudo apt-get install mesa-utils libglu1-mesa-dev freeglut3-dev mesa-common-dev
sudo apt-get install libglew-dev libglfw3-dev libglm-dev
sudo apt-get install libao-dev libmpg123-dev
```

# OpenGL 설정: GLFW 라이브러리

아름다운 그래픽을 생성하기 전에 OpenGL 컨텍스트를 초기화하고 그리기 위한 응용 프로그램 창을 생성해야 합니다. 이 작업을 위해 인기 있는 C 라이브러리인 GLFW(Graphics Library Framework)를 사용할 것입니다. 이 라이브러리는 조이스틱, 키보드 및 마우스로부터의 입력을 처리하는 데 도움이 됩니다.

<div class="content-ad"></div>

아래 명령어를 실행하면 시스템에 GLFW를 설치할 수 있어요:

```js
cd /usr/local/lib/
git clone https://github.com/glfw/glfw.git
cd glfw
cmake .
make
sudo make install
```

# OpenGL 설치: GLAD 라이브러리

알다시피, OpenGL은 귀하의 그래픽 카드가 지원하는 드라이버 내부에 구현된 사양에 불과해요. OpenGL 드라이버의 버전이 다양하기 때문에 대부분의 함수의 위치를 컴파일 시에 알 수 없으며 실행 시에 조회해야 해요. 함수의 위치를 찾아 해당 함수를 함수 포인터로 로드하는 것은 번거로운 과정일 수 있어요. 다행히도 이런 불편함을 덜어줄 수 있는 라이브러리가 있어요: GLAD.

<div class="content-ad"></div>

GLAD (Multi-language Loader Generator)은 웹 서비스를 이용하여 사용자가 OpenGL 버전을 정의하고 해당 버전에 따라 모든 관련 OpenGL 함수를 로드할 수 있도록 하는 오픈 소스 라이브러리입니다.

이 라이브러리를 설치하려면 다음 단계를 수행하십시오:

- GLAD 웹 서비스로 이동합니다.
- 언어를 C++로 설정하고 OpenGL 사양을 선택합니다.
- API 섹션에서 최소 3.3 버전의 gl 버전을 선택하고 프로필을 Core로 설정하고 로더 생성 옵션이 선택되었는지 확인합니다.
- 확장을 무시하고 Generate를 클릭하여 결과 라이브러리 파일을 생성합니다.
- 지금쯤에 GLAD는 glad.zip이라는 두 폴더(include 및 src)를 포함하고 있는 zip 파일을 제공해야 합니다.
- include 폴더 안의 폴더들(glad 및 KHR)을 include 디렉토리로 복사합니다: cp -R include/* /usr/include/
- 이제 src 폴더 안의 glad.c 파일을 현재 작업 디렉토리로 복사합니다.

# 첫 번째 OpenGL 프로그램 실행하기

<div class="content-ad"></div>

이제 필요한 라이브러리를 설치하는 작업이 완료되었습니다.
모든 것이 올바르게 설치되었는지 확인해보는 시간입니다. "Hello Triangle"을 사용하여 삼각형을 렌더링하는 간단한 프로그램입니다. 여기서 코드를 복사하여 hello_triangle.cpp이라는 파일에 저장할 수 있습니다.

우리의 코드를 컴파일하고 실행 파일 a.out을 생성해 봅시다:


g++ hello_triangle.cpp glad.c -ldl -lglfw

./a.out을 실행하여 프로그램이 작동하는지 확인해보세요. 아래와 같은 결과가 표시되어야 합니다:

<div class="content-ad"></div>


<img src="/assets/img/2024-06-23-ABeginnersGuidetoSetupOpenGLinLinuxDebian_1.png" />

OpenGL 설정이 완료되었습니다. 간단하고 성공적이었기를 바랍니다. 방금 복사한 소스 코드에 대해 더 알고 싶다면 이 글을 읽어보세요!

질문이나 어디에 꼬였는지에 대한 문제가 있다면 자유롭게 댓글을 달아주세요. 최선을 다해 도와드리겠습니다!
그리고 이 정보가 도움이 되었다면 박수를 치고 친구들과 공유해주세요!

그럼 이만 준비되었습니다. 다음에 또 만나요! :))
