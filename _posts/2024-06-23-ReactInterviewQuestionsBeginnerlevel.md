---
title: "React 초보자를 위한 필수 인터뷰 질문 10가지"
description: ""
coverImage: "/assets/img/2024-06-23-ReactInterviewQuestionsBeginnerlevel_0.png"
date: 2024-06-23 21:12
ogImage: 
  url: /assets/img/2024-06-23-ReactInterviewQuestionsBeginnerlevel_0.png
tag: Tech
originalTitle: "React Interview Questions (Beginner level)"
link: "https://medium.com/@sadrulvala.rayeen/react-interview-questions-beginner-level-3d868b846141"
---


아래는 다음 면접에서 만날 수 있는 초보자 수준의 React 인터뷰 질문 몇 가지입니다. 행운을 빕니다, 이 자료가 면접 준비에 도움이 되기를 바랍니다.

# React란 무엇인가요?

React는 사용자 인터페이스를 구축하기 위한 JavaScript 라이브러리로, 컴포넌트 기반 아키텍처에 초점을 맞추고 성능을 위한 가상 DOM, 렌더링을 위한 JSX 구문 및 일방향 데이터 흐름을 제공합니다. 효율적인 대화형 웹 애플리케이션을 만드는 데 널리 사용됩니다.

<div class="content-ad"></div>

웹 및 모바일 앱의 뷰 레이어를 구성 요소 기반의 선언적 방식으로 처리하는 데 사용됩니다.

## 리액트의 주요 특징은 무엇인가요?

- 컴포넌트 기반 아키텍처.
- 효율적 렌더링을 위한 가상 DOM.
- 선언적 UI 구문을 위한 JSX.
- 일방향 데이터 흐름.
- 함수형 컴포넌트를 위한 Hooks.
- Redux 및 React Router와 같은 라이브러리를 갖춘 풍부한 생태계.

## JSX란 무엇인가요?

<div class="content-ad"></div>

JSX는 JavaScript XML의 약자입니다. JavaScript 구문을 확장하여 개발자가 JavaScript 내에서 HTML과 유사한 코드를 작성할 수 있게 해줍니다. JSX를 사용하면 React 애플리케이션에서 DOM 구조를 생성하고 조작하는 작업이 쉬워집니다.

- JavaScript와 HTML을 결합: JSX를 사용하면 JavaScript와 HTML 요소를 한 파일 내에서 함께 작성할 수 있습니다.
- 구문 확장: React.createElement(component, props, …children) 함수 호출에 대한 구문적 설탕을 제공합니다.
- 컴파일 시 변환: 빌드 시 Babel과 같은 도구를 사용하여 JSX 코드가 일반적인 JavaScript 객체로 변환됩니다.

위 예시에서 `h1`Hello, JSX!`/h1`은 JSX 구문으로, 내부적으로 React.createElement(`h1`, null, `Hello, JSX!`)으로 변환됩니다. 이를 통해 React 애플리케이션에서 UI 구성 요소를 작성하고 시각화하는 프로세스가 간소화됩니다.

<div class="content-ad"></div>

# 리액트 Fragment란 무엇인가요?

리액트 Fragment는 DOM에 추가적인 노드를 생성하지 않고 여러 자식 요소를 그룹화하는 방법을 제공합니다. 이를 통해 컴포넌트의 렌더 메서드에서 여러 요소를 반환할 수 있으며 `div` 또는 `span`과 같은 컨테이너 요소로 감싸지 않아도 됩니다. Fragment는 리액트 16.2에서 소요량이 적은 구문으로 요소를 그룹화하는 데 도입되었습니다.

# 컴포넌트와 엘리먼트의 차이는 무엇인가요?

- 정의: 컴포넌트는 옵션으로 입력(props)을 받아들이고 리액트 엘리먼트를 반환하는 JavaScript 함수 또는 클래스입니다.
- 목적: 재사용 가능한 UI 조각을 정의합니다.
- 종류: 함수를 사용하는 Functional 컴포넌트와 ES6 클래스를 사용하는 Class 컴포넌트가 있습니다.
- 사용: 컴포넌트는 자체 상태를 관리하며 (훅 또는 클래스 컴포넌트의 setState를 사용하여) 라이프사이클을 관리합니다.

<div class="content-ad"></div>

```js
function Welcome(props) {
  return <h1>Hello, {props.name}</h1>;
}
```

- 정의: 엘리먼트는 리액트 컴포넌트의 일반 객체 표현입니다.
- 목적: 화면에 표시하고 싶은 것을 설명합니다.
- 생성 방법: JSX 구문 또는 React.createElement() 함수를 사용하여 만듭니다.
- 불변성: 엘리먼트는 변하지 않으며 특정 시점의 UI를 나타냅니다.

```js
const element = <h1>Hello, world!</h1>;
```

# 리액트에서 key란 무엇인가요?

<div class="content-ad"></div>

