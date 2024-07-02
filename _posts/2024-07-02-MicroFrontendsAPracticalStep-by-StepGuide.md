---
title: "마이크로 프론트엔드 실전 단계별 가이드"
description: ""
coverImage: "/assets/img/2024-07-02-MicroFrontendsAPracticalStep-by-StepGuide_0.png"
date: 2024-07-02 22:03
ogImage: 
  url: /assets/img/2024-07-02-MicroFrontendsAPracticalStep-by-StepGuide_0.png
tag: Tech
originalTitle: "Micro Frontends: A Practical Step-by-Step Guide"
link: "https://medium.com/bitsrc/micro-frontends-a-practical-step-by-step-guide-df10edf0e8d0"
---


## Module Federation과 Bit를 활용한 확장 가능한 Micro Frontends 솔루션 구축

![이미지](/assets/img/2024-07-02-MicroFrontendsAPracticalStep-by-StepGuide_0.png)

Micro Frontends를 구현하는 유일한 해결책은 없습니다. 접근 방식은 프로젝트의 특정 요구사항과 맥락에 맞게 맞춰져야 합니다. 그러나 한 가지 솔루션을 구현함으로써 대체 방법이나 보완적 전략과 같은 대안을 평가할 수 있습니다.

이 블로그에서는 Module Federation과 Bit를 사용하여 Micro Frontends의 런타임 통합을 구현할 것입니다. Bit는 호스트 앱과 원격 모듈을 위한 사전 구성된 템플릿을 제공합니다. 이를 통해 마이크로 프론트엔드 간에 컴포넌트를 공유하는 것이 매우 쉽고, 재사용성을 촉진하며 서로 다른 애플리케이션 부분 사이의 일관성을 유지할 수 있습니다.

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

![Micro Frontends Guide 1](/assets/img/2024-07-02-MicroFrontendsAPracticalStep-by-StepGuide_1.png)

![Micro Frontends Guide 2](/assets/img/2024-07-02-MicroFrontendsAPracticalStep-by-StepGuide_2.png)

![Micro Frontends Guide 3](/assets/img/2024-07-02-MicroFrontendsAPracticalStep-by-StepGuide_3.png)

# 호스트 앱 및 원격 모듈

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

호스트 앱과 원격 모듈은 미리 구성된 템플릿을 사용하여 생성되었습니다(Bit에서 제공됨):

```js
npx @teambit/bvm install # Bit 설치
bit init my-modfed-solution # 새 Bit 워크스페이스 생성
cd my-modfed-solution
bit create modfed-remote-mfe storefront # 원격 모듈 생성
bit create modfed-host-app shell-app # 호스트 앱 생성
```

사용 가능한 앱(및 원격 모듈)을 나열하려면 다음을 실행하세요:

```js
bit app list
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

다음은 구성 요소 ID와 해당 앱 이름을 나열한 출력입니다:

```js
┌─────────────────────────────────────────────────┬─────────────────────┐
│ id                                              │ name                │
├─────────────────────────────────────────────────┼─────────────────────┤
│ bit-bazaar.storefront/storefront                │ storefront          │
├─────────────────────────────────────────────────┼─────────────────────┤
│ bit-bazaar.shell-app/shell-app                  │ shell-app           │
└─────────────────────────────────────────────────┴─────────────────────┘
```

위의 앱 이름을 사용하여 로컬에서 앱을 실행할 수 있습니다:

```js
bit run storefront
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

# 공유 의존성

우리의 솔루션은 앱 번들에서 제외되고 별도의 청크로로 로드되도록 구성된 많은 공유 의존성으로 구성되어 있습니다. 이것이 ModFed의 강점 중 하나입니다. 이는 번들 크기를 최적화하고 일관성을 유지하며 동일한 모듈의 다른 버전 간의 충돌을 피할 수 있습니다.

공유 의존성은 프로젝트(호스트 앱 및 원격 모듈) 전체에서 공유되는 Bit 컴포넌트로 유지됩니다. 이를 통해 팀이 독립적으로 작업하면서도 일관성을 유지할 수 있습니다.

공유 의존성 목록은 주로 런타임 라이브러리와 디자인 시스템으로 구성되어 있습니다:

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


![MicroFrontends Step 4](/assets/img/2024-07-02-MicroFrontendsAPracticalStep-by-StepGuide_4.png)

![MicroFrontends Step 5](/assets/img/2024-07-02-MicroFrontendsAPracticalStep-by-StepGuide_5.png)

For example:

```js
/**
 * @filename: storefront.bit-app.ts
 * @component-id: bit-bazaar.storefront/storefront
*/

import { MfReact } from '@frontend/module-federation.react.apps-types.mf-rspack';
/* import the 'shared dependnecies' components */
import { shellAppSharedDependencies } from '@bit-bazaar/shell-app.shared-dependencies';


export default MfReact.from({
  name: 'storefront',
  clientRoot: './storefront.app-root.js',
  moduleFederation: {
    exposes: {
      // ...
    },
    shared: shellAppSharedDependencies,
  }
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

# 공유 디자인 시스템

저희의 컴포넌트 라이브러리와 테마는 Material UI를 기반으로 하고 있습니다. 이들은 "design" 스코프에서 유지보수되며 Micro Frontends 간에 공유됩니다.

![이미지](/assets/img/2024-07-02-MicroFrontendsAPracticalStep-by-StepGuide_6.png)

# 공유 컨텍스트

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

'Theme Provider', 'Auth Provider', 및 다른 컨텍스트 구성 요소는 "호스트 앱" 또는 "셸 앱"의 일부입니다. 따라서, 이러한 구성 요소들은 "셸 앱" 팀에 의해 유지보수됩니다.

![이미지](/assets/img/2024-07-02-MicroFrontendsAPracticalStep-by-StepGuide_7.png)

MFE를 작업하는 팀들은 인증, 권한 또는 다른 공유 기능에 신경 쓸 필요가 없습니다. "호스트" 또는 "셸" 팀이 모두 제공합니다.

예를 들어, 'storefront' 팀이 사용자 인증을 기반으로 기능을 구현해야 하는 경우, '셸 앱' 범위를 탐색하고 적절한 "SDK"를 찾을 것입니다.

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

![Image 1](/assets/img/2024-07-02-MicroFrontendsAPracticalStep-by-StepGuide_8.png)

# 라우팅 및 네비게이션

쉘 앱은 마이크로 프론트엔드(원격 모듈)가 단순한 링크를 넘어서 상호작용할 수 있는 "플러그인 시스템"을 제공합니다. 이를 통해 각 "플러그인"에 대한 형식을 제공함으로써 이를 가능하게 합니다.

![Image 2](/assets/img/2024-07-02-MicroFrontendsAPracticalStep-by-StepGuide_9.png)

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

예를 들어, 원격 모듈은 네비게이션 옵션을 포함한 "내비게이션 항목" 인터페이스를 구현할 수 있습니다.

![이미지](/assets/img/2024-07-02-MicroFrontendsAPracticalStep-by-StepGuide_10.png)

그런 다음 셸 앱을 위해 노출할 수 있습니다 (런타임에로 로드됩니다):

```js
/**
 * @filename: blog.bit-app.ts
 * @component-id: bit-bazaar.blog/blog
*/

export default MfReact.from({
  name: 'blog',
  clientRoot: './blog.app-root.js',
  moduleFederation: {
    exposes: {
      /** 
       * 셸 앱이 런타임에 로드할 MFE 네비게이션 노출
       **/
      './blognav': './navitem.js',
       /**
        * 'blog' MFE의 주요 청크
        **/
      './blog': './blog.js',
    },
    shared: shellAppSharedDependencies,
  },
  deploy: Netlify.deploy(netlifyConfig),
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

라우팅은 각 모듈에 맞는 수준에서 처리됩니다. 예를 들어, 셸 앱은 블로그(/blog/*)와 스토어프런트(/storefront/*)로의 라우팅만 처리합니다. 각 MFE(예: storefront/products) 내부의 라우팅은 결정하지 않습니다.

Markdown 형식의 테이블 태그를 수정하겠습니다.

```js
/**
 * @filename: shell-app.tsx
 * @component-id: bit-bazaar.shell-app/shell-app
*/

export function ShellApp() {
  return (
    <BrowserRouter>
          <Routes>
            <Route path="/" element={<Layout />}>
              <Route index element={<Homepage />} />
              <Route path="store/*" element={<Store />} />
              <Route path="blog/*" element={<Blog />} />
              <Route path="*" element={<div>Not Found</div>} />
            </Route>
          </Routes>
    </BrowserRouter>
  );
}
```

따라서 블로그와 같은 원격 모듈은 /blog/* 라우팅(블로그 MFE로의 라우팅)에 대해서 책임을 지지 않습니다 — 중첩된 경로에 대해서만 책임을 집니다.

```js
/**
 * @filename: blog.tsx
 * @component-id: bit-bazaar.blog/blog
*/

export function Blog() {
  return (
      <Routes>
        <Route path="articles" element={<ArticlesPage />} />
        <Route path="categories" element={<CategoriesPage />} />
      </Routes>
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

# DevX

개발 경험을 극대화하기 위해 각 팀은 "플랫폼" 구성 요소를 사용하여 불변 버전의 셸 앱 및 다른 원격 모듈을 소비하고 개발 중에 이를 컨텍스트로 사용하여 마이크로 프런트엔드를 실행합니다. 이를 통해 규칙적이고 원활한 개발 경험을 제공하면서 적절한 권한 및 접근 제어를 시행할 수 있습니다(예: '블로그' 팀은 '스토어프론트' MFE나 셸 앱을 수정할 수 없습니다).

![이미지](/assets/img/2024-07-02-MicroFrontendsAPracticalStep-by-StepGuide_11.png)

![이미지](/assets/img/2024-07-02-MicroFrontendsAPracticalStep-by-StepGuide_12.png)

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

예를 들어 'storefront' 팀은 개발용으로 유지하는 'platform' 앱을 실행하여 'storefront' MFE를 완전한 컨텍스트에서 실행할 수 있습니다 (쉘 앱 및 다른 MFE도 포함):

```js
bit run storefront-platform
```

![이미지](/assets/img/2024-07-02-MicroFrontendsAPracticalStep-by-StepGuide_13.png)