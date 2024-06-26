---
title: "링크를 클릭했을 때 벌어지는 네트워킹 과정 "
description: ""
coverImage: "/assets/img/2024-06-22-TheNetworkingbehindclickingalink_0.png"
date: 2024-06-22 23:41
ogImage:
  url: /assets/img/2024-06-22-TheNetworkingbehindclickingalink_0.png
tag: Tech
originalTitle: "The Networking behind clicking a link"
link: "https://medium.com/@hnasr/the-networking-behind-clicking-a-link-b2ce36b7cf14"
---

<img src="/assets/img/2024-06-22-TheNetworkingbehindclickingalink_0.png" />

하이퍼링크를 클릭하면 브라우저가 원격 서버에서 링크의 콘텐츠를로드하고 렌더링합니다. 배경에서는 연결 설정, 세션 암호화, 프로토콜 협상, 리다이렉션, 도메인 표시 등이 발생합니다.

이 기사에서는 브라우저에서 하이퍼링크를 클릭할 때 발생하는 네트워킹 측면을 안내하겠습니다. 데모 용도로 내 강좌 링크 중 하나를 사용할 것입니다.

기사에서 언급하는 클라이언트는 TLS 1.3 및 HTTP/2를 지원하는 브라우저를 의미합니다. 현대의 모든 브라우저가 이러한 프로토콜을 2022년에 지원합니다.

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

# backend.husseinnasser.com

저의 소프트웨어 엔지니어링 각각의 강좌마다, DNS 공급 업체가 호스팅하는 CNAME 레코드가 있어 고유한 Netlify 도메인으로 연결됩니다. Netlify에서는 HTML 페이지를 호스팅하여 실제 강좌 링크로 리디렉션합니다. 이렇게 함으로써 CNAME 도메인을 소셜 미디어에서 공유할 수 있으면서 강좌 쿠폰을 업데이트하거나 전체적으로 다른 링크로 리디렉트할 때 완전한 제어를 갖게 됩니다. 원래 링크는 그대로 유지됩니다.

제 최신 강좌인 '백엔드 엔지니어링 기초' 강좌인 backend.husseinnasser.com을 살펴봅시다. 링크를 클릭하면 브라우저가 udemy의 강좌로 리디렉션됩니다. 이 프로세스는 DNS, TCP, TLS, ALPN/SNI 및 HTTP/2를 통해 진행되며, 각 섹션을 자세히 설명하겠습니다.

