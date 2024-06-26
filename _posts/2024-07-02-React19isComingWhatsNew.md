---
title: "React 19가 온다, 어떤 최신 기능들이 추가됐을까"
description: ""
coverImage: "/assets/img/2024-07-02-React19isComingWhatsNew_0.png"
date: 2024-07-02 22:04
ogImage: 
  url: /assets/img/2024-07-02-React19isComingWhatsNew_0.png
tag: Tech
originalTitle: "React 19 is Coming, What’s New?"
link: "https://medium.com/stackademic/react-19-is-coming-whats-new-79e2d4b948e4"
---


<img src="/assets/img/2024-07-02-React19isComingWhatsNew_0.png" />

리액트가 마지막으로 릴리즈된 버전은 2022년 6월 14일에 18.2.0 버전으로 나왔어요. 프론트엔드 개발 분야에서 이렇게 인기 있는 기술의 업데이트 속도가 느린 것은 정말 드물죠. 이로 인해 커뮤니티의 일부 중요 인물들 사이에서 불만이 커졌는데, 제 이전 게시물에서 언급했었어요. 관심 있는 분들은 이 글을 클릭해서 확인할 수 있어요: 리액트 커뮤니티 내의 의견 차이.

커뮤니티에서 불만이 계속해서 증가하자, 새로운 리액트 버전의 소식이 드디어 전해졌어요.

리액트 팀은 이제까지 새로운 공식 버전을 출시하지 않은 불만에 대해 응답했어요: 이전에 캐너리 버전에서 출시된 기능들은 서로 상호작용이 필요했기 때문에, 이를 안정 버전에 점진적으로 출시하기 전에 리액트 팀이 함께 작동할 수 있는지 확인하기 위해 많은 시간을 투자해야 했어요.

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

사실, 거의 두 년 동안 공식 버전이 출시되지 않았지만 Canary 버전에서 몇 가지 중요한 업데이트가 있었습니다. use, useOptimistic hook, use client, use server 디렉티브와 같은 업데이트는 리액트 에코시스템을 객관적으로 풍부하게 만든 바 있습니다. 특히 Next.js와 Remix와 같은 풀 스택 프레임워크의 빠른 개발을 촉진했습니다.

리액트 팀은 다음 버전이 19.0.0일 것으로 확인했습니다.

# v19에서 예상되는 새로운 기능 예측

이제 리액트 팀의 최신 소식을 기반으로 공식적으로 버전 19에서 출시될 수 있는 새로운 기능을 미리 살펴보겠습니다.

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

## 자동 메모이제이션

React Conf 2021에서 황 쉔이 소개한 React Forget을 기억하시나요?

![이미지](/assets/img/2024-07-02-React19isComingWhatsNew_1.png)

이제 이것이 여기 있습니다.

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

인스타그램의 제품 환경에서 이미 적용된 컴파일러입니다. React 팀은 이를 Meta의 더 많은 플랫폼에 적용할 계획이며, 향후 오픈소스로 공개할 예정입니다.

새로운 컴파일러를 사용하기 전에는 useMemo, useCallback 및 memo를 사용하여 수동으로 상태를 캐시하여 불필요한 다시 렌더링을 줄였습니다. 이 구현은 가능하지만, React 팀은 이것이 그들이 상상한 이상적인 방식은 아니라고 믿습니다. React가 상태 변경 시 필요한 부분만 자동으로 다시 렌더링할 수 있는 방법을 찾고 있었습니다. 몇 년의 노력 끝에 새로운 컴파일러가 성공적으로 출시되었습니다.

새로운 React 컴파일러는 즉시 사용할 수 있는 기능으로, 개발자들에게 또 다른 패러다임 전환을 제공할 것입니다. 이것은 v19의 가장 기대되는 기능입니다.

재미있는 점은 새로운 컴파일러를 소개할 때 React Forget을 언급하지 않은 React 팀이었는데, 이로 인해 커뮤니티에서 우스꽝스러운 댓글이 나왔습니다: 그들은 React Forget을 잊고 Forget 섹션에서 Forget을 언급하는 것을 잊었습니다.🤣

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

## 작업

React Actions은 React 팀이 클라이언트에서 서버로 데이터를 전송하는 솔루션을 탐색하면서 개발한 것입니다. 이 기능을 사용하면 개발자가 DOM 요소 (예: ``form/``)에 함수를 전달할 수 있습니다:

```js
<form action={search}>
   <input name="query" />
   <button type="submit">Search</button>
</form>
```

action 함수는 동기적 또는 비동기적일 수 있습니다. 작업을 사용할 때 React는 개발자를 위해 데이터 제출의 라이프사이클을 관리합니다. useFormStatus 및 useFormState 훅을 사용하여 현재 폼 작업의 상태와 응답에 액세스할 수 있습니다.

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

클라이언트와 서버 간 상호 작용 시나리오에서 사용할 수 있는 액션은 데이터베이스 변경(데이터 추가, 삭제, 업데이트) 및 양식(예: 로그인 양식, 등록 양식) 구현과 같은 경우에 사용됩니다.

useFormStatus 및 useFormState와 결합하는 것 외에도, 액션은 useOptimistic 및 use server와 함께 사용될 수 있습니다. 이에 대해 자세히 확장하면 길고 복잡한 토론이 될 수 있지만, 곧 발표될 기사를 통해 액션의 상세 사용법을 소개할 예정이니 기대해 주세요.

## 지시문: use client 및 use server

use client와 use server 지시문은 오랜 기간 동안 Canary 버전에서 사용 가능했으며, 이제 v19에서 안정 버전과 함께 사용할 수 있습니다.

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

이전에 Next.js가 이 두 가지 지시문을 제품으로 사용하는 것에 대한 커뮤니티에서의 빈번한 불만이 있었으며, Next.js는 React 생태계를 손상시키고 있는 것으로 비난되었고, React 팀은 Next.js가 불안정한 기능을 시간 초과로 사용할 수 있도록 허용함에 대해 비판을 받았습니다. 그러나 이 우려는 대부분 불필요합니다. 이 두 가지 지시문은 Next.js나 Remix와 같은 풀스택 프레임워크를 위해 설계되어 있으며, React를 사용하여 응용 프로그램을 개발하는 보통 개발자들은 단기적으로는 거의 필요하지 않을 것입니다.

React를 사용하는 경우 풀스택 프레임워크가 아니라면, 이 두 가지 지시문의 목적을 이해하는 것만으로 충분합니다. use client와 use server은 프론트엔드와 서버 사이드 환경 사이의 "분할 지점"을 표시합니다. use client는 패키지 도구에 `script` 태그를 생성하도록 지시하고, use server는 패키지 도구에 POST 엔드포인트를 생성하도록 지시합니다. 이러한 지시문을 사용하면 개발자는 동일한 파일에서 클라이언트 사이드 및 서버 사이드 코드를 작성할 수 있습니다.

## 낙관적 업데이트를 위한 useOptimistic

useOptimistic는 v19에서 안정적으로 표시될 것으로 예상되는 새로운 훅입니다. useOptimistic를 사용하면 비동기 작업(예: 네트워크 요청) 중에 UI를 낙관적으로 업데이트할 수 있습니다. 현재 상태와 업데이트 함수를 매개변수로 받아들이며, 비동기 작업 중에 상태의 복사본을 반환합니다. 현재 상태와 작업의 입력을 받아 작업을 기다리는 동안 사용할 낙관적 상태를 반환하는 함수를 제공해야 합니다.

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

여기에 그 내용이 정의되어 있습니다:

```js
const [optimisticState, addOptimistic] = useOptimistic(state, updateFn);

// 또는
const [optimisticState, addOptimistic] = useOptimistic(
  state,
  // updateFn
  (currentState, optimisticValue) => {
    // 낙관적인 값으로 새로운 상태를 병합 및 반환합니다.
  }
);
```

매개변수

state: 초기 상태 값 및 작업이 진행 중이 아닐 때 반환되는 값입니다.

updateFn(currentState, optimisticValue): addOptimistic에 전달된 현재 상태 및 낙관적인 값을 사용하여 낙관적인 상태 결과를 반환하는 함수입니다. updateFn은 currentState 및 optimisticValue 두 매개변수를 받습니다. 반환 값은 currentState와 optimisticValue의 병합된 값이 됩니다.

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

반환 값

