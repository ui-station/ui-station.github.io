---
title: "분산 애플리케이션에서 캐싱 마스터하기"
description: ""
coverImage: "/assets/img/2024-05-23-MasteringCachinginDistributedApplications_0.png"
date: 2024-05-23 13:23
ogImage:
  url: /assets/img/2024-05-23-MasteringCachinginDistributedApplications_0.png
tag: Tech
originalTitle: "Mastering Caching in Distributed Applications"
link: "https://medium.com/@yt-cloudwaydigital/mastering-caching-in-distributed-applications-e7449f4db399"
---

![마스터링 분산 애플리케이션의 캐싱](/assets/img/2024-05-23-MasteringCachinginDistributedApplications_0.png)

소프트웨어 시스템의 캐싱 구현에서 버그를 만날 때마다 1달러가 있다면... 아마도 Redis Enterprise의 연간 기업 구독 비용을 지불할만큼의 돈이 쌓였을 것입니다.

캐싱은 거의 올바르게 할 수 있지만, 결코 완벽하게 할 수 없는 것처럼 보입니다. 그것에는 좋은 이유가 있습니다. 결국 - 캐싱(또는 캐시 무효화)은 컴퓨터 과학에서 가장 어려운 두 가지 기본 문제 중 하나로 간주됩니다. 다른 하나는 변수의 명명이겠지요.

농담인지 아니든 - 캐싱을 제대로 이해하는 것은 정말 어렵습니다 - 특히 대규모 분산 애플리케이션에서. 결과적으로 팀은 종종 캐싱 전략과 구현을 조정하기 위한 반복과 실험 과정을 거치며 - 마침내, 어느 정도 합리적이고 반최적적인 상태로 이르기를 희망하며.

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

이 글에서는 종종 간과되거나 오해되는 캐싱에 대한 몇 가지 측면을 명확하게 하고자 합니다.

이 글을 읽은 후에는 캐싱이 무엇인지, 캐싱의 주요 접근 방식, 주의해야 할 사항 및 다양한 캐싱 기술을 실제 사용 사례에 어떻게 적용하는지에 대해 더 명확한 이해를 가지게 될 것입니다.

그러니 더 이상 미루지 말고....

# 캐싱이란?

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

캐싱은 간략히 말해서 데이터를 임시 저장하는 동작으로, 데이터를 원본 저장소(기록 시스템)에서 검색하는 것보다 더 저렴하거나 빠르거나 최적화된 방법으로 검색할 수 있는 임시 매체에 데이터를 저장하는 것을 말합니다.

다른 말로 하면, 다음과 같은 사용 사례를 상상해보세요.

주문 관리 시스템이 재고 시스템에서 제품 정보를 검색해야 하는 상황입니다. 재고 시스템이 그다지 효율적이지 않다고 가정해보겠습니다. 요청이 들어올 때마다 제품 정보를 가져오기 위해 중앙 데이터베이스로 이동해야 합니다. 이 데이터베이스는 느리며 너무 많은 병렬 요청을 처리할 수 없습니다.

![이미지](/assets/img/2024-05-23-MasteringCachinginDistributedApplications_1.png)

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

성능을 향상시키고 재고 데이터베이스에 가해지는 부담을 완화하기 위해 캐싱 레이어를 도입했습니다. 이제 동일한 제품 정보를 저장하는 캐시가 추가되었습니다. 이제 버거운 데이터베이스를 거치지 않고 먼저 캐시에 접근하며, 캐시에 데이터가 있다면 그곳에서 가져옵니다.

![이미지](/assets/img/2024-05-23-MasteringCachinginDistributedApplications_2.png)

여기서 한 일은 성능을 향상시키고 원본 재고 데이터베이스의 자원 사용을 최적화하기 위해 임시 저장 매체(캐시)를 도입한 것입니다.

# “캐시”란 무엇을 의미할까요?

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

사람들이 혼동하기 시작하는 지점 중 하나는 캐시의 기술적 성격에 대해입니다.

소프트웨어 개발 분야에 종사하는 대다수의 사람들은 "캐시"라는 용어를 들었을 때 매우 구체적인 연상을 갖게 됩니다. 종종 이 용어를 Redis, Memcached 또는 EHCache와 같은 분산 캐시 제품과 연관시킵니다. 때로는 브라우저 캐시, 데이터베이스 캐싱, OS 캐싱 또는 하드웨어 캐싱을 떠올리기도 합니다.

