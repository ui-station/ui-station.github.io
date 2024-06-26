---
title: "Jetpack Compose에서 안정성을 마스터하여 앱 성능 최적화하기"
description: ""
coverImage: "/assets/img/2024-05-17-OptimizeAppPerformanceByMasteringStabilityinJetpackCompose_0.png"
date: 2024-05-17 18:39
ogImage:
  url: /assets/img/2024-05-17-OptimizeAppPerformanceByMasteringStabilityinJetpackCompose_0.png
tag: Tech
originalTitle: "Optimize App Performance By Mastering Stability in Jetpack Compose"
link: "https://medium.com/proandroiddev/optimize-app-performance-by-mastering-stability-in-jetpack-compose-69f40a8c785d"
---

![Jetpack Compose](/assets/img/2024-05-17-OptimizeAppPerformanceByMasteringStabilityinJetpackCompose_0.png)

젯팩 컴포즈는 구글의 첨단 UI 툴킷으로서, 안정적인 1.0 버전 이후 뛰어난 가능성을 보여주었습니다. Google의 보고에 따르면, 제트팩 컴포즈를 사용하여 개발된 앱이 이미 12만 5천 개 이상이 Google Play Store에서 성공적으로 출시되면서 프로덕션 목적으로의 채택이 급증했습니다.

제트팩 컴포즈는 내장된 최적화 기능을 갖추고 있지만, 개발자들은 컴포즈가 UI 요소를 어떻게 렌더링하는지 이해하고, 다양한 시나리오에서 제트팩 컴포즈의 성능을 최적화하기 위한 전략을 파악해야 합니다. 이러한 지식은 애플리케이션의 성능에 잠재적인 영향을 최소화하고 더 나은 사용자 경험을 제공하기 위한 중요한 역할을 합니다.

이 기사에서는 제트팩 컴포즈의 내부 작업을 이해하고, 안정성을 관리하여 애플리케이션의 성능을 향상시키는 방법을 안내하겠습니다.

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

# Jetpack Compose 단계

안정성에 대해 자세히 살펴보기 전에 Jetpack Compose의 단계를 이해하는 것이 중요합니다. 이 단계에서는 화면에 Compose UI 노드를 렌더링하는 과정이 여러 연속적인 단계를 통해 진행됩니다.

![Jetpack Compose Phases](/assets/img/2024-05-17-OptimizeAppPerformanceByMasteringStabilityinJetpackCompose_1.png)

Jetpack Compose는 한 프레임의 렌더링을 세 가지 구분된 단계를 통해 실행합니다:

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

- 구성: 이 단계에서는 Composable 함수에 대한 설명을 생성하고 여러 메모리 슬롯을 할당함으로써 프로세스가 시작됩니다. 이러한 슬롯은 각 Composable 함수를 메모이즈하여 런타임 중에 효율적인 호출 및 실행을 가능하게 합니다.
- 레이아웃: 이 단계에서는 Composable 트리 내 각 노드의 위치를 설정합니다. 레이아웃 단계는 주로 각 Composable 노드를 측정하고 적절히 배치하여 UI의 전체 구조 내에서 모든 요소가 정확히 배열되도록 보장합니다.
- 그리기: 이 마지막 단계에서는 Composable 노드가 일반적으로 장치의 화면인 캔버스에 렌더링됩니다. 이 중요한 단계는 UI를 시각적으로 구축하여 설계된 Composables을 사용자 상호 작용을 위해 사용할 수 있게 합니다.

내부 메커니즘은 훨씬 더 복잡하지만, 기본적으로 Composable 함수를 작성할 때 화면에 표시되기 위해 이러한 단계를 거칩니다.

이제 레이아웃의 크기 및 색상과 같은 UI 요소를 수정하려고 한다고 가정해 봅시다. 그림 그리기 단계가 완료된 경우, Compose는 이러한 새 값들을 적용하기 위해 처음부터 다시 이러한 단계를 다시 방문해야 합니다. 이러한 업데이트 사이클을 재구성이라고 합니다:

![이미지](/assets/img/2024-05-17-OptimizeAppPerformanceByMasteringStabilityinJetpackCompose_2.png)

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

재구성은 입력 변경에 대한 응담으로 조합 가능한 함수가 새롭게 실행되어 Composition 단계에서 시작됩니다. 이 프로세스는 State에서 관찰하거나 내재적으로 Compose 런타임과 컴파일러 메커니즘들이 연관된 다양한 요소에 의해 트리거될 수 있습니다.

전체 UI 트리와 요소를 재구성하는 것은 상당한 계산 리소스를 필요로 하며 앱의 성능에 직접적인 영향을 미칠 수 있습니다. 필요한 경우에만 재구성을 트리거함으로써(불필요할 때 재구성을 건너뛸 경우) 계산 리소스를 최소화하여 UI 성능을 향상시킬 수 있습니다.

따라서, Compose 런타임의 작동 방식을 포함한 재구성 프로세스에 대한 심층적인 이해, 재구성을 건너뛸 기회를 식별하고 재구성을 트리거하는 요소를 인식하는 것이 중요합니다.

이제 안정성 개념을 탐구하고 재구성 비용을 최적화하여 응용 프로그램 성능을 향상시킬 방법을 살펴보겠습니다.

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

