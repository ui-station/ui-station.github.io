---
title: "React Query  React Native 완벽한 협업 이야기"
description: ""
coverImage: "/assets/img/2024-06-22-ReactQueryReactNativeALoveStoryatFound_0.png"
date: 2024-06-22 23:28
ogImage:
  url: /assets/img/2024-06-22-ReactQueryReactNativeALoveStoryatFound_0.png
tag: Tech
originalTitle: "React Query + React Native: A Love Story at Found"
link: "https://medium.com/found-engineering/react-query-react-native-a-love-story-at-found-c1fa06093506"
---

<img src="/assets/img/2024-06-22-ReactQueryReactNativeALoveStoryatFound_0.png" />

모든 좋은 사랑 이야기처럼, 기쁨, 절망, 고통, 고난이 있습니다. 유명한 속담처럼 "사랑하고 잃는 것이 전혀 사랑하지 않는 것보다 나은지" 생각해 볼 때도 있죠.

그러나 이 사랑 이야기의 스포일러 경고는 저와 공유하고 싶어요. 위아래로 변동이 있었지만 사랑은 잃어버린 적이 없습니다. 저희는 React Query (준말 Tanstack Query)와 함께 Found 모바일 애플리케이션에서 서버 상태를 처리하는 방식에 완전히 사랑에 빠져 있습니다.

# 첫눈에 반한, React Query 선택하기

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

리액트 쿼리가 리액트 애플리케이션에서 서버 상태를 관리하는 산업 표준 라이브러리 중 하나로 자리를 잡은 것은 의심할 여지가 없습니다. 리액트 네이티브로 구축된 Found의 모바일 앱은 자바스크립트 스레드에서 리액트가 렌더링되는 곳에서 리액트 쿼리를 사용할 수 있습니다.

그렇기에 선언적인 성격, 후크 지향 아키텍처, 강력한 캐시 메커니즘이 조화를 이루어 완벽한 패키지가 되었습니다. 저에게(그리고 Found에게) 첫눈에 반한 것이 정말입니다 😍.

리액트 쿼리는 그 존재 이유를 설명하는 "매니페스토"로 매력을 발산했습니다:

제 무릎이 약해지기 시작했습니다. 더 이상 리덕스? 사용자 정의 API 클라이언트 없이도 가능한가요? 로컬 상태와 서버 상태 간 분명한 관심사 분리? 사랑에 빠졌습니다.

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

(정말 공정하게 말하자면, 내가 합류하기 전에 Found 팀도 아예 같은 생각을 했다. 실제로 Found의 모바일 팀에 합류하는 데 중요한 이유 중 하나였어. React Native와 React Query를 사용하나요? 어디에 등록하면 되나요?)

# 신혼 여행 단계: React Query로 상태 관리 스택

로컬 및 서버 상태 간에 분명하게 구분되어 스케일을 빠르게 확장할 수 있었습니다. React Query는 서버 상태를 다루는 것에 대한 선언문에 충실했고, 우리는 더 행복할 수 없었습니다.

![이미지](/assets/img/2024-06-22-ReactQueryReactNativeALoveStoryatFound_1.png)

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

Zustand과 Context는 애플리케이션의 로컬 상태에 관한 문제를 다루며, stores를 유지하여 단일 책임 원칙(SRP)을 준수하는 것을 목표로 합니다.

최종적으로 Zustand와 Context stores는 React Query의 쿼리 양에 비해 애플리케이션의 총 상태(로컬 상태 + 서버 상태)의 약 20%만 차지했습니다. 우리는 Redux 또는 다른 복잡한 상태 관리 라이브러리로 스택을 과도하게 설계할 필요가 없었습니다. 우리의 스택은 비즈니스와 사용자 요구에 맞추어 잘 확장되고 있었습니다.

하지만, 좋은 로맨스 코미디와 마찬가지로, 좋은 것 없이는 나쁜 것도 없습니다.

# 문제점: 완벽한 사람은 없습니다.

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

모든 관계는 문제가 있을 수 있고, 우리의 React Query와의 관계도 그 예외는 아니었습니다. 처음에는 잘 시작했지만, 기능 세트가 성장함에 따라 데이터가 더 복잡해지고 문제가 발생하기 시작했습니다:

- 캐싱. 제품과 사용자가 기대하는대로 데이터가 업데이트되지 않아 UX가 나빠지고 캐싱 버그의 대규모 backlog이 생겼습니다.
- 대량의 로컬 상태, 쿼리 훅 및 캐시 쿼리들이 한 화면에 밀어넣어져 작업하기 번거로워졌습니다. 이는 관리하기 힘들어졌습니다.
- 캐시를 직접 업데이트하는 것은 즐겁지 않았습니다. 이게 왜 이렇게 어려워야 하는 걸까요?

긴 이야기를 짧게 말하자면, 대부분의 로맨스 영화 스토리처럼 우리는 화가 나서 그 후 자체성장 몽타주를 겪었습니다 (이는 React Query에서 작동하지 않는 이유를 이해하고 그에 대해 어떻게 해야 하는지 확인하는 작업을 맡은 것이었습니다).

## 1. 문제가 너에게 있는 게 아니야, 나에게 있다: 캐시가 모든 것을 통제한다.

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

React Query를 우리에게 매료시킨 것은 낮은 보일러플레이트, 설정 가능하고 자동 백그라운드 다시 불러오기와 같은 강력한 캐싱 메커니즘이었습니다. 이 기능을 통해 서버의 데이터를 클라이언트로 동기화할 수 있었습니다. 그러나 캐싱 관련 버그와 사용자 불만이 증가함에 따라, 우리의 애플리케이션과 React Query의 캐시에 문제가 있는 것으로 분명해졌습니다.

많은 사람들이 손가락을 가리고 안경이 깨지는 등 여러 문제가 있었지만, 이 문제의 해결책은 "React Query 때문이 아니라 우리 때문이다"라는 것으로 밝혀졌습니다.

## 서버 상태와 캐시를 적절하게 동기화하기

서버와 애플리케이션의 상태를 동기화하기 위해서는 다음과 같이 간단한 패러다임 전환이 필요합니다:

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

- 대부분의 경우에는 cacheTime과 staleTime만으로 신뢰할 수 없을 것입니다. 언제나 캐시를 직접 업데이트하거나 응용 프로그램의 어느 시점에서는 해당 쿼리를 무효화해야 한다고 가정하십시오.
- 서버가 올바른 데이터 구조로 응답하는 경우 캐시를 직접 업데이트합니다.
- 서버가 올바른 데이터 구조로 응답하지 않거나 캐시를 쉽게 업데이트할 수 없는 경우, 단순히 쿼리를 무효화하십시오.

## 캐시 무효화

캐시를 직접 무효화하는 방법은 React Query의 유지 관리자가 "스마트 재요청"이라고 부르는 방법으로, 캐시를 직접 업데이트할 수 없을 때 클라이언트와 서버 상태를 동기화하는 많은 문제를 해결했습니다.

```js
// 특정 포커스에 대한 사용자 작업 로그를 업데이트합니다. ex) Getting More Sleep
const {
  mutate: taskLogMutate,
  isSuccess: isTaskLogMutationSuccess,
  isLoading: isTaskLogMutationLoading,
  data: taskLogResponse,
} = useTaskLogMutation({
  onSuccess: ({ taskLog }) => {
    invalidateQueries([
      queryStore.focuses.fetch._def,
      queryStore.focuses.fetchDetails._def,
      queryStore.logs.fetch._def,
    ]);
  },
});
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

이 예시에서 사용자는 특정 관심사를 가진 작업 로그를 변경하고, 그 변경이 성공한 경우 해당 작업 로그를 가져오는 쿼리들과 새로운 작업 로그를 반영하기 위한 포커스/포커스 세부 정보는 모두 무효화됩니다.

이를 통해 클라이언트의 서버 상태를 업데이트하도록 다시 가져올 것이 보장됩니다. 이는 해당 쿼리에 활성 옵저버가 있는 경우 즉시 또는 사용자에게 해당 쿼리를 보유하고 있는 화면이 표시될 때 백그라운드에서 업데이트됩니다.

## React Navigation (Mobile) 레슨

모바일 앱에서의 네비게이션은 일반적인 브라우저 URL 라우팅 네비게이션보다 더 복잡성을 추가합니다. 여러 개의 중첩된 네비게이션 패턴(스택, 탭, 모달, 드로어)에 의존하기 때문입니다.

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

이것은 Found 팀이 쿼리의 라이프사이클을 이해하고 캐시 구성 값인 staleTime과 cacheTime에 기반하여 언제 다시 가져올지에 대해 배우는 과정이 분명히 있었습니다. 여기서 그 값에 대해 자세히 다루지는 않겠지만, 궁금하다면 React Query의 블로그 포스트를 확인해보세요.

우리가 배운 가장 큰 두 가지 교훈은 다음과 같습니다:

1. staleTime: 0, cacheTime: 0로 설정된 쿼리가 있는 화면에 네비게이션 스택 위로 화면을 푸시하고, 현재 화면을 팝할 때 이전 쿼리를 다시 가져오고 싶다면, 쿼리를 수동으로 무효화해서 가져와야 한다는 것입니다.
2. #1에서 이어서, 탭 네이게이터는 루트 화면이 항상 마운트되어 있어서, 백그라운드 다시 가져오기가 발생하길 원한다면, 해당하는 쿼리들은 수동으로 무효화되어야 합니다.

요약하자면, 화면이 마운트되어 있고 해당 화면으로 이동하면 백그라운드에서 자동으로 다시 가져오지 않습니다. 설정한 staleTime과 cacheTime과 상관없이 그 화면의 쿼리는 데이터를 백그라운드에서 다시 가져오고자 할 때 queryClient.invalidateQueries()를 통해 수동으로 무효화해야 합니다.

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

## Query Store

우리는 75개 이상의 쿼리를 가지고 있었고 쿼리 키 스토어가 없었기 때문에 절대적인 혼란이었습니다. 쿼리 스토어를 도입하는 것은 쿼리를 실행하고 캐시를 무효화하고 업데이트해야 할 때 큰 성과였습니다. 우리는 루크 모랄레스의 쿼리 키 팩토리를 선택했지만 여러분도 직접 만들 수 있습니다.

```js
import type { inferQueryKeys } from "@lukemorales/query-key-factory";