이것이 바로 핵심입니다. 캐시의 개념은 컴퓨터 과학 분야 내의 특정 제품이나 영역으로 제한되지 않습니다. "캐싱"은 널리 생각되는 바에 따르면, 우리가 어떤 레코드 시스템으로부터 데이터를 복제하는 임시 매체의 어떤 형태라도 될 수 있습니다. 그렇게 하는 이유는 그 데이터를 임시 매체에 저장하는 것이 한 방이나 다른 방식으로 유리하기 때문입니다.

이는 일반적으로 비용 절감, 성능 향상 또는 원본 저장소보다 더 나은 확장성 때문에 그렇습니다.

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

이전 주문 관리 및 재고 시스템의 예제를 살펴보면 캐싱 레이어는 이론상 여러 가지로 구성될 수 있습니다:

- 분산 캐싱 제품(예: Redis)
- 자체 데이터베이스를 갖춘 다른 마이크로서비스
- 실제 재고 관리 시스템 내부의 인메모리 저장소

위의 모든 것은 서로 다른 구현이지만 각각 캐시의 조건을 충족할 것입니다.

간단히 말하면 위에 언급된 모든 것들이 캐시가 될 수 있습니다. 컴퓨터 시스템 스택의 모든 수준과 다양한 디지털 도메인에서 캐싱이 구현될 수 있다는 개념으로, 직접 적용될 수 있습니다.

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

# 용어정의

계속하기 전에 캐싱 주제 주변의 다양한 용어를 이해하는 것이 중요합니다.

- **시스템 레코드(System of Record):** 데이터가 저장되는 영구 저장소입니다. 대부분 데이터베이스일 가능성이 높습니다. 참 값 시스템(source-of-truth system)이라고도 합니다.

- **캐시 미스(Cache Miss):** 응용프로그램이 캐시를 쿼리하지만 해당 레코드가 캐시에 존재하지 않을 때 발생합니다.

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

새로 고침된 데이터: 캐시에 있는 레코드가 기본 시스템과 얼마나 동기화되어 있는지를 나타냅니다.

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

캐시 만료: 캐시 레코드를 에백션 프로세스의 일부로 또는 캐시 무효화의 일부로 시간 기반으로 제거하는 것을 의미합니다.

이제 우리 모두가 캐싱 용어에 완전히 익숙해졌으니, 캐시가 구현될 수 있는 몇 가지 장소와 계층에 대해 살펴보겠습니다.

# 캐싱은 어디에 구현되나요?

이미 언급했듯이, 캐싱은 기술 영역 전반에 걸쳐 사용되며, 모든 수준 및 다양한 기술 스택 내에서 사용됩니다.

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

하드웨어 수준에서 캐싱은 CPU 아키텍처의 일부로 사용되며, 예를 들어 레벨 1-3 (L1/L2/L3) 캐시 형식으로 사용됩니다.

운영 체제 커널 수준에서는 페이지 캐시라고 알려진 디스크 캐시 형식이 있습니다. 다른 형태도 있습니다.

웹 기반 시스템에서는 물론 브라우저 캐시와 CDN(Content Delivery Networks)가 있습니다. 이 캐시는 일반적으로 정적 리소스(이미지, 스타일시트 등)를 사용자에게 빠르고 효율적으로 제공하고 대역폭을 줄이는 데 사용됩니다.

다양한 종류의 응용 프로그램 및 미들웨어에도 자체 캐시가 있습니다. 예를 들어, 데이터베이스는 자주 사용되는 쿼리 및 자주 반환되는 결과 집합을 저장하기 위해 캐싱을 사용합니다.

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

또한, Redis, EHCache, Memcached, Hazelcast, Infinispan 등과 같은 많은 견고한 소프트웨어 캐싱 제품이 존재합니다. 이 제품들은 분산 애플리케이션 내에서 확장 가능한 분산 캐싱을 가능하게 합니다.

이제 강조할 한 가지는 "분산" 캐시 개념이 "로컬" 또는 "지역화된" 캐시와 대조될 수 있다는 점입니다. 분산 캐시는 네트워크 상에서 여러 기기에 분산된 캐시 형태입니다. 로컬 캐시는 한 기기에만 존재합니다.

이 두 개념 간의 차이를 이해하는 가장 좋은 방법은 클러스터 서버에 배포된 애플리케이션을 상상해보는 것입니다. 다시 말해, 동시에 여러 애플리케이션 인스턴스가 실행되는 것이 큰 규모 애플리케이션 개발자에게 익숙한 상황일 것입니다.

이러한 시스템에 분산 캐시를 도입한다면, 어떤 애플리케이션 인스턴스에서든 해당 캐시에 접근하고 레코드를 수정할 수 있게 됩니다.

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