```js
backend.husseinnasser.com
 ---> zen-mccarthy-34c0bb.netlify.app
 ---> udemy.com/backendcourse
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

# DNS

https://backend.husseinnasser.com에 클릭하면 HTTP 클라이언트(당신의 브라우저)가 backend.husseinnasser.com의 IP 주소를 얻기 위해 DNS 조회를 발행합니다. backend.husseinnasser.com은 DNS 권위 있는 이름 서버 enom에서 호스팅된 CNAME(정규 이름)으로 내 HTML 파일이 호스팅되어 있는 Netlify DNS 레코드 zen-mccarthy-34c0b.netlify.app를 가리킵니다.

DNS 쿼리는 backend.husseinnasser.com의 IP 주소를 요청하는 고유한 쿼리 ID가 포함된 UDP 데이터그램입니다. DNS 쿼리의 첫 번째 중단점은 당신의 DNS 리커서(또는 리졸버)입니다. 이는 Google 8.8.8.8 또는 Cloudflare의 1.1.1.1과 같을 수 있습니다. 리커서는 루트 DNS 서버에 .com 최상위 도메인(TLD) 서버를 요청합니다. 그런 다음 리커서는 husseinnasser.com이 호스팅된 권위 있는 이름 서버를 요청하기 위해 TLD에 쿼리를 보내 한 of my enom servers를 반환합니다. 마지막으로 리커서는 IP 주소를 얻기 위해 enom 서버로 backend.husseinnasser.com의 DNS 쿼리를 전송합니다. 그것은 zen-mccarthy-34c0bb.netlify.app을 가리키는 CNAME임을 발견하고 zen-mccarthy-34c0bb.netlify.app의 IP 주소를 찾기 위해 새로운 DNS 쿼리를 수행하고 IP 주소가 발견될 때까지 프로세스가 반복됩니다. 이 모든 것은 캐시가 없다고 가정합니다.

우리는 이를 도메인에 대한 nslookup(또는 dig)를 수행함으로써 볼 수 있습니다.

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

![이미지](/assets/img/2024-06-22-TheNetworkingbehindclickingalink_1.png)

DNS 쿼리의 일부로 zen-mccarthy 도메인을 받았을 때, zen-mccarthy netlify 도메인의 IPv4 주소와 연관된 A 레코드 두 개도 함께 받습니다. 클라이언트는 IP 중 하나를 선택하고 35.247.66.204로 TCP 연결을 설정합니다.

# TCP

이제 IP 주소가 있으므로 TCP 연결을 설정할 수 있습니다. TCP 연결 설정에는 소스 IP, 소스 포트, 대상 IP 및 대상 포트의 4개 튜플이 필요합니다. 클라이언트는 연결하기 전에 이 네 가지가 필요합니다.

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

고객이 DNS 덕분에 목적지 IP를 알고 있습니다. 35.247.66.204이며, 목적지 포트는 링크가 명시적으로 https://를 사용하고 있으므로 443입니다. 소스 포트는 0에서 2¹⁶ 사이의 사용 가능한 모든 포트가 될 수 있으며, 소스 IP는 여러분의 기기입니다.

이 4개의 튜플로 고객은 IP 패킷에 실려있는 SYN TCP 세그먼트를 보냅니다. 서버는 SYN을 받고 목적지 IP를 고객의 IP(보통은 게이트웨이)로 바꿔서 SYN/ACK를 응답합니다. 마지막으로 고객은 ACK를 통해 핸드셰이크를 완료합니다. 이제 연결이 수립되었습니다.

```js
고객 ------------SYN -------> netlify (35.247.66.204)
       <---------SYN/ACK------
       ------------ACK-------->
```

# TLS

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

현재 TCP 연결에서 전송되는 모든 데이터는 일반 텍스트이며 교통량을 가로채는 사람이 관찰할 수 있습니다. 따라서 통신은 보안을 보장하기 위해 암호화됩니다 (HTTPS의 S).

암호화를 위한 우리의 프로토콜 선택은 TLS 또는 전송 계층 보안입니다. TLS의 주요 부분은 핸드셰이크이며 주요 목표는 다음과 같습니다:

- 암호화에 사용할 수 있는 대칭 키 교환
- 응용 프로그램 프로토콜 협상
- 서버 표시 및 인증

클라이언트는 TLS 클라이언트 헬로 메시지를 보내어 TLS 핸드셰이크를 시작하고 세션 암호화를 요청하며 이 과정에서 HTTP/1.1과 HTTP/2를 제안합니다. 서버는 TLS 핸드셰이크를 완료하기 위해 서버 헬로 메시지로 응답합니다.

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
Client ------------SYN -------> netlify (35.247.66.204)
       <---------SYN/ACK------
       ------------ACK-------->
       ------Client Hello ---->
       <-----Server Hello------
```

고객은 클라이언트 헬로 메시지의 일부로 여러 TLS 확장을 설정합니다. 특히 우리는 두 가지를 중점적으로 살펴볼 것입니다. 첫 번째는 "ALPN"으로 응용 프로그램 계층 프로토콜 협상을 의미하며, 두 번째는 "SNI"으로 서버 이름 지정을 의미합니다. 이들의 목적에 대해 설명하겠습니다.

## ALPN

