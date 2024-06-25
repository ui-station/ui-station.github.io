---
title: "요청당 스레드 VS WebFlux VS 가상 스레드 최신 비교"
description: ""
coverImage: "/assets/img/2024-06-22-ThreadPerRequestVSWebFluxVSVirtualThreads_0.png"
date: 2024-06-22 22:10
ogImage:
  url: /assets/img/2024-06-22-ThreadPerRequestVSWebFluxVSVirtualThreads_0.png
tag: Tech
originalTitle: "Thread Per Request VS WebFlux VS VirtualThreads"
link: "https://medium.com/@sridharrajdevelopment/thread-per-request-vs-virtualthreads-vs-webflux-33c9089d22fb"
---

# 개요

이 분석은 전통적인 명령형 코딩의 성능을 차단된 스레드와 비교하면서 REST 서비스에서 HTTP 요청을 처리하는 두 가지 접근 방식을 비교합니다: 반응형 프레임워크인 Spring-WebFlux와 Spring 3.2에서 최근 소개된 가상 스레드. 우리는 WebFlux의 논블로킹 기능을 구체적으로 검토하고 가상 스레드에서 제공되는 내장 논블로킹 기능과 어떻게 비교되는지 살펴볼 것입니다.

# 사용 사례

세 가지 서비스가 동일한 기능을 수행하며 사용하는 프레임워크만 다릅니다.

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

![이미지](/assets/img/2024-06-22-ThreadPerRequestVSWebFluxVSVirtualThreads_0.png)

# 테스트 준비

- 사용한 머신: AWS t3.micro (2 vCPU, 1 GiB RAM) 및 Amazon Linux 2023.
- 사용한 HTTP 클라이언트: ThreadPerRequest 및 VirtualThread의 RestClient, WebFlux의 WebClient.
- Spring Boot 버전: 3.2.0
- Java 버전: v21
- 부하 테스트에 사용한 도구: jMeter 및 VisualVM

# 측정 매개변수

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

아래 기준을 사용하여 성능을 측정하고 접근법/프레임워크를 평가할 예정이에요:

- 더 높은 처리량 값은 더 나은 성능을 나타냅니다.
- 더 낮은 99백분위수 지연 시간은 더 나은 성능을 나타냅니다.
- 최적의 리소스 활용은 더 나은 성능을 나타냅니다.

# 로드 테스트 성능

# 0번째 로드 테스트

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

사용자 수: 150
램프업: 10
지속 시간: 100초

## 기계 성능

![이미지 1](/assets/img/2024-06-22-ThreadPerRequestVSWebFluxVSVirtualThreads_1.png)

![이미지 2](/assets/img/2024-06-22-ThreadPerRequestVSWebFluxVSVirtualThreads_2.png)

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

![ThreadPerRequestVSWebFluxVSVirtualThreads_3](/assets/img/2024-06-22-ThreadPerRequestVSWebFluxVSVirtualThreads_3.png)

## ThroughPut

![ThreadPerRequestVSWebFluxVSVirtualThreads_4](/assets/img/2024-06-22-ThreadPerRequestVSWebFluxVSVirtualThreads_4.png)

![ThreadPerRequestVSWebFluxVSVirtualThreads_5](/assets/img/2024-06-22-ThreadPerRequestVSWebFluxVSVirtualThreads_5.png)

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

![Latency](/assets/img/2024-06-22-ThreadPerRequestVSWebFluxVSVirtualThreads_6.png)

![Chart 1](/assets/img/2024-06-22-ThreadPerRequestVSWebFluxVSVirtualThreads_7.png)

![Chart 2](/assets/img/2024-06-22-ThreadPerRequestVSWebFluxVSVirtualThreads_8.png)

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

<img src="/assets/img/2024-06-22-ThreadPerRequestVSWebFluxVSVirtualThreads_9.png" />

# 1’st LoadTest

사용자: 200
램프업: 10
지속시간: 100초

## 기계 성능

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

아래는 Markdown 형식으로 변환한 텍스트입니다.

![image1](/assets/img/2024-06-22-ThreadPerRequestVSWebFluxVSVirtualThreads_10.png)

![image2](/assets/img/2024-06-22-ThreadPerRequestVSWebFluxVSVirtualThreads_11.png)

![image3](/assets/img/2024-06-22-ThreadPerRequestVSWebFluxVSVirtualThreads_12.png)

## ThroughPut

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

![ThreadPerRequestVSWebFluxVSVirtualThreads_13](/assets/img/2024-06-22-ThreadPerRequestVSWebFluxVSVirtualThreads_13.png)

![ThreadPerRequestVSWebFluxVSVirtualThreads_14](/assets/img/2024-06-22-ThreadPerRequestVSWebFluxVSVirtualThreads_14.png)

