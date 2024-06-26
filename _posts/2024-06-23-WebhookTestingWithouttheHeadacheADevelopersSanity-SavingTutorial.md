---
title: "개발자를 위한 간편한 웹훅 테스트 방법 스트레스 없는 튜토리얼"
description: ""
coverImage: "/assets/img/2024-06-23-WebhookTestingWithouttheHeadacheADevelopersSanity-SavingTutorial_0.png"
date: 2024-06-23 21:18
ogImage:
  url: /assets/img/2024-06-23-WebhookTestingWithouttheHeadacheADevelopersSanity-SavingTutorial_0.png
tag: Tech
originalTitle: "Webhook Testing Without the Headache: A Developer’s Sanity-Saving Tutorial"
link: "https://medium.com/itnext/webhook-testing-without-the-headache-a-developers-sanity-saving-tutorial-d6aea887f582"
---

![웹훅 테스트하지 않고 괴로움을 덜어내는 개발자를 위한 튜토리얼_0.png](/assets/img/2024-06-23-WebhookTestingWithouttheHeadacheADevelopersSanity-SavingTutorial_0.png)

안녕하세요! 이 튜토리얼에서는 웹훅을 효과적으로 테스트하는 방법을 알아볼 거에요. Ngrok를 사용하여 로컬 머신에서 서드파티 웹훅을 직접 수신하는 방법을 알려드릴 거에요. 이 모든 과정은 공개 도메인이 필요하지 않아요. 웹훅 테스트 과정을 전보다 더 효율적으로 운용할 수 있도록 준비하세요!

또한, Wiremock를 사용하여 웹훅을 효율적으로 모킹하는 방법에 대해 알려드릴 거에요. 웹훅을 모킹하는 방법을 배우는 이유는 완전히 오프라인 테스트 환경을 갖게 되어 의존성이 없는 환경을 구축할 수 있기 때문이에요. 더불어, 일부 서비스에서 웹훅의 과도한 사용에 대해 요금을 청구하는 경우도 있으니 이를 피하도록 합시다.

이 튜토리얼을 마치신 후에는

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

- 제 3자 서비스에서 웹훅을 로컬 응용 프로그램(로컬호스트)으로 라우팅합니다.
- 로컬 및 오프라인 개발을 위한 웹훅 모의 테스트

## 웹훅이란?

![이미지](/assets/img/2024-06-23-WebhookTestingWithouttheHeadacheADevelopersSanity-SavingTutorial_1.png)

웹훅은 알림 시스템처럼 작동합니다. 친구에게 전화번호를 알려주면 그들이 뉴스가 있을 때마다 전화를 합니다. 디지털 세계에서 웹훅은 전화번호 대신에 URL을 통해 동작합니다. 제 3자 소프트웨어에게 URL을 제공하면 그들이 무언가가 발생할 때 즉시 알림을 받습니다.

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

예를 들어, GitHub 저장소에서 실패한 파이프라인과 같은 변경사항이 발생하면 GitHub가 즉시 웹훅(webhook) 메시지를 지정된 URL로 보냅니다. 이 URL은 당신이 선택한 서비스로 이어져야 합니다.

웹훅을 사용하기 위해 필요한 것은 서비스에 제공된 URL에서 수신하는 API입니다.

## 공개 도메인 없이 Ngrok을 사용하여 웹훅 테스트하기

![이미지](/assets/img/2024-06-23-WebhookTestingWithouttheHeadacheADevelopersSanity-SavingTutorial_2.png)

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

웹훅을 테스트하는 것은 공개 URL이 필요하기 때문에 종종 귀찮을 수 있습니다. 특히 로컬에서 코드를 작성하려고 할 때 이런 장애물이 있습니다. 하지만 걱정하지 마세요. 이 문제를 해결하는 방법이 있으며 생각보다 쉽습니다.

Ngrok를 이용해보세요. Ngrok를 통해 개발 중에도 API를 공개 도메인에 호스팅하지 않아도 됩니다. Ngrok를 사용하면 로컬 컴퓨터와 인터넷 사이에 중간자 역할을 하는 프록시를 우리의 기기에서 설정할 수 있습니다.

Ngrok는 컴퓨터에서 에이전트를 실행하여 작동합니다. 그런 다음 Ngrok가 무료 도메인을 할당해주고 그 도메인으로의 모든 트래픽을 에이전트로 라우팅합니다. Ngrok 문서에서 자세한 정보를 찾을 수 있습니다.

Ngrok를 사용하려면 계정을 만들어야 하지만 무료로 사용할 수 있습니다. Ngrok에서 계정을 생성하여 사용을 시작해보세요.

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

그 후에는 호스트에서 실행할 수 있는 에이전트를 설치해야 합니다. Ngrok 대시보드에서 설치 지침과 권한 토큰을 찾을 수 있습니다.

