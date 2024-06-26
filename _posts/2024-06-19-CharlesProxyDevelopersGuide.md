---
title: "찰스 프록시 개발자 안내서"
description: ""
coverImage: "/assets/img/2024-06-19-CharlesProxyDevelopersGuide_0.png"
date: 2024-06-19 11:21
ogImage:
  url: /assets/img/2024-06-19-CharlesProxyDevelopersGuide_0.png
tag: Tech
originalTitle: "Charles Proxy : Developer’s Guide"
link: "https://medium.com/@greenSyntax/charles-proxy-developers-guide-81f59bb71466"
---

![CharlesProxyDevelopersGuide_0](/assets/img/2024-06-19-CharlesProxyDevelopersGuide_0.png)

## 목표

만약 개발자가 반드시 가지고 있거나 배워야 할 필수 개발자 도구를 추천해야 한다면, Charles는 그 중 하나일 것입니다. 특히 모바일 개발자에게는 꼭 필요한 도구로, 당신의 툴킷에 넣어둘 수 있습니다. 저는 개발 경력이 매우 늦은 시점에 이 제품의 진정한 힘을 깨달았습니다. 이전에는 HTTP 네트워크 모니터링 도구로 사용했지만, 분명히 그 이상입니다.
네, 저는 이 제품을 구매하는 데 전적으로 찬성합니다. 이를 만들어 준 Karl von Randow에게 너무 감사드려야 합니다. 이 최고의 가이드를 만들어 줘서✌️

## 일정

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

- Charles 설정하기
- HTTP 트래픽 모니터링
- 응답 가짜 만들기
- 중단점 설정
- 다시 작성
- 대역폭 제한
- 반복
- Charles 문제 해결

## 설정

- 공식 웹사이트에서 Charles 설치 및 녹화 시작 - 상단 빨간 버튼.
- 그다음 해야 할 일은 맥과 기기(아이폰 또는 안드로이드)에 인증서를 설치하는 것입니다.

🐞 참고: 여기서는 제 핸드폰을 아이폰으로 고려하지만, 안드로이드는 거의 동일한 단계를 가지고 있습니다.

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

맥에서 인증서 설치하기 —

