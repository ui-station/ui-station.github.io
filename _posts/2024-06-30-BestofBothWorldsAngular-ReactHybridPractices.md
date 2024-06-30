---
title: "Angular-React 하이브리드 기법 두 기술의 장점 모음"
description: ""
coverImage: "/assets/img/2024-06-30-BestofBothWorldsAngular-ReactHybridPractices_0.png"
date: 2024-06-30 22:27
ogImage: 
  url: /assets/img/2024-06-30-BestofBothWorldsAngular-ReactHybridPractices_0.png
tag: Tech
originalTitle: "Best of Both Worlds: Angular-React Hybrid Practices"
link: "https://medium.com/@davidsergeev973/best-of-both-worlds-angular-react-hybrid-practices-e80e93cf3d09"
---


![이미지](/assets/img/2024-06-30-BestofBothWorldsAngular-ReactHybridPractices_0.png) 

지금까지 프론트엔드 개발자들 사이에서 Angular가 React보다 더 나은지 아니면 그 반대인지에 대한 논쟁이 있었습니다. React 개발자로서 몇 년 동안 내가 옳은 쪽에 있다고 확신하고 있던 중에, 상황이 나를 Angular를 배우도록 강요했죠. 처음에는 정말 어색했고 그 개념들을 비판하기 시작했지만, 몇 주간의 학습 후에 드디어 그것에 익숙해졌습니다. 시간이 지나고 더 많은 지식을 쌓으면서 확신 있는 Angular 개발자가 되었고, 이제 "어떤 프레임워크가 더 나은가"라는 질문에 답할 수 있을 것 같지만, 더 깊이 배우면 배울수록 이 두 독특한 도구를 비교하는 것이 더 어려워집니다. 그러나 이 글의 목적은 이들을 비교하는 것이 아니라 한 프레임워크에서 채택한 실천법을 다른 프레임워크로 재사용하는 경험을 공유하는 것입니다.

# HTML 템플릿

React의 컴포넌트는 JSX와 함께 논리가 섞여있어서 매우 엉망이 될 수 있다는 일반적인 불만이 있습니다. 컴포넌트가 커지면, 코드를 읽으려는 누군가에게 덜 이해하기 쉬워집니다. 이것은 Angular 지지자들이 논쟁에서 사용하는 주장 중 하나입니다. 정확한가요? 음, 부분적으로 맞습니다. React는 Angular와 달리 특정한 범위와 규칙을 부과하지 않고 원하는 대로 코드를 작성할 수 있게 해줍니다. 때로는 React의 이 자유가 엉망으로 이어질 수 있습니다.

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

구글 프레임워크를 몇 년 동안 사용한 후에 그 방식에 적응되었고 심지어 그 방식에 대한 동정심까지 키웠어요. 그래서 생각했죠: ‘왜 리액트에서도 똑같은 작업을 할 수 없을까?’ 답은 간단해요: ‘할 수 있어요.’ 이를 실현하기 위해선 우리의 리액트 컴포넌트를 두 개의 파일로 분리하는 규칙을 따르면 돼요. 템플릿과 로직을 각각의 파일에 위치시켜야 해요. 아래의 예시처럼 말이죠.”

```js
// MyComponent.jsx

import { useState } from "react"
import { MyComponentTemplate } from "./my.component.template";

export const MyComponent = () => {
    const [count, setCount] = useState(0);

    const decrement = () => {
        setCount(count - 1);
    }

    const increment = () => {
        setCount(count + 1);
    }

    return <MyComponentTemplate increment={increment} decrement={decrement} count={count}></MyComponentTemplate>
}
```

```js
// MyComponent.template.jsx

export const MyComponentTemplate = ({amount, increment, decrement}) => {
    return <>
        <h1>
            {amount}
        </h1>
        <button onClick={increment}> + </button>
        <button onClick={decrement}> - </button>
    </>
}
```

그리고 여기까지입니다. 어떻게 생각하시나요? :)

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

# 의존성 주입

의존성 주입은 Angular에서 채택된 주요 디자인 패턴 중 하나입니다. 느슨한 결합 및 관심사 분리를 촉진하여 모듈화, 유지 관리성, 테스트 용이성을 향상시킵니다. 서비스는 비즈니스 로직과 UI 구성 요소를 분리하는 훌륭한 방법을 제공합니다. 데이터 가져오기, 상태 관리 또는 기타 작업과 관련된 기능을 캡슐화함으로써 서비스는 구성 요소를 깔끔하고 집중시킵니다. 서비스는 다른 프레임워크에서 잘 작동하지만 React에서는 자연스럽지 않을 수 있습니다. React의 컴포넌트 중심 접근 방식은 종종 개발자가 로직을 직접 처리하도록 유도합니다. 과제는 컴포넌트 간의 간단함과 로직 분리 사이의 적절한 균형을 찾는 데 있습니다. React의 Context API가 해결책을 제공합니다. Context를 생성함으로써 종속성을 명시적으로 props를 통해 전달하지 않고 컴포넌트 트리 전체에서 공유할 수 있습니다.