React.js에서 key 속성은 각 요소나 컴포넌트에 고유한 식별자를 제공하는 특별한 속성입니다. 배열이나 반복 가능한 자식 목록에서 각 요소나 컴포넌트에 고유한 식별자를 제공하는 특별한 속성입니다. 이는 React가 내부적으로 구성 요소의 UI를 효율적으로 관리하고 업데이트하는 데 사용됩니다. 목록에 항목이 추가, 제거 또는 재배열 될 때.

key의 목적:

요소 식별: 배열에서 여러 요소를 렌더링하거나 구성 요소를 반복하는 경우 React는 항목 간 차이를 파악하기 위해 키를 사용합니다. 이를 통해 React는 변경된 항목, 추가된 항목 또는 제거된 항목을 결정할 수 있습니다.

조정 최적화: React는 조정 프로세스(차이 알고리즘) 중에 키를 사용하여 DOM 업데이트를 최소화합니다. 각 항목에 안정적인 식별자가 있기 때문에 React는 UI를 효율적으로 업데이트할 수 있으며 변경되지 않은 구성 요소를 다시 렌더링하지 않거나 구성 요소 상태를 잃지 않고 업데이트할 수 있습니다.

<div class="content-ad"></div>

# React에서의 '상태'란 무엇인가요?

React에서 '상태'는 컴포넌트가 내부 데이터를 추적할 수 있도록 하는 내장 객체입니다. 이는 컴포넌트의 렌더링과 동작에 영향을 주는 변할 수 있는 데이터를 나타냅니다. 중요한 점은 상태 객체가 변경될 때마다 컴포넌트가 다시 렌더링된다는 것입니다.

# React에서 '속성'이란 무엇인가요?

React에서 '속성' (속성의 약어)은 한 컴포넌트에서 다른 컴포넌트로 데이터를 전달하는 방법입니다. 이는 JavaScript의 함수 인수나 다른 프로그래밍 언어의 매개변수와 유사합니다. 속성은 읽기 전용이며 컴포넌트를 보다 동적이고 재사용 가능하게 만들어 다른 값으로 구성할 수 있도록 돕습니다.

<div class="content-ad"></div>

부모 컴포넌트에서 자식 컴포넌트로 전달되는 데이터입니다. 이들은 변경할 수 없으며(read-only), 컴포넌트를 사용자 정의하고 재사용할 수 있게 합니다.

부모 컴포넌트가 자식 컴포넌트로 name 속성을 전달하는 것을 고려해보세요:

```js
// ParentComponent.jsx
import React from 'react';
import ChildComponent from './ChildComponent';

function ParentComponent() {
  const name = "Alice";

  return (
    <div>
      <ChildComponent name={name} />
    </div>
  );
}

export default ParentComponent;
```

<div class="content-ad"></div>

```jsx
// ChildComponent.jsx
import React from 'react';

function ChildComponent(props) {
  return <p>안녕, {props.name}!</p>;
}

export default ChildComponent;
```

- ParentComponent: ChildComponent를 렌더링하고 이름 prop에 "Alice" 값을 전달합니다.
- ChildComponent: prop로 전달된 이름( `props.name` )을 받아 해당 prop을 사용하여 인사말을 표시합니다(안녕, 'props.name'!).

- 이름은 ParentComponent에서 ChildComponent로 전달된 prop입니다.
- Props를 사용하면 ParentComponent가 전달한 내용에 따라 ChildComponent가 동적으로 다른 이름을 표시할 수 있습니다.

# 상태(state)와 프롭스(props)의 차이점은 무엇인가요?

<div class="content-ad"></div>

- 컴포넌트 자체에서 내부적으로 관리됩니다.
- 클래스 컴포넌트에서 setState() 메서드를 사용하거나 함수형 컴포넌트에서 Hooks를 사용하여 업데이트할 수 있습니다.
- 변경사항이 컴포넌트 및 하위 컴포넌트를 다시 렌더링합니다.
- 가변적입니다 (컴포넌트 내에서 수정 가능).
- 내부 상태 및 시간이 지남에 따라 변경될 수 있는 데이터를 관리하는 데 사용됩니다.
- 로컬 상태 관리와 효과적으로 사용함으로써 컴포넌트의 재사용성을 증가시킬 수 있습니다.

- 상위 컴포넌트에서 하위 컴포넌트로 전달됩니다.
- 수신 컴포넌트 내에서 읽기 전용(불변)입니다.
- 컴포넌트를 구성하고 상위 컴포넌트로부터 데이터를 제공하기 위해 사용됩니다.
- 컴포넌트가 다양하게 구성될 수 있어 재사용성이 향상됩니다.
- 수신 컴포넌트에서 수정할 수 없습니다.
- 컴포넌트 계층구조에서 데이터 및 콜백을 전달하는 데 사용됩니다.

# React에서 prop drilling이란 무엇인가요?

