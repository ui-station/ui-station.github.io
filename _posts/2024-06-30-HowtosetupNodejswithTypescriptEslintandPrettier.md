---
title: "Nodejs, Typescript, Eslint, Prettier 설정하기 한 번에 끝내는 방법"
description: ""
coverImage: "/assets/img/2024-06-30-HowtosetupNodejswithTypescriptEslintandPrettier_0.png"
date: 2024-06-30 22:33
ogImage: 
  url: /assets/img/2024-06-30-HowtosetupNodejswithTypescriptEslintandPrettier_0.png
tag: Tech
originalTitle: "How to setup Node.js with Typescript, Eslint and Prettier"
link: "https://medium.com/@pushpendrapal_/how-to-setup-node-js-with-typescript-eslint-and-prettier-46bd968a97ac"
---


이 글은 typescript, eslint 및 prettier와 함께 즉시 nodejs 서버를 설정하는 방법에 대해 안내해줄 것입니다. 이 글을 따라 boilerplate를 얻어 애플리케이션을 구축하는 데 집중할 수 있습니다.

이 프로젝트에서는 yarn 패키지 매니저를 사용할 것이며, npm 또는 기타 패키지를 사용할 수도 있습니다.

# 단계 1 — 프로젝트 초기화

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

먼저 프로젝트 디렉토리를 만들어주세요. 터미널에서 서버를 생성하고 yarn을 사용하여 초기 설정을 진행해보세요.

```js
yarn init
```

이 명령을 실행하면 여러 질문이 나오는데, 필요에 맞게 답변해 주시면 됩니다.

![이미지](/assets/img/2024-06-30-HowtosetupNodejswithTypescriptEslintandPrettier_1.png)

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

또한 프로젝트 디렉토리 내에 src 디렉토리를 생성하여 소스 코드를 유지하고 관리하기 쉽게 유지할 것입니다.

![이미지](/assets/img/2024-06-30-HowtosetupNodejswithTypescriptEslintandPrettier_2.png)

# 단계 2 — TypeScript 추가

프로젝트에 typescript 패키지를 추가해야 합니다. TypeScript 컴파일러 및 관련 도구를 사용할 수 있게 될 것입니다.

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
yarn add typescript --dev
```

이 명령어를 실행하면 프로젝트에 typescript 패키지를 개발 의존성으로 추가할 수 있어요.

이제 typescript 구성 파일을 추가해야 합니다. 아래 명령어를 사용해 구성 파일을 생성해보세요.

```js
yarn tsc --init
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

아래 이미지에 보이는 대로 기본 컴파일러 구성을 가진 tsconfig.json 파일을 만들겠습니다.

![이미지](/assets/img/2024-06-30-HowtosetupNodejswithTypescriptEslintandPrettier_3.png)

tsconfig.json 파일에서 주석 처리된 rootDir 옵션을 제거하고 수정하여 typescript의 루트 디렉토리를 src로 설정합니다.

```js
"rootDir": "./src",
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

마찬가지로, outDir 옵션도 동일하게 변경해주세요.

```js
"outDir": "./build",
```

src 폴더 안에 있는 .ts 파일을 컴파일한 후, 모든 .js 파일은 이 build 폴더에 생성될 것입니다.

마지막으로, tsconfig.json 파일 끝에 이 두 옵션도 추가해주세요. 이는 컴파일러에게 어떤 파일을 컴파일할지 알려줄 것입니다.

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

```json
{
    // "skipDefaultLibCheck": true,                      /* TypeScript와 함께 포함된 .d.ts 파일의 유형 검사를 건너뛰기. */
    "skipLibCheck": true                                 /* 모든 .d.ts 파일의 유형 검사를 건너뛰기. */
  },
  "include": ["src/**/*.ts"],
  "exclude": ["node_modules"],
