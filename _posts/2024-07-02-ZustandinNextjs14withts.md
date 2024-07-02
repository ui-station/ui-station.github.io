---
title: "2024년 Nextjs 14에서 TypeScript와 함께 Zustand 사용하기"
description: ""
coverImage: "/assets/img/2024-07-02-ZustandinNextjs14withts_0.png"
date: 2024-07-02 22:01
ogImage: 
  url: /assets/img/2024-07-02-ZustandinNextjs14withts_0.png
tag: Tech
originalTitle: "Zustand in Next.js 14 (with ts)"
link: "https://medium.com/@saleh_aka_jim/zustand-in-next-js-14-with-ts-353a1cf79ae0"
---


<img src="/assets/img/2024-07-02-ZustandinNextjs14withts_0.png" />

이 게시물에서는 Next.js 프로젝트에서 Zustand를 사용하는 방법에 대해 설명하겠습니다. Zustand란 무엇일까요? 공식 문서에 따르면 다음과 같습니다:

"간단하고 빠르며 확장 가능한 간단한 Flux 원리를 사용하는 상태 관리 솔루션입니다. 편리한 API는 후크를 기반으로 하며, 보일러플레이트적이거나 의견이 강요되지 않습니다."

# Atomic vs Boilerplate...

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

상황에 따라 다르지만, React.js 또는 Next.js에서는 프로젝트, 팀, 복잡성 등에 따라 보일러플레이트 또는 아토믹 상태 관리 중 선택할 수 있어요.

제 경험에 따르면, 저는 아토믹 상태 관리를 선택했어요. 프로젝트의 다른 부분에 의존하지 않고 각 페이지/디렉터리를 독립적으로 개발할 수 있었어요. "다른 부분에 의존하지 않는다"는 것을 어떤 의미인지 예시를 들어 설명해 드릴게요.

# 새 프로젝트를 시작해 볼까요

```js
npx create-next-app@latest
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
프로젝트 이름이 무엇인가요? zustand
TypeScript를 사용하시겠습니까? 예
ESLint를 사용하시겠습니까? 예
Tailwind CSS를 사용하시겠습니까? 아니오
src/ 디렉토리를 사용하시겠습니까? 아니요
App Router를 사용하시겠습니까? (권장) 예
기본 import 별칭(@/)을 사용자 정의하시겠습니까? 예
구성할 import 별칭은 무엇인가요? @/
```

![Zustand in Next.js with TypeScript](/assets/img/2024-07-02-ZustandinNextjs14withts_1.png)

# Zustand 추가

```js
npm install zustand # 또는 yarn add zustand 또는 pnpm add zustand
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

# 이제 첫번째 페이지를 개발할 준비가 되었습니다

## 시작할 새로운 페이지를 추가해 봅시다

```js
// 디렉토리: /app/bears/page.tsx

export default function Page() {
  return (
    <main>
      <h1>Bears</h1>
      <p>저희 상점에는 얼마나 많은 곰이 있을까요? {0}</p>

      <button>증가</button>
      <button>감소</button>
    </main>
  );
}
```

![이미지](/assets/img/2024-07-02-ZustandinNextjs14withts_2.png)

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

## 우리 가게는 이 페이지 안에서 생성될 것입니다

이 예시에서는 첫 번째 가게를 페이지 디렉토리 안에 생성할 것입니다. 이 가게는 이 페이지 안에서만 사용되며 다른 페이지나 컴포넌트에서는 전혀 필요하지 않을 겁니다 (이것이 원자 상태 관리의 장점이죠).

```js
// 디렉토리: /app/bears/_store/index.ts
import { create } from 'zustand';

// 상태 타입
interface States {
  bears: number;
}

// useBearStore
export const useBearStore = create<States>(() => ({
  bears: 0,
}));
```

## 페이지 내에서 상태 호출하기

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
// 디렉토리: /app/bears/page.tsx

'use client';
import { useBearStore } from './_store';

export default function Page() {
  // 스토어 내의 모든 것을 가져올 것입니다.
  // 주의: 상태 변경 시마다 컴포넌트가 업데이트되는 원인이 될 수 있습니다!
  const store = useBearStore();

  return (
    <main>
      <h1>Bears</h1>
      <p>우리 스토어에는 얼마나 많은 곰이 있을까요? {store.bears}</p>

      <button>증가</button>
      <button>감소</button>
    </main>
  );
}
```