```js
wget https://bin.equinox.io/c/bNyj1mQVY4c/ngrok-v3-stable-linux-amd64.tgz
sudo tar -xvzf ngrok-v3-stable-linux-amd64.tgz -C /usr/local/bin
ngrok config add-authtoken YOURTOKEN
```

다음으로 에이전트를 시작하여 애플리케이션으로 웹훅을 받을 수 있습니다.

```js
ngrok http http://localhost:8080
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

명령을 실행하면 약간의 정보가 표시됩니다. 가장 중요한 정보는 Webhooks에 사용할 수있는 URL인 Forwarding입니다.

![Webhook](/assets/img/2024-06-23-WebhookTestingWithouttheHeadacheADevelopersSanity-SavingTutorial_3.png)

## Webhooks를 수신하는 프로젝트 설정

우리는 Webhooks를 트리거하는 서비스가 필요합니다. 간단히 하기위해 저는 이 서비스로 Github를 사용할 것입니다. GitHub 계정을 방문하십시오(또는 마음에 드는 다른 서비스를 사용하십시오)

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

![Webhook Tutorial](/assets/img/2024-06-23-WebhookTestingWithouttheHeadacheADevelopersSanity-SavingTutorial_4.png)

If you create or select a repository you own, you can visit the settings. There should be a Webhooks tab that allows you to set up a webhook.

Usually, webhook services ask you for a URL. This URL should be the domain that Ngrok prints out for you.

The secret can be used to validate requests. Read more on GitHub. The secret will be used to create a hash that we can use to validate that the request comes from GitHub.

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

![WebhookTestingWithouttheHeadacheADevelopersSanity-SavingTutorial_5](/assets/img/2024-06-23-WebhookTestingWithouttheHeadacheADevelopersSanity-SavingTutorial_5.png)

## 웹훅을 수락하는 애플리케이션 생성하기

우리는 Ngrok이 포트 8080에서 수신 대기하고 있으며 Github을 설정하여 이벤트를 Ngrok URL로 푸시하도록 했습니다.

마지막으로 필요한 것은 포트 8080에서 수신 대기하는 웹훅 핸들러입니다. 이를 위해 간단한 Go HTTP 핸들러를 생성하여 요청 내용을 출력하겠습니다.

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

이 서비스는 우리 머신으로 전송된 모든 데이터를 출력하는 매우 간단한 서비스입니다. 테스트하는 데 충분합니다.

```js
package main

import (
 "io"
 "log/slog"
 "net/http"
)

