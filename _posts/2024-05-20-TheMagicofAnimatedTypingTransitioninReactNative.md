---
title: "리액트 네이티브에서의 애니메이션으로 구현한 타이핑 효과의 매력"
description: ""
coverImage: "/assets/img/2024-05-20-TheMagicofAnimatedTypingTransitioninReactNative_0.png"
date: 2024-05-20 16:23
ogImage: 
  url: /assets/img/2024-05-20-TheMagicofAnimatedTypingTransitioninReactNative_0.png
tag: Tech
originalTitle: "The Magic of Animated Typing Transition in React Native"
link: "https://medium.com/javascript-in-plain-english/the-magic-of-animated-typing-transition-in-react-native-1df5d74b8541"
---


제 웹사이트에 방문해주셔서 감사합니다!

![Click Here](https://miro.medium.com/v2/resize:fit:852/1*nUVr8lekbEbpfkq53IZ2fQ.gif)

당신의 React Native 앱의 사용자 경험에 몇 가지 풍미를 더하고 싶나요? 애니메이션된 타자 효과의 매료되는 세계로 빠져들어보세요! 이 글에서는 글자가 한 글자씩 나타나는 화려한 애니메이션 효과를 생성하는 방법을 살펴볼 것입니다. 이 애니메이션은 사용자에게 매혹적인 타자 경험을 제공합니다.

이 매혹적인 효과를 어떻게 달성하는지 단계별로 코드를 자세히 살펴보면서 시작해봅시다.

<div class="content-ad"></div>

- 초기 설정: 필요한 모듈을 가져와 컴포넌트의 props를 정의하는 것으로 시작합니다.
- 상태 관리: 컴포넌트는 React 훅 (useState 및 useRef)을 사용하여 상태와 참조를 관리합니다. text 상태는 현재 표시된 텍스트를 보유하고, completed는 입력이 완료되었는지를 나타내며, cursorColor는 커서 표시 여부를 관리합니다. 또한 textIndex를 추적하여 다음에 표시할 문자를 파악합니다.
- 스타일링: StyleSheet.create를 사용하여 텍스트의 모양을 사용자 정의하는 스타일을 정의합니다.
- 타이핑 애니메이션: typingAnimation 함수는 주요 애니메이션 논리를 처리합니다. text prop에서 문자를 순차적으로 추가하여 입력하는 것을 모방하여 현재 표시된 텍스트에 문자를 추가합니다. 부드러운 애니메이션을 위해 재귀적 setTimeout이 사용됩니다.
- 커서 애니메이션: cursorAnimation 함수는 깜빡이는 효과를 만들기 위해 커서의 색상을 토글합니다.
- 효과 및 정리: useEffect 훅은 애니메이션 타이밍과 정리를 관리합니다. 컴포넌트가 마운트될 때 타이핑 애니메이션 및 커서 깜박임을 시작하고 언마운트할 때 타이머를 정리합니다.

이제 앱에서 이 컴포넌트를 생성하는 방법을 살펴보겠습니다:

```js
import React, { useRef, useState, useEffect } from 'react';
import { StyleSheet, Text } from 'react-native';

interface AnimatedTypingProps {
  text: string;
  onComplete?: () => void;
}

const AnimatedTyping: React.FC<AnimatedTypingProps> = (props) => {
  // 코드 생략

export default AnimatedTyping;
```

컴포넌트에서 AnimatedTyping을 가져와 원하는 텍스트 및 onComplete 콜백과 함께 사용하면 됩니다.

<div class="content-ad"></div>

```jsx
<AnimatedTyping
    text={query}
    onComplete={handleAnimationDefaultComplete}
/>
```

저희와 함께 이 자극적인 애니메이션 기술을 살펴봐 주셔서 감사합니다! LinkedIn에서 의견과 피드백을 남겨주시면 감사하겠습니다. 연락해서 경험을 공유해주세요. 즐거운 코딩하세요!

# 매우 쉽게 설명하기 🚀

In Plain English 커뮤니티의 일원이 되어 주셔서 감사합니다! 떠나시기 전에:

<div class="content-ad"></div>

- 저자를 응원하고 팔로우하기 잊지 마세요! 👏️️
- 저희를 팔로우하세요: X | LinkedIn | YouTube | Discord | 뉴스레터
- 다른 플랫폼에서도 만나보세요: Stackademic | CoFeed | Venture | Cubed
- 알고리즘 콘텐츠를 다루도록 강요하는 블로깅 플랫폼에 지쳤나요? Differ를 시도해보세요
- PlainEnglish.io에서 더 많은 콘텐츠를 만나보세요