ALPN은 고객이 지원하는 응용 프로그램 프로토콜을 나타내는 TLS 확장입니다. 여기서 고객은 ALPN의 일부로 HTTP/1.1 및 HTTP/2 (h1 및 h2)를 제안하며, 고객 및 서버 양측에서 지원하는 가장 높은 프로토콜이 일반적으로 선택됩니다. 이 경우 서버는 HTTP/2를 선택합니다.

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

![이미지](/assets/img/2024-06-22-TheNetworkingbehindclickingalink_2.png)

## SNI

TLS 핸드셰이크에서 가장 중요한 요소는 아마도 SNI 또는 서버 이름 표시일 것입니다. 이 확장은 클라이언트가 관심 있는 도메인을 서버에 알려줍니다. 이는 한 IP 주소가 수천 개의 웹사이트를 호스팅할 수 있으므로 도메인을 표시함으로써 서버가 클라이언트가 관심 있는 정확한 웹사이트를 알 수 있게 되어 증명서를 반환할 때 어떤 웹사이트의 증명서를 반환해야 하는지 알 수 있습니다.

클라이언트의 SNI는 backend.husseinnasser.com으로 설정되어 있습니다. Netlify 서버는 TLS 클라이언트 헬로 핸드셰이크를 수신하고 SNI를 이용하여 인증을 위해 클라이언트에 제공해야 하는 정확한 인증서를 파악할 수 있습니다. 이 인증서는 Netlify에서 내 도메인을 등록했을 때 Netlify가 생성한 것으로, 인증 기관은 Let's Encrypt입니다.

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

![이미지](/assets/img/2024-06-22-TheNetworkingbehindclickingalink_3.png)

서버는 백엔드인 husseinnasser.com 인증서, 암호화 매개변수 및 선택한 응용 프로그램 프로토콜(HTTP/2)을 포함한 TLS 핸드셰이크를 완료하기 위해 서버 헬로를 보냅니다. 클라이언트와 서버 모두 암호화를 위한 대칭 키를 가지고 있으며, 이제 HTTP 요청을 암호화하여 보낼 준비가 되어 있습니다.

# HTTP/2

TCP 연결 위에 암호화된 세션이 있습니다. 이제 클라이언트는 페이지를 가져오기 위해 HTTP GET 요청을 보낼 수 있습니다. 선택한 응용 계층 프로토콜은 HTTP/2이므로 클라이언트는 요청을 보낼 HTTP/2 스트림이 필요합니다.

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

이것은 새로운 연결이기 때문에, 클라이언트가 요청을 위해 새로운 스트림을 생성합니다 (스트림 1). 클라이언트는 GET 메서드를 사용하여 HTTP 요청을 보내며, 경로는 / 입니다. backend.husseinnasser.com 다음에 아무것도 없기 때문입니다. 그리고 프로토콜 버전은 HTTP/2 입니다. 클라이언트는 HTTP 헤더를 설정하고 요청을 보냅니다.

이렇게 보이는 예시가 있습니다. Host 헤더에 주목해 주세요. 이는 가장 중요한 HTTP 헤더 중 하나입니다.

![이미지](/assets/img/2024-06-22-TheNetworkingbehindclickingalink_4.png)

## Host 헤더

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

호스트 헤더가 없으면 모든 것이 망가집니다. 호스트 헤더는 웹 마스터들을 위한 몇 가지 문제를 해결하기 위해 HTTP/1.0에 선택적으로 추가되었습니다. 이러한 도전 과제들은 다음과 같습니다:

- 하나의 IP 주소에서 여러 웹사이트 호스팅 가능
- 프록시 서버 지원

이에 따라 호스트 헤더는 나중에 HTTP/1.1 및 향후 프로토콜에서 필수 요소로 지정되었습니다.

넷리파이를 예로 들어보면, IP 35.247.66.204는 수천 개의 웹사이트를 호스팅합니다. 많은 클라이언트가 동일한 IP 주소에 연결하지만, 넷리파이 서버가 클라이언트가 실제로 소비하려는 웹사이트를 어떻게 알 수 있을까요. 서버는 호스트 헤더를 사용하여 정확히 어떤 웹사이트를 가져올지 알 수 있습니다. 클라이언트가 백엔드.husseinnasser.com을 호스트 헤더로 설정하면 넷리파이 서버는 내 깃허브 레포지토리 내용의 사본을 가리키고 HTML 페이지를 제공할 수 있습니다.

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