optimisticState: 생성된 낙관적인 상태입니다. 작업이 진행 중일 때는 updateFn이 반환하는 값과 동일하며, 작업이 진행 중이 아닌 경우에는 상태와 동일합니다.
addOptimistic: 낙관적 업데이트 중에 호출되는 디스패치 함수입니다. 낙관적 값 (어떤 유형이든)을 하나의 매개변수로 받아와 상태와 낙관적 값과 함께 updateFn을 호출합니다.

더 자세한 예제:

```js
import { useOptimistic } from 'react';
function AppContainer() {
  const [state, setState] = useState(initialState); // 초기 상태가 있다고 가정합니다
  const [optimisticState, addOptimistic] = useOptimistic(
    state,
    // updateFn
    (currentState, optimisticValue) => {
      // 병합하고 반환: 새로운 상태, 낙관적인 값
      return { ...currentState, ...optimisticValue };
    }
  );
  // 양식 제출과 같은 비동기 작업이 있는 경우를 가정합니다
  function handleSubmit(data) {
    // 실제 데이터 제출 전에 낙관적 업데이트 사용
    addOptimistic({ data: '낙관적인 데이터' });
    // 그런 다음 비동기 작업 수행
    fetch('/api/submit', {
      method: 'POST',
      headers: {
        'Content-Type': 'application/json',
      },
      body: JSON.stringify(data),
      })
      .then(response => response.json())
      .then(realData => {
        // 실제 데이터로 상태 업데이트
        setState(prevState => ({ ...prevState, data: realData }));
    });
  }
  return (
    // optimisticState을 사용하여 UI 렌더링
    <div>{optimisticState.data}</div>
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

useOptimistic은 비동기 작업 중에 예상 결과를 렌더링하고, 작업이 완료되고 상태가 업데이트된 후에 실제 결과(성공 또는 실패)를 렌더링합니다.

## 기타 업데이트

게다가 React 팀 멤버인 Andrew Clark는 2024년에 다음과 같은 변경 사항이 있을 것을 공개했습니다:

🟡 forwardRef → ref는 prop으로: 자식 컴포넌트에서 내부 요소 또는 컴포넌트에 대한 참조를 다룰 때 ref를 일반 prop으로 취급하여 간소화합니다.
🟡 React.lazy → RSC, promise-as-child: 코드 분할과 lazy로딩 기능을 강화합니다.
🟡 useContext → use(Context): Context에 접근하는 새로운 방법을 제공합니다.
🟡 throw promise → use(promise): 비동기 데이터 로딩 처리를 개선합니다.
🟡 `Context.Provider` → `Context`: 컨텍스트 프로바이더 사용을 간편화합니다.

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

그러나 공식 React 웹사이트에서는 아직 이러한 잠재적인 업데이트에 대한 자세한 정보를 제공하지 않았습니다.

# 결론

React는 위대한 비전을 갖고 있습니다. 그들은 프론트엔드와 백엔드 간의 경계를 흐린 채 클라이언트 측 능력에서 우위를 유지하고 커뮤니티의 풀 스택 프레임워크를 위한 인프라를 제공하려고 합니다. 나는 그들의 방식을 매우 존경합니다. 왜냐하면 프론트엔드와 백엔드 간의 장벽을 허물면 프론트엔드 엔지니어들이 경력적으로 더 나아갈 수 있기 때문입니다.

React 19는 후크 소개를 이어받는 또 다른 마일스톤 버전이 될 것입니다. 앤드류 클락은 새로운 버전이 3월이나 4월에 출시될 것이라고 말했습니다. 기대해 봅시다!

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

저에 관하여

Front-End 엔지니어이자 Full-Stack 실무자이며 AI 통합 옹호자입니다.

Next.js 및 Node.js 프로젝트에서 작업하며, 이 분야에 대한 지식을 공유합니다.

Twitter: https://twitter.com/weijunext
Github: https://github.com/weijunext
Blog: https://weijunext.com/

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

# 스택데믹 🎓

끝까지 읽어주셔서 감사합니다. 떠나시기 전에:

- 작가를 박수로 격려하고 팔로우해주세요! 👏
- 팔로우하기: X | LinkedIn | YouTube | Discord
- 다른 플랫폼 방문하기: In Plain English | CoFeed | Venture | Cubed
- 더 많은 콘텐츠는 Stackademic.com에서 확인하세요