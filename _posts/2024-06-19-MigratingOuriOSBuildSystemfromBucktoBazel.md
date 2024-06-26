---
title: "iOS 빌드 시스템을 Buck에서 Bazel로 전환하기"
description: ""
coverImage: "/assets/img/2024-06-19-MigratingOuriOSBuildSystemfromBucktoBazel_0.png"
date: 2024-06-19 14:05
ogImage:
  url: /assets/img/2024-06-19-MigratingOuriOSBuildSystemfromBucktoBazel_0.png
tag: Tech
originalTitle: "Migrating Our iOS Build System from Buck to Bazel"
link: "https://medium.com/airbnb-engineering/migrating-our-ios-build-system-from-buck-to-bazel-ddd6f3f25aa3"
---

<img src="/assets/img/2024-06-19-MigratingOuriOSBuildSystemfromBucktoBazel_0.png" />

## Airbnb은 Buck에서 Bazel로의 iOS 움직임을 효율적이고 투명하게 성공적으로 달성했습니다. 개발자의 작업에 최소한의 방해를 주면서요.

작성자: Qing Yang, Andy Bartholomew

Airbnb에서는 엔지니어들에게 최고의 경험을 제공하기 위해 노력하고 있습니다. 모든 플랫폼에서 일관되고 효율적인 빌드 경험을 제공하기 위해 저희는 빌드 시스템으로 Bazel을 도입하기로 결정했습니다. Bazel은 산업에서 널리 사용되는 견고한 빌드 시스템입니다. Airbnb의 기술 계획에 부합하여, 백엔드 및 프론트엔드 팀은 모두 Bazel로의 이관 프로세스를 시작했습니다. 첫 번째 Bazel 포스트에서는 iOS 개발에서 Buck에서 Bazel로의 이관 작업에 대해 시작합니다.

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

저희는 두 가지 주요 작업으로 구성된 이주 접근 방식을 설명하겠습니다: 빌드 구성 이주 및 IDE 통합 이주. 이러한 전환은 엔지니어들의 작업 흐름을 방해하거나 새로운 기능 개발을 저해할 수 있지만, 우리는 개발자들의 일상 경험을 방해하지 않고 성공적으로 이주할 수 있었습니다. 저희 목표는 현재 유사한 이주를 진행하고 있는 분들이나 이와 유사한 이주를 계획 중인 분들을 도와드릴 수 있도록 하는 것입니다.

# 빌드 구성 이주

빌드 구성에 관련하여, Buck와 Bazel은 상당한 유사성을 보여줍니다. 두 빌드 시스템은 유사한 디렉토리 구조를 사용하고 유도 명령어 호출도 비슷하며 중요한 것은 둘 다 Starlark 언어를 사용한다는 점입니다. 이러한 유사성은 두 빌드 시스템 간의 구성 공유 기회를 제공합니다. 이를 통해 우리는 Buck 구성을 Bazel에서 재사용할 수 있게 되었고, 두 빌드 시스템을 모두 사용하면서 이주를 진행하고 있는 "중첩" 단계에서 지연을 피할 수 있었습니다.

하지만, Buck와 Bazel은 서로 다른 매개변수를 가진 구별된 규칙을 사용합니다. 예를 들어, Buck은 apple_library 및 apple_binary와 같은 규칙을 제공하는 반면, Bazel은 외부 규칙 세트에 따라 swift_library와 apple_framework와 같은 규칙을 갖습니다. 두 시스템 모두 genrule과 같은 이름의 규칙이 있는 경우라도, 해당 규칙을 구성하는 구문은 종종 다릅니다. 이 두 시스템의 다른 디자인 철학은 다양한 호환성 문제로 이어집니다. 예를 들어, Bazel은 macro에서 명령줄 옵션을 읽는 read_config 함수를 갖고 있지 않습니다.

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

## rules_shim으로 차이점 숨기기