```

이제 모든 것이 잘 작동하는지 확인하려면 src 폴더 내에 index.ts 파일을 만드세요. 그 안에 코드를 넣고 터미널에서 yarn tsc를 실행하세요. build 폴더 내에 index.js 파일이 생성된 것을 볼 수 있습니다.

코드에서 빨간 선이 보인다면, 아마 Node.js 런타임 및 해당 모듈에 대한 유형 정의를 제공하는 패키지를 추가해야 할 수도 있습니다.

```json
yarn add -D @types/node
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

# 단계 3 — TypeScript용 스크립트 추가

프로젝트에 대한 개발 및 시작 스크립트를 추가해야 합니다. 개발 환경에서 변화를 감지하기 위해 nodemon을 사용할 것입니다. 그러나 별도의 빌드 단계 없이 Node.js에서 직접 TypeScript 파일을 실행할 수 있도록 ts-node 패키지를 사용해야 합니다.

```js
yarn add -D nodemon ts-node
```

아래 스크립트를 package.json 파일에 추가해주세요.

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
  "scripts": {
    "dev": "nodemon src/index.ts",
    "start": "node build/index.js",
    "build": "tsc"
  }
```

이제 yarn dev 명령을 사용하여 개발 서버를 시작할 수 있습니다. Nodemon과 ts-node는 ts 파일의 변경 사항을 직접 감지하고 서버를 다시 시작할 것입니다.

# 단계 4 — Eslint 추가

Eslint를 추가하기 위해 아래에 제공된 필요한 패키지를 설치하겠습니다.

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
yarn add --dev eslint @eslint/js @types/eslint__js typescript typescript-eslint
```

지금 프로젝트 디렉토리의 루트에 eslint.config.mjs 파일을 만들고 아래의 코드를 추가해주세요.

```js
// @ts-check

import eslint from '@eslint/js';
import tseslint from 'typescript-eslint';

export default tseslint.config(
  eslint.configs.recommended,
  ...tseslint.configs.recommended,
  {
    ignores: ['node_modules', 'build'],
  }
);
```

이제 터미널에서 `yarn eslint .`을 실행하면 eslint가 작동하는 것을 확인할 수 있습니다.

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

<img src="/assets/img/2024-06-30-HowtosetupNodejswithTypescriptEslintandPrettier_4.png" />

패키지.json 파일에 eslint를 위한 스크립트도 추가할 수 있어요.

```js
 "lint": "eslint src/**/*.ts",
  "lint:fix": "eslint src/**/*.ts --fix",
```

# 단계 5 — Prettier 추가하기

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

프로젝트에 prettier 패키지를 추가하세요.

```js
yarn add --dev --exact prettier
```

이제 프로젝트 루트에 .prettierrc와 .prettierignore 파일을 만드세요.

.prettierrc 파일에 prettier의 기본 구성을 포함시킵니다.

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
{
    "trailingComma": "es5",
    "tabWidth": 4,
    "semi": false,
    "singleQuote": true
}
```

또한, 우리는 prettier에게 어떤 파일을 서식을 지정하지 말아야 하는지 알려주어야 합니다. .prettierignore 파일 안에 다음을 포함시킵니다.

```js
# 아티팩트 무시:
build
coverage
```

마지막으로, package.json 파일에 prettier를 위한 스크립트를 추가할 수 있습니다.

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
"format": "prettier . --write"
```

프로젝트를 포맷팅하려면 터미널에서 yarn format을 실행할 수 있습니다.

![Image](/assets/img/2024-06-30-HowtosetupNodejswithTypescriptEslintandPrettier_5.png)

# 결론


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

이제 프로젝트를 시작할 준비가 끝났어요.

또한 Git 훅을 추가하여 각 커밋 전에 서식 지정 및 린팅을 수행할 수 있습니다. 이를 위해 husky와 lint-staged가 사용됩니다. 저는 husky와 lint-staged가 사용된 Github 레포지토리 https://github.com/Pushpendra100/Nodejs-backend-boilerplate 를 만들었어요. 이 레포지토리를 방문하여 husky와 lint-staged를 어떻게 포함시키는지에 대한 아이디어를 얻을 수 있어요.

즐거운 코딩! ✌️