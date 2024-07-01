---
title: "프론트엔드 머신 코딩 트래픽 라이트 구현 "
description: ""
coverImage: "/assets/img/2024-07-01-FrontendMachineCodingTrafficLight_0.png"
date: 2024-07-01 16:38
ogImage: 
  url: /assets/img/2024-07-01-FrontendMachineCodingTrafficLight_0.png
tag: Tech
originalTitle: "Frontend Machine Coding: Traffic Light 🚦"
link: "https://medium.com/@uttkarshsingh789/frontend-machine-coding-traffic-light-9108ce657cf4"
---


문제 설명:

일정한 간격을 지나면 녹색 → 노란색 → 빨간색으로 전환되는 교통 신호등을 만들어주세요. 각 신호등의 밝기 지속 시간은 다음과 같아야 합니다:

- 빨간불: 4000ms
- 노란불: 500ms
- 녹색불: 3000ms

교통 신호등의 외관을 스타일링하는 데 창의력을 발휘해보세요.

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

앞으로 들어올 요구 사항을 고려하여 확장 가능하게 만들어야 해요.

솔루션: ( CodeSandBox 링크 )

![이미지](/assets/img/2024-07-01-FrontendMachineCodingTrafficLight_0.png)

여기서는 React js를 사용하여 솔루션을 개발하고 있어요.

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

여기 아래의 컴포넌트들과 그 props 목록이 있어요:

- TrafficLight :

```js
props: {
    config: Array,
    lightChangeHandler: Function
}
```

- Light

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
props: {
    color: string,
}
```

아래에서부터 솔루션을 구축해보겠습니다.

**Light Component (Light.js)**

현재 교통 신호등의 불빛을 표시하기 위해 Light 컴포넌트를 만들고 있습니다. 이 컴포넌트는 빛의 현재 색상을 props으로 받습니다.

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
import "./App.css";
export const Light = ({ color }) => {
  return (
    <div className="traffic-light" style={{ backgroundColor: color }}></div>
  );
};
```

TrafficLight Component (TrafficLight.js)

여기에는 트래픽 라이트 색상을 제어하고 무한정으로 실행하는 주요 로직이 작성되어 있습니다. config 및 handlerFunction을 props로 받아들이고 무한정으로 실행되는 트래픽 라이트를 반환합니다.

```js
import { useEffect, useState } from "react";
import { Light } from "./Light";
import "./App.css";

export const TrafficLight = ({ config, changedState = () => {} }) => {
  const [currentLight, setCurrentLight] = useState("green");

  useEffect(() => {
    // 현재 광원을 변경하는 로직
    const { duration, next, currentState } = config[currentLight];
    
    // 부모 구성 요소에서 전달된 함수 호출
    changedState(config[currentLight]);

    const timerId = setTimeout(() => {
      setCurrentLight(next);
    }, duration);

    // 타이머 이벤트 제거
    return () => clearTimeout(timerId);
  }, [currentLight]);

  return (
    <div className="traffic-light-container">
      {Object.keys(config).map((color) => {
        const backgroundColor =
          color == currentLight ? config[color].color : undefined;
        return <Light key={color} color={backgroundColor} />;
      })}
    </div>
  );
};
```

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

부모 컴포넌트 (App.js)

App 컴포넌트는 TrafficLight 컴포넌트 및 해당 프롭 및 핸들러 함수를 보유합니다. 핸들러 함수를 사용하여 신호에 여러 개의 광고등이 있는 경우 확장할 수 있도록하여 동기화하여 작동할 수 있습니다.

```js
import { useState } from "react";
import { TrafficLight } from "./TrafficLight";
import "./App.css";

export default function App() {
  const [displayText, setDisplayText] = useState("");
  const config = {
    red: {
      color: "red",
      duration: 4000,
      next: "yellow",
      currentState: "Stop 🛑",
    },
    yellow: {
      color: "yellow",
      duration: 500,
      next: "green",
      currentState: "Ready ⚠️",
    },
    green: {
      color: "green",
      duration: 3000,
      next: "red",
      currentState: "Go 🟢",
    },
  };
  
  // 핸들러 함수
  const lightChangeHandler = (event) => {
    setDisplayText(event.currentState);
  };

  return (
    <div className="app">
      <h2>신호등</h2>
      <p>{displayText}</p>
      <TrafficLight config={config} changedState={lightChangeHandler} />
    </div>
  );
}
```

모든 스타일은 단일 스타일시트인 App.css에 포함되어 있습니다.

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
.app {
  margin: auto;
  width: 25%;
  text-align: center;
}

.traffic-light-container {
  display: flex;
  flex-direction: column;
  align-items: center;
  margin: auto;
  border: 1px solid grey;
  border-radius: 18px;
  background-color: #f0f0f0;
}

.traffic-light {
  margin: 20px;
  padding: 10px;
  width: 20px;
  height: 20px;
  border-radius: 50%;
  border: 1px solid black;
}
```

개선 사항

다음은 보다 확장 가능하게 만들기 위해 수행할 수 있는 개선사항 목록입니다.

- 타입스크립트 사용 및 모든 컴포넌트 속성을 나타내는 enum 정의
- 설정을 별도로 코드의 정적 부분으로 이동하여 별도의 폴더로 분리합니다.

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

기사가 마음에 드셨으면 좋겠어요. 더 많은 기계 코딩 문제를 위해 팔로우해주세요.

의견 또는 개선 제안은 언제든 환영합니다 🤗 .

끝!