React에서 prop drilling은 상위 수준 컴포넌트에서 하위 수준 컴포넌트로 props가 중간에 위치한 컴포넌트를 통해 전달되는 프로세스를 의미합니다. 데이터가 여러 수준의 중첩된 컴포넌트로 전달되어야 하지만 일부 중간 컴포넌트가 실제로 데이터를 사용하지 않을 때 발생합니다.

<div class="content-ad"></div>

프롭 드릴링은 React에서 일반적으로 사용되는 패턴으로, 프롭스가 데이터가 필요한 깊게 중첩된 자식 컴포넌트에 도달하기 위해 여러 수준의 중첩된 컴포넌트를 통해 전달됩니다. 구현하기는 간단하지만 코드 복잡성과 효율성에 영향을 미칠 수 있습니다. React의 Context API나 상태 관리 라이브러리를 사용하면 복잡한 응용 프로그램에서 구성 요소 간 데이터를 관리하고 전달하는 더 깔끔한 솔루션을 제공할 수 있습니다.

**에러 바운더리란 무엇인가요?**

에러 바운더리는 React 컴포넌트로, 자식 컴포넌트 트리의 어디에서든 JavaScript 오류를 잡아서 해당 오류를 기록하고 전체 React 애플리케이션을 충돌하지 않게 대체 UI를 표시합니다. 렌더링 중에 또는 React 컴포넌트의 라이프사이클 메소드 및 생성자에서 발생하는 오류를 관리하고 고급스럽게 처리하는 데 사용됩니다.

**제어 컴포넌트와 비제어 컴포넌트의 차이점은 무엇인가요?**

<div class="content-ad"></div>

제어 컴포넌트와 비제어 컴포넌트는 React에서 폼 입력 요소를 관리하는 두 가지 다른 방식입니다. 이 둘의 주된 차이점은 상태를 다루고 관리하는 방식에 있습니다, 특히 폼 데이터와 관련하여.

- 상태 처리: 제어 컴포넌트에서는 폼 데이터가 React 상태에 의해 처리됩니다 (일반적으로 상위 컴포넌트 내에서).
- 데이터 흐름: 폼 입력 요소의 값 (`input`, `textarea`, `select`와 같은)은 상태에 의해 제어되며 프롭스로 컴포넌트에 전달됩니다.
- 이벤트 처리: 폼 요소의 변경 사항은 onChange 이벤트 핸들러를 사용하여 처리되며, 상태는 각 변경 시에 업데이트됩니다.

- 상태 처리: 비제어 컴포넌트는 DOM 자체를 통해 폼 데이터를 관리합니다.
- Ref 사용: 참조 (ref)는 주로 DOM 요소에 직접 액세스하여 해당 값들을 가져오기 위해 사용됩니다.
- 이벤트 처리: onSubmit, onClick 등의 이벤트 또는 직접 DOM 이벤트 (element.value)에 액세스하여 폼 데이터를 가져옵니다.

# 가상 DOM이란 무엇인가요?

<div class="content-ad"></div>

가상 DOM(Document Object Model)은 React 및 기타 가상 DOM 기반 라이브러리에서 사용되는 개념으로, 실제 DOM 트리의 가벼운 복사본을 나타냅니다. 이는 효율적으로 UI를 업데이트하기 위해 사용되는 프로그래밍 개념 및 추상화 계층입니다.

- 정의: 가상 DOM은 React에 의해 생성된 실제 DOM 요소 및 속성(속성, 스타일 등)의 JavaScript 표현입니다.
- 목적: UI의 중간 표현으로 사용됩니다. React 구성 요소의 상태나 속성이 변경될 때, React는 실제 DOM이 아닌 가상 DOM을 먼저 업데이트합니다.
- 작동 원리: React는 현재 가상 DOM과 이전 버전을 비교하여 변경된 내용을 식별합니다(조정 프로세스). 이 비교는 실제 브라우저 DOM과 직접 상호 작용하는 것보다 가상 DOM을 조작하는 것이 빠르기 때문에 효율적입니다.
- 효율성: React가 차이점을 식별하면(차별화 알고리즘), 변경된 실제 DOM의 부분만 업데이트합니다. 이렇게 하면 비용이 많이 드는 DOM 조작 작업을 최소화하고 성능을 향상시킬 수 있습니다.
- 예시: 상태를 업데이트하는 React 구성 요소가 있다고 가정해 보겠습니다. React는 가상 DOM을 먼저 업데이트하고, 이전 가상 DOM 상태와 비교한 다음 실제 DOM에 필요한 변경 사항을 적용합니다.

이 면접 질문들이 유익하게 느껴지고 효과적으로 준비하는 데 도움이 되었으면 좋겠습니다! 지금 당장 필요하진 않더라도 북마크해 두시고 자유롭게 사용하세요. 앞으로 있을 면접에서 행운을 빕니다!

더 많은 흥미로운 게시물을 보려면 저를 팔로우해주세요. 제 글쓰기 열정을 키우는 데 도움이 됩니다!