![ThreadPerRequestVSWebFluxVSVirtualThreads_15](/assets/img/2024-06-22-ThreadPerRequestVSWebFluxVSVirtualThreads_15.png)

## Latency

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

![image 16](/assets/img/2024-06-22-ThreadPerRequestVSWebFluxVSVirtualThreads_16.png)

![image 17](/assets/img/2024-06-22-ThreadPerRequestVSWebFluxVSVirtualThreads_17.png)

![image 18](/assets/img/2024-06-22-ThreadPerRequestVSWebFluxVSVirtualThreads_18.png)

# 2’th LoadTest

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

사용자: 300
RampUp: 10
지속 시간: 100초

## 기계 성능

![이미지 1](/assets/img/2024-06-22-ThreadPerRequestVSWebFluxVSVirtualThreads_19.png)

![이미지 2](/assets/img/2024-06-22-ThreadPerRequestVSWebFluxVSVirtualThreads_20.png)

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

![ThroughPut](/assets/img/2024-06-22-ThreadPerRequestVSWebFluxVSVirtualThreads_22.png)

![Image](/assets/img/2024-06-22-ThreadPerRequestVSWebFluxVSVirtualThreads_23.png)

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

![](/assets/img/2024-06-22-ThreadPerRequestVSWebFluxVSVirtualThreads_24.png)

## Latency

![](/assets/img/2024-06-22-ThreadPerRequestVSWebFluxVSVirtualThreads_25.png)

![](/assets/img/2024-06-22-ThreadPerRequestVSWebFluxVSVirtualThreads_26.png)

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

<img src="/assets/img/2024-06-22-ThreadPerRequestVSWebFluxVSVirtualThreads_27.png" />

# 3번째 부하 테스트

사용자 수: 500명
램프업: 10초
지속 시간: 100초

## 기계 성능

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

![그림 28](/assets/img/2024-06-22-ThreadPerRequestVSWebFluxVSVirtualThreads_28.png)

![그림 29](/assets/img/2024-06-22-ThreadPerRequestVSWebFluxVSVirtualThreads_29.png)

![그림 30](/assets/img/2024-06-22-ThreadPerRequestVSWebFluxVSVirtualThreads_30.png)

## 처리량

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

아래는 Markdown 형식으로 표시한 내용입니다.

![Image 1](/assets/img/2024-06-22-ThreadPerRequestVSWebFluxVSVirtualThreads_31.png)

![Image 2](/assets/img/2024-06-22-ThreadPerRequestVSWebFluxVSVirtualThreads_32.png)

![Image 3](/assets/img/2024-06-22-ThreadPerRequestVSWebFluxVSVirtualThreads_33.png)

## Latency

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

![Image 1](/assets/img/2024-06-22-ThreadPerRequestVSWebFluxVSVirtualThreads_34.png)

![Image 2](/assets/img/2024-06-22-ThreadPerRequestVSWebFluxVSVirtualThreads_35.png)

![Image 3](/assets/img/2024-06-22-ThreadPerRequestVSWebFluxVSVirtualThreads_36.png)

# Summary

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

<img src="/assets/img/2024-06-22-ThreadPerRequestVSWebFluxVSVirtualThreads_37.png" />

위 테스트 결과를 토대로 제 개인적인 관찰을 통해 얻은 결론을 아래에 소개합니다. 이것은 제 개인적인 의견일 뿐입니다.

- 동시 요청 수가 증가할수록 WebFlux가 처리량 측면에서 ThreadPerRequest와 Virtual Threads 모델보다 우수함을 보여줍니다.
- 지연 시간 측면에서도 WebFlux가 약간 향상됩니다. 다만 처리량만큼 명확하게 드러나지는 않습니다.
- 그러나 WebFlux의 지연 시간은 다른 모델의 간헐적인 짧은 피크와는 달리 안정적입니다.
- CPU 사용량 측면에서, WebFlux는 저하되는 부하에서 ThreadPerRequest와 Virtual Threads보다 더 많은 CPU를 소비하는 경향이 있습니다. 그러나 부하가 증가할수록 WebFlux 간의 CPU 사용량은 비교 가능해집니다.
- 힙 활용 측면에서도 ThreadPerRequest와 Virtual Threads가 더 많은 메모리를 사용하는 것으로 나타납니다.

이러한 테스트 결과를 토대로 WebFlux가 우세함을 입증했습니다! 그러나 이 문서는 WebFlux를 Virtual Threads보다 우선시하도록 밀어붙이고자 하는 것이 아닙니다. 선택은 특정 프로젝트와 팀이 반응형 접근 방식에 친숙한지에 따라 다를 수 있습니다.
