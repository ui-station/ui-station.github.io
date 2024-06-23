---
title: "Telegram 봇 백엔드로 AWS Lambda 사용 하는 방법"
description: ""
coverImage: "/assets/img/2024-06-23-UsingAWSLambdaasTelegrambotbackend_0.png"
date: 2024-06-23 22:31
ogImage: 
  url: /assets/img/2024-06-23-UsingAWSLambdaasTelegrambotbackend_0.png
tag: Tech
originalTitle: "Using AWS Lambda as Telegram bot backend"
link: "https://medium.com/serverless-bots/using-aws-lambda-as-telegram-bot-backend-52cd2cbeebc5"
---


Idea부터 구현까지: 빠르고 유연하며 서버리스하고 비용 효율적인 상호 작용을 위해 AWS Lambda를 사용하여 Telegram Bot을 만들고 구성하는 방법.

![이미지](/assets/img/2024-06-23-UsingAWSLambdaasTelegrambotbackend_0.png)

텔레그램 봇은 두 가지 모드로 작동할 수 있습니다:

- Pull 모드 — 이 모드에서는 봇이 지속적으로 실행되어 Telegram을 폴링하여 새 업데이트가 있는지 확인합니다. 새 메시지가 있으면 해당 메시지에 따라 작동합니다.
- Webhook 모드 — 이 모드에서는 Telegram이 새 메시지가 있는 경우 지정된 URL로 HTTP 요청을 보냅니다. 그런 다음 봇은 이러한 수신 요처르 처리합니다.

<div class="content-ad"></div>

풀 모드에서는 텔레그램을 주기적으로 폴링하는 코드가 계속 실행되어야 합니다. 이 방법은 수신 연결을 다룰 필요가 없어 안전합니다. 하지만 코드를 어딘가에 호스팅해야하는 번거로움이 있습니다.

웹훅 모드에서는 비슷한 호스팅 문제가 발생합니다. 텔레그램이 요청을 보낼 수 있는 리소스가 필요합니다.

왜 우리가 웹훅 API를 AWS에 호스팅하지 않을까요? 서버리스 인프라는 이러한 유형의 솔루션에 완벽히 적합합니다. 다음은 이를 설정하는 간단한 방법입니다:

![사진](/assets/img/2024-06-23-UsingAWSLambdaasTelegrambotbackend_1.png)

<div class="content-ad"></div>

위 작업을 구현하려면 다음 단계를 수행해야 합니다:

- 텔레그램 봇을 등록합니다.
- 텔레그램에 의해 호출될 람다 함수를 만들고 이 함수를 REST API를 통해 "world"에 공개합니다.
- HTTP API URL을 웹훅으로 등록하여 텔레그램이 람다를 호출할 수 있도록 합니다.
- 텔레그램 HTTP API를 통해 텔레그램 요청에 응답하는 방법을 배웁니다.

시작해봅시다!

봇 등록하기

<div class="content-ad"></div>

온라인에서 이 작업을 수행하는 방법에 대한 많은 지침이 있습니다. 간단히 말해서, @BotFather에 연락하고 /newbot 명령을 사용하여 새 봇을 만들 수 있습니다. 등록 결과로 우리 봇을 식별할 수 있는 Bot Token(특정 코드)을 받게 됩니다.

람다 함수 생성하기
추가 작업을 위해서는 AWS 계정에 액세스할 수 있어야 합니다. 아직 계정이 없다면 여기에서 계정을 만들 수 있습니다.

AWS 콘솔에서 Lambda 섹션으로 이동하여 새 함수를 만듭니다. 예를 들어, 우리는 Python을 사용할 것입니다. 하지만 편리한 다른 언어로 Python을 대체할 수 있습니다.

<div class="content-ad"></div>

아래는 새 함수를 생성할 때 파이썬 블루프린트를 선택하고 함수 이름을 지정하세요:

![이미지](/assets/img/2024-06-23-UsingAWSLambdaasTelegrambotbackend_4.png)

<div class="content-ad"></div>

함수를 만든 후, 코드를 다음과 같이 변경할 것입니다:

```js
import json

def lambda_handler(event, context):
    print("*** Received event")
    print(json.dumps(event))
    
    return "Ok"  # Echo back the first key value
```

![Lambda Function](/assets/img/2024-06-23-UsingAWSLambdaasTelegrambotbackend_5.png)

이 단계에서 람다 함수의 자리 표시자가 준비되었습니다. 그러므로 API를 생성하는 작업을 진행할 수 있습니다.

<div class="content-ad"></div>

# 람다를 위한 HTTP API 만들기

API를 생성하려면 API Gateway 섹션으로 이동하고 새 HTTP API를 만드세요.

![image1](/assets/img/2024-06-23-UsingAWSLambdaasTelegrambotbackend_6.png)

![image2](/assets/img/2024-06-23-UsingAWSLambdaasTelegrambotbackend_7.png)

<div class="content-ad"></div>