다른 한편으로, 로컬 캐시가 있다면 각 인스턴스마다 자체 캐시가 있을 것입니다. 그 캐시는 대부분 해당 인스턴스의 메모리 내에 위치할 것입니다. 서로 다른 인스턴스들은 다른 인스턴스의 캐시에 접근할 수 없을 것입니다. 그들은 자신들의 캐시에만 접근할 수 있을 것입니다.

이러한 두 가지 접근 방식에는 장단점이 있습니다.

한편으로, 여러 인스턴스가 캐시에 접근하는 경우 - 동기화 문제, 경쟁 조건, 데이터 손상 및 분산 애플리케이션에서 발생하는 기타 도전 과제를 해결해야 할 수도 있습니다. 다른 한편으로, 공유 캐시는 강력한 개념입니다. 왜냐하면 그것으로 가능했던 사용 사례를 처리할 수 있게 해줍니다. 로컬, 더 단순한 캐시로는 불가능했던 것들도 처리할 수 있게 해주기 때문입니다.

예를 들어, 여러 가용 영역 내에서 클라우드 환경에 애플리케이션을 배포할 수 있습니다. 각 가용 영역은 애플리케이션을 실행하는 VM 인스턴스 클러스터를 가질 것입니다. 이러한 클러스터는 아마도 각각 자체 분산 캐시를 가질 것입니다. 분산 캐시의 전제조건 중 하나는 그것에 빠르고 효과적으로 액세스할 수 있어야 한다는 것입니다. 이는 캐시의 인스턴스에 서비스하는 네트워크 근접성(물리적일 필요는 없지만 가상적일 수도 있음)을 가지는 것을 의미합니다.

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

동시에 분산 및 로컬 캐싱에 공통적으로 발생하는 몇 가지 도전 과제가 있습니다.

주요 도전 과제는 데이터 신선도 유지, 최적의 캐시 무효화 및 제거, 그리고 캐시 관리 방식을 특정 사용 사례에 잘 맞추는 것 사이의 꾸준한 균형입니다.

캐시 관리 — 캐싱 패턴은 다음에 다룰 중요한 개념입니다.

소프트웨어 엔지니어링에서의 대부분의 결정과 마찬가지로, 각 접근 방식에는 각자의 절충안(또는 다른 말로 장단점)이 있습니다. 아래에서 각 접근 방식의 장단점을 다룰 것입니다.

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

캐싱은 소프트웨어 엔지니어와 소프트웨어 아키텍트 모두가 이해해야 하는 중요한 개념입니다. 그러나 이것이 유일한 개념이라고는 할 수 없습니다.

내 안내서 — 소프트웨어 아키텍트의 경력을 여는 법, 에서는 시니어 이상의 소프트웨어 엔지니어 및 소프트웨어/솔루션 아키텍트가 숙달해야 할 다른 개념, 기술 및 기술을 설명합니다.

여기에서 확인하세요

![마스터링 분산 애플리케이션에서의 캐싱 이미지](/assets/img/2024-05-23-MasteringCachinginDistributedApplications_3.png)

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

# 로컬 및 분산 캐싱 시스템의 패턴

캐싱 시스템에는 다섯 가지 주요 캐싱 패턴이 있으며, 이들은 캐시가 데이터를 읽고 쓰는 방식 및 기본 시스템과 동기화하는 방식과 관련이 있습니다.

# 캐시 옆에

캐시 옆에 캐싱 전략은 아마도 가장 인기 있는 전략이며 대부분의 소프트웨어 엔지니어가 익숙한 전략입니다. 이 캐싱 접근 방식은 애플리케이션에 캐시 쓰기 및 읽기 제어를 완전히 맡깁니다. 여기서 애플리케이션은 데이터베이스 또는 캐시에서 읽을 때와 쓸 때를 모두 제어합니다.

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

아래는 그것이 작동하는 방법에 대한 예시입니다.

당신의 애플리케이션이 사용자의 로그인 요청을 받고, 결과적으로 사용자의 우편 주소를 가져오게 된다고 상상해보세요.

- 애플리케이션은 먼저 사용자의 주소가 캐시 내에 존재하는지 확인합니다.
- 만약 해당 사용자의 주소 항목이 없다면, 애플리케이션은 데이터를 데이터베이스에서 가져옵니다.
- 그러나 캐시 내에서 정보가 존재한다면, 해당 데이터는 즉시 검색되어 데이터베이스로의 여행을 절약합니다.
- 새로운 정보를 가져온 후, 애플리케이션은 해당 데이터를 캐시에 기록합니다.

