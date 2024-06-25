---
title: "쿠버네티스 x509 인증서가 만료되었거나 아직 유효하지 않음 오류"
description: ""
coverImage: "/assets/img/2024-06-19-Kubernetesx509certificatehasexpiredorisnotyetvaliderror_0.png"
date: 2024-06-19 13:09
ogImage:
  url: /assets/img/2024-06-19-Kubernetesx509certificatehasexpiredorisnotyetvaliderror_0.png
tag: Tech
originalTitle: "Kubernetes: “x509: certificate has expired or is not yet valid” error"
link: "https://medium.com/@houcem.laouiti/kubernetes-x509-certificate-has-expired-or-is-not-yet-valid-error-2bc8c1a61a81"
---

<img src="/assets/img/2024-06-19-Kubernetesx509certificatehasexpiredorisnotyetvaliderror_0.png" />

만약 잘 작동되던 쿠버네티스 환경에서 갑자기 "x509: certificate has expired or is not yet valid" 오류가 발생한다면 어떻게 하시겠어요?

오늘 나에게 일어난 것과 똑같이요. 저도 이 오류를 경험했어요. 왜 나타나는지도 모르는데, 뭔가 조치를 취하지 않은 상태에서 발생한 문제였죠!

저는 인증서 만료 문제라고 알고 있었지만, 문제를 어떻게 해결할지 알려고 많은 시간을 보냈어요. 그래서 이 주제에 대해 짧은 기사를 써서 이런 문제를 겪는 다른 분들에게 도움이 되길 바라는 마음으로 준비했어요.

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

Kubernetes 문서에 따르면 kubeadm으로 생성된 클라이언트 인증서는 1년 후에 만료됩니다.

그래서 제가 먼저 한 일은:

- 인증서 세부 정보 확인하기

```js
kubeadm certs check-expiration
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

2. 그런 다음 모든 인증서를 갱신했어요

```js
kubeadm certs renew all
```

3. 마지막으로 kubelet을 다시 시작했어요

```js
sudo systemctl restart kubelet
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

하지만, 아무것도 바뀌지 않았네요! 계속해서 같은 오류가 발생하고 있습니다.

여기서 뭔가 빠진 게 있는 것 같아요.

이 문제를 조사하는 데 시간을 많이 쏟았는데, ~/.kube/config 파일을 살펴보던 중 예전 클라이언트 인증서 항목을 사용하고 있다는 것을 깨달았어요.

그리고 kubeadm으로 클러스터를 구축할 때 /etc/kubernetes/admin.conf을 /.kube/config 파일로 복사해야 했다는 것을 기억했네요.

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

인증서를 갱신하면 /etc/kubernetes/admin.conf 파일이 업데이트되므로, 당연히 내 /.kube/config 파일도 그에 맞게 업데이트해야 했어요.

다음 명령어를 실행하여 문제를 해결했어요.

```js
sudo cp -i /etc/kubernetes/admin.conf ~/.kube/config
sudo chown $(id -u):$(id -g) ~/.kube/config
```

쿠버네티스 문서에 따르면:
