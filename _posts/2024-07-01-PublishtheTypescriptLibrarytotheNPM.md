---
title: "TypeScript 라이브러리를 NPM에 배포하는 방법"
description: ""
coverImage: "/assets/img/2024-07-01-PublishtheTypescriptLibrarytotheNPM_0.png"
date: 2024-07-01 16:23
ogImage: 
  url: /assets/img/2024-07-01-PublishtheTypescriptLibrarytotheNPM_0.png
tag: Tech
originalTitle: "Publish the Typescript Library to the NPM"
link: "https://medium.com/@yagizhanyakali/publish-the-typescript-library-to-the-npm-8512dcfeab04"
---


이 게시물에서는 TypeScript 라이브러리를 NPM에 게시하는 방법에 대해 이야기하려고 합니다. 게시하기 전에 따라야 할 몇 가지 팁이 있습니다. 이 게시물에서는 모든 팁과 해야 할 사항을 모두 써 보겠습니다. 함께 시작해 봅시다!

먼저 NPM 계정이 있어야 합니다. 아직 생성하지 않았다면 링크를 따라가서 새로운 계정을 만드세요.

훌륭합니다! 이제 우리의 간단한 TypeScript 라이브러리를 만들어 보겠습니다. 빈 폴더를 만들고 개발 환경을 준비하기 위해 아래 명령을 실행하세요.

```js
## 노드 초기화
npm init -y
## 타입스크립트 설치
npm install typescript
## 타입스크립트 초기 설정
npx tsc -init
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

이 명령을 실행한 후에는 tsconfig.json 파일에 일부 변경을 해야 합니다. 당신의 tsconfig.json 파일은 다음과 같이 보여야 합니까? Markdown 형식으로 테이블 태그를 변경하세요.

```js
{
 "compilerOptions": {
 /* 언어 및 환경 */
 "target": "es2016" /* 생산된 JavaScript에 대한 JavaScript 언어 버전 및 호환되는 라이브러리 선언 포함 설정. */,
/* 모듈 */
 "module": "commonjs" /* 생성된 모듈 코드를 지정합니다. */,
 "rootDir": "src" /* 소스 파일 내의 루트 폴더를 지정합니다. */,
/* 생성 */
 "declaration": true /* TypeScript 및 JavaScript 파일에서 .d.ts 파일을 생성합니다. */,
 "outDir": "dist" /* 모든 생성된 파일을 위한 출력 폴더를 지정합니다. */,
 "esModuleInterop": true /* CommonJS 모듈 가져오기를 지원하기 위해 추가 JavaScript를 생성합니다. 이것은 type 호환성을 위해 'allowSyntheticDefaultImports'를 활성화시킵니다. */,
 "forceConsistentCasingInFileNames": true /* 가져오기 시 대소문자를 정확하게 하도록 합니다. */,
/* 형식 확인 */
 "strict": true /* 모든 엄격한 형식 확인 옵션을 활성화합니다. */,
 "skipLibCheck": true /* 모든 .d.ts 파일에 대한 형식 확인을 건너뜁니다. */
 },
 "include": ["src/**/*"],
 "exclude": ["node_modules"]
}
```

다음으로 루트 디렉터리에 src 폴더를 만들고 이 src 폴더에 index.ts 파일을 생성해야 합니다. 현재 프로젝트 디렉토리 구조는 다음과 같아야 합니다:

```js
minimal-library
│
+ - node_modules
+ - src
│ + - index.ts
+ - package-lock.json
+ - package.json
+ - tsconfig.json
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

코드 작성해보세요!

index.ts 파일 안에 greets 라는 간단한 함수를 작성하고 내보내세요. 그런 다음, src 디렉토리 아래에 interfaces라는 폴더를 생성해주세요. 이 폴더에 사용자 정의 인터페이스를 저장할 거에요. 타입도 만들어도 돼요. 이 폴더 안에 greetResponse.interface.ts 라는 인터페이스 파일을 만들어주세요. 이렇게 변경한 후 최신 디렉토리 구조는 다음과 같아요:

```js
minimal-library
│
+ - node_modules
+ - src
│ + - index.ts
+ - interfaces
| + - greetResponse.interface.ts
+ - package-lock.json
+ - package.json
+ - tsconfig.json
```

그런 다음, .ts 파일들 안에 코드를 작성해볼까요?

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
// index.ts
import { GreetResponse } from "./interfaces/greetResponse.interface";
export function greets(name: string): GreetResponse {
  return {
    message: "Hello " + name,
  };
}
```

```js
// greetResponse.interface.ts
export interface GreetResponse {
  message: string;
}
```

완벽합니다! 우리 라이브러리를 사용할 준비가 되었습니다. 배포하기 전에 package.json 파일을 사용자 정의해야 합니다. 아래 라인들은 배포를 위해 필수적입니다.

```js
"main": "dist/index.js",
"types": "dist/index.d.ts"
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

위의 표를 마크다운 형식으로 변경해 주세요.

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

아래 명령어를 실행하기 전에, 해당하는 npm 패키지 이름이 이미 존재하지 않는지 확인해주세요.

```js
## npm 계정에 로그인하기
npm login
## 배포하기
npm publish
```

해피 해킹! 🔥