클라이언트가 GET 요청과 함께 쿠키 헤더를 보낼 수도 있습니다. 서버는 이를 사용하여 사용자를 식별합니다.

프록시 구성에서 클라이언트의 목적지 IP는 프록시이므로, 클라이언트가 실제로 연결하려는 웹 사이트를 프록시가 알기 위해 응용 프로그램 레이어에서 추가 표시가 필요합니다.

서버는 스트림 1에서 GET 요청을 받고 호스트 헤더를 확인하여 기본 페이지 index.html을 가져옵니다. 쿠키가 전송되었는지 여부에 따라 서버는 같은 스트림으로 다른 HTTP 응답을 보냅니다.

# udemy로 리디렉션

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

클라이언트가 index.html 페이지와 함께 HTTP 응답을 드디어 받습니다. 이 페이지는 다음과 같이 간단한 HTML을 포함하고 있습니다.

```js
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta http-equiv="X-UA-Compatible" content="IE=edge">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <meta http-equiv="refresh" content="0; URL=https://www.udemy.com/course/fundamentals-of-backend-communications-and-protocols/?couponCode=BACKEND10V2" />

    <title>백엔드 통신 디자인 패턴과 프로토콜 기초</title>
</head>
<body>
   <h1>udemy로 리디렉팅 중...</h1>
   <h2>제 백엔드 엔지니어링 강좌를 즐기세요</h2>

</body>
</html>
```

meta 헤더 중 http-equiv refresh와 content=0 그리고 URL에 주목해주세요. 이것은 브라우저에 해당 URL로 즉시 리다이렉트하라고 알려줍니다. 사용자가 다른 링크를 클릭한 것처럼 정확히 동일한 단계가 반복됩니다. 여기서 수행되는 정확한 단계들은:

- udemy.com의 IP 주소 DNS를 조회
- TCP 연결 생성
- TLS 설정
- HTTP 프로토콜 협상
- HTTP/2 스트림 생성
- 경로가 /course/fundamentals-of-backend-communications-and-protocols/?couponCode=BACKEND10V2인 새로운 GET 요청 보내기
- udemy에 로그인되어 있다면 쿠키가 전송됨
- Udemy 서버는 회원 가입 상태에 따라 코스 페이지로 응답합니다

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

쿠폰 코드는 URL 끝에 couponCode라는 쿼리 매개변수로 지정됩니다. 매달 쿠폰을 업데이트하고 변경 사항을 GitHub에 푸시하여 Netlify 재빌드를 트리거합니다. backend.husseinnasser.com을 방문하는 사람은 항상 최신 쿠폰을 받을 수 있습니다. 나중에 코스 제공업체를 변경하기로 결정한다면 index.html에서 URL을 업데이트하면 됩니다. 원래 링크는 결코 변경하지 않습니다.

# 요약

이 기사의 목표는 소프트웨어 엔지니어링의 기술을 알리는 데 있습니다. 인터넷을 구동하는 통신 프로토콜을 설계하고 구현한 우수 엔지니어들이 수고를 아끼지 않았습니다.

저는 엔지니어로서 이 작업이 종종 당연히 여기고 감사히 여겨지지 않는다고 생각합니다. 프로토콜이 어떻게 작동하는지 이해하는 것은 네트워크 엔지니어링의 진화에 기여하고 더 나은 프로토콜을 만들 수 있는 첫걸음입니다. 링크를 클릭하는 등 간단한 작업을 수행하는 데 필요한 것을 보셨습니다. 그러면, 더 나은 방법으로 만들 수 있을까요?

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

읽어주셔서 감사합니다.
