---
title: "피그마로부터 코틀린 Compose를 위한 자동 거의 자원 생성"
description: ""
coverImage: "/assets/img/2024-06-19-AutomaticalmostresourcegenerationfromFigmaforCompose_0.png"
date: 2024-06-19 13:44
ogImage:
  url: /assets/img/2024-06-19-AutomaticalmostresourcegenerationfromFigmaforCompose_0.png
tag: Tech
originalTitle: "Automatic (almost) resource generation from Figma for Compose"
link: "https://medium.com/proandroiddev/automatic-almost-resource-generation-from-figma-for-compose-596c9c664a62"
---

![image](/assets/img/2024-06-19-AutomaticalmostresourcegenerationfromFigmaforCompose_0.png)

자동화에 대해 이야기해보겠습니다. 루틴적이고 시간이 많이 소요되는 프로세스를 자동화해야 할 때가 많습니다. 이전 프로젝트에서는 클라이언트를 위한 다양한 UI 사용자 정의가 포함된 단일 코드베이스 기반의 화이트 레이블 솔루션을 갖고 있었습니다. 이 게시물에서는 디자이너의 변경 사항을 찾는 데 추가 시간을 낭비하지 않고 즉시 코드 작성을 시작하는 데 도움이 된 다양한 접근 방식과 도구에 대해 이야기하겠습니다.

# FIGMA/WHATEVER DSM

기초부터 시작해보겠습니다. 디자이너들도 DRY 원칙을 따르기 위해 노력하며 Design System Manager와 같은 훌륭한 도구를 개발합니다. 먼저, 프로젝트에서 반복될 수 있는 구성요소를 식별하고 색상 및 스타일의 구조화된 세트를 개발합니다. 따라서 한 곳에서 색상이나 스타일이 변경되면 전체 프로젝트에서 자동으로 변경됩니다. 편리하죠? Figma나 다른 도구를 사용하여 이러한 구성을 특별한 파일로 내보낼 수 있으며, 그 파일을 분석하여 이를 기반으로 코드를 생성할 수 있습니다.

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

Jetpack Compose을 사용하면 ColorScheme과 TextStyle과 같은 클래스를 알 수 있을 것입니다. Material 철학에 따라, 우리는 ColorScheme을 사용하여 색상을 설명하고 TextStyle을 사용하여 텍스트의 모양을 설명합니다. 이는 컴포넌트가 렌더링하는 데 사용되는 것입니다.

# 코드 생성

Room, Dagger2와 같은 라이브러리를 사용해 본 적이 있다면, 이미 익숙한 개념일 수 있습니다.

Java에서는 이 개념을 주석 처리(annotation processing)이라고 하며, Kotlin에서는 kapt 또는 ksp로 참조됩니다. 본질적으로 코드를 분석하고 추가 파일을 생성하는 컴파일러 기능입니다. 그러나 JSON 파일에 주석을 첨부할 수 없고 Kotlin 파일이 아니기 때문에, 여기에는 다른 해결책이 필요합니다. 따라서, 입력 데이터를 가져와 필요한 파일을 찾아 필요한 클래스를 생성하는 플러그인을 개발하는 방법을 배울 것입니다.

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

# Gradle 플러그인

Gradle 플러그인을 만드는 방법은 main 그레이들 파일에 작업을 작성하는 것에서 buildSrc에 코드를 배치하는 것까지 다양합니다. 그러나 저희는 권장 사항을 따라 composite 빌드를 사용할 것입니다. 이 아이디어는 간단합니다: 플러그인은 애플리케이션 코드와 분리되어 유지되고, 그런 다음 모든 것이 결합됩니다. 여기에서 AGP와 함께 이 메커니즘을 사용하는 방법에 대한 많은 예를 찾을 수 있으며, 오랫동안 가지고 있던 질문에 대한 답변을 찾을 수 있습니다.

![이미지](/assets/img/2024-06-19-AutomaticalmostresourcegenerationfromFigmaforCompose_1.png)

여기에 특별한 것은 없습니다. 차이는 플러그인이 가장 높은 수준에서 어떻게 연결되는지에 있습니다. includeBuild()를 사용하여 연결합니다.

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

당신의 플러그인 프로젝트의 build.gradle.kts 파일에서, 플러그인이 제대로 작동하기 위해 필요한 종속성 및 기본 구성을 명시해야 합니다. 다음은 플러그인을 위한 예시 설정입니다:

# Gradle 플러그인 구현

젯팩 콤포즈에 대한 Figma 파일 생성을 자동화하기 위한 목표를 달성하기 위해, Gradle 플러그인을 사용하여 다음 단계를 따라야 합니다:

- DSM 토큰의 구조를 분석하고 해당 모델을 준비합니다.
- 프로젝트에 각 클라이언트 디자인을 설명하는 토큰을 포함하는 모든 가능한 플레이버를 찾습니다.
- 이 데이터를 분석하여 필요한 파일을 생성하는 방법을 학습합니다.
- 이를 편리하게 만들고 Gradle 작업에 포장합니다.
- ...
- 코드를 작성하세요!

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

파일에는 색상 설명이 포함되어 있습니다. 이 설명은 여러 논리적 수준으로 구성될 수 있으며, 마지막 수준에는 우리가 관심을 가지는 정보가 포함되어 있습니다. 일반적으로 리소스는 유사한 구조를 가지며, 타입과 값이 명시됩니다.