2단계에서, 특정 아이템을 위한 캐시에 항목이 없다면 — 이것은 "캐시 미스"로 자주 언급됩니다.

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

![Image](/assets/img/2024-05-23-MasteringCachinginDistributedApplications_4.png)

## 장점

- 구현이 간단합니다.
- 제어권은 애플리케이션에 완전히 남습니다.
- 필요할 때만 캐시된 항목을 가져오므로 (게으른 로딩), 최소한의 메모리를 사용합니다. (이론적으로는)

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

### Write-Through Caching

- 캐시 미스 발생 시 더 느린 저장소에서 데이터를 가져와야 하므로 지연 시간이 높아집니다. 캐시 미스가 많아지면 성능에 영향을 줄 수 있습니다.
- 애플리케이션 로직이 더 복잡해집니다 (전반적인 아이디어는 구현하기 쉽지만요).

### 사용 시기

- 캐시가 어떻게 채워지는지에 대한 완전한 제어를 원할 때.
- 데이터베이스 읽기/쓰기를 관리할 수 있는 캐싱 제품이 없을 때.
- 캐시에 대한 액세스 패턴이 불규칙할 때.

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

Write-Through 캐싱은 캐시와 기본 영속 데이터 저장소 사이의 일관성을 보장합니다. 다시 말해, 쓰기가 발생할 때 캐시와 데이터베이스 양쪽으로 동시에 전파됩니다.

다음은 예시입니다:

- 재무 애플리케이션이 사용자 계정을 새로운 잔액으로 업데이트하는 요청을 받습니다.
- 사용자 계정 잔액은 데이터베이스와 캐시 둘 다에 존재합니다.
- 데이터베이스와 캐시가 동일한 트랜잭션 내에서 새 값으로 업데이트됩니다.
- 다른 요청이 발생하면, 이번에는 사용자의 잔액을 읽는 요청이 옵니다. 먼저 캐시에서 값을 찾아 사용합니다. 캐시가 가장 최신 값을 가지고 있기 때문에 기본 데이터베이스와 동기화되지 않을까 걱정할 필요가 없습니다.

참고로 3단계는 애플리케이션 로직을 통해 수행할 수 있습니다. 그러나 실제 캐싱 제품에서는 해당 역할을 하게 됩니다. 예를 들어 EHCache나 Infinispan을 사용하는 경우 애플리케이션은 Redis 캐시를 업데이트하고, 다시 데이터베이스를 업데이트할 수 있도록 구성됩니다.

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

아래는 코드가 Markdown 형식으로 변경된 것입니다.

![이미지](/assets/img/2024-05-23-MasteringCachinginDistributedApplications_5.png)

# 장점

- 캐시와 기본 데이터 저장소 사이의 일관성을 보장합니다.

# 단점

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

- 트랜잭션 복잡성은 이제 캐시와 데이터베이스 업데이트 모두를 보장하기 위해 어떤 종류의 2단계 커밋 로직이 필요합니다 (캐시에 의해 제어되지 않는 한)
- 운영상의 복잡성, 위의 어느 하나가 실패하면 사용자 경험을 세련되게 처리해야 합니다.
- 쓰기가 더 느려집니다. 왜냐하면 이제 두 군데 (캐시 및 데이터 저장소)를 업데이트해야 하기 때문에 데이터 저장소에 하나만 업데이트할 때보다 더 많은 시간이 걸리게 됩니다.

# 사용 시기

Strong data consistency를 필요로 하고 퇴보된 데이터를 제공할 여유가 없는 애플리케이션에 적합합니다. 데이터가 작성된 직후 즉시 정확하고 최신 상태여야 하는 환경에서 흔히 사용됩니다.

# Write-Around Caching

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

이 전략은 기본 저장소를 채우지만 캐시 자체는 채우지 않습니다. 다시 말해, 이 쓰기는 캐시를 우회하고 기본 저장소에만 쓰입니다. 이 기술과 Cache-Aside 기술 간에는 일부 중첩이 있습니다.

차이점은 Cache-Aside에서 읽기와 지연로딩에 초점을 맞추는 반면, Write-Around 캐싱에서는 쓰기 성능에 초점을 맞추는 것입니다. 이 기술은 자주 데이터를 쓰지만 드물게 읽을 때 캐시 오염을 피하기 위해 종종 사용됩니다.

<img src="/assets/img/2024-05-23-MasteringCachinginDistributedApplications_6.png" />

# 장점

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

- 캐시 오염이 줄어듭니다. 이는 모든 쓰기 작업에서 캐시가 채워지지 않기 때문입니다.

## 단점

