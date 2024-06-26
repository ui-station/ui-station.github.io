---
title: "뷰모델이 데이터를 구성 변경만 유지하는 이유에 대해 궁금했던 적이 있나요"
description: ""
coverImage: "/assets/img/2024-06-19-EverwonderwhyViewModelretaindataonlyforconfigurationchanges_0.png"
date: 2024-06-19 11:23
ogImage:
  url: /assets/img/2024-06-19-EverwonderwhyViewModelretaindataonlyforconfigurationchanges_0.png
tag: Tech
originalTitle: "Ever wonder why ViewModel retain data only for configuration changes?"
link: "https://medium.com/@pranay-panda/android-viewmodel-retaining-data-eb8467445ce1"
---

`<img src="/assets/img/2024-06-19-EverwonderwhyViewModelretaindataonlyforconfigurationchanges_0.png" />`

ViewModel이 데이터를 구성 변경으로 유지하지만 활동을 다시 인스턴스화하려고 할 때는 그렇지 않은 이유는 무엇인가요? 공식 문서에 따르면.

`<img src="/assets/img/2024-06-19-EverwonderwhyViewModelretaindataonlyforconfigurationchanges_1.png" />`

여기가 라이프사이클 소유자인 MainActivity입니다.

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

회전 또는 구성 변경 전:

![이미지1](/assets/img/2024-06-19-EverwonderwhyViewModelretaindataonlyforconfigurationchanges_2.png)

회전 또는 구성 변경 후:

![이미지2](/assets/img/2024-06-19-EverwonderwhyViewModelretaindataonlyforconfigurationchanges_3.png)

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

여기서 활동(소유자)의 인스턴스와 라이프사이클이 회전 후에 변경되는 것을 명확히 볼 수 있습니다.

한편, 같은 활동의 새 인스턴스를 수동으로 생성하여 이 시나리오를 다시 만들어 보려고 했을 때 뷰 모델은 데이터를 유지하지 않습니다.

데이터를 보존하려면 ViewModel이 결정하는 매개변수가 무엇인가요? 그리고 ViewModel은 설정 변경에만 데이터를 유지하고 동일한 활동의 새 인스턴스와 같은 것으로는 새 인스턴스가 아닐 때 데이터를 보존하는 이유가 무엇인가요?

ViewModel 코드를 살펴보면 ComponentActivity 생성자 내부의 활동/프래그먼트 수명주기에 대한 Observer가 설정되어 있습니다.

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

![이미지](/assets/img/2024-06-19-EverwonderwhyViewModelretaindataonlyforconfigurationchanges_4.png)

ComponentActivity는 Fragment 및 AppCompactActivity의 상위 클래스입니다. 라이프사이클 콜백이 발생할 때마다 트리거되며 onDestroy() 콜백이 발생하고 구성 변경이 아닌 경우에만 ViewModelStore를 지우게 됩니다.

각 활동/프래그먼트마다 ViewModel 인스턴스를 유지하는 것은 실제로 해결되는 사용 사례가 없는 상태에서 더 많은 메모리를 차지하는 것이 맞다고 생각됩니다. 구성 변경 이상으로 동일한 ViewModel 인스턴스를 사용하고 싶은 사용 사례를 생각해 볼 수 있나요?

![이미지](/assets/img/2024-06-19-EverwonderwhyViewModelretaindataonlyforconfigurationchanges_5.png)

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

위의 코드를 살펴보면 판단 기준인 isChangingConfigurations()를 알 수 있어요.

글을 읽어주셔서 감사합니다. 무언가를 배우셨다면 추천 부탁드려요. 👏
