---
title: "방화벽 우회하기 오리진 IP 찾아내기"
description: ""
coverImage: "/assets/img/2024-05-20-BypassFirewallbyFindingOriginIP_0.png"
date: 2024-05-20 21:25
ogImage:
  url: /assets/img/2024-05-20-BypassFirewallbyFindingOriginIP_0.png
tag: Tech
originalTitle: "Bypass Firewall by Finding Origin IP"
link: "https://medium.com/@ott3rly/bypass-firewall-by-finding-origin-ip-41ba984e1342"
---

웹 애플리케이션 방화벽이 진짜 짜증날 때가 있죠. 보안 취약점을 알고 있는데도요! 대개 오래된, 정말 잘 관리되지 않는 웹사이트들이겠지요. 그래서 대부분의 경우에는 그 위에 WAF를 두는 게 더 간단하죠. 그 방어층을 우회할 방법이 있다고 말해 드릴까요 — 저는 원본 IP 주소를 찾는 방법을 말하는 것입니다. 여러 가지 방법을 탐색해볼 거예요.

# 방화벽 기본

이것은 방화벽이 작동하는 기본 다이어그램입니다:

![바이패스 방화벽을 찾는 방법](/assets/img/2024-05-20-BypassFirewallbyFindingOriginIP_0.png)

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

일반 사용자는 방화벽을 통해 요청을 보내고, 방화벽은 해당 요청을 확인하여 유효한 요청인지 확인한 후 서버로 전달합니다. 그런 다음 서버는 해당 요청을 처리하고 다시 방화벽으로 전송한 후 방화벽은 이를 클라이언트에게 보냅니다. 이것은 원본 서버가 자신의 IP를 클라이언트에게 노출하고 싶지 않기 때문에 이를 수행합니다. 반면 해커(해골 아이콘으로 표시)는 중간자를 통해 가지 않고 직접 서버로 가길 원합니다. 예를 들어 SQL 인젝션, XSS 또는 다른 페이로드를 전송하는 경우 - 이러한 요청은 방화벽 규칙에 의해 차단될 수 있습니다. 이것이 WAF 우회하는 고난을 건너뛰기 좋은 아이디어인 이유입니다. 서버의 위치를 알고 원본 IP를 찾음으로써 WAF를 우회할 수 있습니다.

가끔은 서버 자체에 의해 요청이 차단될 수 있거나, 때로는 직접 가는 것이 아니라 방화벽으로 다시 경유해야 하는 경우가 있을 수 있지만 이러한 경우는 예외입니다. 이번에는 여기서 원본 IP에 접근하여 직접 통신을 시도하여 해결책을 찾아보겠습니다.

# WAF Recon

항상 해야 할 첫 번째 작업은 대상이 웹 애플리케이션 방화벽을 실제로 가지고 있는지 확인하는 것입니다. 이를 확인하는 몇 가지 쉬운 방법이 있습니다. 그래서, 제가 자주 하는 첫 번째 작업은 대상을 핑하는 것입니다. 이 예시에서는 제 자신의 웹사이트를 핑해보겠습니다:

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

![이미지1](/assets/img/2024-05-20-BypassFirewallbyFindingOriginIP_1.png)

서버의 원본 IP와 같을 필요는 없습니다. 웹 응용 프로그램 방화벽 IP 주소일 수도 있습니다. 직접 액세스하려고 하면 일반적인 Cloudflare 오류가 표시됩니다:

![이미지2](/assets/img/2024-05-20-BypassFirewallbyFindingOriginIP_2.png)

다른 해결 방법은 Wappalyzer 플러그인을 사용하는 것입니다. 이 웹 사이트를 검사하면 Cloudflare를 사용하고 있다는 것을 보여줍니다.

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

![Screenshot](/assets/img/2024-05-20-BypassFirewallbyFindingOriginIP_3.png)

Another thing is also going back to the terminal and using tools like dnsrecon:

```js
dnsrecon -d ott3rly.com
```

This command will access DNS records, which could also indicate what WAF the server could use:

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

