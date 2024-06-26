---
title: "안드로이드에서 WebP 이미지 형식"
description: ""
coverImage: "/assets/img/2024-06-19-WebPImageFormatonAndroid_0.png"
date: 2024-06-19 11:11
ogImage:
  url: /assets/img/2024-06-19-WebPImageFormatonAndroid_0.png
tag: Tech
originalTitle: "WebP Image Format on Android"
link: "https://medium.com/@domen.lanisnik/optimizing-android-app-size-by-leveraging-the-webp-image-format-87189f8c7603"
---

드로어블은 앱 크기를 늘리는 주요 요인 중 하나입니다. 이제 웹피 이미지 형식으로 이동하여 이미지를 최적화하는 방법에 대해 알아볼 거에요.

이 게시물에서는 웹피 형식이 무엇인지, 안드로이드에서 사용해야 하는 이유, 이미지를 변환하는 방법, 유의해야 할 점, 잘 알려진 기업들이 앱 크기를 줄이기 위해 어떻게 활용하는지, 그리고 성능에 미치는 영향을 다룰 것입니다.

## 안드로이드의 이미지 형식 개요

과거에 안드로이드 앱은 아이콘, 이미지 자산 및 기타 드로어블에 JPEG 및 PNG 형식을 사용했습니다. 같은 자산에 대해 서로 다른 해상도의 파일을 생성하여 drawable-xxxhdpi, drawable-xxhdpi 등과 같은 적절한 밀도 폴더에 배치하는 것이 표준적인 방법이었습니다.

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

나중에 Android 5.0(API 수준 21)은 VectorDrawable을 지원하게 되었어요. VectorDrawable은 XML 파일에 정의된 벡터 그래픽이에요. 품질 손실 없이 확대/축소할 수 있어서 같은 파일이 다른 화면 밀도에 대해 손실 없이 크기가 조정돼요. 이는 다른 해상도의 파일을 제공할 필요가 없어서 더 작은 앱 크기를 만들어냅니다.

Compose를 통해 Android는 ImageVector를 사용하여 아이콘을 정의하는 또 다른 방법을 가져왔어요. Compose는 개발자가 활용할 수 있는 미리 정의된 Material 아이콘 세트를 제공하며 프로젝트에 추가 자산을 추가로 제공할 필요가 없습니다.

그러나 VectorDrawable과 ImageVector은 일반적으로 점, 선 및 곡선만 사용하여 달성할 수 있는 한계가 있기 때문에 주로 아이콘이나 간단한 삽화에 사용됩니다.

더 복잡한 그림을 위해 주로 PNG 또는 JPG 형식을 계속 사용하며, 사진이나 배경 이미지와 같이 잘 작동하지만 더 큰 앱 크기를 가져올 수 있어요. 이때 WebP가 등장하는 거죠.

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

![WebP](/assets/img/2024-06-19-WebPImageFormatonAndroid_0.png)

## 웹피(WebP)란?

웹피(WebP)는 Google에서 개발한 이미지 형식으로, JPEG와 PNG보다 더 나은 압축을 제공합니다. 이 형식은 유실 압축과 비유실 압축을 모두 지원하며 투명도도 지원합니다.

Google에 따르면, WebP의 사용은 비손실 압축의 경우 PNG보다 평균 26% 더 작은 파일 크기를 가지게 되고, 유실 압축의 경우 25-34% 정도 더 작아진다고 합니다.

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

공식 페이지나 위키피디아에서 WebP의 작동 방식에 대해 더 많이 읽을 수 있어요.

## 안드로이드에서의 WebP

손실 압축 WebP 이미지는 Android 4.0(API 레벨 14) 이상에서, 그리고 무손실 및 투명한 WebP 이미지는 Android 4.3 (API 레벨 18) 이상에서 지원됩니다.

기존의 BMP, JPG, PNG 또는 정적 GIF 이미지를 Android Studio를 사용하여 WebP 형식으로 변환할 수 있어요. Android Studio를 사용하여 무손실이나 투명한 WebP 이미지를 작성하려면 프로젝트가 minSdkVersion을 18 이상으로 선언해야 해요.

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

