---
title: "Part 3 React  폴더 구조 쉽게 정리하는 방법"
description: ""
coverImage: "/assets/img/2024-06-30-Part3ReactFolderStructure_0.png"
date: 2024-06-30 22:10
ogImage: 
  url: /assets/img/2024-06-30-Part3ReactFolderStructure_0.png
tag: Tech
originalTitle: "Part 3: React — Folder Structure"
link: "https://medium.com/javascript-in-plain-english/part-3-react-folder-structure-984384e226aa"
---


# 📖 이전 파트

이 시리즈의 2부는 여기에서 찾을 수 있습니다:

https://javascript.plainenglish.io/part-2-react-naming-convention-return-signatures-for-hooks-b9e31f5e7f58

가끔, 새 프로젝트를 시작하거나 애플리케이션이 성장함에 따라 파일을 어디에 둬야 할지에 대한 지침이 필요할 때가 있습니다. 이 컴포넌트를 어디에 두어야 할까요? 여기에 추가해야 할까요? 아이디어를 얻으셨죠? 만약 이미 그에 대해 헤매고 있다면, 필요한 파일을 찾는 것은 더 어려울 수 있습니다.

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

일반적으로 답은 더 나은 폴더 구조에 있습니다.

폴더 구조는 프로젝트에서 가장 중요한 요소 중 하나입니다. 파일을 어디에 두고 어떻게 조직화할지 알아야 하며, 애플리케이션이 성장함에 따라 유용합니다. 이는 당신과 팀이 필요한 파일을 쉽게 찾을 수 있도록 합니다.

각 팀의 목표는 누구나 즉시 파일을 찾을 수 있고 새 파일을 추가하거나 기존 파일을 업데이트하는 방법을 아는 것입니다.

이 파트에서는 내 프로젝트에서 사용하는 폴더 구조를 탐색하고, 당신에게 더 나을 수도 있는 다른 가능한 해결책을 살펴보겠습니다.

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

# ⚙️ 폴더 구조

제가 일반적으로 프로젝트를 시작할 때 사용하는 업데이트된 폴더 구조입니다:

![Folder Structure](/assets/img/2024-06-30-Part3ReactFolderStructure_0.png)

각 폴더를 설명해보겠습니다.

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

- api

이 폴더에는 애플리케이션이 수행할 모든 API 호출이 포함되어 있습니다. 백엔드에서 데이터를 가져오는 기능을 모두 넣을 곳입니다.

최신 버전의 Next.js에서는 서버 액션이라는 새로운 개념이 있습니다. 이는 서버에서 데이터를 가져오거나 데이터 변경을 수행하는 함수입니다. 이를 api 폴더에 넣을 수도 있습니다.

예시 구조:

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


![image](/assets/img/2024-06-30-Part3ReactFolderStructure_1.png)

React-Query 쿼리 및 뮤테이션을 혼합하여 Next.js 서버 동작도 구현했습니다.

- assets

이 폴더에는 애플리케이션이 사용할 모든 이미지, 글꼴 및 기타 에셋이 포함되어 있습니다.


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

우리 앱에서 로컬 이미지, 폰트 및 기타 에셋이 필요한 경우가 있습니다. 여기서는 앱에서 사용할 모든 에셋을 넣을 곳입니다.

예시 구조:

![이미지](/assets/img/2024-06-30-Part3ReactFolderStructure_2.png)

- components

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

이 폴더에는 앱에서 사용할 모든 재사용 가능한 구성 요소가 포함되어 있습니다. 여기에는 여러 화면에서 사용할 구성 요소를 모두 넣게 됩니다.

가능한 한 각 구성 요소가 "독립적"이 되도록 해야 합니다. 이것은 각 구성 요소가 독립적으로 존재할 수 있고 다른 구성 요소에 의존하지 않는다는 것을 의미합니다.

그러나 항상 그런 것은 아닙니다. 때로는 구성 요소가 다른 구성 요소를 사용해야 하는 경우도 있습니다. 이는 일반적으로 구성 요소의 조합을 의미합니다. Button + Input과 같이 구성 요소 조합이 필요할 때는 새로운 구성 요소로 취급하고 새 폴더에 넣어야 합니다.

