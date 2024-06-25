---
title: "Ruby on Rails 7에서 Javascript 및 Bootstrap 에셋 번들링 하는 방법"
description: ""
coverImage: "/assets/img/2024-06-23-JavascriptBootstrapAssetBundlinginRubyonRails7_0.png"
date: 2024-06-23 20:50
ogImage:
  url: /assets/img/2024-06-23-JavascriptBootstrapAssetBundlinginRubyonRails7_0.png
tag: Tech
originalTitle: "Javascript , Bootstrap — Asset Bundling in Ruby on Rails 7"
link: "https://medium.com/@pietropugliesi/javascript-bootstrap-asset-bundling-in-ruby-on-rails-7-3640a220f2ce"
---

최근 몇 달 동안 (2023년 초반) 저는 Ruby on Rails를 통해 프론트엔드 웹 개발을 배우고 있는 중이에요. 그리고 지금까지 정말 멋진 여정이었어요. 그런데 프로젝트에 자산 (자바스크립트 및 CSS 라이브러리)을 넣는 방법에 몇 가지 변화가 있었던데, 이는 초보자에게는 조금 어려울 수 있어요.

이번 시간 동안 많은 검색을 해본 결과, Ruby on Rails 포럼에서 해당 질문에 대한 포스트를 찾았어요. 그리고 여기서 4가지 방법으로 프로젝트에 자산을 로드하는 방법을 설명하겠어요. 또한 내가 발견한 몇 가지 결과물도 이 가이드에서 설명하려고 해요. 이 결과물은 대부분 David Heinemeier Hansson (또는 커뮤니티에서는 DHH로 알려진)의 유튜브 튜토리얼에서 나온 것들이에요. 그는 Ruby on Rails 창시자이기도 하죠.

# 무슨 일이 일어나고 있지?

Ruby on Rails는 Ruby로 작성된 프레임워크로, 웹 애플리케이션 개발을 많이 도와줘요 (프론트엔드 및 백엔드 모두, 즉: HTML, Javascript, CSS, 그리고 데이터베이스 및 URI 경로 통합이요). Ruby로 HTML과 Javascript를 작성할 수도 있지만, 서버를 시작하고 브라우저에서 사이트를 볼 때는 HTML만이 기본적으로 렌더링될 뿐이에요. 따라서, 우리는 사용하고 싶은 자바스크립트 및 라이브러리, 그리고 사용자 정의 CSS와 CSS 라이브러리를 프로젝트에 컴파일할 방법이 필요해요.

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

부트스트랩(버튼이나 네비게이션 바와 같은 스타일이 적용된 컴포넌트를 제공하는 CSS 라이브러리)을 제 프로젝트에 번들링하려고 노력했는데, 때로는 CSS 라이브러리가 제대로 로드되지 않아 사이트가 90년대 페이지처럼 순수 HTML로 렌더링되고, 대부분의 경우 CSS 라이브러리가 로드되더라도 상호작용(예: 네비게이션 바 축소 메뉴 확장)에 반응하지 않았습니다. 이는 Bootstrap이 작동하기 위해 필요한 JavaScript가 제대로 로드되지 않음을 의미합니다.

또한 대부분의 자습서들이 작동하지 않아서 무엇이 문제인지 정말로 이해하기로 결심했습니다.

마지막으로 이 게시물에서 설명한대로, 이 목표를 달성하는 네 가지 방법을 발견했습니다(2023년 3월 현재, Ruby On Rails 7 기준):

- 이전 방식(7버전 이전) : 스프로켓 및 젬을 통해 부트스트랩을 수동으로 번들링하는 방식;
- Webpacker 젬 사용: 이 젬은 자바스크립트 Webpack, Node 및 yarn을 랩핑합니다. JavaScript ES6부터는 번역이 더 이상 필요하지 않으며 이 젬은 폐지되었고 shakapacker 젬으로 대체되었습니다. 해당 젬의 GitHub에 따르면 :

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

참고: HMR은 Hot Module Reloading의 약자로, 코드를 편집할 때 사이트가 자동으로 다시로드됩니다.