## 왜 사용해야 하나요?

앱의 다운로드 크기는 특히 개발도상국에서 성공을 거두는 데 중요한 지표입니다. 사용자가 다운로드해야 하는 메가바이트 수가 적은 앱을 소유하고 있는 것은 동료들과 비교했을 때 이점이 될 수 있습니다.

드로어블은 대기업들이 앱 크기 최적화를 수행함에 따라 크기가 늘어나는 주요 원인 중 하나로 확인되었습니다 (Square, Grab). 그들이 .png 및 .jpg 형식에서 변경함으로써 앱 크기를 상당한 비율로 줄일 수 있었다는 교훈을 얻었습니다.

이로써 WebP는 Android에서 사용하기 좋은 이미지 형식입니다.

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

## 이미지를 WebP로 변환하는 방법

Android Studio에는 JPG, PNG, BMP 또는 정적 GIF 이미지를 WebP 이미지로 변환하는 내장 변환 도구가 있습니다.

- 기존 이미지를 변환하려면 파일을 마우스 오른쪽 버튼으로 클릭하고 "Convert to WebP" 옵션을 선택하십시오. 여러 파일을 변환하려면 모두 선택한 다음 그 중 하나에 마우스 오른쪽 버튼을 클릭하십시오.

![이미지](/assets/img/2024-06-19-WebPImageFormatonAndroid_1.png)

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

2. 새 대화 상자가 열립니다. 여기에서 인코딩 유형과 품질을 설정할 수 있습니다. 손실 압축을 선택하면 인코딩 품질을 설정할 수 있습니다. 품질이 자동으로 100%로 설정되는 손실 없는 인코딩에는 이 설정이 사용할 수 없습니다. "미리보기/검토" 확인란이 선택되어 있는지 확인하여 원본 이미지와 변환된 이미지를 비교할 수 있습니다. 미리보기로 진행하려면 확인 버튼을 누르세요.

![WebPImageFormatonAndroid_2.png](/assets/img/2024-06-19-WebPImageFormatonAndroid_2.png)

3. 다음 단계에서는 변환을 확인하기 전에 이미지 품질의 차이를 확인할 수 있습니다. 왼쪽에는 원본 이미지 (PNG, JPG, ...)가 표시되고, 오른쪽에는 변환된 WebP 이미지가 있으며, 중앙에는 품질 손실이나 차이가 표시됩니다. 아래에서 품질 설정을 변경하여 수용할 수 있는 값으로 변경할 수 있습니다. 또한 원본 대 변환된 파일 크기도 표시되므로 얼마나 많은 공간을 절약할 수 있는지 알 수 있습니다.

여러 파일을 변환할 때 각 이미지에 대해 이 비교 대화 상자가 제공됩니다. 각 이미지에 대한 설정을 조정하거나 모든 이미지에 대해 일괄 적용 및 확인할 수 있습니다.

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

이미지를 변환하려면 Finish를 눌러주세요.

4. 이미지를 변환한 후 공간을 얼마나 절약했는지에 대한 정보 메시지가 표시됩니다.

![WebPImageFormatonAndroid_3.png](/assets/img/2024-06-19-WebPImageFormatonAndroid_3.png)

5. 이제 끝났어요! 코드를 수정할 필요가 없습니다. 표준 방식 (R.drawable.image_name)으로 드로어블을 참조하고 있다면요. 파일 이름으로 직접 참조하고 있다면 .png 또는 .jpg 확장자를 .webp로 변경해야 합니다.

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

## 주의 사항

1. PNG: PNG 이미지/아이콘을 변환할 때 대부분의 경우 손실 없는 (100% 품질) 설정을 사용할 수 있습니다. 품질에 손상이 없으며 변환된 파일이 상당히 작아집니다.

   이 샘플 PNG 이미지의 경우, 변환된 파일 크기는 품질 손실 없이 65% 작아집니다 (22.0 KB 대 63.0 KB).

