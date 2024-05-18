---
title: "K9s를 사용하여 스타일리시하게 Kubernetes 클러스터를 간편하게 관리하기"
description: ""
coverImage: "/assets/img/2024-05-18-Hassle-freemanagementofyourKubernetesclusterinstyleusingK9s_0.png"
date: 2024-05-18 16:46
ogImage: 
  url: /assets/img/2024-05-18-Hassle-freemanagementofyourKubernetesclusterinstyleusingK9s_0.png
tag: Tech
originalTitle: "Hassle-free management of your Kubernetes cluster in style using K9s"
link: "https://medium.com/@mrpunitsolanki/hassle-free-management-of-your-kubernetes-cluster-in-style-using-k9s-28bdac4142b4"
---


일상생활에서 마이크로서비스 및 컨테이너 오케스트레이션을 다루는 개발자라면, 이전에 제가 자주 만났던 상황에 있었을지도 모릅니다. 쿠버네티스 명령어를 입력하는 것은 재미있을 수 있지만, 모든 작업과 명령어에 익숙해지면 그저 응소가 된다는 느낌을 받을 수 있습니다.

월요일 아침에 일어나서 월요일 블루가 시작되고 있는데, 한 가지 팟의 로그를 보기 위해 여러 명령어를 입력할 기분이 전혀 아니라고 가정해 봅시다.

어느 순간에는 삶이 매우 단조로워지고, 이것은 철학적인 이야기가 아닙니다! 동일한 오래된 명령 프롬프트나 터미널이 어느 순간 짜증을 유발하기 시작할 수 있습니다. 이해합니다. 당신의 삶에 가질 수 있는 유일한 두 가지 색상은 아니라는 것을 알고 있습니다.

<div class="content-ad"></div>

# 만약 이렇게 해볼 수 있다고 말한다면:

![이미지1](/assets/img/2024-05-18-Hassle-freemanagementofyourKubernetesclusterinstyleusingK9s_1.png)

# 이렇게 바뀌길 원하나요:

![이미지2](/assets/img/2024-05-18-Hassle-freemanagementofyourKubernetesclusterinstyleusingK9s_2.png)

<div class="content-ad"></div>

멋지게 보이죠, 맞지 않나요?! 이렇게 하는 방법을 배워봅시다!

## 단계:

이 인터페이스를 제공하고 우리가 더 자세히 논의할 많은 바로 가기를 제공하는 K9s를 설치하는 과정을 진행해 보겠습니다.

K9s는 Linux, macOS 및 Windows 플랫폼에서 사용할 수 있습니다. 여기에서 자신의 OS에 알맞게 K9s를 설치하세요. 또는 저와 같이 게으르다면, 아래를 따라가 보세요.

<div class="content-ad"></div>

```js
# 홈브류를 통해 설치하기
 brew install derailed/k9s/k9s
 # 맥포트를 통해 설치하기
 sudo port install k9s
```

```js
# 리눅스브류를 통해 설치하기
 brew install derailed/k9s/k9s
 # 팩맨을 통해 설치하기
 pacman -S k9s
```

```js
# scoop을 통해 설치하기
scoop install k9s
# 초콜렛티를 통해 설치하기
choco install k9s
```

여기까지가 가장 어려운 부분이라고 믿어줘요. 이제부터는 모든 게 매우 쉽고 직관적입니다!
```

<div class="content-ad"></div>

테스트를 위해 Azure에서 클러스터를 만들었어요.

![클러스터 이미지](/assets/img/2024-05-18-Hassle-freemanagementofyourKubernetesclusterinstyleusingK9s_3.png)

이제 Azure 포털에서 제공하는 명령어를 사용하여 클러스터에 연결해 보겠어요:

![연결 명령어 이미지](/assets/img/2024-05-18-Hassle-freemanagementofyourKubernetesclusterinstyleusingK9s_4.png)

<div class="content-ad"></div>

지금 명령 프롬프트 또는 터미널에서 k9s -A를 입력하면 멋진 UI가 팝업됩니다! UI의 좌측 상단에 언급된 일부 세부 정보를 확인하여 올바른 클러스터에 연결되어 있는지, 올바른 컨텍스트를 사용하고 있는지, 그리고 한눈에 CPU 및 메모리 활용을 확인할 수 있습니다.

<img src="/assets/img/2024-05-18-Hassle-freemanagementofyourKubernetesclusterinstyleusingK9s_5.png" />

당신의 주의를 끈 사이, K9s를 사용하여 이동하는 데 사용할 수 있는 몇 가지 기본 명령어를 배워보겠습니다. 기본 네임스페이스의 리소스를 보려면 단순히 1을 누르고 모든 네임스페이스의 기본 뷰로 전환하려면 0을 누릅니다. 화살표 키를 사용하여 탐색하고 리소스 내부로 이동하려면 Enter 키를 누릅니다.

이제 pod, 네임스페이스, 인그레스 등과 같은 리소스에 빠르게 액세스하려면 여기에 있습니다.

<div class="content-ad"></div>

## 참고 사항:

- :ns: 네임스페이스 전환.
- :pod: 모든 파드 나열.
- :svc: 모든 서비스 나열.
- :deploy: 모든 배포 나열.
- :ing: 모든 인그레스 나열.
- :jobs: 모든 작업 나열.
- :cronjob: 모든 크론잡 나열.
- :hpa: 모든 수평 파드 자동 확장기 나열.

명령 모드에 진입하려면 단순히 ‘:’를 누르고 액세스하려는 리소스 이름을 입력하면 됩니다. 여기에 간단한 예제가 있습니다:

<img src="/assets/img/2024-05-18-Hassle-freemanagementofyourKubernetesclusterinstyleusingK9s_6.png" />

<div class="content-ad"></div>

테이블 태그를 Markdown 형식으로 변경하세요.

<div class="content-ad"></div>

테이블 태그를 마크다운 형식으로 변경하세요.

비슷하게 kubectl logs my-app-pod-849f8d7d6d-x9s7z를 입력하는 대신에, 'l'을 눌러주세요.

![image](/assets/img/2024-05-18-Hassle-freemanagementofyourKubernetesclusterinstyleusingK9s_9.png)

## 여기에 리소스를 관리하는 또 다른 치트 시트가 있습니다:

- Enter: 선택한 리소스의 세부 정보 보기
- d: 선택한 리소스 설명
- l: 선택한 파드 또는 컨테이너의 로그 보기
- s: 선택한 파드로 셸 접속
- e: 선택한 리소스 편집
- ctrl-c: 현재 작업 중지 (로그 또는 셸보기에서 유용함)
- ctrl-d: 선택한 리소스 삭제 (확인을 위한 프롬프트 표시)
- /: 리소스 검색 또는 필터링
- Esc: 필터 지우기 또는 검색/필터 모드 나가기
- Space: 여러 리소스 선택 (대량 작업에 유용함)
- v: 리소스 매니페스트 보기
- b: Kubernetes 벤치마크 표시
- t: 테이블 및 YAML 모드 간 보기 전환

<div class="content-ad"></div>

참고 자료:

- [K9s 설치](https://k9scli.io/topics/install/)
- [K9s 명령어](https://k9scli.io/topics/commands/)
- [K9s GitHub 저장소](https://github.com/derailed/k9s)