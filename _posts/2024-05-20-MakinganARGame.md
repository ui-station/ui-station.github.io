---
title: "AR 게임 만들기"
description: ""
coverImage: "/assets/img/2024-05-20-MakinganARGame_0.png"
date: 2024-05-20 16:35
ogImage: 
  url: /assets/img/2024-05-20-MakinganARGame_0.png
tag: Tech
originalTitle: "Making an AR Game"
link: "https://medium.com/samsung-internet-dev/making-an-ar-game-with-aframe-529e03ae90cb"
---


## AFRAME, THREE.js 및 WebXR을 사용하여 4.5시간의 라이브 스트리밍 동안 게임을 만들었어요.

게임의 최종 결과물 .gif (Jiff?)가 여기 있어요. 게임을 플레이하려면 여기를 클릭하세요. 이 게임은 WebXR DOM Overlay API와 WebXR Hit Test API를 사용해요. 그래서 지금 이 게임을 플레이하는 가장 좋은 방법은 모바일 크롬이나 삼성 인터넷 베타를 사용하는 거에요.

<img src="https://miro.medium.com/v2/resize:fit:1400/1*uxubAT7y_4V7U0cUCtEiQg.gif" />

라이브 스트림의 전체 네 시간 이상을 시청하고 싶다면 다음 URL을 참고하세요:

<div class="content-ad"></div>