- 새로운 방법 #1: Rails 7 Importmaps를 사용하는 방법: 단일 파일 importmap.rb에 가져오기를 정의합니다. 이 파일은 코드를 JS 및 CSS로 변환하고 이 파일들을 번들링합니다.
- 새로운 방법 #2: Rails 7 JS 번들링 및 CSS 번들링 gems를 사용하는 방법: 이를 새 프로젝트에 선언하거나 기존 프로젝트에 추가하고, esbuild 및 yarn (또는 다른 도구를 선택할 수도 있음)를 사용하여 CSS 파일을 변환하고 번들링합니다.

포럼에서 설명한대로, rails pipeline은 세 가지 작업을 수행합니다: 변환(다른 확장자를 .js 및 .css로 변환), 번들링(모든 파일을 하나의 .js 및 .css 파일로 결합하여 브라우저에서 작동하도록 함), 그리고 파일 다이제스팅(파일을 해싱하고 브라우저 캐시를 가능하게하며 개발중 캐시를 무효화해 코드 변경이 브라우저 캐시에 덮어씌워지지 않도록 합니다). Sprockets는 모든 세 가지를 실행했었기 때문에 옵션 #1이 작동하지만, importmaps 또는 js/css 번들링 gems를 사용하는 경우 첫 2단계만 수행하고 다이제스팅은 sprockets에 남깁니다.

# 튜토리얼

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

레일즈 7은 이미 importmaps를 사용할 수 있도록 사전 구성되어 있습니다. 따라서 importmap 및 번들링 젬 방식을 설명하고 있어요. 번들링 젬 방식은 다음 튜토리얼에서 소개될 예정입니다.

# Importmaps 튜토리얼

- 레일즈를 사용하여 새 앱을 시작하세요. [앱이름]으로 rails new [appname];

![이미지](/assets/img/2024-06-23-JavascriptBootstrapAssetBundlinginRubyonRails7_0.png)

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

<img src="/assets/img/2024-06-23-JavascriptBootstrapAssetBundlinginRubyonRails7_1.png" />

우리가 볼 수 있듯이, Rails 7은 이미 우리를 위해 스프라켓(4)을 설정하고 importmaps(1)를 설치하며, 메인 HTML 파일(2)에서 사용하고, 우리의 import JS 파일을 스프라켓 매니페스트.js에 연결하고(3 및 4), 필요하다면 CDN을 통해 CSS 및 JS 파일을 핀(import)할(importmap) 수 있도록 명령 줄에서 importmap 명령을 활성화합니다.

- 부트스트랩의 경우, 우리는 젬 튜토리얼을 따릅니다: Gemfile에서 부트스트랩 젬을 가져와 sprockets-rails이 사용되는지 확인합니다. 젬은 루비 라이브러리이며, Rails는 rubygems.org에서 이를 로드합니다. 우리의 모든 젬은 Gemfile에 선언됩니다.

<img src="/assets/img/2024-06-23-JavascriptBootstrapAssetBundlinginRubyonRails7_2.png" />

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

![screenshot1](/assets/img/2024-06-23-JavascriptBootstrapAssetBundlinginRubyonRails7_3.png)

- Change the application.css extension to .scss and replace the file contents by @import "bootstrap"

![screenshot2](/assets/img/2024-06-23-JavascriptBootstrapAssetBundlinginRubyonRails7_4.png)

- Add bootstrap and popper to importmap (popper is required by bootstrap to show popovers, like the dropdown on our navbar)

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

![Image 1](/assets/img/2024-06-23-JavascriptBootstrapAssetBundlinginRubyonRails7_5.png)

![Image 2](/assets/img/2024-06-23-JavascriptBootstrapAssetBundlinginRubyonRails7_6.png)

- Add Bootstrap (and popper) to be precompiled in our assets.rb

![Image 3](/assets/img/2024-06-23-JavascriptBootstrapAssetBundlinginRubyonRails7_7.png)

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

- 번들 설치를 통해 gems를 설치하세요 (또는 단축키 번들)

![이미지1](/assets/img/2024-06-23-JavascriptBootstrapAssetBundlinginRubyonRails7_8.png)