```js
{
  "color": {
    "m3": {
      "white": {
        "description": "",
        "type": "color",
        "value": "#ffffffff",
        "blendMode": "normal"
      },
      "black": {...},
      "sys": {
        "light": {...},
        "dark": {...}
      },
      "ref": {...},
      "key-colors": {...},
      "source": {...},
      "surfaces": {...},
      "state-layers": {...}
    }
  },
  "font": {...},
  "typography": {...}
}
```

색상에 대해서는 해당 값을 관심 있게 살펴보고 있으며, 해당 클래스에는 해당 색상의 16진수 표현이 포함됩니다.

# Gradle 작업

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

작업 등록 중에는 주석으로 표시된 모든 필드를 설정해야 합니다. 즉, JSON 파일의 경로, 플레이버 폴더의 경로 및 생성된 파일의 최종 패키지입니다.

우리의 삶을 편하게 하기 위해 몇 가지를 하드코딩할 것이지만, 더 많은 유연성이 필요한 경우 gradle 확장 기능을 살펴볼 가치가 있습니다.

이 작업은 추상적이므로 일부 구성은 구현에 맡겨둡니다. 따라서 색상 파일을 생성할 작업의 구조는 다음과 같이 보일 것입니다.
우리의 작업을 실행할 수 있게 하려면 프로젝트에 등록해야 합니다.

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

우선 플러그인을 사용하여 프로젝트에 존재하는 빌드 변형을 결정하고 각 유형에 대한 작업을 등록해야 합니다.

이제 가장 흥미로운 부분으로 넘어가 봅시다. 파일 생성이 실제로 어떻게 일어나는지요.

이를 위해 KotlinPoet라는 도구를 사용해야 합니다. 이 도구를 사용하면 필요한 파일을 쉽게 생성할 수 있습니다. 이 전체 매커니즘은 다양한 빌더로 구성되어 있으며, 여기에 파일 유형, 속성, 값 등을 추가합니다.

우리의 작업 핵심은 JSON 구문 분석, 필요한 데이터 획득 및 해당 파일에 쓰는 데 있습니다.

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

JSON 파일의 구조를 알고 있기 때문에 데이터를 수집하는 함수를 작성할 수 있어요. 결과적으로 필드 이름을 키로, 모델을 값으로 하는 맵이 만들어질 거예요.

모든 필드의 목록을 갖게 되면, KotlinPoet을 사용하여 파일에 생성하고 싶은 내용을 설명할 수 있어요. 그 결과, 다음과 같은 모습의 파일을 얻게 될 거예요:

![이미지](/assets/img/2024-06-19-AutomaticalmostresourcegenerationfromFigmaforCompose_2.png)

![이미지](/assets/img/2024-06-19-AutomaticalmostresourcegenerationfromFigmaforCompose_3.png)

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

생성된 파일을 사용하여 테마를 설명할 수 있습니다.

![Image 4](/assets/img/2024-06-19-AutomaticalmostresourcegenerationfromFigmaforCompose_4.png)

![Image 5](/assets/img/2024-06-19-AutomaticalmostresourcegenerationfromFigmaforCompose_5.png)

![Image 6](/assets/img/2024-06-19-AutomaticalmostresourcegenerationfromFigmaforCompose_6.png)

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

그게 다야! 우리의 초기 작업은 완료되었습니다. 이제 파일을 프로젝트에 업로드하고 작업을 실행하여 개발에 사용할 수 있는 파일을 얻을 수 있습니다. 이전에는 수동으로 수행되어 인간 요인으로 인한 오류가 포함될 수 있었지만, 이제는 원활하게 작동하며 추가 종속성이 필요하지 않습니다(Android와 iOS 솔루션은 기존의 Node.js 모듈을 설치하여 작업을 수행하지 못했으며 여전히 개발자 개입이 필요했습니다).

# 명백하지 않은 도전 과제

플러그인 개발은 문제 해결의 절반입니다. 언제나 문제가 발생할 수 있습니다:

- 프로젝트 복잡성으로 인해 디자이너들은 Material Design을 제대로 사용하지 않고 수십, 아니 수백 개의 사용자 정의된 이름을 가진 색상과 스타일을 만들었습니다. 이로 인해 표준 MaterialTheme을 사용할 수 없었고, 결과적으로 우리 플러그인은 사용자 정의 ColorScheme과 Typography 클래스를 생성했습니다.
- 초기 개발 및 이전 이관에 상당한 시간이 소요되었지만, Material 설정이 깨끗하고 디자이너와의 토큰 이름 문제를 해결하는 데 몇 시간을 보낼 필요가 없다면 이것은 문제가 되지 않습니다.
- @Preview 주석은 모두 우리 사용자 정의 테마로 래핑되어야 합니다.
- 디자인 자체가 문제일 수 있습니다. 고객 중 한 명은 그라데이션을 가진 버튼을 가졌는데, 이 토큰에는 색상을 사용해야 했습니다. 이 문제는 임시 해결 방법으로 우아하게 해결되었지만, 미래에 이러한 충돌이 발생하지 않도록 디자이너에게 강력히 당부했습니다.
- 구성 복잡성 - 과정 자체는 간단하지만, 이전 문제와 같은 문제는 플러그인의 논리나 모델 구조를 크게 변경해야 할 수 있습니다.

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

그거면 끝이에요. 비슷한 솔루션을 만들어 본 경험이 있거나 미리보기를 위해 매번 사용자 정의 테마를 작성하는 것을 피하는 방법을 아시는 분들은 댓글에서 공유해 주세요. 코드가 필요한 경우 전체 프로젝트는 여기에서 확인할 수 있습니다.