![PNG 이미지](/assets/img/2024-06-19-WebPImageFormatonAndroid_4.png)

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

2. 사용 사례: 선택하는 품질은 사용 사례에 따라 다릅니다. 화면 배경으로 이미지를 사용하는 경우에는 알아챌 수 없기 때문에 낮은 품질 설정이 허용됩니다. 화면에 두드러지게 표시되는 이미지에는 높은 품질 설정을 사용하세요.

3. JPG: JPG 이미지를 변환할 때 이미지 품질과 변환된 파일 크기 사이의 균형을 찾아야 합니다. 더 높은 이미지 품질(80% 이상)을 사용하면 때로는 원본보다 더 큰 파일이 생성될 수 있습니다.

예시: 아래 변환된 샘플 이미지의 크기는 736.5 KB이며 원본은 922.5 KB인데 품질을 75%로 설정했을 때입니다. 이는 21%의 절감을 의미합니다. 60% 품질로 사용하면 파일 크기는 627.5 KB(32% 절감)가 됩니다. 그러나 85% 설정을 사용하면 1.0MB의 파일 크기를 가지게 되며 원본보다 14% 증가합니다. 품질이 중요하다면 이 특정 이미지를 변환하는 데는 가치가 없습니다.

![2024-06-19-WebPImageFormatonAndroid_5](/assets/img/2024-06-19-WebPImageFormatonAndroid_5.png)

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

Initial dialog(2단계)에서 "변환 결과가 원본보다 큰 파일을 건너뛰기" 설정을 확인하여 변환된 파일이 큰 이미지를 걸러낼 수 있어요. 이 설정은 기본적으로 활성화되어 있어요.

![Image](/assets/img/2024-06-19-WebPImageFormatonAndroid_6.png)

JPG와 WebP 간 이미지 품질 및 파일 크기 차이를 볼 수 있는 샘플 앱이 궁금하시다면, 이 샘플 앱을 확인해보세요.

![Image](/assets/img/2024-06-19-WebPImageFormatonAndroid_7.png)

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

4. 방향: Whatnot Engineering은 최근 ExifInterface의 버그로 WebP 이미지를 올바른 방향으로 표시하는 문제를 해결하는 데 어려움을 겪었던 내용을 하이퍼디프로 발표했습니다. 그들은 Google에 수정 사항을 제출했고, 최신 AndroidX 릴리스에서 이 문제가 해결되었을 것으로 예상됩니다.

5. 취약점: 2023년 9월에 libwebp 라이브러리에서 두 가지 취약점이 발견되었습니다. 공격자는 특정 .webp 무손실 파일을 작성하여 응용 프로그램에서 경계를 벗어나거나 오버플로 조건을 발생시킬 수 있습니다. 대부분의 모바일 앱에서는 정적 WebP 이미지를 사용하고 있기 때문에 이는 큰 문제가 되지 않을 것입니다. 그러나 서버에서 WebP 이미지를 제공하고 기기에서 WebP 이미지를 업로드하는 경우, 서버에서 추가적인 유효성 검사와 보안 검사를 고려해야 합니다.

## WebP 형식을 사용하여 앱 크기를 최적화하는 회사들

여러 기업이 앱 크기를 줄이기 위해 노력한 경험을 공유했습니다. 대부분의 경우에서 주요 요소는 적절한 형식을 사용하여 이미지를 압축하는 것입니다. 여기에는 이미지 자산을 WebP로 변환하여 앱 크기를 최적화한 회사들의 몇 가지 예시가 있습니다:

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

