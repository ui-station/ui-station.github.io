---
title: "특정 지역 자원 및 잘못된 로컬라이제이션 - 안드로이드"
description: ""
coverImage: "/assets/img/2024-05-27-RegionspecificresourcesandbrokenlocalizationAndroid_0.png"
date: 2024-05-27 17:53
ogImage:
  url: /assets/img/2024-05-27-RegionspecificresourcesandbrokenlocalizationAndroid_0.png
tag: Tech
originalTitle: "Region specific resources and broken localization — Android"
link: "https://medium.com/proandroiddev/region-specific-resources-and-broken-localization-android-27a0d20734c6"
---

![이미지](/assets/img/2024-05-27-RegionspecificresourcesandbrokenlocalizationAndroid_0.png)

이 기사의 일환으로, 우리 애플리케이션에서 로컬라이제이션 지원을 망가뜨린 리소스 구성에 대한 최근 경험을 설명하겠습니다.

프랑스어 번역에서 망가진 부분의 샘플은 다음과 같습니다.

우리의 클라이언트 애플리케이션은 라이브러리를 사용하여 이 UI에 노출됩니다.

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

다음은 라이브러리 개발자들이 문자열 리소스를 넣은 파일들입니다 👇

- strings.xml
- 🇨🇦 strings.xml (fr-rCA)

![이미지](/assets/img/2024-05-27-RegionspecificresourcesandbrokenlocalizationAndroid_1.png)

👆에서 보듯이, 프랑스어 문자열을 캐나다 지역을 대상으로 하는 파일에 넣었습니다.

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

우리 애플리케이션에는 지역별이 아닌 프랑스어 문자열이 공통 파일에 저장되어 있어요👇

![이미지](/assets/img/2024-05-27-RegionspecificresourcesandbrokenlocalizationAndroid_2.png)

앱 수준 build.gradle에 다음 구성을 추가하기 전에 예상대로 작동했습니다

Kotlin

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
안드로이드 {
    defaultConfig {
        ...
        resourceConfigurations.addAll(listOf("en", "fr"))
    }
}
```

그루비

```js
안드로이드 {
    defaultConfig {
        ...
        resConfigs "en", "fr"
         // 또는
        resourceConfigurations += ["en", "fr"]
    }
}
```

# 이 설정은 무엇을 하는 건가요:

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

## Android 개발자 문서에서의 정의

사용되지 않는 대체 리소스 제거 👇

이 방법의 주요 이점은 애플리케이션에 필요하지 않은 모든 리소스를 제거할 수 있다는 것입니다.

이 시점에서 우리 케이스에서 프랑스어 지원이 깨졌다는 이유를 이해하셨으면 좋겠습니다.

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

저희 애플리케이션에는 두 가지 리소스만 필요하다는 구성이 추가되었습니다.👇

```js
resConfigs "en", "fr"
         // 또는
resourceConfigurations += ["en", "fr"]
```

이에 따라 Resource Shrinker는 fr-rCA 🇨🇦 리소스를 제거하고 애플리케이션에서는 [“en”, “fr”] 리소스만 사용할 수 있게 되었습니다.

# 문제를 해결해보는 시간입니다.

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

- 빠르게 이 문제를 해결하기 위해 몇 가지 옵션이 있습니다:

1st

- build.gradle을 업데이트하여 fr-rCA 🇨🇦 리소스를 제거하지 않도록합니다.

```js
android {
    defaultConfig {
        ...
        resConfigs "en", "fr", "fr-rCA"
        // 또는
        resourceConfigurations += ["en", "fr", "fr-rCA"]
    }
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

# 2nd

- 대안으로, 도서관을 제어하고 지역별 자원을 사용하고 싶지 않은 경우, 모든 지역에서 액세스할 수 있는 파일로 이동할 수 있습니다.

# 함께 연결해요

https://www.linkedin.com/in/navczydev/

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

# 참고문헌