- 일부 레코드가 자주 읽히고 캐시에 사전으로 로드되어 첫 번째 히트 시 데이터베이스로의 전송을 방지해야 하는 경우 성능이 저하됩니다.

## 언제 사용해야 할까요?

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

많은 양의 쓰기 작업이 이루어지지만 읽기 작업은 상대적으로 적을 때 자주 사용됩니다.

# Write-Back (Write-Behind) 캐싱

쓰기 작업이 먼저 캐시를 채우고 데이터 저장소에 기록됩니다. 이곳의 핵심은 데이터 저장소에 쓰기가 비동기적으로 발생한다는 점입니다. 그러므로 이러한 경우 두 단계 트랜잭션 커밋이 필요 없어집니다.

쓰기 지연 캐싱 전략은 보통 캐싱 제품에서 처리됩니다. 캐싱 제품이 이러한 메커니즘을 갖고 있다면, 응용 프로그램은 캐시에 쓰기를 하고, 캐싱 제품은 변경 사항을 데이터베이스로 전송하는 책임이 있습니다. 만약 캐시 제품에서 이를 지원하지 않는다면, 응용 프로그램 자체가 데이터베이스로 비동기적인 업데이트를 트리거할 것입니다.

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

![Image](/assets/img/2024-05-23-MasteringCachinginDistributedApplications_7.png)

## 장점

- 초기 트랜잭션 내에서 캐시에만 쓰기 작업이 발생하므로 쓰기 속도가 빨라집니다. 데이터베이스는 나중에 업데이트됩니다.
- 흐름을 캐싱 제품이 처리하면 애플리케이션 로직이 덜 복잡해집니다.

## 단점

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

- 데이터베이스와 캐시가 새로운 변경 사항을 수신하게 될 때까지 둘 사이에 불일치 가능성이 있습니다.
- 캐시가 최종적으로 데이터베이스를 업데이트하려고 할 때 오류가 발생할 위험이 있습니다. 이 경우 데이터베이스가 가장 최신 데이터를 수신하도록 보장하기 위해 더 복잡한 메커니즘이 필요할 수 있습니다.

# 사용 시기

쓰기 퍼포먼스가 중요하고 데이터베이스의 데이터가 캐시와 잠시 동안 약간 동기화되어 있어도 괜찮을 때 쓰기 지연 캐싱을 사용할 수 있습니다. 높은 쓰기 부하를 처리해야 하지만 일관성 요구사항이 덜 엄격한 애플리케이션에서 적합합니다. 이 방법이 사용될 수 있는 한 예는 캐시된 콘텐츠를 빠르게 업데이트한 다음 레코드 시스템에 동기화하는 CDNs(콘텐츠 전송 네트워크)입니다.

# 읽기 - 스루

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

캐시 생성 방식은 일반적으로 캐시 옆에 두어 캐시 미스 발생 시 데이터베이스로부터 데이터를 가져온후 캐시에 저장하는 것에 유사합니다. 그러나 캐시 생성 방식은 애플리케이션에게 캐시와 데이터베이스를 모두 질의하는 책임을 맡기는 반면, 읽기-스루는 해당 메카니즘을 가지고 있을 경우 해당 제품에게 질의하는 방식입니다.

![이미지](/assets/img/2024-05-23-MasteringCachinginDistributedApplications_8.png)

# 장점

- 간편함 — 모든 로직이 캐싱 애플리케이션에 캡슐화되어 있습니다.

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

# 단점

- 캐시 미스 발생 시 데이터베이스에서 데이터를 읽을 때 잠재적인 지연이 발생할 수 있습니다. 데이터 업데이트를 위한 복잡한 무효화 메커니즘이 필요합니다.

# 사용 시기

리드-스루 캐싱은 데이터에 접근하는 코드를 간소화하고자 할 때 사용됩니다. 또한, 캐시가 항상 데이터 저장소의 가장 최근 데이터를 포함하도록 보장하고 싶을 때 사용됩니다. 쓰기보다 읽기가 더 자주 발생하는 애플리케이션에 유용합니다. 그러나 여기서 중요한 점은 캐싱 제품이 구성 또는 기본 시스템에서 이러한 읽기를 수행할 수 있는 능력을 가져야 한다는 것입니다.

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

# 캐싱 전략 요약

다섯 가지 캐싱 패턴에 대해 이야기한 내용을 아래에서 요약했습니다.

## 캐시 옆에 캐싱

애플리케이션이 캐시에서 데이터를 찾지 못하고 요청할 때만 요청에 따라 데이터가 캐시로 로드됩니다.

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

실제 예시: 온디맨드로 제품 세부 정보를 캐싱하는 전자 상거래 웹사이트.