또는

```js
// 디렉토리: /app/bears/page.tsx

'use client';
import { useBearStore } from './_store';

export default function Page() {
  // 또는, 스토어에서 필요한 것을 가져올 수도 있습니다.
  const { bears } = useBearStore((state) => state);

  return (
    <main>
      <h1>Bears</h1>
      <p>우리 스토어에는 얼마나 많은 곰이 있을까요? {bears}</p>

      <button>증가</button>
      <button>감소</button>
    </main>
  );
}
```

## 상태 변경을 위한 두 가지 액션을 추가합니다.

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
// 디렉토리: /app/bears/_store/index.ts

import { create } from 'zustand';

// 상태 타입
interface States {
  bears: number;
}

// 액션 타입
interface Actions {
  increase: () => void;
  decrease: () => void;
}

// useBearStore
export const useBearStore = create<States & Actions>((set) => ({
  bears: 0,

  increase: () => set((state) => ({ bears: state.bears + 1 })),
  decrease: () => set((state) => ({ bears: state.bears - 1 })),
}));
```

```js
// 디렉토리: /app/bears/page.tsx

'use client';
import { useBearStore } from './_store';

export default function Page() {
  // 또는, 스토어에서 필요한 것을 가져올 수도 있습니다
  const { bears, increase, decrease } = useBearStore((state) => state);

  return (
    <main>
      <h1>Bears</h1>
      <p>저희 스토어에는 얼마나 많은 곰이 있을까요? {bears}</p>

      <button onClick={increase}>증가</button>
      <button onClick={decrease}>감소</button>
    </main>
  );
}
```

## 이제 준비되었으니, 작동 방식을 확인해봅니다

```js
npm run dev
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


![Persist Data](/assets/img/2024-07-02-ZustandinNextjs14withts_3.png)

## 데이터 영속적 저장 방법

첫 번째 단계는 영속 데이터를 처리할 수 있도록 스토어를 변경하는 것입니다. localStorage, AsyncStorage, IndexedDB 등을 선택할 수 있습니다.

```js
// 디렉토리: /app/bears/_store/index.ts

import { create } from 'zustand';
import { persist, createJSONStorage } from 'zustand/middleware';

// 상태 유형
interface States {
  bears: number;
}

// 액션 유형
interface Actions {
  increase: () => void;
  decrease: () => void;
}

// useBearStore
export const useBearStore = create(
  persist<States & Actions>(
    (set) => ({
      bears: 0,

      increase: () => set((state) => ({ bears: state.bears + 1 })),
      decrease: () => set((state) => ({ bears: state.bears - 1 })),
    }),
    {
      name: 'bearStore',
      storage: createJSONStorage(() => localStorage),
    }
  )
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

## 상태를 유지하고 오류를 피하는 가장 좋은 방법

상태를 유지하기 위해 사용자 정의 후크를 만들 것입니다

```js
// /helpers/usePersistStore/index.ts

import { useState, useEffect } from 'react';

const usePersistStore = <T, F>(
  store: (callback: (state: T) => unknown) => unknown,
  callback: (state: T) => F
) => {
  const result = store(callback) as F;
  const [data, setData] = useState<F>();

  useEffect(() => {
    setData(result);
  }, [result]);

  return data;
};

export default usePersistStore;
```

## 우리의 페이지는 다음과 같을 것입니다:

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
// 디렉토리: /app/bears/page.tsx

'use client';
import usePersistStore from '@/helpers/usePersistStore';
import { useBearStore } from './_store';

export default function Page() {
  // 또는 스토어에서 필요한 것을 가져올 수 있습니다
  const store = usePersistStore(useBearStore, (state) => state);

  return (
    <main>
      <h1>Bears</h1>
      <p>저희 상점에는 얼마나 많은 곰이 있나요? {store?.bears}</p>

      <button onClick={store?.increase}>증가</button>
      <button onClick={store?.decrease}>감소</button>
    </main>
  );
}
```

## GitHub에서 코드를 확인하고 사용해보세요...

https://github.com/SalehAkaJim/medium-zustand