그리고 그것을 ButtonInput 또는 다른 원하는 이름으로 지을 수 있습니다. 단지 의미가 통하면 됩니다.

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

지금까지의 구조를 가장 단순하게 만들어봤어. 이렇게 하면 필요한 컴포넌트를 더 쉽게 찾을 수 있을 거야.

예시 구조:

- constants

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

이 폴더에는 앱에서 사용할 모든 상수가 포함되어 있습니다. 상수는 앱 전체에서 변하지 않는 값입니다.

예를 들어 환경 변수를 담고 있는 Environment.ts 파일을 여기에 넣을 수 있습니다.

예시 구조:

![Folder Structure](/assets/img/2024-06-30-Part3ReactFolderStructure_4.png)

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

- hooks

이 폴더에는 애플리케이션이 사용할 모든 사용자 지정 React 훅이 포함되어 있습니다.

예시 구조:

![Hooks](/assets/img/2024-06-30-Part3ReactFolderStructure_5.png)

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

- 제공자

이 폴더에는 애플리케이션이 사용할 모든 리액트 컨텍스트 제공자가 포함되어 있습니다.

컨텍스트 제공자의 구현을 하나의 파일에 모두 넣고 싶다면 hooks 폴더에 넣을 수 있습니다.

예시 코드:

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

<img src="/assets/img/2024-06-30-Part3ReactFolderStructure_6.png" />

예제 구조:

<img src="/assets/img/2024-06-30-Part3ReactFolderStructure_7.png" />

- screens

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

이 폴더에는 애플리케이션이 사용할 모든 화면이 포함되어 있어요.

저희 애플리케이션에서는 Next.js이든 React Native이든, 보통 일종의 "라우터"를 가지고 있어요. 여기서 우리가 사용할 애플리케이션의 경로를 정의해요.

이는 폴더 기반 라우팅으로 이뤄진 Next.js와 같은 형식일 수도 있고, 단일 파일에서 경로를 정의하는 react-router-dom과 같은 라우터 라이브러리 형식일 수도 있어요.

React는 웹, 모바일 및 데스크톱과 같은 여러 플랫폼에서 사용될 수 있기 때문에 경로를 정의하는 방식이 다를 수 있어요. 이 폴더 구조를 갖고 있으면 여전히 화면을 정의하고 사용 중인 플랫폼에 맞게 쉽게 적응시킬 수 있어요.

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

예시 구조:

![이미지](/assets/img/2024-06-30-Part3ReactFolderStructure_8.png)

- 화면 '컴포넌트

이 폴더에는 화면에 특화된 모든 컴포넌트가 포함되어 있습니다.

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

이 특별한 디렉토리는 "프랙탈" 패턴에서 영감을 받아 폴더의 무한 중첩을 만들 수 있도록 합니다.

그러나 우리의 경우에는 중첩이 하나뿐이며 간단한 수준을 유지할 것입니다. `components` 디렉토리는 화면에 특정한 섹션 구성 컴포넌트를 모두 넣을 수 있는 곳입니다. "섹션"이라는 단어에 주목해주세요.

화면 컴포넌트 폴더가 없으면:

![이미지](/assets/img/2024-06-30-Part3ReactFolderStructure_9.png)

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

화면 구성 요소 폴더에 저장될 모든 구성 요소는 해당 화면에서만 사용해야 합니다. 이는 각 화면이 방대할 수 있기 때문에 코드베이스의 유지 관리성을 향상시키기 위한 것입니다.

구성 요소 폴더의 구성 요소를 사용할 수 있다면 해당 구성 요소 폴더에 있어야 합니다. 그러나 해당 화면에 대해 특정한 사용자 정의가 필요하다면 화면 구성 요소 폴더에 있어야 합니다.

예시 구조:

![이미지](/assets/img/2024-06-30-Part3ReactFolderStructure_10.png)

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

- utils

이 폴더에는 애플리케이션이 사용할 유틸리티 함수들이 포함되어 있습니다.

여기에는 컴포넌트 또는 화면과 관련되지 않은 모든 함수를 넣을 것입니다. 이는 애플리케이션 여러 곳에서 사용되는 함수일 수 있습니다.