데이터베이스 작업 책임: 응용 프로그램

## 쓰기-스루

일괄 쓰기 작업은 일괄 캐시 및 기본 데이터 저장소에 동시에 작성되어 일관성을 유지합니다.

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

실제 예시: 거래마다 일관된 계좌 잔액을 유지하기 위한 은행 시스템

DB 작업 책임: 캐싱 제품 또는 애플리케이션

## Write-Behind (Write-Back)

쓰기 작업은 먼저 캐시에 기록되고 나중에 데이터 저장소에 비동기적으로 기록됩니다.

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

실제 예시: CDN이 먼저 캐시에 콘텐츠를 업데이트하고 나중에 스토리지 시스템에 동기화하는 방식입니다.

DB 작업 책임: 제품 또는 애플리케이션 캐싱

## 라이트-어라운드

쓰기 작업은 캐시를 우회하고 데이터 저장소를 직접 업데이트하여 즉시 필요하지 않은 데이터를 캐싱하는 것을 피합니다.

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

실제 예시: 로그 작업에 대한 어디에서 캐싱없이 직접 스토리지로 기록합니다.

데이터베이스 작업 책임: 애플리케이션

## Read-Through

캐시는 읽기를 위한 주요 인터페이스 역할을 합니다. 캐시에 데이터가 없으면 시스템에서 해당 데이터를 가져와 캐시에 저장합니다.

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

실제 예시: 사용자 프로필 서비스가 캐시 미스 상황에서 사용자 데이터를 가져오고 캐싱하는 경우.

DB 작업 책임: 제품 또는 응용 프로그램 캐싱

## 캐시 무효화

이제 우리는 캐시를 채우는 다양한 방법을 이해했으니, 기록 시스템과 동기화하여 유지하는 방법도 이해해야 합니다.

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

캐시 무효화에 관한 두 가지 주요 접근 방식은 시간 기반 방식과 이벤트 기반 방식입니다. 무효화를 위한 시간 기반 접근 방식은 대부분의 캐싱 제품에서 제공되는 TTL(Time-To-Live) 설정으로 제어할 수 있습니다. 이벤트 기반 접근 방식은 응용 프로그램이나 다른 요소가 새 레코드를 캐시로 전송해야 합니다.

데이터 캐시에 대한 중요한 점은 거의 항상 기본 데이터 저장소(시스템 기록)와 어느 정도 동기화되어 있지만 매우 빨리 구식이 된다는 것입니다. 다시 말해 — 구식 상태가 됩니다. 캐시를 가능한 한 시스템 기록과 동기화된 상태로 유지하기 위해 캐시 무효화 전략을 구현해야 합니다.

다시 말해, 캐시 내에서 데이터 "신선도"를 보장해야 합니다.

캐시 무효화는 새 레코드가 시스템 기록으로부터 검색되어 캐시로 입력되는 현상을 유발합니다. 따라서 캐시 무효화와 위에서 논의한 캐싱 전략 사이의 관계를 이해하는 것이 매우 중요합니다.

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

캐싱 전략은 데이터가 캐시에서 로드되고 검색되는 방식과 관련이 있습니다. 반면에 캐시 무효화는 시스템 레코드와 캐시 간 데이터 일관성과 신선도와 더 관련이 있습니다.

따라서 이 두 가지 개념 사이에는 약간의 중첩이 있으며 일부 캐싱 전략에서는 무효화가 다른 것보다 간단할 수 있습니다. 예를 들어 캐시-쓰기 쓰기 방식의 경우 캐시가 모든 쓰기마다 업데이트되므로 추가 구현이 필요하지 않습니다. 하지만 삭제는 반영되지 않을 수 있기 때문에 명시적으로 이를 다루는 응용 프로그램 논리가 필요할 수 있습니다.

캐싱 엔트리를 무효화하는 두 가지 방법이 있습니다:

## 이벤트-드리븐

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

이벤트 기반 접근 방식을 사용하면 응용 프로그램이 기록의 기반 저장소에서 변경이 발생할 때마다 캐시를 알립니다. 레코드가 변경될 때마다 동기적 또는 비동기적으로 캐시에 알림을 트리거합니다.

이 작업은 응용 프로그램을 통해 수행할 수 있으며, 코드가 캐시를 최신 상태로 유지하는 것에 책임이 있습니다. 또는 일부 캐싱 제품에서는 퍼브/섭 기능이 제공될 수 있으며, 캐싱 제품이 이러한 유형의 알림에 가입할 수 있습니다. 그 경우 응용 프로그램에서 할 작업이 덜 할 수 있지만, 여전히 이러한 알림 이벤트를 생성해야 합니다.