만약 rails 서버를 통해 앱을 시작하면 (또는 단축키 rails s를 사용하면), 레일즈 페이지가 표시됩니다:

![이미지2](/assets/img/2024-06-23-JavascriptBootstrapAssetBundlinginRubyonRails7_9.png)

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

우리 앱에 페이지를 추가하려면 controllers(home/index)을 생성합니다. 레일즈 generate controllers home index(단축어로는 rails g)를 사용하세요. 이 명령어는 home_controller(관례에 따라 지어지는 새로운 컨트롤러)와 index 액션을 생성합니다.

![이미지1](/assets/img/2024-06-23-JavascriptBootstrapAssetBundlinginRubyonRails7_10.png)

![이미지2](/assets/img/2024-06-23-JavascriptBootstrapAssetBundlinginRubyonRails7_11.png)

정말 멋지죠! 하지만 이 페이지를 루트(localhost:3000/)로 설정하려면 routes.rb에서 변경하면 됩니다.

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

아래는 Markdown 형식으로 변환되었습니다.

![이미지](/assets/img/2024-06-23-JavascriptBootstrapAssetBundlinginRubyonRails7_12.png)

원래 우리는 Rails에게 앱의 루트가 home 컨트롤러이고 인덱스라는 액션에 있다고 말합니다. 이제 rails s로 서버를 재시작하고 브라우저를 엽니다:

![이미지](/assets/img/2024-06-23-JavascriptBootstrapAssetBundlinginRubyonRails7_13.png)

Bootstrap이 작동하는지 확인하기 위해 우리는 부트스트랩 사이트에서 데모 네비게이션 바를 새로운 *navbar.html.erb 파일에 복사합니다 (이름 앞의 *는 중요합니다!)

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

![Image](/assets/img/2024-06-23-JavascriptBootstrapAssetBundlinginRubyonRails7_14.png)

그리고 이를 app/views/shared 폴더에 넣어주세요 (views 폴더 안에 shared 폴더를 만드세요).

우리의 \_navbar.html.erb는 부분(partial)이라고 불립니다: 이것은 우리의 html의 일부, 컴포넌트로 렌더링됩니다. 또한, 우리 Rails 프로젝트의 "html" 파일들은 실제로 순수한 html이 아니라 특별한 .html.erb (Embedded Ruby) 확장자를 사용합니다. 이렇게 하면 우리는 HTML 코드 안에 Ruby 코드를 삽입할 수 있습니다.

Application.html.erb로 이동하여 render_partial에 우리의 부분을 추가해보세요:

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

![Screenshot 1](/assets/img/2024-06-23-JavascriptBootstrapAssetBundlinginRubyonRails7_15.png)

yield는 컨트롤러에서 요청한 페이지를 렌더링하는 특별한 단어입니다. 이 방법을 사용하면 전체 HTML에 application.html.erb 헤더와 네비게이션 바가 모든 페이지에 포함되며, yield는 요청한 페이지(이 경우 루트)를 렌더링합니다:

![Screenshot 2](/assets/img/2024-06-23-JavascriptBootstrapAssetBundlinginRubyonRails7_16.png)

부트스트랩 네비게이션 바가 성공적으로 로드되었지만 드롭다운 메뉴 클릭이 작동하지 않습니다. sprockets에게 부트스트랩에 필요한 Javascript를 번들하도록 알려야 합니다. javascript/application.js 파일에 누락된 require를 추가해줍니다:

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

<img src="/assets/img/2024-06-23-JavascriptBootstrapAssetBundlinginRubyonRails7_17.png" />

<img src="/assets/img/2024-06-23-JavascriptBootstrapAssetBundlinginRubyonRails7_18.png" />

이제 모든 것이 잘 작동합니다!

이 간단한 튜토리얼로 도움이 되었기를 바랍니다. Rails는 웹 애플리케이션을 개발하는 데 많은 도움이 되며, 여러분도 사용하면 좋겠네요.

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

아래에서 뵙겠습니다!

## 유용한 링크:

- Rails 포럼, 포스트 및 제 답변;
- Bootstrap Rubygems 설치 및 Bootstrap gem의 깃허브;