API를 생성할 때 람다 함수와의 통합을 정의하고 API 이름을 지정하세요:

![이미지](/assets/img/2024-06-23-UsingAWSLambdaasTelegrambotbackend_8.png)

다음 페이지에서는 리소스 경로를 변경할 것입니다.

![이미지](/assets/img/2024-06-23-UsingAWSLambdaasTelegrambotbackend_9.png)

<div class="content-ad"></div>

API를 생성한 후 브라우저에서 접근 가능한지 확인해보세요. API URL은 "단계" 섹션에서 찾을 수 있습니다(거기서 URL을 복사하세요).

![API URL screenshot](/assets/img/2024-06-23-UsingAWSLambdaasTelegrambotbackend_10.png)

링크를 열어보세요. Invoke URL에 /webhook 경로를 추가하는 것을 잊지 마세요.

![Invoke URL screenshot](/assets/img/2024-06-23-UsingAWSLambdaasTelegrambotbackend_11.png)

<div class="content-ad"></div>

브라우저에서 올바르지 않은 JSON 응답에 대한 오류가 표시되지만 괜찮아요. 가장 중요한 것은 "Ok" 응답을 받았다는 것을 확인했다는 점이에요. 이는 람다 함수의 전반적인 인프라가 작동 준비가 되어 있다는 것을 의미해요.

이제 우리는 람다 함수 자체를 수정하여 텔레그램 요청을 처리할 수 있도록 할 수 있어요.

# 람다 함수 튜닝

텔레그램 요청 형식은 문서에서 찾을 수 있어요.

<div class="content-ad"></div>

람다 함수로 전달되는 메시지는 다음과 유사할 것입니다:

```js
{
  "update_id": 10000,
  "message": {
    "date": 1441645532,
    "chat": {
      "last_name": "Test Lastname",
      "id": 1111111,
      "first_name": "Test",
      "username": "Test"
    },
    "message_id": 1365,
    "from": {
      "last_name": "Test Lastname",
      "id": 1111111,
      "first_name": "Test",
      "username": "Test"
    },
    "text": "/start"
  }
}
```

이 단계에서 다음 필드가 중요합니다:

- $.message.chat.id - 이것은 텔레그램 사용자가 우리 봇에게 무언가를 쓴 채팅 ID입니다.
- $.message.from.username - 이것은 메시지를 보낸 사용자의 텔레그램 사용자 이름입니다.
- $.message.text - 이것은 사용자 메시지의 텍스트입니다.

<div class="content-ad"></div>

우리 람다 작업에서 이러한 필드를 로그로 기록해 보겠습니다. 이를 위해 다음과 같이 함수 코드를 다음과 같이 교체할 것입니다:

```js
import json

def lambda_handler(event, context):
    print("*** 이벤트 수신됨")
    print(json.dumps(event))

    chat_id = event['message']['chat']['id']
    user_name = event['message']['from']['username']
    message_text = event['message']['text']

    print(f"*** 채팅 ID: {chat_id}")
    print(f"*** 사용자 이름: {user_name}")
    print(f"*** 메시지 본문: {message_text}")

    return "Ok"  # 첫 번째 키 값을 다시 에코합니다
```  

또한 테스트를 위해 람다 함수에 전달되는 새 테스트 이벤트를 생성할 것입니다. 람다 함수는 HTTP API와 통합되어 있으므로 다음과 유사한 메시지를 수신하게 될 것입니다:

```js
{
  "version": "2.0",
  "routeKey": "ANY /webhook",
  "rawPath": "/webhook",
  "rawQueryString": "",
  "headers": {
    "accept": "*/*",
    "accept-encoding": "gzip, deflate, br",
    "content-length": "394",
    "content-type": "application/json",
    "host": "ng79a68gph.execute-api.eu-central-1.amazonaws.com",
    "postman-token": "cbdf81be-3707-4383-b5b4-0d6fc71940e6",
    "user-agent": "PostmanRuntime/7.39.0",
    "x-amzn-trace-id": "Root=1-6676d931-3d6752cf6f055cd810e8f4e2",
    "x-forwarded-for": "185.244.156.97",
    "x-forwarded-port": "443",
    "x-forwarded-proto": "https"
  },
  "requestContext": {
    "accountId": "126459111222",
    "apiId": "ng79a68gph",
    "domainName": "ng79a68gph.execute-api.eu-central-1.amazonaws.com",
    "domainPrefix": "ng79a68gph",
    "http": {
      "method": "POST",
      "path": "/webhook",
      "protocol": "HTTP/1.1",
      "sourceIp": "185.244.156.97",
      "userAgent": "PostmanRuntime/7.39.0"
    },
    
    ... 중략 ...
    
  },
  "body": "{\r\n  \"update_id\": 10000,\r\n  \"message\": {\r\n    \"date\": 1441645532,\r\n    \"chat\": {\r\n      \"last_name\": \"Test Lastname\",\r\n      \"id\": 1111111,\r\n      \"first_name\": \"Test\",\r\n      \"username\": \"Test\"\r\n    },\r\n    \"message_id\": 1365,\r\n    \"from\": {\r\n      \"last_name\": \"Test Lastname\",\r\n      \"id\": 1111111,\r\n      \"first_name\": \"Test\",\r\n      \"username\": \"Test\"\r\n    },\r\n    \"text\": \"/start\"\r\n  }\r\n}",
  "isBase64Encoded": false
}
```