- Part 1: [https://youtu.be/ee7PPDmPuqY](https://youtu.be/ee7PPDmPuqY)
- Part 2: [https://youtu.be/RWFQ2FqEMi4](https://youtu.be/RWFQ2FqEMi4)
- Part 3: [https://youtu.be/5XTDOcMU3Vg](https://youtu.be/5XTDOcMU3Vg)

만약 여기서 소스 코드를 살펴보고 싶다면 아래에 있습니다:

첫 번째로 전체 프로젝트를 실시간 스트리밍하는 것이었습니다. 재미있었고, 이런 작은 프로젝트에 대해서 다시 해볼 생각이 있습니다. 이 블로그 포스트에서는 제가 그것을 만드는 데 사용한 도구와 사용된 몇 가지 노하우에 대해 이야기하겠습니다.

## AFrame 설정하기

<div class="content-ad"></div>

우선 새 HTML 파일을 만드는 것이 중요합니다. index.html이라는 파일을 생성하고 일부 HTML 뼈대를 넣어주세요. 저는 VSCode에서 Emmet 약어를 사용하여 !를 입력하여 기본 HTML을 자동으로 작성합니다.

```js
<!DOCTYPE html>
<html lang="en">
<head>
  <meta charset="UTF-8">
  <meta name="viewport" content="width=device-width, initial-scale=1.0">
  <title>My AR Game</title>
</head>
<body>
  
</body>
</html>
```

다음으로 AFrame 스크립트를 추가하기 위해 AFrame 문서에서 스크립트 태그를 복사하여 붙여넣습니다.

```js
<script src="https://aframe.io/releases/1.1.0/aframe.min.js"></script>
```

<div class="content-ad"></div>


## 테스트

로컬 http 서버를 시작하여 컴퓨터에서 표시할 수 있도록 테스트해봅니다. node http-server 모듈을 사용하겠습니다. 만약 노드 환경이 없다면 Chrome Web Server 확장 프로그램이나 glitch.com과 같은 웹 사이트에서 구축할 수 있는 다른 옵션을 사용할 수 있습니다.

<div class="content-ad"></div>

WebXR를 실행하려면 안전한 출처가 필요합니다. 안전한 출처는 일반적으로 https://로 시작하지만 특별한 안전한 출처인 http://localhost는 로컬 http 서버에 액세스할 수 있게 합니다.

만약 Glitch를 사용 중이라면 핸드폰에서 URL을 열어서 테스트할 수 있습니다.

안타깝게도 안전한 출처 요구사항은 외부 장치에서 로컬 서버를 테스트하기 까다로워집니다. 일반적으로 개발 컴퓨터에서 IP 주소를 통해 서버에 액세스하지만 http://192.168.0.10:8080과 같은 IP 주소는 안전한 출처가 아니기 때문에 WebXR에 사용할 수 없습니다.

핸드폰에서 테스트하려면 두 가지 옵션이 잘 동작합니다. 가장 편리한 옵션은 핸드폰을 USB로 연결하고 Chrome의 원격 디버깅을 사용하여 http 서버의 포트를 전달하는 것입니다. 이 기능에 액세스하려면 Chrome에서 chrome://inspect를 열어주세요.

<div class="content-ad"></div>


![AR Game Screenshot](/assets/img/2024-05-20-MakinganARGame_0.png)

한번 설정하면 폰에서 컴퓨터에서 하는 것처럼 http://localhost:8080 (8080은 사용 중인 포트로 대체)을 열고 테스트할 수 있어요.

실시간 스트림 중에는 폰의 USB-C 포트를 사용하여 HDMI를 캡처했기 때문에 이 기능을 사용할 수 없었어요. 그래서 로컬 서버를 위한 실제 https: URL을 얻기 위해 https://ngrok.com/을 사용했어요.

## AR 기능 활성화


<div class="content-ad"></div>

AR을 사용해보면 AFRame 씬에서 장면이 표시되기 전에 장치 카메라를 잠깐 볼 수 있다는 것을 먼저 알아차릴 수 있어요. 이것은 `a-sky` 요소가 전체 장면을 가리기 때문입니다.

이 문제를 해결하기 위해 AR 모드로 전환될 때 객체를 숨기는 새로운 AFrame 컴포넌트를 추가할 거에요. 이 코드는 Klaus Weidner가 AFrame 데모 중 하나에서 작업한 것을 참고했어요:

```js
AFRAME.registerComponent('hide-in-ar-mode', {
  init: function () {
    this.el.sceneEl.addEventListener('enter-vr', (ev) => {
      if (this.el.sceneEl.is('ar-mode')) {
        this.el.setAttribute('visible', false);
      }
    });
    this.el.sceneEl.addEventListener('exit-vr', (ev) => {
      this.el.setAttribute('visible', true);
    });
  }
})
```

나는 이 스니펫을 프로젝트 간에 복사하는 유용한 컴포넌트 라이브러리에 포함했어요. 다른 유용한 부분들도 함께 있어서 페이지에 그 스크립트를 추가할 거에요:

<div class="content-ad"></div>

```js
<script src="https://ada.is/basketball-demo/ar-components.js"></script>
```

여기에는 hide-in-ar-mode 구성 요소와 Aframe에서 사용할 수 있는 ar-hit-test가 추가됩니다. hide-in-ar-mode 구성 요소를 AR에서 숨기고 싶은 HTML 요소에 추가하면 `a-sky`와 같은 요소를 숨길 수 있습니다. 따라서 다음과 같이 보입니다:

```js
<a-sky color="#ECECEC" hide-in-ar-mode></a-sky>
```

다른 문제는 크기입니다. AFrame의 Hello World 예제는 VR용으로 설계되었습니다. VR에서는 물리적으로 큰 장면이 잘 작동합니다. 무제한 공간을 활용할 수 있기 때문입니다. AFrame Hello World 예제에서 컨텐츠는 사용자로부터 5미터 떨어지고 높이가 2.5m인 큰 크기로 배치됩니다. 이는 VR에서 인상적으로 보이지만 AR에서는 많은 사람들의 실제 환경에 맞지 않을 정도로 매우 큽니다.

<div class="content-ad"></div>

AR 씬을 디자인할 때는 콘텐츠의 크기가 0.5m보다 크지 않도록 하는 것이 좋습니다. 사람들이 그것을 환경에 맞춰놓을 수 있도록 할 수 있으니까요. 작은 아파트에서 살고 있는 사람으로서 이야기하는 거예요.

이 규칙을 어기고 싶을 때는, AR 씬이 외부에서만 사용되도록 설계되었거나 가상 개체가 특정한 치수를 가진 실제 개체를 대신하는 경우입니다. 가구 구매 등 특정 가구 조각을 구입하고 집에 어떻게 맞는지 보고 싶을 때와 같은 상황이죠.

씬을 업데이트하기 위해, 씬 안의 모든 개체를 원래 크기의 10%로 만들었습니다. WebXR과 AFrame의 모든 단위는 미터이기 때문에, 1.25m를 0.125m로 변경했어요 (12.5cm 또는 약 6인치).

## 히트 테스팅 추가하기

<div class="content-ad"></div>

히트 테스트는 가상 콘텐츠에서 실제 세계로 광선을 쏘는 것을 의미합니다. 따라서 바닥, 테이블 및 벽과 같은 실제 세계 객체와 일치하게 가상 객체를 배치할 수 있습니다.

히트 테스트를 사용하면 충돌 지점의 위치와 법선을 얻을 수 있어 수직 및 수평 서비스에 객체를 배치할 수 있습니다.

히트 테스트 기능은 사용자 환경에 대한 추가 정보를 얻을 수 있어 WebXR에서 기본적으로 제공되지 않는 기능입니다.

그러나 AFrame에서 XR 세션을 시작할 때 추가 정보를 요청할 수 있습니다. `a-scene` 요소에 webxr 구성 요소를 사용하여 다음과 같이 추가할 수 있습니다:

<div class="content-ad"></div>

```js
<a-scene webxr="optionalFeatures: hit-test;">
```

사용 방법을 돕기 위해 앞서 언급한 라이브러리에서 도움이 되는 컴포넌트를 만들었습니다:

```js
<script src="https://ada.is/basketball-demo/ar-components.js"></script>
```

ar-hit-test 컴포넌트는 가장 관련된 사용자 입력을 사용하여 계속해서 히트 테스트를 수행하여 다양한 하드웨어에서 작동하는 무언가를 구축하는 데 도움을 줄 것입니다. 예를 들어, 헤드셋 위치를 기본으로 사용하지만 가능한 경우 VR 컨트롤러나 핸드 트래킹을 사용할 것입니다.

<div class="content-ad"></div>

어디에 맞은지와 상관없이 해당 객체를 배치합니다. 이는 AR에서 목표 지시 표시기로 사용하기 편리합니다.

가이드 지시 기호에 사용할 20cm 제곱형을 만들어 보겠습니다:

```js
<a-plane
 rotation="-90 0 0"
 width="0.2"
 height="0.2"
 src="./arrow.png"
 material="transparent:true;"
></a-plane>
```

<img src="/assets/img/2024-05-20-MakinganARGame_1.png" />

<div class="content-ad"></div>

`div` 태그가 HTML에서 사용되는 것과 유사하게 AFrame에서는 `a-entity`를 사용합니다. 이는 3D 모델과 같은 추가 속성이 없는 일반 요소입니다. ar-hit-test 컴포넌트를 사용하여 위치를 조정하는 새로운 `a-entity`를 만들겠습니다.

```js
<a-entity ar-hit-test="doHitTest:false" visible="false">
Reticle HTML goes here ...
</a-entity>
```

우리는 ar-hit-test 컴포넌트가 가능해지면 자동으로 다시 보이도록하는 것이므로 이를 보이지 않도록 만들었습니다.

## 농구 골대 만들기

<div class="content-ad"></div>

이제 우리는 원하는 실제 세계 객체의 위치를 얻는 방법이 있습니다. 벽에 설치할 테스트할 방법을 만들어 보겠습니다.

히트 테스트를 사용하여 표면에 배치된 객체는 그들의 y축(위아래 축)이 배치된 표면의 법선과 정렬됩니다. 이는 바닥에 배치된 객체는 보통 나타날 것이고, 벽이나 천장과 같은 표면에 배치된 객체는 회전될 것임을 의미합니다. 객체는 보통 이 법선 주위로 회전하여 히트 테스트 원본을 향해 정렬되지만, 이 행동은 명세에 포함되어 있지 않으므로 다를 수 있습니다.

벽에 배치된 객체는 90도 회전될 것이므로 설계할 때 약간의 초기 회전을 시작해야 합니다. 따라서 포장 엔터티 #hoop은 rotation="90 0 0"을 가질 것이며, 이는 벽에 배치된 객체와 유사합니다. 만약 바닥에 놓는다면 0 0 0의 회전이 충분합니다. 이 회전은 벽에 객체를 배치할 때 벽의 법선의 방향에 따라 재설정될 것입니다.

후프는 3개의 간단한 모양으로 이루어질 것입니다. 배경 보드를 위한 평면, 후프를 위한 토러스 및 그물을 위한 열린 끝이 있는 원뿔입니다.

<div class="content-ad"></div>


## 훈련 목표 설명하기


<div class="content-ad"></div>

플레이어가 자신의 벽에 후프를 배치할 수 있도록 하고 싶습니다. 이 함수는 후프를 가시적으로 만들고, 선택이 발생할 때 선택한 위치에 후프를 위치시키는 역할을 합니다. 선택을 하면 함수가 호출됩니다. 하지만 이 단순한 함수는 후프를 조금 비스듬하게 만들 수 있습니다. 후프가 히트 테스트 원점을 향하도록 기울어져 있지만 정렬하기가 어려울 수 있습니다. 따라서 후프의 z 방향이 y 축과 일치하도록 하기 위해 일부 백터 수학을 사용해야 합니다.

<div class="content-ad"></div>

조금 더 복잡한 함수는 시야를 수직 위치로 회전시키기 위해 필요한 쿼터니온 회전을 계산할 것입니다. 그런 다음 해당 회전과 시야의 회전을 곱하여 후크의 쿼터니온 회전을 설정합니다. 결과적으로, 방향이 올바른 후크가 만들어지지만 약간 휘어져 있어 상단을 가리킵니다.

## 씬에 물리학 추가하기

물리는 매우 유용한 aframe-physics-system에 의해 처리됩니다. 이를 통해 씬 안의 객체들이 물리적으로 현실적인 방식으로 행동하도록 할 수 있습니다.

물리학 객체에는 두 가지 유형이 있습니다:

<div class="content-ad"></div>

- 정적 바디는 움직이지 않고 다른 객체에 부딪혀도 반응하지 않습니다. 사실상 무한한 질량을 가지고 있으며 중력의 영향을 받지 않습니다. 사용하기는 저렴하지만 움직일 수 없습니다. 움직이지 말아야 하는 것은 바닥, 벽 및 이 경우 농구 골대와 같은 정적 바디여야 합니다.
- 동적 바디는 질량을 가지며 중력에 의해 일정한 가속도로 떨어집니다. 정적 바디에서 튕길 수 있거나 다른 동적 바디와 충돌할 수 있습니다. 장면에서 유일한 동적 바디는 공 자체입니다.

물리 시스템을 설정하려면 A-Frame 스크립트 다음에 스크립트를 추가하고 물리 컴포넌트를 장면 요소에 추가하십시오.

```js
<script src="https://cdn.jsdelivr.net/gh/n5ro/aframe-physics-system@v4.0.1/dist/aframe-physics-system.min.js"></script>
...
<a-scene physics="debug: false">
```

장면을 설정하는 데 도움이 필요하면 디버그 값을 true로 설정하여 만들어진 모양을 빨간색 윤곽으로 볼 수 있습니다.

<div class="content-ad"></div>

다음은 다이나믹-바디 컴포넌트가 적용된 구를 추가합니다. 이 구가 공이 될 것입니다.

```js
<a-sphere id="ball" dynamic-body radius="0.1" color="orange" position="0.1 2.36 -1.5"></a-sphere>
```

페이지를 새로고침하면 공이 계속해서 떨어질 것입니다.

다음 단계는 바닥면에 스태틱-바디를 추가하여 공을 막을 수 있는 물체를 만드는 것입니다. 또한, 공이 바닥면에 부딪히면 구를 굴릴 수 있도록 바닥면을 훨씬 크게 만들었습니다.

<div class="content-ad"></div>

```js
<a-plane
    rotation="-90 0 0"
    width="20"
    height="20"
    color="#43A367"
    static-body
    hide-in-ar-mode
></a-plane>
```

게임을 플레이할 때 후프와 상호 작용하고 싶습니다. 그래서 다음에는 백보드 평면에 static-body를 추가할 것입니다.

후프에 대해 어떻게 해야 할까요? 이것은 훨씬 더 복잡합니다. 후프는 볼록한 객체이며 상당히 복잡한 위상을 가지고 있으며 3D 모델에는 많은 정점이 있어서 물리학적으로 매우 비싸지게 만듭니다.

여기서 꼼수는 가능한 한 많은 다각형을 가진 보이지 않는 토러스를 가지는 것입니다. 그것을 정적 바디로 만들지만 보이지 않게 하고 고해상도 모델 위에 놓습니다. 이것은 비디오 게임에서 공개된 객체보다 훨씬 간단한 기하학을 가진 물리 객체를 가지는 것이 일반적인 꼼수입니다.

<div class="content-ad"></div>

```yaml
<table scale="0.6 0.6 0.6" static-body="shape: mesh;" position="0 0.173 -0.1" visible="false" radius="0.27" radius-tubular="0.02" geometry="radius: 0.29; segmentsRadial: 5; segmentsTubular: 12">
</table>
```

훌륭한 점을 홀의 정적 객체를 홀 엔티티 안에 두면 시각적 객체와 일관되게 유지됩니다.

AFrame 물리 시스템에는 두 개의 객체가 충돌했을 때 감지하거나 객체의 속도를 설정하기 위한 JavaScript API가 있습니다. 원하는 엔티티의 body 속성을 사용할 수 있습니다. 단, 이는 정적 또는 동적 body여야 합니다.

볼과 같은 객체의 위치와 속도를 설정하려면 이 메서드를 사용합니다. 현재 활성 컨트롤러에서 공을 발사하는 방법은 다음과 같습니다:

<div class="content-ad"></div>


```js
const ball = document.getElementById('ball');
reticle.addEventListener('select', function (e) {
  // Set the ball location to the controller position
  const pose = e.detail.pose;
  ball.body.position.copy(pose.transform.position);  // {x, y, z}
  // Have an initial velocity vector of 5ms into the screen
  tempVector.set(0, 0 ,-5);
  // Set our velocity vector direction to the controller orientation
  // {x, y, z, w}
  tempVector.applyQuaternion(pose.transform.orientation);
  // set the velocity of the ball to our velocity vector
  ball.body.velocity.copy(tempVector);
});
```

## Dom Overlay

The last thing we need is to make some UI so that the user can say when they have set the hoop position and are ready to play. We can build a normal HTML interface for this:

```js
<div id="overlay" class="container">
  <h1>Welcome To Basketball</h1>
  <section class="overlay-content">
    <p id="instructions">Place the basket along a wall</p>
  </section>
  <div style="display: flex; justify-content: space-between; align-self: stretch;">
    <button id="go-button">Ready to Play!</button>
    <button id="exit-button">Stop AR</button>
  </div>
</div>
```  

<div class="content-ad"></div>

그럼 웹XR 컴포넌트에서 scene 객체에 그것을 선언하여 사용할 수 있습니다:

```js
<a-scene webxr="optionalFeatures: hit-test, dom-overlay; overlayElement:#overlay;" >
```

실제 HTML 버튼과 텍스트를 사용하면 사용자에게 많은 이점이 있습니다. 접근성 도구와 함께 작동하며 더 읽기 쉽습니다. 일반적인 CSS로 스타일을 지정할 수 있고 일반적인 JavaScript로 코딩할 수 있습니다.

다만 유의해야 할 점은 사용자가 DOM 오버레이 요소를 탭하면 `click`, `mousedown`, `touchstart`와 같은 입력 이벤트가 일반적으로 발생하나 추가로 WebXR `select` 이벤트가 먼저 발생한다는 것입니다!

<div class="content-ad"></div>

'`select`' 이벤트에서 HTML 버튼으로부터의 입력을 기다리는 중이라면 버튼이 눌러지지 않았는지 확인하기 위해 `setTimeout`을 사용해야 할 수 있습니다.

DOM 오버레이 지원을 감지하려면 xrsession.domOverlayState.type를 찾아보세요. domOverlayState가 설정되어 있지 않으면 브라우저에 domOverlay가 없습니다. type이 설정되어 있지 않으면 현재 하드웨어/브라우저 구성이 DOM Overlay를 지원하지 않으므로 다음 함수를 사용하여 확인할 수 있습니다:

```js
function hasDomOverlay(xrsession) {
  if (!xrsession.domOverlayState) {
    // DOM Overlay가 지원되지 않습니다
    return false;
  }
  if (!xrsession.domOverlayState.type) {
    // DOM Overlay가 사용되지 않았습니다
    return false;
  }
  return true;
}
```

필수 경로에 DOM 오버레이를 사용 중이라면 이를 사용하여 가용성을 감지하고 대체 동작을 제공할 수 있습니다.

<div class="content-ad"></div>

## 완성된 데모의 소스 코드 읽기

여기 데모의 소스 코드가 있어요. 이 가이드가 도움이 되어 데모 소스 코드와 의사 결정에 대해 이해하는 데 도움이 되기를 바랍니다. 더 궁금한 점이 있다면 트위터를 통해 연락해 주세요.

읽어 주셔서 정말 감사합니다!