```js
// ContextProvider.jsx
import { MyService} from '@services/myService';

export const MyServiceInstance = createContext({});

export const MyServiceProvider= ({ children }) => {
  return <MyServiceInstance.Provider value={new MyService()}>{children}
         </MyServiceInstance.Provider>;
};
```

```js
// App.jsx

export const App = () => {
  return (   
    <MyServiceProvider>
      // ...내 앱 콘텐츠
    </MyServiceProvider> 
  );
}
  
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

```js
// MyComponent.jsx

const MyComponent= () => { 
  const [data, setData] = useState(null);
  const myService = useContext(MyServiceInstance);
  
  useEffect(() => {
    if(myService) getData();
  }, [myService])

  const getData = () => {
    myService.getData().then(res => setData(res));
  }

  return <MyComponentTemplate data={data} />
}
```

# 타입스크립트

이게 꽤 명백한 것 같아요. 그러나 저는 종종 타입스크립트를 사용하지 않는 React 프로젝트를 만납니다. 그렇게 되면 Angular 개발자들이 React 앱이 엉망이라고 불평하는 이유가 하나 더 생기게 되는데, 저는 완전히 동의해요. 타입스크립트는 코드를 더 안전하고 이해하기 쉬운 방법을 제공합니다.

# 프로미스 또는 옵저버블?

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

그 질문을 여러 번 받아봤어요. Angular는 React가 프로미스를 사용하는 용도로 사용하는 것과 같은 목적을 위해 옵저버블을 사용하지만, 저는 이 두 도구를 비교하는 대신 둘 다 사용할 수 있다고 생각해요. 둘 다 장단점이 있지만, 각 도구가 만들어진 목적에 맞게 사용하면 개발 시간과 경험을 향상시킬 수 있을 거예요.

React는 프로미스 기반이라는 사실은 알려진 사실이에요. Angular는 옵저버블 기반이죠. 두 도구를 함께 사용할 때 마주치는 문제 중 하나는 때로는 프로미스를 반드시 사용해야 할 때에도 Angular가 옵저버블을 사용하도록 강요하고, 한 번 호출할 메소드에 불필요한 구독을 생성해야 한다는 것이었어요. 그 말인 즉슨 구독을 해제해야 한다는 것이죠. 이것은 너무 많은 보일러플레이트 코드라고 제 생각에는요. 반면에 React 코드에서는 옵저버블을 자주 사용하지 않는데, 대신에 해결책을 만들어내고, 여러 "useEffect" 훅을 사용하다 보면 엉망이 될 수도 있어요. "useEffect"는 아주 유용한 도구이지만 때로는 옵저버블이 필요할 수도 있어요. RxJs는 Angular 애플리케이션에서 일반적으로 사용되는 강력한 라이브러리이며, React와도 잘 어울린다고 해요.

```js
// MyService.ts 조각
export class MyService {
  isReady = new BehaviorSubject<boolean>(false);
    
  constructor() {
    this.connectToSocket();
  }

  private connectToSocket = () => {
    this.socket.connect().subscribe((message) => {
      if(message.status === 'READY') {
        this.isReady.next(true);
      }
    })
  }
}
```

```js
// MyComponent.tsx 조각

const myService= useContext(MyServiceInstance);  

useEffect(() => {
  const subscription = myService.isReady.subscribe((isReady: boolean) => {
    if (isReady) {
      // ...할 일
    }
  });

  return () => subscription.unsubscribe();
}, [myService]);
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

# 결론

소프트웨어 개발은 문제를 다양한 방법으로 해결할 수 있는 풍부한 세계입니다. 어떤 방법을 선택할지는 당신의 몫입니다. 이 글에서는 내게 잘 작용한 방법을 공유했고, 누군가에게 유용할 수도 있다고 생각했습니다. React와 Angular를 비교하는 것은 그리 의미가 없습니다. 둘 다 놀라울 정도로 좋으니까요. 다음에 누군가가 어떤 것이 더 나은지 물어보면, '둘 다!' 라고 대답할 거예요. 여러분은 어떠세요?