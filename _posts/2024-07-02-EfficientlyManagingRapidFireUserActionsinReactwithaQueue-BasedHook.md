---
title: "큐 기반 훅으로 빠른 사용자 액션을 효율적으로 관리하는 방법 React"
description: ""
coverImage: "/assets/img/2024-07-02-EfficientlyManagingRapidFireUserActionsinReactwithaQueue-BasedHook_0.png"
date: 2024-07-02 21:59
ogImage: 
  url: /assets/img/2024-07-02-EfficientlyManagingRapidFireUserActionsinReactwithaQueue-BasedHook_0.png
tag: Tech
originalTitle: "Efficiently Managing Rapid Fire User Actions in React with a Queue-Based Hook"
link: "https://medium.com/@ronitbhatia98/efficiently-managing-rapid-fire-user-actions-in-react-with-a-queue-based-hook-16d88dccdf62"
---


내 React Native 기반 소셜 네트워킹 앱에서 프로필 페이지를 구축하면서 흥미로운 문제를 마주했어요:

만약 사용자가 좋아요 버튼을 스팸하는 경우 API 엔드포인트 남용을 어떻게 관리할까요?

![이미지](https://miro.medium.com/v2/resize:fit:1400/1*_KQNCttwub7NvtykSKLv3g.gif)

- 사용자가 좋아요 버튼을 스팸하면 취약해집니다.
- 버튼을 누를 때마다 API 호출이 발생하여 서버에 사용자의 작업을 알리고, 서버에서는 데이터베이스 상태를 업데이트하는 DB 호출이 이어집니다.
- 첫눈에는 무해한 호출이라고 생각되지만 반복해서 발생할 경우 앱의 캐싱 아키텍처에 심각한 차질을 일으킬 수 있습니다. 효율적으로 관리되지 않은 경우 데이터베이스 상태 업데이트로 인해 여러 번 캐시를 몇 초 내에 여러 번 재설정해야 할 수도 있습니다.

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

# 문제와 UI 요구 사항을 명확히 하기

이 문제를 효율적으로 해결하기 위해 필요한 모든 요구 사항을 기록해 봅시다. 최종 사용자는 부드러운 UX를 필요로 하고, 서버는 최소한의 부하를 원합니다.

## UX 요구 사항

- '좋아요' 동작은 앱에 즉시 반영되어야 합니다. 서버의 응답이 늦게 올 경우에도, 게시물을 좋아하는 경우 하트 아이콘이 즉시 채워지고, 싫어하는 경우 비워져야 합니다.
이는 일반적인 API 호출 흐름과는 다릅니다. 일반적으로 서버 응답을 기다리는 동안 로더를 표시하고 동작 버튼을 비활성화하는 것이 일반적이지만, 이 접근 방식을 여기서 사용하면 UX를 망치게 됩니다. 전체 경험을 느릿느릿하게 만들어 버릴 수 있습니다.
- UI 상태는 결국 서버의 상태와 일관되어야 합니다. 즉, API 호출이 성공하지 않는 경우(사용자의 조치) 좋아요 아이콘과 좋아요 수가 표시되어야 합니다.
- 좋아요 및 싫어요 동작은 번갈아가며 일어납니다. 따라서 서버의 5xx 응답으로 인해 API 호출 중 하나가 실패할 경우 우리는 이 사실을 우리의 이점으로 활용할 수 있습니다.

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

## 서버 요구 사항

- 서버 호출은 최소화되어야 합니다.
- 당신이 좋아하지 않은 게시물에 싫어요를 표시할 수 없습니다. 시도하면 400 BAD REQUEST 응답이 반환됩니다.
- 당신이 이미 좋아한 게시물을 좋아할 수 없습니다. 시도하면 다시 한 번 400 BAD REQUEST 응답이 반환됩니다.
- 서버는 요청을 처리하는 데 어려움을 겪으면 5xx 응답을 보낼 수 있습니다.

# 용어

더 나아가기 전에, 다음 용어의 의미를 명확하게 설명하고 싶습니다. 만약 명백하지 않다면:

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

- 클라이언트 상태 또는 UI 상태는 사용자에게 표시되는 상태입니다. 이에는 "좋아요" 아이콘(좋아요 시 채워지고 좋아요 취소 시 비워짐)와 좋아요 수가 포함됩니다.
- 서버 상태는 데이터베이스의 상태로, 우리 데이터의 실제 상태입니다.

서버 상태 업데이트(API 호출)는 클라이언트 상태 업데이트(React 상태 업데이트)보다 수십 배 시간이 더 소요됩니다. 
우리의 목표는 클라이언트 상태 업데이트가 서버 상태 업데이트로 인해 느려지지 않으면서 이 두 상태를 최종적으로 조율하는 것입니다.

# 가능한 해결책

이 문제에 대한 전통적인 해결책에는 UI에서 디바운싱 및 함수 쓰로틀링 또는 서버에서 API의 속도 제한이 포함됩니다.
이 큐 기반 접근 방식에서 나의 생각 프로세스는 API 호출이 수백 밀리초가 걸릴 수 있다는 사실을 활용하고 호출을 자연적인 디바운서로 사용하는 것이었습니다. API의 속도 제한도 여전히 유효합니다.

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

# 최종 솔루션

잠재적인 해결책에 대한 심사숙고 끝에 사용자의 작업(좋아요/싫어요)을 유지하는 React 훅을 만들었습니다. pushToQueue 메서드를 노출하여 작업을 추가할 수 있게 했습니다.

- 사용자 상태는 즉시 업데이트됩니다.
서버 상태를 업데이트하는 네트워크 호출은 큐에 추가됩니다.
- 비어 있지 않은 큐가 감지되면 앱은 처리 상태로 전환됩니다. 큐를 파싱하여 최종 작업(좋아요/싫어요)이 결정되고, 서버에 해당 작업을 알리는 API 호출이 발생합니다.
- 이제 API 호출은 여러 밀리초가 걸릴 수 있습니다.
사용자는 통화 중에 동일한 버튼에 추가 상호작용을 실행할 수 있습니다. 이 작업들은 나중에 처리하기 위해 큐에 추가됩니다. (다시 말해, 클라이언트 상태는 이러한 상호작용 발생 즉시 업데이트됩니다. 큐가 처리된 후에 다시 업데이트되며, 서버 상태를 반영합니다.)
- API 호출이 완료되면 큐에 처리할 추가 항목이 있는지 확인합니다. 존재하는 경우 전체 프로세스가 반복됩니다. 그렇지 않으면 처리가 완료되고 클라이언트 상태가 그에 맞게 업데이트됩니다.
API 호출에 의한 지연은 자연스러운 디바운서 역할을 합니다.

수고하셨습니다. 이제 코드를 빌드해 봅시다!

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

# 구현

## 앱 초기화

먼저, 좋아요 아이콘과 좋아요 수를 표시하는 간단한 앱을 만들어봅시다:

그 상태에는 다음이 저장됩니다:

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

- 게시물이 좋아요를 받았는지 아닌지를 저장하는 부울 값
- 좋아요 수 (숫자).

```js
const [state, setState] = useState({
  liked: false,
  likeCount: 0,
});
```

하트 아이콘과 텍스트를 사용하여 정보를 표시합니다:

```js
return (
    <div className="App">
      <div id="heart_container">
        /*Fill the icon if liked, else no fill (transparent)*/
        <HeartIcon
          height={96}
          width={96}
          stroke="#aaa"
          fill={liked ? "rgb(207, 102, 121)" : "transparent"}
        />
      </div>
      /* 좋아요 수를 표시 */
      <p>{likeCount}</p>
    </div>
  );
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

다음으로, 좋아요/싫어요를 설정하는 간단한 onClick Handler를 정의합니다:

- 좋아요는 boolean 값을 true로 설정하고 좋아요 수를 증가시킵니다.
- 싫어요는 boolean 값을 false로 설정하고 좋아요 수를 감소시킵니다.

```js
const like = useCallback(() => {
    setState((prevState) => ({
      liked: true,
      likeCount: prevState.likeCount + 1,
    }));
  }, []);

  const dislike = useCallback(() => {
    setState((prevState) => ({
      liked: false,
      likeCount: prevState.likeCount - 1,
    }));
  }, []);

  const onClick = useCallback(() => {
    if (liked) {
      dislike();
    } else {
      like();
    }
  }, [liked]);
```

스타일링을 조금 추가하면 앱이 이렇게 보입니다:

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

마지막으로, 훅을 설정해 봅시다.

## 훅 — Props

우리의 훅은 다음과 같은 프롭스를 받을 것입니다:

- postId: 숫자: 좋아요를 눌러야 할 게시물의 ID. 이 ID와 결합된 API 호출이 실행될 것입니다.
- mutate(postId: number, liked: boolean, postId: number) =` Promise`boolean` :
API를 호출하기 위한 책임을 지는 비동기 함수입니다. 이 함수는 응답의 상태를 부울 값으로 반환해야 합니다 (호출이 성공했는지 여부).
모든 네트워크 호출과 마찬가지로, 오류가 발생할 수도 있습니다.
- setLikedState: (liked: boolean) =` React.SetStateAction:
UI 상태를 업데이트하는 함수입니다.
- onError?: (error: Error) =` unknown: 만약 API 호출이 오류를 발생시킬 경우 실행할 콜백 함수입니다.

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

## The Hook — 큐 저장하기

액션의 큐를 React ref에 저장할 것입니다. 그 값들은 가능한 액션인 '좋아요' 또는 '싫어요'일 것입니다.

```js
const queueRef = useRef<("like" | "dislike")[]>([]);
```

마지막으로, 현재 큐가 처리 중인지 여부를 알려주는 상태를 저장할 것입니다.

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
const [processing, setProcessing] = useState(false);
```

최종 작업이 결정되고 API 호출이 이루어질 때 `processing`이 true로 설정됩니다. 큐가 비어있을 때 (처리된 경우) false로 설정됩니다.

## 훅 — 큐 처리하기

다음은 우리 큐의 작동 메커니즘입니다:

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

- 처리 상태를 True로 설정합니다.
- 원본 큐를 변경하지 않기 위해 큐의 복사본을 만듭니다.
- 복사본을 이용하여 최종 동작을 결정하기 위해 반복합니다 (좋아요, 싫어요 또는 아무 작업도 안 함):

```js
setProcessing(true);
const initialQueueLength = queueRef.current.length;
// 처리 중에 참조를 변경하지 않기 위한 참조의 복사본.
const queueCopy = [...queueRef.current];
let finalAction: "like" | "dislike" | "none" = "none";
while (queueCopy.length) {
    const type = queueCopy.shift();
    if (type === "like") {
      finalAction = finalAction === "dislike" ? "none" : "like";
    } else {
      finalAction = finalAction === "like" ? "none" : "dislike";
    }
}
```

이제 결정을 내려야 합니다 — API를 호출해야 할까요?
최종 동작이 아무 작업도 안 함으로 결정된 경우 API 호출이 필요하지 않습니다. 다음을 수행합니다:

- 초기 처리된 항목의 수( initialQueueLength)만큼 참조를 잘라냅니다.
- 큐가 비어 있는지 확인합니다. 루프가 실행 중에 사용자가 좋아요 버튼을 눌렀다면 큐는 비어 있지 않을 수 있습니다.
- 큐가 비어 있지 않으면 프로세스를 반복하고 processQueue 함수를 다시 호출합니다. 그렇지 않으면 함수를 종료합니다.

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
if (finalAction === "none") {
  queueRef.current =
    queueRef.current.slice(initialQueueLength);
  if (queueRef.current.length > 0) {
    await processQueue();
  }
  setProcessing(false);
  return;
}
```

만일 최종 작업이 '좋아요' 또는 '좋아요 취소'인 경우, API를 호출합니다. 호출이 성공했는지 여부를 추적하는 execSuccess 변수를 유지합니다.

```js
let execSuccess = false;
try {
    execSuccess = await mutate(postId, finalAction === "like");
} catch(error) {
    execSuccess = false;
    onError(error);
}
```

마지막으로, 큐에 더 이상 항목이 없다면 서버 응답에 따라 UI 상태를 업데이트해야 합니다. 
API 호출이 실패했고 큐가 비어 있지 않으면 큐의 첫 번째 요소를 삭제할 수 있습니다. 작업은 대체로 번갈아가며 발생하기 때문입니다.
예: 만일 '좋아요' 호출이 실패했다면 큐에 추가된 다음 작업은 '좋아요 취소'일 것입니다. 따라서 우리는 처리되기 전에 큐에 추가된 '좋아요 취소' 작업을 삭제할 수 있습니다. '좋아요' 작업이 실패하면 UI 상태에선 '좋아요 취소' 작업과 동일하기 때문입니다.


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
마지막으로, 푸시 함수를 정의할 것입니다.

이 함수는 like 또는 dislike와 같은 액션을 인수로 받아서 간단히 큐에 푸시합니다.
큐가 처리 중이 아닌 경우(처리 상태 변수가 false인 경우), processQueue 함수를 호출합니다.
우리의 훅은 이 함수를 반환합니다.

const pushToQueue = useCallback(
  (action: "like" | "dislike") => {
    const newQueue = [...queueRef.current, action];
    queueRef.current = newQueue;
    if (!processing) processQueue();
  },
  [processing]
);

return [pushToQueue];

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

# Hook 사용 및 앱 테스트하기

## API

우리는 코드를 테스트하기 위해 모의 API를 사용할 것입니다. 이 API는 20%의 실패 비율을 가지며, 성공하면 true를 반환하고 그렇지 않으면 false를 반환합니다. 500ms의 인위적 지연이 발생할 것입니다.

const API_DELAY = 500;
const responses = [true, true, false, true, true];
let idx = 0;
const postLikeMapping = new Map<number, boolean>();
export const mockedApiCall = async (postId: number, liked: boolean) => {
  // 가짜 지연
  await new Promise((resolve) => setTimeout(resolve, API_DELAY));
  if (!postLikeMapping.get(postId) && !liked) {
    throw new Error("좋아요 표시하지 않은 게시물에 싫어요를 표시할 수 없습니다.");
  }
  if (postLikeMapping.get(postId) && liked) {
    throw new Error("이미 좋아요한 게시물에 또 다시 좋아요를 표시할 수 없습니다.");
  }
  //   80% 성공 확률 시뮬레이션
  if (responses[idx++ % responses.length]) {
    postLikeMapping.set(postId, liked);
    return true;
  }
  return false;
};

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

우리 앱은 사용자가 좋아요 또는 싫어요 버튼을 누를 때마다 이 함수를 호출합니다.

## 우리 앱에서 Hook 사용

마지막으로, 우리가 만든 훅을 앱에 통합합니다.

- 좋아하는 상태를 React 리듀서로 변경합니다.
- 동일한 작업이 연속적으로 호출될 때(한 번은 UI에서 즉시 호출되고 다른 한 번은 훅이 성공적인 API 호출 후 호출될 때), 리듀서는 상태를 변경하지 않습니다.
- UI가 상태를 즉시 업데이트하지만 API 호출이 실패하여 상태가 재설정될 때 작업에 의해 상태가 업데이트됩니다.
- Hook을 소비하고 사용자 상호작용 시 훅이 노출하는 pushToLikeQueue 메서드를 호출합니다.

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

// Reducer 코드
const likedReducer: Reducer<{ liked: boolean; likeCount: number }, boolean>
= (
    state,
    liked
  ) => {
  const { likeCount } = state;
  if (liked === state.liked) {
    return state;
  }
  return {
    liked,
    likeCount: liked ? likeCount + 1 : likeCount - 1,
  };
};

// Hook 사용하기
const [pushToLikeQueue] = useLikes({
  setLikedState: dispatchLiked,
  postId: 1,
  async mutate(postId, liked) {
    return await mockedApiCall(postId, liked);
  },
});

const like = useCallback(() => {
  dispatchLiked(true); // 바로 UI 상태 업데이트
  pushToLikeQueue("like"); // API 호출을 대기열에 추가
}, [pushToLikeQueue]);

const dislike = useCallback(() => {
  dispatchLiked(false); // 바로 UI 상태 업데이트
  pushToLikeQueue("dislike"); // API 호출을 대기열에 추가
}, [pushToLikeQueue]);

## Logic 테스트

우리의 mocked API 코드는 매 다섯 번째 시도마다 실패할 것입니다. 아래에서 라이브 데모를 확인할 수 있습니다: 왼쪽에는 UI가 있고, 오른쪽에는 뒷단에서 무슨 일이 벌어지고 있는지가 나와 있습니다.

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

에헤! 이제 신뢰할 수 있는 좋아요 / 싫어요 메커니즘이 생겼습니다. 이 방법은 API 호출을 최소화하고 UI를 반응적으로 유지하며 최신 상태로 유지합니다!

이 훅을 만드는 PR을 볼 수 있습니다.
이 앱을 데모하려면 다음 단계를 따라주세요:

- Playgrounds 리포지토리를 복제하세요.
- react/use-likes-hook-demo로 이동하십시오.
- 폴더에서 npm install을 실행하십시오.
- 폴더에서 npm start를 실행하십시오.
- 앱을 사용해보세요.