찰스 `도움말` SSL 프록시 ` 인증서 설치 로 이동

![이미지](/assets/img/2024-06-19-CharlesProxyDevelopersGuide_1.png)

그리고 — '찰스 루트 인증서 저장'을 클릭합니다. \*.cer 파일이 나옵니다. 해당 파일을 더블 클릭하여 키체인에 넣어주시면 됩니다.

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

![이미지](/assets/img/2024-06-19-CharlesProxyDevelopersGuide_2.png)

이제 iPhone에서 인증서를 설치할 시간입니다—

- Charles의 `Help` 메뉴로 이동해 로컬 IP를 확인하고 이 IP를 기억해주세요 😅
- iPhone을 꺼내어 설정을 열고, Mac이 연결된 동일한 Wi-Fi 네트워크에 연결하세요 — 설정 `Wi-Fi` 연결된 Wi-Fi의 (i)를 탭합니다 `프록시 구성` 수동 선택
- 서버: 단계-1에서 얻은 동일한 IP 주소
  포트: 8888
  인증: 비활성화

![이미지](/assets/img/2024-06-19-CharlesProxyDevelopersGuide_3.png)

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

- 이후 Mac에서 장치가 프록시되고 있음을 허용하는 경고를 받게 됩니다. 장치를 모니터링할 수 있도록 허용을 선택해 주세요. 그렇지 않을 경우, 장치가 모니터링되지 않습니다.
- 같은 네트워크에 연결한 후,
  1. Safari를 열고 - https://chls.pro/ssl 에 접속합니다 (iPhone이 Charles로 프록시될 때 인증서를 설치했는지 확인하세요 ⚠️).
  2. "이 웹사이트는 구성 프로필을 다운로드하려고 시도합니다. 이를 허용하시겠습니까?"와 같은 팝업이 표시됩니다. 일시적으로 허용해주세요. 그런 다음, "프로필 다운로드됨" 메시지가 표시됩니다 ✅

![이미지](/assets/img/2024-06-19-CharlesProxyDevelopersGuide_4.png)

4. 설정으로 이동하여 `일반` - `VPN 및 장치 관리` - `다운로드한 프로필` 에 Charles Proxy Custom Root Certificate가 나타납니다. 해당 인증서를 탭하여 설치해주세요.

![이미지](/assets/img/2024-06-19-CharlesProxyDevelopersGuide_5.png)

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

5. 이제 설치가 성공적으로 완료되었음을 확인할 수 있으며 — 설정 프로필 아래에 있습니다.

6. 마지막 단계가 남았습니다. 설정으로 돌아가기 `일반` 정보 `인증서 신뢰 설정` Charles Root Custom Certificate를 켜기(기본적으로 끔)

![이미지](/assets/img/2024-06-19-CharlesProxyDevelopersGuide_6.png)

7. 마지막으로, Charles 설정이 완료되었습니다 🚀

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

## HTTP 트래픽 모니터링

만약 Charles가 설치되어 있다면, 당신은 당신의 디바이스의 네트워크 트래픽을 볼 수 있습니다. 여기서 나는 사파리에서 https://jsonplaceholder.typicode.com/users를 열려고 시도했고, 그러면 트래픽을 트리 형태로 볼 수 있습니다.

한편, Charles 트리에서 https://jsonplaceholder.typicode.com을 마우스 오른쪽 버튼으로 클릭하고 이 호스트 이름에 대한 SSL Proxying을 활성화합니다.
참고: 이 단계를 수행하지 않으면 귀하의 트래픽 데이터를 읽을 수 없으며, 암호화된 형태로 남게 될 것입니다 — HTTPS SSH/TLS가 문제입니다 ⚠️

![이미지](/assets/img/2024-06-19-CharlesProxyDevelopersGuide_7.png)

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

보시다시피, 개요 탭에는 호스트 이름, DNS 주소, HTTP 응답 코드, HTTP 메소드, 프로토콜 이름 등 메타데이터가 있습니다. 이 메타데이터는 메트릭 계산에 필요한 응답 시간, 지연 시간, 핸드셰이크 타임스탬프 등이 포함되어 있습니다.

다음으로 Request 탭은 요청된 HTTP 데이터를 보여줍니다. 이 요청은 GET 요청이므로 본문은 헤더 및 HTTP 매개변수만 볼 수 있습니다.

<img src="/assets/img/2024-06-19-CharlesProxyDevelopersGuide_8.png" />

그리고 Response 탭에는 HTTP 응답 정보가 포함되어 있습니다. 여기에는 헤더, HTTP 본문 및 Raw HTTP 데이터(관심이 있다면)가 포함됩니다. 개인적으로 JSON 텍스트만 필요합니다. 왜냐하면 여기서 HTTP 본문이 예쁘게 출력되기 때문이죠.

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

<img src="/assets/img/2024-06-19-CharlesProxyDevelopersGuide_9.png" />

## 목 태그

화면보다 마크다운 양식이 더 깔끔하네요.요게 표 형식이니까 마크다운 테이블로 바꿔볼게요.

| 사진                                                                   | 설명           |
| ---------------------------------------------------------------------- | -------------- |
| <img src="/assets/img/2024-06-19-CharlesProxyDevelopersGuide_9.png" /> | 모의 응답      |
| <img src="image_url_here" />                                           | 모의 응답 설명 |

모의 응답을 하려면 두 가지 방법을 알아야 해요. 요는 로컬 방식과 원격 방식이에요.모바일 앱 개발자인너 니정에서 사용자의 JSON 데이터를 가져오는 중이야 그 데이터는 GET API– (https://jsonplaceholder.typicode.com/users/1) 로부터 가져오는 데이터야. 그런데 이상한 일이 발생할 때를 대비해서 값을 임의로 설정하고 싶어.

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

긍정적인 예상되는 응답:

```js
{
  "id": 1,
  "name": "Leanne Graham",
  "username": "Bret",
  "email": "Sincere@april.biz",
  "address": {
    "street": "Kulas Light",
    "suite": "Apt. 556",
    "city": "Gwenborough",
    "zipcode": "92998-3874",
    "geo": {
      "lat": "-37.3159",
      "lng": "81.1496"
    }
  },
  "phone": "1-770-736-8031 x56442",
  "website": "hildegard.org",
  "company": {
    "name": "Romaguera-Crona",
    "catchPhrase": "Multi-layered client-server neural-net",
    "bs": "harness real-time e-markets"
  }
}
```

이제 이름이 null이고, 주소가 비어 있는 응답을 원한다면 (만약 그런 응답이 클라이언트에 전달되어도 응용프로그램이 정상 작동하는지 확인하기 위해서),
다음과 같은 JSON을 만들었습니다 —

```js
{
  "id": 1,
  "name": null,
  "username": "Bret",
  "email": "Sincere@april.biz",
  "address": {},
  "phone": "1-770-736-8031 x56442",
  "website": "hildegard.org",
  "company": {
    "name": "Romaguera-Crona",
    "catchPhrase": "Multi-layered client-server neural-net",
    "bs": "harness real-time e-markets"
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

찰스는 당신이 https://jsonplaceholder.typicode.com/users/1의 응답을 모의할 수 있게 해줍니다.

찰스의 `도구` 메뉴에서 `맵 로컬 (Map Local)`을 선택하고, 맵 로컬을 활성화한 후 추가하세요.

또한, 모의하는 동안 \*.json 파일 경로를 선택해야 합니다. 이 파일은 진실의 근원이 될 것입니다. 저는 mock_response.json을 만들었습니다. 이 JSON 파일은 서버의 JSON 응답을 덮어쓸 것입니다.

<img src="/assets/img/2024년-06월-19일-찰스프록시개발가이드_10.png" />

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

![Charles Proxy Developers Guide 11](/assets/img/2024-06-19-CharlesProxyDevelopersGuide_11.png)

이제 https://jsonplaceholder.typicode.com/users/1를 요청하면 실제 서버가 제공하는 대신 모의 JSON 응답을 받게 됩니다. 이것을 보여드릴게요 —

![Charles Proxy Developers Guide 12](/assets/img/2024-06-19-CharlesProxyDevelopersGuide_12.png)

📕 노트: 매우 특이한 API 응답을 재현하고 싶을 때에는 이 Map Local을 사용하여 클라이언트에 제공할 수 있습니다.

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

여기에 대체제가 또 하나 있어요 — Map Remote이라고 해요. 이름에서 알 수 있듯이 API 경로를 다른 API 경로로 라우팅합니다.
예를 들어, /posts/1을 /users/1 API로 리디렉션하고 싶다고 가정해봅시다. 사용자가 https://jsonplaceholder.typicode.com/posts/1를 요청하려고 할 때, 해당 요청은 https://jsonplaceholder.typicode.com/users/1로 리디렉션됩니다. 제가 일상생활에서 별로 사용하지는 않았지만, 이제 비슷한 상황이 닥치면 어떻게 해야 하는지 알게 되었죠.

찰스 `Tools` Map Remote `Enable` Add

![이미지](/assets/img/2024-06-19-CharlesProxyDevelopersGuide_13.png)

## 중단점

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

브레이크포인트는 HTTP 요청 또는 응답을 변경하는 데 도움이 되는 도구입니다. 변경이란 헤더, 본문 등을 자유롭게 수정하는 것을 말합니다.

브레이크포인트를 활성화하려면 다음 단계를 따라야 합니다.

1. Charles > Proxy > Breakpoint Settings > Enable Breakpoint > Add

![image](/assets/img/2024-06-19-CharlesProxyDevelopersGuide_14.png)

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

여기, 모든 요청과 응답에 대해 jsonplaceholder.typicode.com에 대한 중단점을 추가했습니다. 이것은 마치 인터셉터처럼 작동합니다. HTTP 요청을 만들 때마다 Charles는 HTTP 요청을 변경할 수 있는 요청을 중지할 것입니다.

![Charles Proxy](/assets/img/2024-06-19-CharlesProxyDevelopersGuide_15.png)

여기서는 현재 요청을 변경하지 않을 것이므로 실행을 선택할 것입니다. 곧 다른 중단점 창이 나타날 것이고 여기서 JSON의 사용자 이름을 abhishekRavi로 변경하고 실행을 누를 것입니다.

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

![이미지](/assets/img/2024-06-19-CharlesProxyDevelopersGuide_16.png)

네, 이제 마법을 볼 수 있어요. 클라이언트는 값이 abhishekRavi인 사용자로 서버가 될 거예요.

![이미지](/assets/img/2024-06-19-CharlesProxyDevelopersGuide_17.png)

📕 노트북: HTTP 요청/응답을 수정하기 위해 중단점을 사용할 수 있어요. 헤더, 상태 코드, JSON 본문 또는 아무 것이나 수정할 수 있어요. 자유롭게 할 수 있는 거예요.

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

## 다시 쓰기

다시 쓰기는 '브레이크포인트'의 개선된 버전입니다. 다시 쓰기는 미리 정의된 브레이크포인트입니다. 특정 API 엔드포인트의 규칙을 설정할 수 있습니다.

Charles `도구` 다시 쓰기 `활성화` 추가 ` 위치 및 작업 생성

<img src="/assets/img/2024-06-19-CharlesProxyDevelopersGuide_18.png" />

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

여기, JSONPlaceholder 규칙을 생성했고 원격 위치 또는 API 경로를 언급했어요. 그리고 Body에는 'title'을 't00tle'로 바꾸라고 요청했어요.

헤더 및 Body에서도 동일한 작업을 할 수 있어요. 정규식을 설정할 수 있어요(만약 당신이 신이 가장 좋아하는 아이라면서 정규식을 작성하는 방법을 알고있다면요 😄).
여기서 HTTP 상태 코드를 200에서 400으로 변경하는 규칙을 설정해봤어요.

📕 노트: 일부 작업을 반복하려면 해당 작업을 위한 규칙을 작성할 수 있어요.

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

## Throttling

앱 개발자에게 매우 유용한 기능 중 하나입니다. 예를 들어 API의 응답 시간과 대역폭을 테스트하고 앱의 동작을 확인하고 싶을 때 활용할 수 있습니다. 즉, Charles Throttler를 구성하여 응답 시간과 대역폭을 조절할 수 있습니다.
먼저 Throttler 설정을 구성한 다음 Throttler를 실행하십시오. 저는 항상 56Kbps 모뎀 프리셋을 선택합니다.

찰스 `Proxy` Throttler 시작

![image](/assets/img/2024-06-19-CharlesProxyDevelopersGuide_20.png)

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

## 반복

안녕하세요! 가끔 실패하는 API가 있어서 해당 API의 부하를 테스트해 보고 싶다면 Charles에는 "Repeat"라는 기능이 있습니다. 이 기능을 사용하면 API를 동시에 n번 호출할 수 있어요.

API 엔드포인트를 우클릭하고 "Repeat Advance"를 선택해보세요 —

![Repeat Advance](/assets/img/2024-06-19-CharlesProxyDevelopersGuide_21.png)

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

![Image](/assets/img/2024-06-19-CharlesProxyDevelopersGuide_22.png)

Also, you can configure your repeat settings —

Setting Iteration to 100 and Concurrency to 3 means you will send 100 HTTP API requests in batches of 3 requests each time.

📕 Notebook: I mostly use this to perform load tests on the API to check if it responds with 2xx status codes.

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

## 문제 해결하기

- 찰스 웹사이트로 이동할 때 SSL 인증서를 설치할 수 없음

먼저 iPhone과 Mac이 동일한 Wi-Fi 네트워크에 연결되어 있는지 확인하고 iPhone에서 수동 프록시를 구성했는지 확인해야 합니다. 이 문서의 설정 섹션을 참고하세요.
둘째, 여전히 프로필을 다운로드할 기회가 없다면 Mac 설정에서 IP 주소를 바꿔야 합니다. `Network Preference` → `Advance` → `TCP/IP` → `Renew DHCP Lease`.

2. 응답이 항상 암호화되어 출력됩니다.

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

먼저, 호스트 이름에 대해 SSL 프록시 설정이 활성화되어 있는지 확인해보세요. 트리에서 호스트 이름을 오른쪽 클릭하여 'Enable SSL Proxying' (아직 활성화되지 않은 경우) 또는 'Disable SSL Proxy' (이미 활성화된 경우)로 확인할 수 있습니다. 또는 개요에서 확인할 수도 있어요 —

![이미지](/assets/img/2024-06-19-CharlesProxyDevelopersGuide_23.png)

둘째, 전화 설정에서 '일반' ` VPN 및 장치 관리 'Charles 프로파일 선택' 프로파일 제거를 통해 인증서를 삭제하고 다시 설치할 수 있어요. 그렇죠, 새로 시작해보세요.

## 결론

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

찰스(Charles)는 클라이언트와 서버 사이에 위치하여 HTTP 트래픽을 모니터링하는 프록시 응용 프로그램입니다. 요청과 응답을 필요에 따라 조작할 수 있습니다. 우리가 위에서 논의한 것보다 더 많은 기능이 있습니다. 찰스의 대안으로 Proxyman을 시도해 볼 수도 있습니다.