- Square: [https://developer.squareup.com/blog/keeping-up-with-android-app-size-growth/](https://developer.squareup.com/blog/keeping-up-with-android-app-size-growth/)
- Grab: [https://engineering.grab.com/project-bonsai](https://engineering.grab.com/project-bonsai)
- Yelp: [https://engineeringblog.yelp.com/2016/05/yelp-android-app-went-on-a-diet.html](https://engineeringblog.yelp.com/2016/05/yelp-android-app-went-on-a-diet.html)

## 원격 WebP 이미지는 어떻게 되나요?

Android는 WebP 형식의 인코딩 및 디코딩을 공식적으로 지원하므로 모든 이미지 라이브러리가 URL에서 WebP 이미지를 표시할 수 있어야 합니다. 이는 Coil, Glide 등을 포함합니다.

Coil 및 Glide를 사용하여 Compose에서 WebP 이미지를로드하는 방법을 확인할 수 있습니다.

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

## 성능은 어떠한가요?

이미지를 100번 불러오고 불러오는 시간을 추적하는 간단한 벤치마크를 작성했습니다. Coil을 사용하여 drawable 폴더에서 이미지를 완전한 해상도로 불러옵니다. 결과는 샘플 앱의 릴리스 빌드에서 순차적으로 세 번 실행한 결과입니다.

벤치마크에 사용된 JPG 이미지는 원본 100% 품질 이미지이고, WebP 이미지는 80% 품질로 변환된 이미지입니다.

```js
Samsung S10 (Android 12):
| 로드 시간 (ms)   | JPG | WebP | 차이 (%)         |
|------------------|-----|------|------------------|
| 평균             | 143 | 161  | +12%             |
| 최소 (Android 14) | 100 | 126  | +26%             |
| 최대             | 164 | 180  | +10%             |


Samsung S21 Ultra (Android 14):
| 로드 시간 (ms)   | JPG | WebP | 차이 (%)         |
|------------------|-----|------|------------------|
| 평균             | 75  | 91   | +21%             |
| 최소 (Android 14) | 67  | 82   | +22%             |
| 최대             | 96  | 113  | +18%             |
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

웹페이지 이미지를 불러오는 데 웹피 파일이 JPG 파일보다 12–21% 더 오래 걸린다는 것을 확인할 수 있었습니다. 차이는 밀리초 단위로 측정되며 사용자들이 눈으로 구별하기에는 미미합니다. 물론 변환 이미지에 선택한 이미지 품질에 따라 차이가 발생합니다. 저품질을 사용하면 더 빨리 불러와지고 파일 크기도 작아집니다.

메모리 소비는 두 경우 모두 비슷합니다.

결과를 토대로 성능 차이가 매우 작아서 대부분의 앱에서 웹피 형식으로 마이그레이션하는 데 영향을 미치지 않을 것으로 판단됩니다.

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

WebP은 JPEG와 JPG보다 압축률이 더 높은 현대 이미지 파일 형식입니다. 따라서 다운로드 크기가 성공에 중요한 지표인 안드로이드 앱에 사용하기에 이상적인 후보입니다, 특히 개발도상국에서는 더욱 그렇습니다.

Android 시스템에서 API 레벨 18부터 완전히 지원되며, Android Studio에는 내장된 쉬운 변환 툴이 제공됩니다.

이 형식은 파일 크기를 줄이는 좋은 방법을 제공하지만, 변환된 이미지는 압축 설정에 따라 품질이 약간 낮아질 수 있고 로드 시간이 조금 더 오래 걸릴 수 있다는 단점이 있습니다. 따라서 사용 사례에 맞게 다양한 품질 설정을 테스트하는 것이 중요합니다.

이 내용이 유용하게 느껴졌으면 좋겠고, 이미지 자산을 WebP로 변환하여 앱 크기를 최적화하시기를 고려해 주셨으면 합니다. 이미 앱에서 WebP를 사용 중이라면 의견을 자유롭게 공유해 주세요.

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

참고 자료:

- [이미지를 WebP로 변환하는 방법](https://developer.android.com/studio/write/convert-webp)
- [APK 크기를 줄이는 공식 권장사항: WebP 형식 사용하기](https://developer.android.com/topic/performance/reduce-apk-size#use-webp)
- [이미지 다운로드 크기를 줄이는 방법에 대한 공식 제안](https://developer.android.com/develop/ui/views/graphics/reduce-image-sizes)