## 시간 기반

시간 기반 접근 방식을 사용하면 모든 캐시 레코드에 TTL(임시 소멸 시간)이 지정됩니다. 레코드의 TTL이 만료되면 해당 캐시 레코드가 삭제됩니다. 이것은 일반적으로 캐싱 제품에 의해 제어됩니다.

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

# 캐시 축출 전략

캐시 축출은 기존 캐시 레코드를 제거하는 캐시 무효화와 유사합니다. 그러나 캐시 축출은 캐시가 가득 차서 더 이상 레코드를 수용할 수 없는 경우에 필요합니다.

기억하세요, 캐시의 목적은 가장 자주 액세스되는 레코드의 부분 집합을 저장하는 것입니다. 전체 진실의 원본 시스템을 복제하는 것이 아닙니다. 따라서 캐시의 크기는 일반적으로 데이터베이스 / 진실의 원본 / 기록 시스템에 저장된 데이터의 크기보다 훨씬 작을 것입니다.

따라서 레코드를 "축출" 또는 다른 말로 삭제할 수 있는 메커니즘이 필요합니다.

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

동시에 캐시의 존재 이유를 완전히 무의미하게 만들지 않기 위해 애플리케이션이 제일 필요하지 않을 것으로 생각되는 레코드부터 시작해야 합니다.

최적으로 레코드를 제거하는 방법을 보장하기 위해 사용할 수 있는 몇 가지 퇴직 전략이 있습니다:

## Least Recently Used (LRU)

이 접근 방식을 통해, 얼마 동안 사용되지 않은 레코드를 제거합니다.

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

사용 시기: 데이터가 마지막으로 액세스된 후 시간이 경과함에 따라 데이터가 곧 액세스될 가능성이 줄어드는 시나리오에서 효과적입니다. 미래 액세스의 강력한 지표인 액세스 최근성을 고려하는 일반적인 캐싱에 적합합니다.

사용하지 말아야 할 때: 데이터 액세스 패턴이 최근성과 관련이 없는 워크로드에는 이상적이지 않습니다.

## 먼저 들어온 것이 먼저 나간다 (FIFO)

다른 레코드보다 이전에 캐시에 저장된 레코드를 제거합니다.

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

사용할 때: 데이터의 나이가 액세스 빈도나 최근성보다 중요한 캐시에 유용합니다. 예측 가능한 수명을 가진 데이터를 캐싱하는 데 적합합니다.

사용하지 않을 때: 이전 데이터가 여전히 빈번하게 액세스되는 워크로드에는 최적이 아닙니다.

## 최소 사용 빈도 순서 (LFU)

빈번하게 사용되지 않거나 액세스되지 않는 레코드를 삭제합니다.

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

사용 시기: 오랜 기간 동안 자주 액세스되는 데이터를 보관해야 하는 상황에 가장 적합합니다. 안정된 액세스 패턴을 갖는 애플리케이션에 적합합니다.

사용하지 말아야 하는 경우: 액세스 패턴이 크게 변할 수 있는 환경에서는 효과가 떨어집니다. 자주 액세스되지 않는 항목들이 캐시를 오염시킬 수 있습니다.

## Time To Live (TTL)

미리 결정된 Time-To-Leave 기간에 따라 퇴거합니다.

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

사용 시기: 특정 기간 이후에 만료되거나 변하지 않는 데이터에 이상적입니다.

사용하지 말아야 할 때: 유효성이 시간이 지나면 자연스럽게 종료되지 않고 다른 요인에 따라 캐시에 영원히 남아있어야 하는 데이터에 적합하지 않습니다.

## 무작위 치환

기록을 무작위로 대체합니다.

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

사용 시점: 고급 추적 메커니즘의 비용이 혜택을 상쇄하는 상황이거나 액세스 패턴이 예측할 수 없어 다른 제거 전략이 적합하지 않은 경우에 사용할 수 있습니다.

사용하지 않는 시점: 대체로 다른 전략에 비해 대부분 예측 가능한 액세스 패턴이 있는 실제 시나리오에서는 효율이 떨어질 수 있습니다.

# 요약

분산 응용 프로그램에서 캐싱의 중요성과 올바른 캐싱 전략을 선택하는 중요성에 대해 이야기했습니다. 일반적으로 사용되는 여러 전략이 있습니다:

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

- 캐시 오프 (Cache-aside)
- 쓰기-스루 캐시 (Write-through cache)
- 읽기-스루 캐시 (Read-through cache)
- 쓰기 배후 캐시 (Write-behind cache)
- 쓰기 주변 캐시 (Write Around)

