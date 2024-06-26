---
title: "ArgoCD ApplicationSet은 v29 버전에서 더 실용적입니다"
description: ""
coverImage: "/assets/img/2024-05-18-ArgoCdApplicationSetismorepracticalinversionv29_0.png"
date: 2024-05-18 16:49
ogImage:
  url: /assets/img/2024-05-18-ArgoCdApplicationSetismorepracticalinversionv29_0.png
tag: Tech
originalTitle: "ArgoCd ApplicationSet is more practical in version v2.9"
link: "https://medium.com/nontechcompany/applicationset-is-more-practical-in-version-v2-9-47cbf08adea7"
---

대부분의 ArgoCD 프로젝트를 따르는 여러분은 이미 이를 알고 있을 것입니다. ArgoCD v2.9에서는 특정 기능이 릴리스의 일부가 되었는데, 이에 대해 이 게시물에서 공유하고 싶습니다.

![ArgoCD v2.9](/assets/img/2024-05-18-ArgoCdApplicationSetismorepracticalinversionv29_0.png)

# 언급한 기능은 무엇이며 왜 이것이 ApplicationSet을 더 실용적으로 만드는가요?

ApplicationSet을 사용하여 Application을 생성하는 경우 생성된 Application의 자동 동기화를 비활성화할 수 없다는 것을 이미 알아차리신 분들이 많으실 겁니다.

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

이 문제에 대한 자세한 내용은 다음 링크에서 확인할 수 있습니다: https://github.com/argoproj/argo-cd/issues/9101

문제에 대한 요약은 다음과 같습니다.

예를 들어, 제 경우에는 특정한 ApplicationSet이 모든 Kubernetes 클러스터에 대해 특정 Application을 생성하도록 설정되어 있습니다.

클러스터 유지 관리자로서 어느 날, 한 클러스터에서 발생한 문제에 대해 알림을 받았는데, 그 문제가 ApplicationSet을 통해 설치된 응용프로그램과 관련이 있는 것으로 보였습니다.

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

특정 클러스터에서 매개변수를 동적으로 변경하여 문제를 완화해야 했습니다. 애플리케이션의 자동 동기화를 비활성화하여 일부 작업을 수행할 수 있도록 시도했지만, ApplicationSet 컨트롤러는 항상 자동 동기화 상태로 되돌리기 때문에 문제가 발생했습니다.

이것은 "버그"라기보다는 존경받는 GitOps 원칙을 시행하며 모든 변경 사항은 특정 애플리케이션의 값들을 수동으로 재정의하는 대신 코드를 통해 수행되어야 한다는 동작입니다.

하지만, 제게는 실용적이지 않았습니다. 특히, 새벽 4시에 빠르게 문제를 해결해야 하는 SRE로서 실시간 제품 시스템을 모니터링해야 하는 경우에는 더욱 그렇습니다.

이 제한은 이 특정 사용 사례에만 해당하는 것이 아닙니다. Github의 이슈에서 설명했듯이, 대부분의 개발 작업 흐름도 생성된 애플리케이션에 일부 값을 재정의해야 하는데, 그 또한 불가능합니다.

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

# 어떤 기능으로 이 문제를 해결할 수 있을까요?

ArgoCD v2.9에서는 애플리케이션세트(ApplicationSet)에서 생성된 애플리케이션의 자동 동기화를 일시적으로 끌 수 있도록 허용합니다.

우리가 해야 할 일은 이 구성 조각을 애플리케이션세트에만 추가하는 것뿐입니다.

![ArgoCD Application Set Configuration](/assets/img/2024-05-18-ArgoCdApplicationSetismorepracticalinversionv29_1.png)

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

응용 프로그램 설정 컨트롤러에서 변경 내용이 롤백되지 않고 응용 프로그램의 자동 동기화를 중지할 수 있습니다.

![이미지](/assets/img/2024-05-18-ArgoCdApplicationSetismorepracticalinversionv29_2.png)

사실, 이 변경의 일부로 필요에 맞게 syncPolicy에 국한되지 않는 여러 매개변수 간의 차이를 무시할 수 있습니다. 주의할 점은 우리가 재정의하려는 필드가 목록인 경우에는 여전히 일부 제한 사항이 있습니다. 그러나 필요할 때 자동 동기화를 비활성화할 수 있게 되었습니다.