# 안정성 이해

이전 섹션에서 언급된 바와 같이, 이미 렌더링된 UI를 업데이트하기 위해 다양한 방법이 존재합니다. Composable 함수의 매개변수의 안정성은 Compose 런타임과 컴파일러의 작동과 깊이 얽혀 있어, 재구성을 시작하는 결정적인 요소로 부각됩니다.

Compose 컴파일러는 Composable 함수의 매개변수를 안정적인 것과 불안정한 것 두 가지로 분류합니다. 이러한 매개변수 안정성의 분류는 Compose 런타임에 의해 Composable 함수가 재구성되어야 하는지 여부를 결정하는 데 사용됩니다.

# 안정함 vs. 불안정함

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

이제, 어떻게 매개변수가 안정적인지 불안정한지로 분류되는지 궁금할 수 있습니다. 이 결정은 Compose 컴파일러에 의해 이루어집니다. 컴파일러는 Composable 함수에서 사용된 매개변수의 유형을 검토하고 다음 기준에 따라 안정적인지를 분류합니다:

- String을 포함한 기본 유형은 본질적으로 안정적입니다.
- (Int) -` String과 같은 람다 표현식으로 나타낸 함수 유형은 안정적으로 간주됩니다.
- 불변하고 안정적인 공개 속성으로 특징지어진 데이터 클래스 또는 안정성 주석인 @Stable이나 @Immutable과 같이 명시적으로 표시된 클래스는 안정적으로 간주됩니다. 이러한 주석의 구체적인 내용에 대해 다음 섹션에서 자세히 살펴볼 것입니다.

예를 들어, 아래와 같이 데이터 클래스를 상상해 볼 수 있습니다:

Composable 컴파일러에 의해 안정적으로 간주되는 불변 기본 속성으로 구성된 User 데이터 클래스입니다.

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

그러나, Composable 함수 내에서 매개변수 유형을 평가하는 컴파일러는 아래 기준에 따라 해당 유형을 불안정하다고 식별합니다:

- List, Map 등의 인터페이스와 Any와 같은 추상 클래스와 같이 컴파일 시간에 구현이 예측할 수 없는 유형은 불안정하다고 간주됩니다. 이 분류의 근거는 다음 섹션에서 더 자세히 논의될 예정입니다.
- 특히 적어도 하나의 가변 또는 본질적으로 불안정한 공용 속성을 포함하는 데이터 클래스와 같은 클래스는 불안정하다고 분류됩니다.

예를 들어, 다음과 같은 데이터 클래스를 상상해볼 수 있습니다:

User 데이터 클래스는 기본 속성으로 구성되어 있지만, 가변 이름 속성의 존재로 인해 Compose 컴파일러가 이를 불안정하다고 분류합니다. 이 분류는 안정성이 모든 속성의 종합적인 안정성을 평가하여 단일 가변 속성이 전체 클래스를 불안정하게 만들 수 있기 때문에 발생합니다.

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

# 스마트 재구성

안정성의 원칙을 탐색하고 Compose 컴파일러가 안정적인지 불안정한지를 구별하는 방법을 살펴봤다면, 이러한 차이점을 활용하여 재구성을 트리거하는 실용적인 사용법에 관심이 생길 수 있습니다. Compose 컴파일러는 변경 가능 함수의 각 매개변수의 안정성을 평가하여 Compose 런타임이 이 정보를 효율적으로 활용할 수 있도록 기초를 마련합니다.

클래스의 안정성이 결정되면, Compose 런타임은 이 통찰력을 활용하여 내부 메커니즘인 스마트 재구성을 통해 재구성을 시작합니다. 스마트 재구성은 제공된 안정성 정보를 활용하여 불필요한 재구성을 선택적으로 건너뛰어 Compose의 전체 성능을 향상시킵니다.

스마트 재구성이 작동하는 원칙 중 일부는 다음과 같습니다:

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

- 등가성 확인: Composable 함수에 새 입력이 전달될 때마다 해당 입력은 반드시 클래스의 equals() 메서드를 사용하여 이전 입력과 비교됩니다.
- 안정성에 따른 결정:

- 매개변수가 안정적이고 값이 변경되지 않은 경우 (equals()가 true를 반환), Compose는 관련 UI 구성 요소를 다시 구성하지 않습니다.
- 매개변수가 불안정하거나 안정적이지만 값이 변경된 경우 (equals()가 false를 반환), 런타임은 다시 구성을 시작하여 UI 레이아웃을 무효화하고 다시 그립니다.

위 시나리오에서 불필요한 다시 구성을 피함으로써 UI 성능을 향상시킬 수 있습니다. 전체 UI 트리를 다시 구성하는 것은 상당한 계산 리소스를 필요로하며, 적절하게 처리되지 않으면 성능에 부정적인 영향을 줄 수 있습니다.

Jetpack Compose는 스마트한 다시 구성을 기본적으로 제공하지만, Composable 함수에서 사용되는 클래스를 안정화하고 최대한 다시 구성을 줄이는 방법을 숙지하는 것이 중요합니다.

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

# 합성 가능한 함수 추론하기

이제 컴포즈 컴파일러가 클래스 안정성을 결정하고 컴포즈 런타임이 이 정보를 내부 메커니즘인 스마트 재구성으로 활용하는 방법을 이해했습니다. 그렇지만 이해해야 할 또 다른 중요한 개념은 합성 가능한 함수의 유형 추론입니다.

컴포즈 컴파일러는 코틀린 컴파일러 플러그인을 사용하여 개발자가 작성한 소스 코드를 컴파일 시 분석할 수 있게 만들어졌습니다. 더불어, 컴포즈 함수의 고유한 특성과 더 잘 일치하도록 원본 소스 코드를 조정할 수 있습니다.

컴파일러는 합성 가능한 함수를 시작 가능함, 이동 가능함, 대체 가능함 등으로 분류하여 실행을 최적화합니다. 이 게시물에서는 특히 재구성에 중요한 역할을 하는 시작 가능한 유형에 대해 자세히 살펴볼 것입니다.

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

# 다시 시작 가능한

다시 시작 가능한은 Composable 함수의 일종으로, Compose 컴파일러에 의해 결정되며 recomposition 프로세스의 중추 역할을 합니다. 이전에 탐구한 대로, Compose 런타임은 입력 값의 변화를 감지하면 이러한 새 입력 값으로 함수를 다시 시작(또는 다시 호출)하여 데이터 변경을 정확하게 반영합니다.

Compose 런타임에서 제공하는 특정 주석으로 Composable 함수를 명시적으로 주석 처리하지 않으면 대부분의 함수는 기본적으로 다시 시작 가능하다고 간주됩니다. 이는 Compose 런타임이 언제든지 입력 또는 상태 변경이 발생할 때 해당 Composable 함수에 대해 recomposition을 트리거할 수 있다는 것을 의미합니다.

# 건너뛰기 가능한

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

Skipable은 또 다른 Composable 함수의 특성을 나타내며, 이는 이전 섹션에서 논의된 스마트 recomposition에 의해 설정된 적절한 조건 하에서 전체 recomposition 프로세스를 완전히 우회할 수 있음을 의미합니다. 따라서, skipable 함수가 바로 recomposition을 건너뛰고 UI 성능을 향상시킬 수 있는 잠재력과 직접적으로 연결된다고 단언할 수 있습니다. 이는 특정 상황에 따라서 달려있는데요.

이 능력은 특히 규모가 큰 함수 호출 계층 구조의 정상 Composable 함수의 성능을 향상시키는 데 중요합니다. 이러한 루트 Composable의 recomposition을 건너뛰면, Compose는 이러한 루트 Composable의 recomposition을 건너뛰면, 해당 계층의 하위 함수들 중 어떤 것도 호출할 필요가 없어지며, 전체 recomposition 프로세스가 간소화됩니다.

Composable 함수가 재시작 가능(restartable)이면서 동시에 skippable로 분류되는 경우도 있음을 기억하는 것이 중요합니다. skippable로 분류되면 재시작 가능한 recomposition을 거칠 수 있다는 것을 함축하므로요. 이제 작성한 Composable 함수가 재시작 가능 또는 skippable로 분류되는지 알아보는 방법을 살펴보겠습니다.

# Compose Compiler Metrics

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

Compose 컴파일러 플러그인을 사용하면 Compose에 고유한 특정 개념에 중점을 둔 자세한 보고서와 메트릭을 생성할 수 있습니다. 이러한 통찰력은 Compose 코드의 복잡성을 파헤치는 데 유용하며, 마이크로 레벨에서 작동 방식을 정확하게 이해할 수 있도록 도와줍니다.

Compose 컴파일러 메트릭을 생성하려면, 아래 예제에 설명된 대로 루트 모듈의 build.gradle 파일에 컴파일러 옵션을 추가하면 됩니다:

프로젝트를 동기화하고 빌드한 후, /build/compose_metrics 디렉토리에 생성된 세 가지 다른 파일인 module.json, composablex.txt 및 classes.txt에 액세스할 수 있습니다. 이 파일들을 각각 자세히 살펴보겠습니다.

## 최상위 메트릭 (modules.json)

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

이 보고서는 Compose에 특화된 고수준 메트릭을 제공하며, 주로 추적할 수 있는 숫자 데이터 지점을 생성하는 데 목적이 있습니다. 이러한 메트릭 간의 관계는 통찰력 있는 관찰을 제공할 수 있습니다. 예를 들어, "skippableComposables"의 수를 "restartableComposables"의 수와 비교하면, Composable 함수의 재구성이 건너뛰어질 비율을 나타내는 백분율이 도출됩니다.

아래는 foundation 모듈을 위한 샘플 보고서입니다:

```js
{
 "skippableComposables": 36,
 "restartableComposables": 41,
 "readonlyComposables": 6,
 "totalComposables": 60,
 "restartGroups": 41,
 "totalGroups": 82,
 "staticArguments": 25,
  "certainArguments": 138,
  "knownStableArguments": 377,
  "knownUnstableArguments": 25,
  "unknownStableArguments": 24,
  ..
```

## Composable Signatures (composables.txt)

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

이 보고서는 사람이 이해하기 쉽도록 작성된 의사 Kotlin 스타일 함수 서명을 사용합니다. 이 모듈 내의 각 구성 가능한 함수를 자세히 살펴보며 각 매개변수를 분석하고 특정 통찰을 제공합니다.

이 보고서는 전체 구성 가능한 함수가 다시 시작 가능한지, 건너뛸 수 있는지 또는 읽기 전용인지를 식별합니다. 또한 각 매개변수를 안정적인지 불안정한지로 레이블링하고 각 기본 매개변수 표현을 정적인지 동적인지로 표시하여 구성 가능한 특성에 대한 포괄적인 개요를 제공합니다.

기본적으로 이러한 서명들은 Composable 함수가 건너뛰기 가능한지 여부를 분석하거나 함수가 건너뛰기가 불가능하도록 제약하는 불안정한 매개변수를 식별하는 데 사용할 수 있습니다.

다음은 Composable 함수에 대한 샘플 보고서입니다:

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

```kotlin
restartable skippable scheme("[androidx.compose.ui.UiComposable]") fun Avatar(
  stable modifier: Modifier? = @static Companion
  stable imageUrl: String? = @static null
  stable initials: String? = @static null
  stable shape: Shape? = @dynamic VideoTheme.<get-shapes>($composer, 0b0110).circle
  stable textSize: StyleSize? = @static StyleSize.XL
  stable textStyle: TextStyle? = @dynamic VideoTheme.<get-typography>($composer, 0b0110).titleM
  stable contentScale: ContentScale? = @static Companion.Crop
  stable contentDescription: String? = @static null
)

## Classes (classes.txt)

This report also utilizes pseudo-Kotlin style function signatures crafted for human readability. This file is designed to help you grasp how the stability inferencing algorithm has interpreted a specific class. At the top level, each class is categorized as stable, unstable, or runtime. “Runtime” indicates that the stability is contingent on other dependencies, which will be determined at runtime (such as a type parameter or a type in an external module).

The stability assessment is based on the class’s fields, with each field listed under the class and labeled as stable, unstable, or runtime stable. The bottom line reveals the “expression” employed to determine this stability at runtime, providing a comprehensive overview of how each class`s stability is evaluated.
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

```kotlin
stable class StreamShapes {
  stable val circle: Shape
  stable val square: Shape
  stable val button: Shape
  stable val input: Shape
  stable val dialog: Shape
  stable val sheet: Shape
  stable val indicator: Shape
  stable val container: Shape
}
```

일련의 Compose 컴파일러 메트릭을 생성하는 프로세스를 탐험하고, 각 파일의 중요성을 이해하며, 이 정보를 사용하여 더 많이 건너뛸 수 있는 Composable 함수를 작성하려고 노력하는 방법을 배웠어요. 이 주제를 깊이 있게 탐구하고 싶다면 상세한 통찰을 얻기 위해 Interpreting Compose Compiler Metrics를 확인해보세요.

# 안정성 주석

이제 Compose 컴파일러가 안정성을 처리하는 방식과 이러한 안정성 결정이 다시 구성에 어떻게 영향을 미치는지, 그리고 당신의 애플리케이션 성능에 어떻게 영향을 미칠 수 있는지 통찰력을 얻었어요.

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

Composed of immutability and stability annotations, let's take a journey into transforming changeable classes into reliable ones using annotations from the compose-runtime library. The main actors in this process are @Immutable and @Stable.

# Immutability

When you decorate your class with the @Immutable annotation, you reassure the Compose compiler that all public properties and fields within the class will remain unchanged (immutable) once they are created. This provides a more solid guarantee compared to the val keyword at the language level. While val prevents properties from being modified via a setter, it still allows creation through mutable data structures like Lists initialized with MutableList.

To make sure your classes are distinctly marked as stable with the @Immutable annotation, adhere to the guidelines below:

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

- 모든 공용 속성에 val 키워드를 사용하여 불변성을 보장하세요.
- 사용자 지정 setter를 피하고 공용 속성이 변경 가능성을 지원하지 않도록 합니다.
- 모든 공용 속성의 유형이 본질적으로 불변 또는 안정적이거나 안정성 주석으로 명시적으로 표시되었는지 확인하세요. 예를 들어 인터페이스는 불안정하다고 간주되므로 속성으로 사용되는 모든 인터페이스 유형도 안정성을 위해 주석을 달아야 합니다.
- 컬렉션인 속성들의 경우, 안정성을 유지하기 위해 kotlinx.collections.immutable에서 제공하는 불변 컬렉션을 선택하세요.

@Immutable 주석은 위의 불변성 규칙을 준수하는 클래스에 효과적이며, 불필요한 recomposition을 건너뛰어 응용프로그램 성능을 향상시킬 때 중요한 역할을 합니다.

그러나 @Immutable 주석을 분별하게 사용하는 것이 중요합니다. 적절하게 사용하지 않으면 의도하지 않은 recomposition 건너뛰기로 인해 Compose 레이아웃이 예상대로 업데이트되지 않을 수 있습니다.

# 안정함

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

`@Stable` 어노테이션은 `@Immutable` 어노테이션보다 Compose 컴파일러에 더 강력하지만 약간 덜 엄격한 커밋먼트를 나타냅니다. 함수나 프로퍼티에 적용되면, `@Stable` 어노테이션은 해당 유형이 가변할 수 있다는 것을 나타냅니다. 처음에는 약간 모순적으로 보일 수도 있습니다. 이 문맥에서 "Stable"이라는 용어는 함수가 동일한 입력에 대해 항상 동일한 결과를 반환할 것이라는 의미로, 잠재적인 가변성에도 불구하고 예측 가능한 동작을 보장합니다.

따라서 `@Stable` 어노테이션은 주로 public 프로퍼티가 불변인 클래스에 적합하지만 클래스 자체가 안정적이지 않을 수 있는 경우에 사용됩니다. 예를 들어, Jetpack Compose의 State 인터페이스는 value라는 불변 프로퍼티만 노출합니다. 그러나 이 불변 프로퍼티의 내부 값은 여전히 setValue 함수를 통해 수정될 수 있으며, 일반적으로 MutableState를 생성하여 이를 수행합니다.

State와 MutableState를 통한 데모로 보여 준 것처럼, MutableState에 의해 생성된 State 인스턴스는 getValue 함수(값 프로퍼티의 게터)로부터 일관되게 동일한 값을 얻을 것이며, setValue 함수에 대한 동일한 입력에 대해 동일한 결과를 반환합니다. 제공된 코드 스니펫에서는 `@Stable` 어노테이션이 지정된 State와 MutableState 인터페이스를 모두 보여줍니다.

# Immutable vs Stable

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

**@Immutable**과 **@Stable** 주석 사이의 구별과 어떤 것을 사용해야 하는지 결정하는 것은 처음에는 혼란스러울 수 있습니다. 하지만 실제로는 꽤 간단합니다. 이전에 언급했듯이, **@Immutable** 주석은 클래스의 모든 공용 속성이 불변이라는 의미로, 생성된 후에 상태가 변경될 수 없음을 나타냅니다. 반면에 **@Stable** 주석은 가변 객체에 적용될 수 있으며, 동일한 입력에 대해 일관된 결과를 생성해야 하는 것을 요구합니다.

**@Immutable** 주석은 대부분 도메인 모델에 적용되는데, 특히 Kotlin 데이터 클래스를 사용할 때 다음 예시에서와 같이 나타납니다:

반면에 **@Stable** 주석은 여러 구현 가능성을 제공하는 인터페이스에 대해 일반적으로 사용되며, 내부 가변 상태를 가질 수 있습니다. 아래 의미 있는 예시는 이 주석을 이해하는 데 도움이 됩니다:

**@Stable** 주석을 적용하면 **UiState** 클래스를 안정적으로 지정할 수 있습니다. 이는 최적화된 건너뛰기와 지능적인 재구성을 가능하게 하여 업데이트의 효율성을 향상시킵니다.

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

# NonRestartableComposable

Jetpack Compose의 @NonRestartableComposable 주석은 특정 구성 가능한 함수의 recomposition 동작을 최적화하기 위해 고안된 비교적 고급 기능입니다. 이 주석은 구성 가능한 함수가 호출 매개변수의 변경으로 인해 recomposition 중 자동으로 다시 시작되지 않아야 함을 Compose 컴파일러에 알리는 역할을 합니다. 일반적으로 구성 가능한 함수의 입력 값이 변경되면 Compose 런타임은 함수를 다시 시작하여 새로운 입력 값을 소비하게 할 수 있습니다.

그러나 이러한 다시 시작이 항상 필요하거나 원하는 것은 아닐 수 있습니다, 특히 함수의 내부 상태나 부작용을 다시 시작해야 할 때가 아닌 경우에 해당합니다. @NonRestartableComposable을 적용하면 런타임이 함수를 다시 시작하지 않고 매개변수를 업데이트하도록 지시하여 내부 상태와 진행 중인 부작용을 유지할 수 있습니다.

@NonRestartableComposable의 일부로 동작하는 대표적인 예는 Compose 런타임 라이브러리의 Side-effect API 내에서 발견됩니다. 예를 들어 LaunchedEffect의 구현은 이 주석을 사용하여 효과가 불필요하게 다시 시작되지 않도록 보장합니다. 아래 코드에서 보여진 것처럼요:

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

그러나 @NonRestartableComposable 주석을 사용할 때 주의해야 하며 앱 성능을 향상시키기 위한 수단으로만 사용해서는 안 됩니다. 선별적으로 사용하지 않으면 원하지 않는 결과로 이어질 수 있습니다.

# Composable 함수 안정화

앱 성능을 최적화하는 목적으로 안정한 클래스를 작성하는 방법에 대해 알아보았습니다. 그러나 Composable 함수의 완전한 안정성 달성은 여기서 멈추지 않습니다. 왜냐하면 일부 클래스(예: Kotlin의 컬렉션 또는 제3자 라이브러리에서 제공하는 클래스)는 직접 제어할 수 없을 수 있기 때문입니다.

이전에 언급했던 바와 같이 스마트 recomposition 중 Composable 함수를 건너뛸 수 있는 능력은 해당 함수의 각 매개변수의 안정성에 의해 결정됩니다. 스마트 recomposition을 위해 Composable 함수 내에서 사용되는 모든 매개변수가 안정적임을 보장하는 것이 중요합니다.

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

이 섹션에서는 Composable 함수를 스킵할 수 있는 네 가지 다른 전략을 탐구하여 효율적인 recomposition으로 성능을 향상시킬 수 있습니다.

# Immutable Collections

처음에는 List가 요소를 수정하는 것을 허용하지 않더라도, 인터페이스인 특히 kotlin.collections이 Jetpack Compose에서 불안정하게 여겨지는 이유를 의심할 수 있습니다.

아래의 좋은 예제를 살펴보면 그 이유를 이해할 수 있습니다:

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

userList 필드는 List로 선언되어 있습니다. List는 기본적으로 요소의 수정을 허용하지 않습니다. 그러나 첫 번째 줄에서 나와 있듯이, 이 List는 MutableList로부터 생성될 수 있습니다. 이는 List 인터페이스 자체가 수정을 제한하지만 해당 내부 구현이 변경 가능할 수 있다는 것을 의미합니다. Compose 컴파일러는 구현 유형을 추론할 수 없어, 이러한 인스턴스들을 안정적이지 않은 것으로 간주하여 정확한 동작을 보장합니다.

그러므로 공식 Android 문서에서는 Composable 함수의 컬렉션 매개변수의 안정성을 보장하기 위해 kotlinx.collections.immutable 라이브러리나 Guava의 Immutable Collections를 활용할 것을 권장합니다.

kotlinx.collections.immutable 라이브러리는 ImmutableList 및 ImmutableSet과 같은 여러 컬렉션을 제공하며, 이는 표준 kotlin.collections의 동작을 모방하지만 변경 불가능합니다. 이러한 컬렉션은 읽기 전용이며, 생성 후에는 수정이 불가능합니다.

이제 Compose 컴파일러가 kotlinx.collections 대비 kotlinx.collections.immutable의 안정성을 결정할 때 고려하는 주요 요소에 대해 궁금해 할 수 있습니다. 구분은 Compose 컴파일러가 변경 불가능 컬렉션을 이해하는 데 있습니다.

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

더 자세한 통찰을 얻으려면 Compose 컴파일러 라이브러리의 일부인 KnownStableConstructs.kt 파일을 참고하십시오. 아래 코드를 보면, Compose 컴파일러가 안정적으로 간주해야 하는 클래스의 패키지 이름 목록을 수동으로 유지한다는 것을 확인할 수 있습니다:

아래 코드 조각을 살펴보면, Composable 함수의 매개변수 안정성을 분석하는 Compose 컴파일러의 일부입니다. 알려진 안정 구조체 클래스에 나열된 매개변수 유형에 대한 안정성을 컴파일러가 유추하지 않는 것이 분명합니다:

## Lambda

Compose 컴파일러에서는 Kotlin 람다 표현식의 처리가 독특한 접근 방식을 취합니다. 이전에 설명한 바와 같이, Compose 컴파일러는 IR(Intermediate Representation) 변환을 통해 개발자가 작성한 소스 코드를 수정합니다. 따라서 컴파일러는 Composable 함수에 전달된 람다의 실행을 최적화하기 위해 Compose 런타임에 일부 규칙을 생성합니다.

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

Compose 컴파일러는 람다 표현식을 처리할 때 람다가 값을 캡처하는지 여부에 따라 다른 방식으로 다룹니다. 클로저의 맥락에서 값들을 캡처하는 것은 람다 표현식이 직접적인 범위 외부에 있는 변수에 의존한다는 의미입니다. 외부 변수에 독립적인 람다라면 아래 예시처럼 값들을 캡처하지 않는다고 말할 수 있습니다:

람다 매개변수가 어떤 값도 캡처하지 않는 경우, Kotlin은 이러한 람다를 싱글톤으로 처리하여 불필요한 할당을 최소화합니다. 반면, 람다가 클로저 밖의 변수에 의존하는 경우, 아래 예시에서 볼 수 있듯이 값들을 캡처한다고 간주됩니다:

람다 매개변수가 외부 값들을 캡처하는 경우, 그 실행 결과는 캡처된 값들에 따라 달라질 수 있습니다. 이를 해결하기 위해, Compose 컴파일러는 메모리제이션 전략을 사용하여 람다를 remember 함수 호출 내에 캡슐화합니다. 캡처된 값은 remember에 대한 키 매개변수로 작용하여, 캡처된 값들의 변화에 적절히 반응하여 람다가 적절하게 재호출되도록 합니다.

결과적으로, 람다가 값들을 캡처하는지 여부에 관계없이, 해당 람다는 Composable 함수 내에서 안정적으로 간주됩니다. Composable 함수가 Any 유형의 매개변수를 수용하는 시나리오를 고려해보면, Any가 변경 불가능한 값을 포함하는 등 다양한 값 범위를 포함할 수 있기 때문에, Compose 컴파일러에서는 불안정하게 취급됩니다:

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

그러나 아래 예시와 같이 람다 표현식을 사용하여 값을 제공하는 경우, Compose 컴파일러는 람다 매개변수를 안정적으로 처리합니다:

# Wrapper Class

Composable 함수를 안정화하는 또 다른 효과적인 전략은 제어 범위를 벗어난 불안정한 클래스에 대한 래퍼 클래스를 만드는 것입니다. 이러한 경우에는 예시와 같이 안정성 주석을 직접 적용할 수 없는 클래스에 적용할 수 있습니다.

그런 다음 Composable 함수의 매개변수 유형으로 이 래퍼 클래스를 활용할 수 있습니다. 아래 코드에서 보여지는 것처럼요:

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

# 파일 구성

Compose 컴파일러 버전 1.5.5부터 구성 파일에서 클래스를 나열할 수 있는 옵션이 추가되었습니다. 이러한 지정된 클래스는 Compose 컴파일러에 의해 안정적으로 인식됩니다. 이 기능은 서드파티 라이브러리에서 가져온 클래스와 같이 제어할 수 없는 클래스들을 사용할 때 매우 유용합니다.

이 기능을 활성화하려면 아래와 같이 앱 모듈의 build.gradle.kts 파일에 Compose 컴파일러 구성을 추가하십시오:

다음으로, 앱 모듈의 루트 디렉토리에 compose_compiler_config.conf 파일을 아래와 같이 생성하십시오:

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
// LocalDateTime을 안정적으로 간주합니다.
java.time.LocalDateTime
// Kotlin 컬렉션을 안정적으로 간주합니다.
kotlin.collections.*
// 내 데이터 계층과 모든 하위 모듈을 안정적으로 간주합니다.
com.datalayer.**
// 제네릭 타입을 첫 번째 타입 매개변수를 기반으로 안정적으로 간주합니다.
com.example.GenericClass<*,_>
```

프로젝트를 빌드하고 Compose 컴파일러 메트릭을 생성하면, 구성 파일에서 지정한 클래스들이 안정적으로 인식되어 스마트 재구성을 건너뛸 수 있습니다.

공식 안드로이드 가이드에 따르면, Compose 컴파일러는 각 프로젝트 모듈 별로 독립적으로 작동하므로 필요에 따라 다른 모듈에 대해 각기 다른 구성을 제공할 수 있습니다. 또는 프로젝트 루트 수준에서 단일 구성을 선택하고 각 모듈에 대해 해당 경로를 지정할 수도 있습니다.

기억해야 할 중요한 점은 구성 파일이 정의된 클래스들을 기본적으로 안정적으로 만들지 않습니다. 대신, 구성 파일을 활용하여 Compose 컴파일러와 계약을 맺는 것입니다. 따라서이 기능을 분별있게 사용하여 특정 시나리오에서 스마트 재구성 프로세스를 우연히 건너뛰는 일을 피해야 합니다.

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

# 멀티 모듈 아키텍처의 안정성

당신의 Gradle 모듈을 모듈화하는 것은 훌륭한 전략으로, 향상된 재사용성, 병렬 빌드, 탈중앙화된 팀 집중력 등의 혜택을 제공합니다. 안드로이드 공식 가이드도 모듈화를 권장하며, 프로젝트 규모에 맞게 코드의 확장성을 향상시키고 가독성을 개선하며 전반적인 코드 품질을 높이는 수단으로 모듈화를 소개합니다.

모듈화는 Jetpack Compose에서 고유한 도전 과제를 소개합니다: 독립된 모듈로부터의 클래스는 그들의 퍼블릭 프로퍼티의 불변성 여부와 상관없이 불안정하다고 간주됩니다. 이를 극복하기 위해, 데이터 모듈에 compose-runtime 라이브러리를 가져오고 데이터 클래스에 안정성 주석을 달 것을 권장합니다.

그러나, Jetpack Compose 런타임 라이브러리에 직접 의존하지 않고 순수한 Kotlin/JVM 라이브러리에 초점을 맞춘 경우에는 compose 런타임 라이브러리에 의존하는 것이 이상적이지 않은 경우가 있을 수 있습니다. 이러한 시나리오에서 두 가지 주요 솔루션이 제시됩니다: compose-stable 마커 라이브러리를 채택하거나 안정성을 보장하기 위해 파일 구성을 활용하는 것입니다.

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

# 안정적인 마커 작성기

"안정적인 마커" 라이브러리는 @Immutable 및 @Stable과 같은 안정성 주석을 제공하며, 이는 compose-runtime 라이브러리 내의 유사한 주석 기능을 반영합니다. compose-runtime 라이브러리를 직접 사용하는 것 대신 compose-stable 마커 라이브러리를 선택하면 아래에 설명된 두 가지 주요 이점이 제공됩니다:

- 가벼움: 클래스, 함수 및 확장 기능이 풍부한 compose-runtime 라이브러리는 응용 프로그램의 크기를 늘릴 수 있는 가능성이 있습니다. 반면에 compose-stable 마커 라이브러리는 안정성 주석에만 초점을 맞춘 가벼운 대안을 제공합니다. 이를 통해 응용 프로그램의 크기를 줄이고 전체 compose-runtime 라이브러리를 사용하는 것보다 빌드 시간을 단축할 수 있습니다.
- 의존성 없음: compose-runtime 라이브러리에는 SideEffect, LaunchedEffect, snapshotFlow 및 Compose 컴파일러와 관련된 기타 주석들과 같은 Compose 런타임 기능을 실행하는 데 필수적인 기능이 포함되어 있습니다. 이러한 설정은 데이터 모듈에 필요하지 않은 경우에도 모듈이 이러한 API에 액세스 할 수 있는 가능성을 발생시킬 수 있습니다. compose-stable 마커 라이브러리를 선택하면 이러한 특수화된 API에 실수로 액세스하는 위험을 제거하여 모듈이 집중되고 효율적으로 유지되도록 보장합니다.

해당 라이브러리의 좋은 사용 사례는 Compose용 Stream의 적응 가능한 채팅 및 비디오 SDK의 코어 모듈에서 발견할 수 있습니다. 이러한 SDK의 코어 모듈은 compose-stable 마커를 활용하여 도메인 클래스를 안정적으로 지정합니다.

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

compose-stable-marker 라이브러리에 대해 더 알아보려면 GitHub 저장소를 방문해 주세요.

# 파일 구성

이전 섹션에서 논의한 것처럼 파일 구성은 Compose 컴파일러와의 계약을 달성하여 원본이나 가변성과 무관하게 특정 클래스를 안정적으로 다루도록 합니다. 이는 다른 모듈에서 클래스를 나열하여 파일 구성에 포함시키면 컴파일러가 자동으로 이를 안정적인 것으로 인식한다는 것을 의미합니다.

한 가지 강조해야 할 점은 이 기능을 분별하여 사용해야 합니다. Compose 컴파일러는 이러한 클래스들을 안정적으로 지속적으로 다룰 것이므로 스마트한 재구성 행동을 조정하여 의도하지 않은 동작을 유발할 수 있습니다. 또한, 이러한 강제적인 안정성으로 인한 디버깅 문제는 애플리케이션 내에서 해결하기 어려울 수 있습니다.

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

# 강한 스킵 모드

컴포저블 함수를 제작할 때 건너뛸 수 있는 또 다른 전략은 강한 스킵 모드를 활성화하는 것입니다. Compose Compiler 버전 1.5.4에서 소개된 이 기능은 불안정한 매개변수를 포함한 경우에도 컴포저블 함수를 건너뛸 수 있게 해주며, 불안정한 캡처를 포함하는 람다를 최적화된 성능을 위해 메모이즈합니다.

현재 실험 단계에 있으며 아직 제품용으로 준비되지 않았지만, 강한 스킵 모드는 Compose 1.7 알파에서 기본으로 활성화될 예정입니다. 효과와 안정성은 베타 단계로 진행되기 전에 철저히 평가될 것입니다. 일시적으로 이 실험적 기능을 활성화하려면 다음 Compose 컴파일러 옵션을 포함시키면 됩니다:

강한 스킵 모드는 재구성 중에 컴포저블 함수를 건너뛸 때 Compose 컴파일러가 사용하는 전통적인 안정성 기준을 수정합니다. 일반적인 상황에서 컴포저블 함수는 오직 안정적인 매개변수만 포함하고 있는 경우에만 건너뛸 수 있는 것으로 간주됩니다. 그러나 강한 스킵 모드는 이 전통을 변경합니다.

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

이 기능이 활성화되면 모든 다시 시작 가능한 Composable 함수가 건카볼입니다. 이전 값을 무시하고 불안정한 매개변수가 포함되었는지 여부에 관계없이. 그러나, 다시 시작할 수 없는 Composable 함수는 영향을 받지 않으며 건너뛸 수 없습니다.

재조합 중 Composable 함수를 건너뛸지 여부를 평가할 때 이 모드는 불안정한 매개변수와 해당 이전 값과의 인스턴스 동등성을 비교하기 위해 사용됩니다. 반면에, 안정적 매개변수는 Object.equals()에 의해 정의된 객체 동등성을 사용하여 비교됩니다.

재조합 중 이러한 기준에 모든 매개변수가 일치하는 경우 Composable 함수가 우회됩니다. 불필요한 업데이트를 줄여 성능을 최적화합니다.

Composable 함수를 강력한 건카볼 모드에서 제외하고 다시 시작할 수 있지만 건너뛸 수 없도록 만들려면 @NonSkippableComposable 주석을 적용할 수 있습니다. 이는 매개변수 안정성과 관계없이 항상 재조합을 위해 해당 함수가 고려됨을 보장합니다.

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

한편, 객체 동등성(object equality)을 사용하여 객체를 비교하려면 여전히 도메인 모델 클래스에 @Stable 주석을 추가해야 합니다.
이 기능과 람다 메모이제이션(Lambda Memoization)의 향상된 개념에 대한 더 깊은 이해를 위해 '강력한 스킵 모드(Strong Skipping Mode)'에 대한 상세 가이드를 참조해보세요.

# 결론

탐험이 마무리되었습니다! 안정성의 개념, 안정성 추론과 스마트 recomposition 뒤에 숨은 메커니즘, 클래스 및 Composable 함수를 안정화하는 효과적인 전략, 그리고 응용 프로그램 성능을 향상시키는 방법에 대해 다루었습니다.

안정성의 중요성을 깨달으면 화면에 UI 노드를 렌더링하는 메커니즘에 영향을 줌으로써 최종적으로 응용 프로그램의 성능에 영향을 미치게 됩니다.

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

이 테이블 태그를 마크다운 형식으로 변경해주세요.

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

원래 getstream.io에 게시되었습니다.