우리는 또한 시간 기반 또는 이벤트 기반 접근 방식을 사용하여 캐시 무효화에 대해 이야기했습니다.

캐시 제거의 중요성과 어떤 전략이 그 일을 수행하는지에 대해 주목했습니다. 이것들은 다음과 같습니다:

- LRU
- FIFO
- LFU
- TTL
- 임의(Random)

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

마지막으로, 캐시는 로컬 또는 분산 형태일 수 있습니다. 전자는 단일 기계/응용프로그램 인스턴스에 한정되어 있습니다. 후자는 여러 기계에 걸쳐 확장되며 일반적으로 (필수는 아니지만) 인스턴스 클러스터에 한정되어 있습니다.

많은 혁신이 시장에서 발생하고 있는 캐시 제품과 관련된 기술들과 유행에 대해 몇 가지 명확한 정보를 제공했기를 바랍니다. 왜 캐싱이 중요한지, 그리고 캐싱 기술을 다룰 때 이해해야 하는 모든 다른 용어와 미묘한 점에 대한 직관력을 향상시켜주길 바랍니다.

# 캐싱: 미래

다른 기술들과 마찬가지로 캐싱 제품이 시장에서 엄청난 혁신이 일어나고 있습니다. 일부 주목할만한 하이라이트는 다음과 같습니다:

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

# 에지 컴퓨팅과 통합

에지 컴퓨팅이 계속해서 성장함에 따라, 캐싱 전략은 더 분산화되어 데이터를 네트워크 가장자리에 필요한 위치에 더 가까이 이동시킵니다. 이 근접성은 레이턴시, 대역폭 및 데이터 제공 비용을 줄입니다. 이는 IoT 및 모바일 앱과 같은 실시간 응용 프로그램에 매우 중요합니다.

예시: 자율 주행 차량은 에지 컴퓨팅을 사용하여 실시간 데이터를 로컬에서 처리합니다. 지도 및 교통 상황과 같은 핵심 데이터를 에지 노드에서 캐싱하면 중앙 서버를 쿼리하는 레이턴시 없이 신속한 의사결정을 할 수 있습니다.

# AI와 머신 러닝 기반 캐싱

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

AI와 머신 러닝은 데이터 사용 패턴을 예측하고 예상된 필요에 따라 미리 데이터를 캐싱함으로써 캐싱 메커니즘을 향상시킬 수 있습니다. 이러한 예방적인 접근 방식은 효율성을 크게 향상시킬 수 있습니다. 특히 데이터 액세스 패턴이 자주 변경되는 동적 환경에서는 더욱 그렇습니다.

예시: 아마존은 머신 러닝을 사용하여 사용자 행동을 예측하고 블랙 프라이데이와 같은 피크 타임에 사용자가 구매할 가능성이 높은 제품을 미리 캐싱합니다. 이는 로드 시간을 줄이면서 사용자 경험을 향상시킵니다.

# 인메모리 데이터 그리드 (IMDG)

IMDG는 분산 시스템 전반에 걸쳐 저지연 복잡한 데이터 액세스를 제공하는 캐싱의 강력한 솔루션이자 빠르게 발전하고 있습니다. IMDG는 데이터를 캐싱뿐만 아니라 캐시 레이어 내에서 다양한 데이터 처리 기능, 실시간 분석 및 의사 결정 기능을 제공합니다.

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

예시: 고주파 트레이딩 플랫폼은 IMDG를 활용하여 시장 데이터와 거래 주문을 메모리에 캐시합니다. 이를 통해 서브초 단위 거래 결정을 내리는 데 필수적인 빠른 액세스와 처리가 가능해집니다.

안녕하세요, 저는 야코프입니다. CloudWay Digital Inc을 운영하고 있는 소프트웨어 아키텍처 컨설팅 기관인 Developer.Coach에서 소프트웨어 엔지니어와 아키텍트들이 경력을 향상시키는 데 도와드리고 있습니다.

제 Medium 무료 기사 외에도, 소프트웨어 엔지니어링 전문가들의 경력 향상에 도움이 되는 가이드 몇 편을 작성했습니다. 아래 링크를 통해 확인해보세요:

👉 소프트웨어 아키텍트의 경력 잠금 해제

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

👉 소프트웨어 아키텍트 인터뷰 마스터하기

👉 소프트웨어 엔지니어링 경력 잠금 해제: 중급에서 시니어로

원본 게시물: 2024년 5월 17일, https://www.cloudwaydigital.com 에서 게시됨.
