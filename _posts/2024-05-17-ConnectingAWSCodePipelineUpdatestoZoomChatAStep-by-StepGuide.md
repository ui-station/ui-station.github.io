---
title: "AWS CodePipeline 업데이트를 Zoom 채팅에 연결하는 방법 단계별 안내"
description: ""
coverImage: "/assets/img/2024-05-17-ConnectingAWSCodePipelineUpdatestoZoomChatAStep-by-StepGuide_0.png"
date: 2024-05-17 18:09
ogImage:
  url: /assets/img/2024-05-17-ConnectingAWSCodePipelineUpdatestoZoomChatAStep-by-StepGuide_0.png
tag: Tech
originalTitle: "Connecting AWS CodePipeline Updates to Zoom Chat: A Step-by-Step Guide"
link: "https://medium.com/@pooja_shrestha/connecting-aws-codepipeline-updates-to-zoom-chat-a-step-by-step-guide-599e63eb32e0"
---

코드 파이프 라인 콘솔에 계속해서 붙어 있어 지친 적이 있나요? 이 안내서를 통해 코드 파이프 라인 실행에 대한 실시간 알림을 직접 Zoom 채널에서 받는 방법을 안내해 드릴 거예요.

파이프 라인 실패를 신속히 식별하고 해결하는 데 도움을 주며, 문맥 전환을 줄여주는 이 통합은 집중력 향상에 도움을 주고 팀원들을 알려주며, 더 나아가 전반적인 의사 소통과 효율성을 향상시키는 데 도움이 될 거예요.

## 절차:

![이미지](/assets/img/2024-05-17-ConnectingAWSCodePipelineUpdatestoZoomChatAStep-by-StepGuide_0.png)

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

### 단계 1: Zoom 수신 웹훅 설정하기

먼저, AWS CodePipeline에서 알림을 보낼 수 있는 Zoom 수신 웹훅을 설정해야 합니다.

- Zoom 계정에 로그인하고 Zoom Marketplace로 이동합니다.
- "수신 웹훅"을 검색하고 앱을 설치합니다. 앱을 설치할 수 없는 경우 계정 관리자에게 문의하세요.
- 앱이 설치되면 Zoom 채팅의 앱 섹션에서 찾을 수 있습니다.

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

- 설치가 완료되면 'Incoming Webhooks' 앱으로 이동하세요.

![이미지](/assets/img/2024-05-17-ConnectingAWSCodePipelineUpdatestoZoomChatAStep-by-StepGuide_2.png)

- 도움말을 입력하는 동안 '앱 비활성화' 메시지가 표시되면, 관리자에게 앱을 활성화할 수 있도록 요청해보세요.
- 웹훅에 대한 명령어를 확인하려면 'help'를 입력하세요.

![이미지](/assets/img/2024-05-17-ConnectingAWSCodePipelineUpdatestoZoomChatAStep-by-StepGuide_3.png)

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

- 원하는 채널/그룹으로 이동하여 CodePipeline 알림을 받기를 원하는 곳에서 '/ '을 메시지 상자에 입력하여 사용 가능한 명령을 확인하세요.

![Image 1](/assets/img/2024-05-17-ConnectingAWSCodePipelineUpdatestoZoomChatAStep-by-StepGuide_4.png)

- 연결 명령을 선택하고 웹훅에 대한 설명적인 이름을 입력하세요.

![Image 2](/assets/img/2024-05-17-ConnectingAWSCodePipelineUpdatestoZoomChatAStep-by-StepGuide_5.png)

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

- 제공된 엔드포인트와 검증 토큰을 확인하세요.

단계 2: 코드파이프라인 알림을 위한 SNS 토픽 설정

- SNS 서비스로 이동합니다.
- "토픽 생성"을 클릭하고 "표준"을 선택합니다.
- 토픽에 이름을 입력하고 토픽을 생성합니다.

단계 3: 코드파이프라인 알림 구성

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

- CodePipeline으로 이동하세요.
- 구성하려는 파이프라인을 선택하세요.
- "알림 관리"를 선택하세요.
- "이벤트 유형" 아래에서, 알림을 받기 원하는 이벤트를 선택하세요.
- "대상" 섹션에서 "SNS 주제"를 선택하고, 이전에 생성한 주제의 SNS 주제 ARN을 붙여넣으세요.

![이미지](/assets/img/2024-05-17-ConnectingAWSCodePipelineUpdatestoZoomChatAStep-by-StepGuide_6.png)

4단계: 람다 함수 생성

- Lambda 서비스로 이동하세요.
- "함수 생성"을 클릭하고, "스크래치부터 작성"을 선택하세요.
- 함수 이름을 제공하고 런타임을 선택하세요.
- 나머지 옵션은 기본값으로 남기고, "함수 생성"을 클릭하세요.

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
import urllib.request
import json

ZOOM_WEBHOOK_URL = "여기에 Zoom 웹훅 URL을 입력하세요"
BEARER_TOKEN = "베어러 토큰"

def lambda_handler(event, context):
    # 받은 이벤트 기록
    print("받은 이벤트: " + json.dumps(event, indent=2))

    for record in event['Records']:
        sns_message = json.loads(record['Sns']['Message'])

        pipeline = sns_message['detail']['pipeline']
        stage = sns_message['detail']['stage']
        state = sns_message['detail']['state']

        # 메시지 준비
        message = f"{pipeline}의 {stage} 스테이지가 {state} 상태입니다."
        data = json.dumps(message).encode('utf-8')

        # Zoom 웹훅으로 메시지 보내기
        req = urllib.request.Request(
            ZOOM_WEBHOOK_URL,
            data=data,
            headers={
                'Content-Type': 'application/json',
                'Authorization': f'Bearer {BEARER_TOKEN}'
            }
        )

        try:
            with urllib.request.urlopen(req) as response:
                print("Zoom으로부터 응답: " + response.read().decode('utf-8'))
        except urllib.error.URLError as e:
            print(f"Zoom에 메시지 보내는 중 오류 발생: {e.reason}")

    return {
        'statusCode': 200,
        'body': json.dumps('Zoom으로 푸시된 알림')
    }
```

URL 및 베어러 토큰을 코드에 직접 입력하는 대신, Parameter Store를 활용할 수 있습니다.

이제 코드를 배포합니다.

스텝 5: SNS 토픽으로 Lambda 함수 구성하기

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

- 이전에 만든 SNS 주제를 Lambda 함수의 트리거로 추가하세요.

![img](/assets/img/2024-05-17-ConnectingAWSCodePipelineUpdatestoZoomChatAStep-by-StepGuide_7.png)

단계 6: 통합 테스트

- 모든 것이 올바르게 설정되었는지 확인하기 위해 SNS 템플릿을 사용하여 람다 함수를 테스트하세요. 테스트를 진행하면 구성된 웹훅 그룹/채널에서 예시 이벤트를 받게 될 겁니다.

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

![Screenshot](/assets/img/2024-05-17-ConnectingAWSCodePipelineUpdatestoZoomChatAStep-by-StepGuide_8.png)

- 이제 콘솔을 통해 수동으로 변경 사항을 CodePipeline에서 해제하거나, 파이프라인이 트리거되기를 기다립니다.
- 파이프라인에서 변경 사항이 있을 때, 이전에 선택한 단계에 대한 알림을 받게 됩니다.

AWS CodePipeline 알림을 Zoom 채팅 채널과 통합하면 지속적인 콘솔 확인이 필요 없어지며, 팀을 최신 상태로 유지하고 개발 워크플로우를 향상시킬 수 있습니다.