포매터, 발리데이터 및 다른 컴포넌트 또는 화면과 관련되지 않은 함수는 여기에 넣어야 합니다.

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

컴포넌트 폴더와 유사하게, 최대한 평면화하십시오. 이는 깊은 폴더 구조를 갖지 않아야 함을 의미합니다. 이렇게 하면 필요한 함수를 쉽게 찾을 수 있습니다.

사용할 각 유틸리티 함수에 대한 폴더를 만들어서 해당 함수에 대한 테스트를 작성할 수 있도록 그룹화하십시오.

예시 구조:

![Folder Structure](/assets/img/2024-06-30-Part3ReactFolderStructure_11.png)

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

## 💡 다른 폴더 구조 아이디어

우리는 문제를 해결하는 여러 가지 방법이 있다는 것을 알 수 있습니다. 폴더 구조도 마찬가지입니다. 그래서 여러분에게 더 나은 해결책일 수도 있는 다른 가능한 솔루션을 탐색할 수 있는 이 섹션을 마련했습니다.

- 원자 디자인

원자 디자인은 디자인 시스템을 만드는 데 도움이 되는 방법론입니다. UI를 더 작은 구성 요소로 나누는 방법입니다. 컴포넌트는 원자, 분자, 유기체, 템플릿 및 페이지로 나누어집니다.

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

아래는 예시 구조입니다:

![이미지](/assets/img/2024-06-30-Part3ReactFolderStructure_12.png)

[링크](https://bradfrost.com/blog/post/atomic-web-design/)

- 기능 기반

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

피처 기반은 애플리케이션의 기능에 따라 파일을 구성하는 방법입니다. 많은 기능을 갖춘 대형 애플리케이션의 경우 파일을 잘 구성하는 방법 중 하나입니다.

예시 구조:

![폴더 구조](/assets/img/2024-06-30-Part3ReactFolderStructure_13.png)

각 폴더는 해당 기능과 관련된 모든 컴포넌트, 훅, 그리고 다른 파일들을 포함하게 됩니다.

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

여기 좋은 커뮤니티 토론이 있어요:

[커뮤니티 링크](https://community.redwoodjs.com/t/how-to-use-feature-based-folders-structure-instead-type-based/2980/7)

- 도메인 기반

Feature-based와 유사하게, 도메인 기반은 당신의 응용프로그램의 도메인을 특징이 아닌 파일들을 구성하는 방법입니다. 이 방법은 여러 도메인이 있는 대규모 응용프로그램의 파일을 구성하는 좋은 방법입니다.

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

아래는 예시 구조입니다. 


![folder structure](/assets/img/2024-06-30-Part3ReactFolderStructure_14.png)


각 폴더는 해당 도메인과 관련된 모든 컴포넌트, 훅, 그리고 다른 파일을 포함하고 있습니다.

## 🚀 결론

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

프로젝트에서 파일을 구성하는 다양한 방법이 있음을 볼 수 있어요. 프로젝트에서 사용할 폴더 구조에 대해 생각을 많이 하면 좋은 일이에요. 이렇게 하면 당신과 팀원들이 필요한 파일을 쉽게 찾을 수 있게 도와줄 거예요.

저는 주로 제 프로젝트에서 React를 사용하는데, 그래서 이 글에서는 주로 React 예시를 보여드리고 있어요. 하지만 다른 프레임워크로도 이를 적용할 수 있을 것이라 확신해요.

언제든지 궁금한 점이나 제안이 있으면 아래 댓글을 남겨주세요. 당신의 이야기를 듣는 걸 무척이나 기대하고 있어요.

읽어주셔서 감사해요. 이 글이 여러분의 여정에 도움이 되길 바래요! ❤️

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

# 쉽게 설명한 것에 오신 것을 환영합니다! 🚀

In Plain English 커뮤니티의 일원이 되어 주셔서 감사합니다! 떠나시기 전에:

- 필자를 박수로 격려하고 팔로우해 주세요 👏
- 팔로우하기: X | LinkedIn | YouTube | Discord | Newsletter
- 다른 플랫폼 방문: CoFeed | Differ
- PlainEnglish.io에서 더 많은 콘텐츠 확인하기