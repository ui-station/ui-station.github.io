---
title: "쿠버네티스를 위한 최신 Grafana 대시보드 세트"
description: ""
coverImage: "/assets/img/2024-06-27-AnupdatedsetofGrafanadashboardsforKubernetes_0.png"
date: 2024-06-27 19:32
ogImage: 
  url: /assets/img/2024-06-27-AnupdatedsetofGrafanadashboardsforKubernetes_0.png
tag: Tech
originalTitle: "An updated set of Grafana dashboards for Kubernetes"
link: "https://medium.com/@dotdc/an-updated-set-of-grafana-dashboards-for-kubernetes-f5d6e4ff5072"
---


<img src="/assets/img/2024-06-27-AnupdatedsetofGrafanadashboardsforKubernetes_0.png" />

# 소개

두 해 전에 저는 Kubernetes를 위해 만든 대시보드를 공유하기 위해 "Kubernetes를 위한 현대적인 Grafana 대시보드 세트"를 발표했습니다. 이 기사에서는 해당 프로젝트의 업데이트를 제공하고, 그 이후에 이루어진 주요 변경 사항을 안내하겠습니다.

# 프로젝트 업데이트

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

최근 두 년간 dotdc/grafana-dashboards-kubernetes 프로젝트가 크게 발전했습니다! 여러 리트윗을 통해 원문 기사와 프로젝트가 주목할 만한 트래픽 증가를 보였고, 새로운 기여자들도 늘어났습니다. 제 대시보드를 자신의 홈 랩이나 Rakuten, Orange, Swisscom, Nokia와 같은 회사에서 사용하는 세계 각지의 많은 사람들을 보니 놀랍기도 했습니다. 일부 프로젝트는 Victoria Metrics, GCP Cloud Foundation Fabric, Microsoft Azure Arc Jumpstart와 같이 대시보드를 직접 포함하기도 했습니다.

현재 프로젝트 통계는 다음과 같습니다:

- ~500만 개의 대시보드 다운로드 (grafana.com에서만!)
- 2293개의 GitHub 스타
- 337개의 포크
- 26명의 기여자

![2024-06-27-AnupdatedsetofGrafanadashboardsforKubernetes_1.png](/assets/img/2024-06-27-AnupdatedsetofGrafanadashboardsforKubernetes_1.png)

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

# 변경 사항

## 일반적인 변경 사항

커뮤니티 요구에 따라 해당 프로젝트는 이제 심벌릭 버전 배포를 따르는 자동으로 생성된 적절한 릴리스를 가지게 되었습니다. 이는 콘벤셔널 커밋을 활용하여 시맨틱 버전 관리를 따르는 버전을 생성합니다.

게다가, 이제 모든 대시보드에는 클러스터 변수 지원이 포함되어 있으며, 이는 안전하고 간단한 해결책과 함께 문서화된 문제 #15에서 구현되었습니다. 이를 통해 Thanos, Mimir 또는 다른 연합 또는 고가용성 설정을 사용하는 사용자도 향상된 호환성을 누릴 수 있게 되었습니다.

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

더불어, 모든 "views" 대시보드에서 CPU 쓰로틀링을 시각화하는 새로운 패널이 추가되었습니다.

# k8s-views-pods.json

이슈 #21에서의 아이디어를 기반으로 하여 추가적인 작업을 통해, k8s-views-pods.json이 주요 업데이트를 받았습니다! 변경 사항 중에는 우선순위 클래스, QoS 클래스, 마지막 종료 이유 및 마지막 종료 종료 코드를 표시하는 새로운 정보 패널들이 있습니다.

게다가, CPU 요청 및 제한을 실제 CPU 사용량과 비교하는 패널과 메모리에 대해 동일한 작업을 수행하는 또 다른 패널이 추가되었습니다. 이를 통해 컨테이너 자원 사용량을 더 잘 시각화하고 컨테이너 크기 결정을 간단하게 할 수 있습니다.

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

<img src="/assets/img/2024-06-27-AnupdatedsetofGrafanadashboardsforKubernetes_2.png" />

# k8s-addons-prometheus.json

프로젝트에는 이제 Prometheus 대시보드가 포함되어 있습니다. Prometheus 인스턴스, TSDB, 쿼리 엔진, 리소스, 스토리지 및 네트워크와 관련된 패널이 포함되어 있습니다.

<img src="/assets/img/2024-06-27-AnupdatedsetofGrafanadashboardsforKubernetes_3.png" />

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

# k8s-addons-trivy-operator.json

Giant Swarm starboard-exporter를 사용한 k8s-addons-starboard-operator.json이 Aqua Security의 trivy-operator에서 가져온 메트릭을 기반으로 한 k8s-addons-trivy-operator.json으로 대체되었습니다.

![이미지](/assets/img/2024-06-27-AnupdatedsetofGrafanadashboardsforKubernetes_4.png)

# 변경 내역

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

k8s-views-global.json:

- throttled cpu가 추가되었습니다 (0427a7f)
- 클러스터 변수가 추가되었습니다 (3d99847)

k8s-views-namespaces.json:

- throttled cpu 패널이 추가되었습니다 (dbf01b1)
- 특정 배포, StatefulSet 또는 데몬셋을 필터링하는 기능이 추가되었습니다 (d173be7)
- 클러스터 변수가 추가되었습니다 (7ac58e5)

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

k8s-views-nodes.json:

- 노드에서 파드로의 링크를 추가했습니다 (50d1aa6)
- 쓰로틀된 CPU 패널을 추가했습니다 (d223755)

k8s-views-pods.json:

- 아이디어를 바탕으로 팟 대시보드를 재작업했습니다 (9d11d49)
- 쓰로틀된 CPU 패널을 추가했습니다 (d223755)
- 파드에서 노드로의 링크를 추가했습니다 (df42ae5)
- OOM과 재시작을 추가했습니다 (ab62016)
- 클러스터 변수를 추가했습니다 (7ac58e5)
- 다중 팟 선택을 추가했습니다 (03281bf)

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

다른 변경 사항:

- 시맨틱 릴리스 (b7d3553)
- ArgoCD 배포 추가 (3518826)
- 여러 버그 수정 및 작은 개선 사항

# 마지막으로

이 문서가 유용하고 원본 문서보다 추가 정보를 제공했기를 바랍니다!

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

정말 보람찬 경험이었어요. 커뮤니티로부터 피드백, 칭찬, 아이디어를 받고 기술적인 토론에 참여하다 보니 결국 그라파나 챔피언 프로그램에 참여하게 되었어요. 감사합니다! ❤️

언제든지 피드백과 제안을 환영해요. 이 프로젝트에 기여하고 싶다면 아래와 같이 참여해보세요:

- 좋다면 GitHub ⭐을 주세요.
- 기능 요청, 버그 신고 또는 아이디어 공유를 위해 이슈를 작성해주세요.
- 코드나 이 프로젝트에 유용한 것을 공유하고 싶다면 풀 리퀘스트를 생성해주세요.

저를 여기 Medium에서 팔로우하거나 아래에서도 만나 볼 수 있어요:

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

- GitHub : https://github.com/dotdc
- Mastodon : https://hachyderm.io/@0xDC
- Twitter : https://twitter.com/0xDC_
- LinkedIn : https://www.linkedin.com/in/0xDC

👋