<img src="/assets/img/2024-05-20-BypassFirewallbyFindingOriginIP_4.png" />

가끔 서버가 WAF를 사용하지 않는 경우 원본 IP 주소가 누출될 수 있습니다. 이 경우에는 많은 Cloudflare 네임 서버를 볼 수 있습니다.

CLI 도구를 좋아하지 않는다면 대안으로 who.is 웹사이트를 확인할 수도 있습니다.

# 방법 #1 — Shodan

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

다음에 권장하는 것은 원본 IP를 찾는 방법으로 Shodan을 사용하는 것입니다. 기본 검색을 사용하여 많은 유출된 IP를 확인하는 것도 쉽습니다. 알려진 WAF 헤더, 응답 등이 포함되지 않도록 이러한 IP를 필터링할 수 있습니다. 일반적으로 200 상태 코드로 필터링합니다. 나는 SSL Shodan dorks를 사용하는 것을 좋아하며 위에서 언급한 필터를 함께 사용합니다:

![이미지](/assets/img/2024-05-20-BypassFirewallbyFindingOriginIP_5.png)

Shodan 도킹 기술에 관심이 있다면 그에 관한 기사를 갖고 있으니 꼭 확인해보세요!

# 방법 #2 — Censys

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

IP recon을 위한 또 다른 좋은 도구는 censys를 사용하는 것입니다. 대상을 검색 창에 붙여넣으면 매우 흥미로운 결과를 받을 수 있습니다:

![이미지](/assets/img/2024-05-20-BypassFirewallbyFindingOriginIP_6.png)

왼쪽에 많은 필터링 옵션이 있는 것을 보실 수 있습니다. 따라서 일부 필터링을 시도해볼 수 있습니다. 예를 들어, Akamai, Amazon과 같은 내용은 우리에게 흥미로운 것이 아니므로 이를 필터링해 보시는 것이 좋습니다.

# 방법 #3 — Security Trails

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

마지막으로, 제가 가장 선호하는 방법은 Security Trails를 사용하는 것입니다. 무료 계정을 만드는 것을 추천하며 자유롭게 사용할 수 있습니다. 이 도구는 특정 웹사이트의 IP 주소를 파악하는 데 좋습니다. 예를 들어, 제 웹사이트를 예로 들어서 역사적 데이터에 접근해 보겠습니다:

![이미지](/assets/img/2024-05-20-BypassFirewallbyFindingOriginIP_7.png)

검색 창에 ott3rly.com을 입력하여 Historial Data에 접속하려고 하면, A DNS 레코드를 살펴보는 것이 정말 유익합니다. Cloudflare를 사용하기 전에는 웹사이트가 WAF에 포함되지 않았기 때문에 원본 IP가 유출되었습니다. 이 경우, 호스팅 제공업체의 원본 IP이므로 크게 문제가 되지는 않지만, 일반적인 테스트 시나리오에서는 VPS IP 주소를 우연히 알 수도 있습니다. 이 경우에는 IP를 통해 페이지에 직접 접근할 수 있는 가능성이 있습니다.

# 마지막 팁

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

만약 해당 방법들을 대상에 시도한다면, WAF 레이더 아래에서 감지되지 않을 가능성이 있습니다. 퍼징 XSS, SQL 인젝션과 같은 특정 공격을 수행해야할 때 더 쉽게 되는데, 그렇게 할 수 있습니다. 따라서 정교한 payload를 만들기 전에 원본 IP를 찾는 것을 강력히 권장합니다.

만약 이 정보를 유용하게 찾으셨다면, 소셜 미디어에서 이 기사를 공유해주시면 정말 감사하겠습니다! 저는 Twitter에 활동하고 있으니, 매일 게시하는 콘텐츠를 확인해보세요! 만약 비디오 콘텐츠에 관심이 있다면, YouTube도 확인해보세요. 또한, 개인적으로 연락하고 싶다면, 제 Discord 서버를 방문해주세요. 건배!

원문 출처: https://ott3rly.com, 2024년 5월 6일에 발행된 글입니다.