<div class="content-ad"></div>

위의 메시지를 테스트 예제로 설정해보고 얻는 결과를 확인해봅시다:


![Image 1](/assets/img/2024-06-23-UsingAWSLambdaasTelegrambotbackend_12.png)

![Image 2](/assets/img/2024-06-23-UsingAWSLambdaasTelegrambotbackend_13.png)


좋아요! 메시지에서 필요한 필드를 성공적으로 읽었습니다!

<div class="content-ad"></div>

저희 함수는 지금부터 텔레그램에서 메시지를 수신할 수 있습니다. 다음으로 응답하는 방법을 알아보아야 합니다. 이를 위해 우리는 텔레그램 API의 sendMessage 메서드를 사용할 것입니다.

지정된 chat_id로 답장 메시지를 보내는 람다 함수에 메소드를 추가해봅시다. 함수 코드를 다시 업데이트하세요. 반드시 "your-bot-token"을 @BotFather에서 받은 텔레그램 봇의 토큰으로 대체하는 것을 잊지 마세요.

```js
import json
import urllib3

BOT_TOKEN="your-bot-token"


def sendReply(chat_id, message):
    reply = {
        "chat_id": chat_id,
        "text": message
    }

    http = urllib3.PoolManager()
    url = f"https://api.telegram.org/bot{BOT_TOKEN}/sendMessage"
    encoded_data = json.dumps(reply).encode('utf-8')
    http.request('POST', url, body=encoded_data, headers={'Content-Type': 'application/json'})
    
    print(f"*** Reply : {encoded_data}")


def lambda_handler(event, context):
    body = json.loads(event['body'])

    print("*** Received event")

    chat_id = body['message']['chat']['id']
    user_name = body['message']['from']['username']
    message_text = body['message']['text']

    print(f"*** chat id: {chat_id}")
    print(f"*** user name: {user_name}")
    print(f"*** message text: {message_text}")
    print(json.dumps(body))

    reply_message = f"Reply to {message_text}"

    sendReply(chat_id, reply_message)

    return {
        'statusCode': 200,
        'body': json.dumps('Message processed successfully')
    }    
```

그 결과, 우리 함수는 텔레그램에서 어떤 것이라도 답장할 수 있어야 합니다. 응답은 사용자의 메시지 뒤에 "Reply to" 문자열이어야 합니다. 이 동작은 나중에 변경해야 할 것이지만, 지금은 전체 웹훅 봇 인프라를 구축하는 데 충분합니다.

<div class="content-ad"></div>

텔레그램 봇을 텔레그램에 연결해 봅시다.

텔레그램에 연결하기

텔레그램에 연결하려면 다음 주소로 요청을 보내야 합니다:

```js
https://api.telegram.org/bot{bot_token}/setWebhook?url={webhook_endpoint}
```

<div class="content-ad"></div>

아래와 같이 사용할 수 있습니다:

- 'bot_token'은 이미 사용한 봇 토큰입니다.
- 'webhook_endpoint'은 우리 API의 URL입니다.

웹훅 URL을 설정한 후의 응답:

```js
{
    "ok": true,
    "result": true,
    "description": "웹훅이 설정되었습니다"
}
```

<div class="content-ad"></div>

연결을 확인하려면 다음과 같이 요청을 할 수 있어요:

```js
https://api.telegram.org/bot{bot_token}/getWebhookInfo
```

응답은 아래와 비슷하게 보일 거에요:

```js
{
    "ok": true,
    "result": {
        "url": "{webhook_endpoint}",
        "has_custom_certificate": false,
        "pending_update_count": 0,
        "max_connections": 40,
        "ip_address": "..."
    }
}
```

<div class="content-ad"></div>

우리 봇에 메시지를 전송해 보도록 해요:

![image](/assets/img/2024-06-23-UsingAWSLambdaasTelegrambotbackend_14.png)

잘 동작하네요!

Lambda 함수의 작동 로그를 CloudWatch에서 확인할 수 있어요:

<div class="content-ad"></div>

아래는 AWS Lambda를 Telegram 봇의 백엔드로 사용하는 방법에 대한 이미지입니다.

또한 Lambda 함수가 예상대로 작동하지 않거나 Telegram에서 응답이 없는 경우 디버깅 도구로 매우 유용한 CloudWatch를 사용할 수 있습니다. CloudWatch에서 메시지는 약간의 지연(1~3분)이 발생한다는 점을 참고해 주세요.