func main() {

 // /에 대한 요청 수락 및 페이로드 출력
 http.HandleFunc("/", func(w http.ResponseWriter, r *http.Request) {
  defer r.Body.Close()

  data, err := io.ReadAll(r.Body)

  if err != nil {
   panic(err)
  }
  slog.Info("새 요청", "페이로드", string(data))
 })

 slog.Info(":8080 포트에서 수신 대기 중")
 // 앱을 :8080에서 제공하며, Ngrok이 웹훅을 로컬 애플리케이션으로 라우팅할 수 있도록 동일한 포트를 사용해야 함(중요)
 http.ListenAndServe(":8080", nil)
}
```

우리는 Ngrok에 사용하라고 한 것과 동일한 포트(여기서는 :8080)를 사용해야 합니다.

로컬호스트:8080의 트래픽을 수신하고 Ngrok이 웹훅을 로컬 애플리케이션으로 라우팅할 수 있도록 go run main.go 명령을 실행하여 Go 애플리케이션을 실행하십시오.

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

GitHub이 웹훅을 생성할 때 PING을 보내게 됩니다. "redeliver"를 눌러 이 PING 훅을 다시 전달할 수 있습니다. "최근 전달"을 방문하여 세 개의 점을 누르고 "redeliver"를 선택하세요.

이번에는 성공적으로 실행되었다는 확인 표시가 보여야 합니다.

![Webhook Testing](/assets/img/2024-06-23-WebhookTestingWithouttheHeadacheADevelopersSanity-SavingTutorial_6.png)

더 검증하기 위해 Go 어플리케이션 출력을 검토해 보세요. GitHub로부터 JSON 페이로드를 출력했어야 합니다.

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
2024/01/24 20:59:47 INFO 포트 :8080에서 수신 대기 중
2024/01/24 21:00:18 INFO 새로운 요청 페이로드="{\"zen\":\"Keep it logically awesome.\
....

## WireMock을 사용한 Webhook 모의

이제 진짜 서비스에서 페이로드를 보내주는 상황에서 몇 가지 문제를 해결해봅시다.

- 만약 서비스가 다운되면 개발 및 테스트를 할 수 없습니다.
- 어떤 서비스는 돈이 들기도 하니, 오프라인 솔루션을 원합니다.
- 서비스들은 일반적으로 사용자 입력이 필요하며 웹훅을 실행하려면, 자동화하고 싶습니다.
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

이런 것들은 Wiremock을 사용하여 해결할 수 있어요.

만약 Wiremock이 익숙치 않다면, 꼭 언급해야겠어요. REST, gRPC, 웹훅 등 다양한 데이터를 모의(mock)하는 데에 탁월한 도구입니다.

우리는 Wiremock을 Docker 컨테이너를 통해 실행할 거에요. Wiremock이 관련된 모든 데이터를 저장할 wiremock라는 새로운 폴더를 만들 거에요.

이 글에서 Wiremock의 모든 내용에 대해 다루지는 않겠지만, 기본적으로 WireMock은 규칙을 정의하는 JSON 파일을 받아들입니다. 이 규칙들은 REST 엔드포인트를 설명하고, 리턴해야 하는 데이터를 정의하거나 보내야 하는 웹훅을 설명할 수 있어요.

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

JSON 파일 안에 이러한 매핑은 mappings 폴더 안에 라이브해야 합니다. 그래서 하나를 생성할 겁니다.

```js
mkdir -p wiremock/mappings
touch wiremock/mappings/webhook.json
```

webhook.json 안에 send-webhook이라는 엔드포인트를 생성할 겁니다. 이 엔드포인트가 GET 요청을 받으면 WebHook을 실행합니다. 우리는 위치 앱의 위치와 사용할 payload를 지정할 수 있습니다.

```js
{
    "request": {
        "urlPath": "/send-webhook",
        "method": "GET"
    },
    "response": {
        "status": 200
    },
    "serveEventListeners": [
        {
            "name": "webhook",
            "parameters": {
                "method": "POST",
                "url": "http://localhost:8080",
                "headers": {
                    "Content-Type": "application/json"
                },
                "body": "{ \"result\": \"SUCCESS\" }"
            }
        }
    ]
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

해당 파일이 있으면 Docker를 사용하여 WireMock를 시작하고 호스트 네트워크에서 실행되는지 확인합니다. 이렇게 하면 포트 8080에서 실행 중인 Go 애플리케이션에 연결할 수 있습니다.

일반적으로 Docker compose를 사용하여 서비스 이름을 대신 사용하는 경우가 많은데, 이번 튜토리얼 중에는 편의를 위해 localhost를 사용할 것입니다. WireMock 폴더를 마운트하고, 프로젝트 루트에 위치해 있는지 확인하세요.

```js
docker run -it --rm --name wiremock --network=host -v $PWD/wiremock:/home/wiremock wiremock/wiremock:3.3.1 --verbose --port=8081
```

WireMock는 포트 8081에서 실행 중이므로 해당 URL에 CURL을 보내고 Webhook을 활성화할 수 있습니다.

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
curl localhost:8081/send-webhook
```

위 명령을 실행한 후 Go 서비스에서 웹훅이 도착하는 것을 확인할 수 있어요.

```js
2024/01/24 21:49:10 INFO New request payload="{ \"result\": \"SUCCESS\" }"
```

실제 데이터를 모킹하기 위해 Ngrok 웹훅에서 Payload를 훔쳐와서 wiremock 매핑의 body 필드에 붙여넣을 수 있어요. 이렇게 하면 서비스에서 제공되는 실제 데이터와 유사한 데이터로 모킹할 수 있어요.

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

## 결론

이 튜토리얼에서는 서비스로부터 웹훅을 로컬 애플리케이션에 받아들이는 간단한 방법을 만들었습니다. 이것은 타사 애플리케이션에서 웹훅을 테스트하는 데 사용될 수 있습니다.

일반적으로 저는 실제 페이로드가 전송되는 Ngrok를 설정하여 시작합니다. 타사 서비스가 어떻게 작동하며 어떤 종류의 데이터를 기대할지를 배우는 데 사용할 수 있습니다.
또한 항상 터널을 사용하여 트래픽에 의존하는 것을 중지할 수도 있습니다.
실제 페이로드를 받은 후에는 일반적으로 WireMock에 넣어서 종속성이나 개발 후크 지불을 피할 수 있습니다.

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

이 세팅을 통해 개발 중에 데이터를 쉽게 목업하고, 웹훅에 의존하는 애플리케이션을 빌드하는 동안 엔드 투 엔드로 테스트할 수 있습니다.

이 튜토리얼이 마음에 들었길 바라며, 아이디어나 생각이 있으시면 언제든지 연락해주세요!

## 부록

Ngrok — https://ngrok.com/

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

**Ngrok 동작 원리** - [링크](https://ngrok.com/docs/how-ngrok-works/)

**WireMock** - [링크](https://wiremock.org/docs/webhooks-and-callbacks/)

**GitHub 웹훅** - [링크](https://docs.github.com/en/webhooks/using-webhooks/creating-webhooks)

**GitHub 웹훅 유효성 검사** - [링크](https://docs.github.com/en/webhooks/using-webhooks/validating-webhook-deliveries)