import { createQueryKeys } from "@lukemorales/query-key-factory";

export const focusesQueryStore = createQueryKeys("focuses", {
  fetch: (category: string) => ["fetch", category],
  fetchDetails: (focusId?: string) => ["details", focusId],
});

export type FocusesQueryStoreType = inferQueryKeys<typeof focusesQueryStore>;
queryClient.invalidateQeries({
  queryKey: queryStore.focuses.fetchDetails(focusId).queryKey,
});
```

기본 제공되는 타이핑이 우리를 홀렸고, React Query에서 서버 데이터 타이핑을 중앙 집중화하는 데 도움을 주기 위해 스토어에 요청 함수를 추가하는 실험을 계획 중입니다.

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

## 2. Talking in Circles: Repository 훅을 사용하여 관심사를 더 분리하기

우리의 화면 구성 요소는 여러 사용자 정의 쿼리 훅, Zustand 스토어 훅, 컨텍스트 및 로컬 및 클라이언트 상태를 병합하는 로직으로 인해 매우 부풀어 있고 길어졌다는 것에 의심의 여지가 없었습니다.

Domain-Driven Design에서 고수준으로 Repository 패턴을 재사용하여 응용 프로그램 전체에서 특정 도메인의 관심을 자체 사용자 정의 훅으로 상위 수준으로 분리했습니다.

![이미지](/assets/img/2024-06-22-ReactQueryReactNativeALoveStoryatFound_2.png)

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

이 훅의 책임은 로컬 상태와 서버 상태를 병합하고 여러 쿼리를 하나로 병합하는 것을 포함하지만 이에 국한되지 않습니다. 이 패턴의 채택은 DDD 레포지토리를 일대일로 따르기보다는 UI를 쿼리 및 응용 프로그램 상태 레이어의 업데이트 및 쿼리 클라이언트로 분리하는 개념을 구현하는 것을 의미합니다.

React Query에서 기본으로 지원하지 않는 훌륭한 사용 사례는 종속 변이(dependent mutation)일 것입니다.

```js
export const useTaskLogMutationRepository = ({
  image,
  taskId,
  isEditing,
}: TaskLogRepositoryParams) => {
  const { invalidateQueries } = useInvalidateQueries();

  // useTaskLogMutation에 종속된 변이
  const {
    isSuccess: isTaskImageMutationSuccess,
    isLoading: isTaskImageMutationLoading,
    mutate: taskImageMutate,
  } = useTaskImageMutation({
    onSuccess: () => {
      invalidateQueries(queryStore.challenges.detail._def);
    },
  });

  const {
    mutate: taskLogMutate,
    isSuccess: isTaskLogMutationSuccess,
    isLoading: isTaskLogMutationLoading,
    data: taskLogResponse,
  } = useTaskLogMutation({
    onSuccess: ({ taskLog }) => {
      invalidateQueries([
        queryStore.focuses.fetch._def,
        queryStore.focuses.fetchDetails._def,
        queryStore.logs.fetch._def,
      ]);

      // 이미지가 있는 경우 taskLog.id를 사용하여 이미지를 변경합니다.
      if (image) {
        taskImageMutate({ taskLogId: taskLog?.id, image });
      } else {
        invalidateQueries(queryStore.challenges.detail._def);
      }
    },
  });

  // 비동기 상태 병합
  const isLoading = isTaskImageMutationLoading || isTaskLogMutationLoading;
  const isSuccess =
    isTaskLogMutationSuccess && (isTaskImageMutationSuccess || !image);

  const mutate = useCallback(
    (data: ITaskLogMutationParams) => {
      taskLogMutate(data);
    },
    [taskLogMutate]
  );

  return useMemo(
    () => ({ mutate, isSuccess, isLoading, taskLogResponse }),
    [mutate, isSuccess, isLoading, taskLogResponse]
  );
};
```

이 저장소의 책임은 작업을 완료할 때 종속 변이 처리, 이미지 업로드에 대한 후속 변이 처리 및 비동기 상태 병합을 다루는 것입니다.

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

비동기 상태의 병합을 함으로써 UI가 종속된 작업 로그 이미지 변이 로딩인지 또는 단순히 작업 로그 변이인지에 대한 올바른 로딩 피드백을 표시할 수 있습니다. 비동기 논리의 병합을 해제하면 비동기 상태 관련 문제를 저장소에 집중시켜 응용 프로그램 전체에서 재사용할 수 있습니다.

## 3. 더 이상 싸우지 말고: Immer가 도와줍니다

서버 데이터는 중첩 구조 때문에 특히 페이지네이션과 함께 중첩된 객체 값 업데이트가 어려웠습니다. 이는 setQueryData를 사용하여 캐시 데이터를 업데이트할 때 불변한 방식으로 수행해야 하는 경우 우리의 관계에서 지속적인 문제였습니다.

```js
queryClient.setQueryData<InfiniteData<ITagsInfiniteQueryResponse>>(
  queryStore.tags.fetch.queryKey,
  tags => {
    if (tags?.pages) {
      return {
        ...tags,
        pages: tags.pages.map(page => ({
          ...page,
          bookmarks: page.bookmarks.map(bookmark =>
            bookmark.bookmarkable.text === favoriteTag.bookmarkable.text
              ? favoriteTag
              : bookmark,
          ),
        })),
      };
    }
    return tags;
  },
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

Immer가 나타났습니다. React Query와는 상호배타적인 문제는 아니지만(툴킷(RTK)은 잠재적으로 내부적으로 immer를 사용합니다), 많은 공개적인 논쟁을 막아줬어요.

```js
queryClient.setQueryData<InfiniteData<ITagsInfiniteQueryResponse>>(
  queryStore.tags.fetch.queryKey,
  tags => {
    // ✅ Immer의 produce 함수로
    const newData = produce(tags, draft => {
      const tag = draft?.pages
        .flatMap(page => page.bookmarks)
        .find(
          bookmark =>
            bookmark.bookmarkable.text === favoriteTag.bookmarkable.text,
        );
      if (tag) {
        tag.isBookmarked = favoriteTag.isBookmarked;
      }
    });
    return newData;
  },
);
```

전체 중첩 구조를 다시 만들 필요가 없고, Immer는 중첩된 객체의 값을 업데이트하기 위한 produce 함수를 제공하여 중첩된 객체를 다시 만들지 않고도 성능적이고 가독성 좋게 업데이트할 수 있어요.

# 화해 후의 키스

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

대부분의 커플들과는 다르겠지만, 우리가 우리의 관계 문제를 해결하는 데 도움이 된 것은 문제를 이해하고 이야기하며 실제로 (특히 이것) 실행하는 것이었습니다.

리포지토리를 사용하여 애플리케이션 상태에 추가적인 추상화 계층을 덧붙이고, 지속적으로 캐시를 무효화하고 업데이트하며, 캐시를 쉽게 업데이트하면서 코드베이스, 기술 스택, 그리고 가장 중요한 것인 Found 모바일 앱의 사용자 경험을 향상시켰습니다.

# 해지로 가는 길

Found에서 우리의 지도원칙 중 하나는 완벽보다 반복인데, 이것이 React Query와의 관계를 가장 잘 대변한 것 같습니다. React Query로 일부 초기 문제를 해결했지만, 아직 개선할 부분이 몇 가지 남아 있습니다.

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

┌──────────────┬──────────────────────────────────────────────────────────────────────────────────────────────────────┐
│ 본문 │
├──────────────┼──────────────────────────────────────────────────────────────────────────────────────────────────────┤
│ Refetch가 실패하면 어떻게 될까요? 간단한 오류 메시지는 충분한 경험인가요? 사용자의 건강과 관련된 상황에서 실패는 심각한 결과로 이어질 수 있습니다. │
│ 이와 같은 이유로 모바일 앱을 개선하는 과정에서 현재 사용 중인 React Query를 통해 서버 상태를 신뢰할 수 있고 반응적으로 만드는 것이 목표입니다. │
│ │
│ (주의: 실제로 인간 파트너가 있긴 하지만, 아마도 React Query와 더 많은 시간을 보낼 것입니다) │
└──────────────┴──────────────────────────────────────────────────────────────────────────────────────────────────────┘