Buck와 Bazel의 심층 분석을 수행한 후, 두 시스템 간의 차이점을 해결하고 유사성을 활용하기 위해 빌드 구성에 대한 포괄적인 아키텍처를 개발했습니다.

![이미지](/assets/img/2024-06-19-MigratingOuriOSBuildSystemfromBucktoBazel_1.png)

이 아키텍처의 핵심에는 rules_shim 레이어가 있으며, Buck와 Bazel을 위한 두 세트의 규칙을 도입합니다. 이 규칙 세트는 네이티브 및 외부 규칙 주변에 래퍼 역할을 하여 상위 레이어에 대한 통합된 인터페이스를 제공합니다.

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

rules_shim이 정확히 어떻게 작동하는지 궁금하신가요? 로컬 저장소를 활용하여 rules_shim 저장소를 빌드 시스템에 따라 다른 구현으로 지정할 수 있습니다.

Buck의 .buckconfig에서 결과물이 어떻게 나타나는지 살펴보겠습니다:

```js
[repositories];
rules_shim = rules_shim / buck[buildfile];
name = BUILD;
```

또한, 이미 존재하는 BUCK 파일들을 BUILD로 이름을 변경하고 Buck에서도 config 파일로 사용하도록 설정했으므로, Buck와 Bazel에서 동일한 구성을 인식할 수 있습니다.

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

Bazel의 WORKSPACE에서는 다음을 수행합니다:

```js
local_repository((name = "rules_shim"), (path = "rules_shim/bazel"));
```

일반적인 BUILD 파일에서는 my_library를 사용하여 네이티브 규칙을 감싸고 각 애플리케이션에 동일한 인터페이스를 제공합니다:

```js
load("@rules_shim//:defs.bzl", "my_library", …)
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

앱별 규칙 레이어는 구현이 아닌 인터페이스만을 알아야 합니다. 그 결과로 Buck 또는 Bazel 명령을 실행할 때, 빌드 시스템은 rules_shim 레이어에서 해당 구현을 검색할 수 있습니다. 이 설계의 주목할만한 장점은 마이그레이션 이후에 rules_shim/buck를 쉽게 제거할 수 있다는 것입니다.

## genrule 인터페이스 통합

우리 iOS 코드베이스 내에서, 우리는 생성된 코드에 많이 의존하여 엔지니어들에 대한 유지관리 부담을 줄이는 데 사용합니다. 두 빌드 시스템 간 genrule 스크립트의 다른 구문 때문에, 우리는 genrule을 위한 통합된 인터페이스를 설계했습니다. 이로 인해 동일한 genrule 스크립트가 두 빌드 시스템 모두에서 작동할 수 있습니다. 아마도 이미 추측했겠지만, 변환 과정은 rules_shim 레이어에서 구현되었습니다.

![링크 텍스트](/assets/img/2024-06-19-MigratingOuriOSBuildSystemfromBucktoBazel_2.png)

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

## read_config을 select로 교체하기

조건부 구성은 피할 수 없습니다. 왜냐하면 디버그 빌드와 릴리스 빌드와 같은 빌드 제품의 다양한 변형이 항상 있기 때문입니다. Buck은 매크로에서 명령줄 옵션을 읽는 read_config라는 함수를 제공하지만, Bazel은 로딩 단계의 엄격한 분리로 인해 이 기능을 제공하지 않습니다. Buck은 select 함수를 지원하지만 문서화되어 있지 않습니다. 따라서 우리는 모든 read_config 인스턴스를 select 기반 조건으로 마이그레이션했습니다.

```js
deps = select({
  "//:DebugBuild": non_production_deps,
  "//:ReleaseBuild": [],
  # SELECT_DEFAULT는 Buck과 Bazel에서 사용하는 서로 다른 기본 문자열을 고려하기 위해 rules_shim에서 정의됩니다
  SELECT_DEFAULT: non_production_deps,
}),
```

전체적으로, 이 설계를 통해 빌드 시스템의 빌드 구성을 단일로 활용하며, BUILD 파일 자체에는 최소한의 변경이 있습니다. 실제로 Airbnb의 iOS 엔지니어들은 주로 BUILD 파일을 수동으로 수정할 일이 거의 없습니다. 이 파일들은 주로 기본 소스 코드 분석을 통해 자동으로 업데이트됩니다. 그러나 파일을 직접 수정해야 하는 경우에는 특정 빌드 시스템에 대한 지식이 필요하지 않고 통합된 인터페이스에 의존할 수 있습니다.

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

# IDE 통합 마이그레이션

에어비앤비의 iOS 엔지니어들은 주로 Xcode를 통해 빌드 시스템과 상호 작용합니다. 처음 Buck를 도입한 이후로, 우리는 로컬 개발을 위해 Buck에서 생성한 Xcode 워크스페이스를 활용해 왔습니다. 몇 년 동안 이러한 설정 위에 다양한 생산성 향상 기능을 개발했습니다. 이에는 단일 모듈에 초점을 맞춘 작은 개발 애플리케이션인 Dev App, 빌드 및 원격 캐시를 활용하는 Buck Local 및 IDE 성능을 크게 향상시키는 Focus Xcode 워크스페이스도 포함됩니다. 이는 작업 중인 모듈만로드하여 IDE 성능을 크게 향상시킵니다.

Bazel 생태계에서는 Xcode 워크스페이스를 생성하기 위한 다양한 솔루션이 존재합니다. 그러나 우리가 평가할 당시, 이 중 어떤 것도 우리의 요구 사항을 완벽하게 충족하지 못했습니다. 또한 IDE 통합은 빌드뿐만 아니라 편집, 색인, 테스트 및 디버깅도 지원해야 합니다. 현재의 워크스페이스 설정이 검증된 track record와 안정성을 가지고 있기 때문에 완전히 새로운 것으로 전환하는 위험이 너무 높다고 판단했습니다. 그래서 우리는 현재의 설정과 유사한 워크스페이스를 생성하기 위해 우리만의 생성자를 개발하기로 결정했습니다. 빌드 시스템 구현 세부 사항을 분리하기 위한 추상화 계층으로 작동하는 YAML 구성에서 Xcode 프로젝트를 생성하는 인기 있는 도구 인 XcodeGen을 선택했습니다.

![이미지](/assets/img/2024-06-19-MigratingOuriOSBuildSystemfromBucktoBazel_3.png)

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

우리는 세 단계로 구성된 마이그레이션 프로세스를 구현했어요.

첫째로, 우리는 코드베이스에서 필요한 모든 정보를 수집하고 Xcode 워크스페이스를 생성하기 위해 buck query를 활용했어요. 이 과정에서 buck project 명령을 대체했어요. 이 새로운 워크스페이스는 빌드 프로세스 중에 buck build를 호출했어요. 빌드 시스템을 변경하지 않고 유지함으로써, 새로운 생성기의 성능을 평가하고 호환성을 보장했어요.

둘째로, 우리는 Bazel을 사용하여 bazel query와 bazel build에서 병렬 구현을 수행했어요. 생성 스크립트에 간단한 --bazel 옵션을 통합하여 Xcode 내에서 두 빌드 시스템 간에 전환할 수 있는 기능을 추가했어요. 빌드 시스템 외에는 사용자 인터페이스가 동일하게 유지되어, IDE 작업이 이전과 동일하게 작동하도록 보장했어요.

마지막으로, 충분한 수의 사용자가 Bazel을 선택하고 모든 Bazel 기능이 철저히 테스트를 거친 후, --bazel 옵션을 기본값으로 설정하여 Bazel로의 원활한 전환을 완료했어요. 문제가 발생했다면 손쉽게 롤백할 수 있는 선택권이 있었어요. 수주 후에는 생성된 프로젝트에서 Buck 지원을 제거했어요.

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

이 마이그레이션의 최종 결과는 인상적입니다. Buck로 생성된 프로젝트와 비교하여 XcodeGen을 사용한 생성 시간이 60% 줄어들었고, Xcode의 오픈 시간도 70% 이상 감소했습니다. 이로 인해 이 새로운 워크스페이스 설정은 내부 개발자 경험 조사에서 최상위 순위를 차지하며, 이 프로세스를 통해 달성된 중요한 개선 사항을 보여주었습니다.

# 마이그레이션 완료 및 전망

Buck를 사용한 부분에 대해 공통 인터페이스 추상화를 도입하고 Buck와 Bazel 간의 차이를 처리하기 위해 별도의 구현을 삽입했습니다. "간접" 원칙 덕분에 코드를 크게 재작성하지 않고 각 구현을 테스트하고 업데이트할 수 있었으며, 로컬 개발, CI 테스트 및 릴리스를 포함한 모든 사용 사례에서 부드럽게 Buck에서 Bazel로 이전하는 데 성공했습니다. 이 마이그레이션 프로세스는 엔지니어들의 작업 흐름을 방해하지 않고 실행되었으며, 실제로 SwiftUI 미리보기 지원을 포함한 여러 새로운 기능을 제공할 수 있었습니다.

Bazel이 iOS 빌드 시스템이 된 후, 증분 빌드에 특히 빌드 시간이 크게 개선되었음을 관찰했습니다. 이러한 전환으로 인해 Airbnb 내에서 원격 캐시와 같은 공유 인프라를 활용할 수 있게 되었으며, 결과적으로 여러 플랫폼 간의 협업이 촉진되었습니다.

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

저희 iOS 빌드 시스템을 이전하는 것은 에어비앤비에서 진행 중이거나 완료된 여러 Bazel 마이그레이션 중 첫 번째입니다. 저희는 Java/Kotlin/Scala로 된 JVM 기반 언어, JavaScript, Go용 리포지토리를 보유하고 있는데, 이미 Bazel을 사용 중인 것이나 미래에 사용할 것입니다. 전체 코드베이스에 걸쳐 함께 사용되는 단일 빌드 도구를 믿고 있어서 빌드 도구 및 교육에 대한 투자를 효과적으로 활용할 수 있을 것으로 기대합니다. 미래에는 다른 Bazel 마이그레이션에서 얻은 교훈을 공유할 예정입니다.

iOS Bazel 마이그레이션의 기술 리드인 Qing Yang은 구성 아키텍처와 새 프로젝트 생성기를 설계하고 구현했습니다. Andy Bartholomew는 모든 테스트의 마이그레이션을 이끌었고 스크립트 추상화 레이어를 구현했습니다. Xianwen Chen은 Bazel의 릴리스 빌드를 마이그레이션하고 관리했습니다.

에어비앤비 내의 다수의 Bazel 전문가로부터 받은 귀중한 지원에 무한한 감사를 표합니다. 특히 Janusz Kudelka에게는 주제에 대한 소중한 조언과 지도를 제공해 준 것에 대해 특별히 감사드립니다.

또한 Bazel iOS 커뮤니티에 우리의 마이그레이션 여정 동안 제공해준 다양한 오픈 소스 프로젝트와 지원에 대해 감사의 마음을 전합니다.

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

저희가 App Store에서 최고의 iOS 앱을 만들기 위한 여정에 참여하고 싶으시다면, 열려 있는 iOS 및 개발자 플랫폼 직무를 확인해주세요.

모든 제품 이름, 로고 및 브랜드는 각 소유자들의 소유입니다. 이 웹사이트에서 사용된 모든 회사, 제품 및 서비스 이름은 식별 목적으로만 사용되었습니다. 이러한 이름, 로고 및 브랜드의 사용은 승인을 시사하지 않습니다.
