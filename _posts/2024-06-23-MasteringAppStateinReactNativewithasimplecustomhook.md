---
title: "간단한 커스텀 훅을 이용한 React Native 앱 상태 마스터하기"
description: ""
coverImage: "/assets/img/2024-06-23-MasteringAppStateinReactNativewithasimplecustomhook_0.png"
date: 2024-06-23 21:44
ogImage:
  url: /assets/img/2024-06-23-MasteringAppStateinReactNativewithasimplecustomhook_0.png
tag: Tech
originalTitle: "Mastering App State in React Native with a simple custom hook"
link: "https://medium.com/@miknoup/mastering-app-state-in-react-native-with-useappstatecheck-1e00cd5d35d3"
---

# 소개

애플리케이션 상태를 관리하는 것은 견고한 React Native 애플리케이션을 구축하는 중요한 측면입니다. 이 글에서는 애플리케이션 상태 변경을 간소화하기 위해 설계된 useAppStateCheck라는 사용자 정의 React 훅을 탐구해 보겠습니다. 이 훅은 React Native의 AppState 모듈을 활용하여 애플리케이션의 라이프사이클 변경을 추적하고 대응하는 우아한 해결책을 제공합니다.

# useAppStateCheck 훅

useAppStateCheck 훅은 React Native 컴포넌트에 애플리케이션 상태 모니터링을 간편하게 통합할 수 있는 다재다능한 도구입니다. 이를 사용해보겠습니다.

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
// useAppStateCheck.tsx

import { useCallback, useEffect } from "react";
import {
  NativeEventSubscription,
  AppState,
  AppStateStatus,
} from "react-native";

interface useAppStateCheckProps {
  setAppStateStatus: React.Dispatch<React.SetStateAction<AppStateStatus>>;
}

export default function useAppStateCheck(props: useAppStateCheckProps) {
  const { setAppStateStatus } = props;

  const handleAppStateChange = useCallback(
    async (nextAppState: AppStateStatus) => {
      setAppStateStatus(nextAppState);
    },
    [setAppStateStatus]
  );

  useEffect(() => {
    let eventListener: NativeEventSubscription;
    eventListener = AppState.addEventListener("change", handleAppStateChange);

    return () => {
      eventListener && eventListener.remove();
    };
  }, [handleAppStateChange]);
}
```

# 애플리케이션에 통합

useAppStateCheck 훅을 사용하는 것은 매우 간단합니다. React Native 컴포넌트에 신속하게 통합하는 방법을 보여드리겠습니다.

App.tsx에서 처음에 AppStateStatusContext에서 사용되는 상태를 선언한 곳에서, useEffect 종속성으로 appStateStatus를 사용할 수 있습니다:

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

```jsx
// App.tsx

import React, { useState, useEffect, useCallback, useContext } from "react";
import { AppStateStatusContext } from "./AppStateStatusContext"; // 앱 상태 관리를 위한 컨텍스트가 있다고 가정합니다

const App = () => {
  const [appStateStatus, setAppStateStatus] = useState(undefined);
  useAppStateCheck({ setAppStateStatus });

  const onAppStateChange = useCallback(() => {
    switch (appStateStatus) {
      case "active":
        // 앱이 활성 상태인 경우 수행해야 할 작업
        break;
      case "background":
        // 앱이 백그라운드 상태인 경우 수행해야 할 작업
        break;
      case "inactive":
        // 앱이 비활성 상태인 경우 수행해야 할 작업
        break;
      default:
        // 다른 앱 상태 시나리오 처리
        break;
    }
  }, [appStateStatus]);

  useEffect(() => {
    onAppStateChange();
  }, [onAppStateChange]);

  return (
    <AppStateStatusContext.Provider
      value={{ appStateStatus, setAppStateStatus }}
    >
      {/* 여러분의 앱 컴포넌트들 */}
    </AppStateStatusContext.Provider>
  );
};
```

다른 앱 부분에서 사용하기 위해서는 AppStateStatusContext로부터 값 가져오기만 하면 됩니다:

```jsx
// Example.page.tsx

import { useContext, useEffect, useCallback } from "react";
import AppStateStatusContext from "../../contexts/AppStateStatusContext";

export const ExamplePage: React.FC = () => {
  const { appStateStatus } = useContext(AppStateStatusContext);

  const onAppStateChange = useCallback(() => {
    switch (appStateStatus) {
      case "active":
        // 앱이 활성 상태인 경우 수행해야 할 작업
        break;
      case "background":
        // 앱이 백그라운드 상태인 경우 수행해야 할 작업
        break;
      case "inactive":
        // 앱이 비활성 상태인 경우 수행해야 할 작업
        break;
      default:
        // 다른 앱 상태 시나리오 처리
        break;
    }
  }, [appStateStatus]);

  useEffect(() => {
    onAppStateChange();
  }, [onAppStateChange]);

  return <View></View>;
};
```

# 앱 상태에 맞게 컨텍스트 설정하기

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

앱 상태에 대한 보다 포괄적인 컨텍스트를 제공하기 위해 컨텍스트 제공자를 사용하는 것을 고려해보세요. 위 예제에서는 AppStateStatusContext를 사용하여 앱 상태 및 setAppStateStatus를 중앙 집중식으로 캡슐화합니다.

```js
// AppStateStatusContext.tsx
import * as React from 'react';
import { AppStateStatus } from 'react-native';

const AppStateStatusContext = React.createContext<{
    appStateStatus: AppStateStatus;
    setAppStateStatus: React.Dispatch<React.SetStateAction<AppStateStatus>>;
}>({
    appStateStatus: 'active',
    setAppStateStatus: () => {},
});

export default AppStateStatusContext;
```

# 결론

React Native에서 앱 상태를 숙달하는 것은 반응성 및 안정성 있는 애플리케이션을 구축하는 데 필수적입니다. useAppStateCheck 사용자 정의 React Hook을 사용하면 앱 상태 변경 처리 과정을 간소화하여 코드베이스가 보다 깔끔하고 유지 관리하기 쉬워집니다.

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

저는 이 접근 방식을 적용한 사용 사례 중 하나로, 앱이 백그라운드로 전환되고 다시 활성 상태로 돌아올 때 기기 시간이 수동으로 변경되었는지 확인하는 것이 있습니다.

전체 구현을 자유롭게 살펴보시고 프로젝트의 구체적인 요구 사항에 맞게 사용자 정의할 수 있습니다. 즐거운 코딩하세요!
