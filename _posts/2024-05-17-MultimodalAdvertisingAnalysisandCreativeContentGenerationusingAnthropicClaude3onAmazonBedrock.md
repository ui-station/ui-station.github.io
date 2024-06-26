---
title: "다중 모달 광고 분석 및 창의적 콘텐츠 생성을 Amazon Bedrock에서 Anthropic Claude 3를 활용하여 수행하기"
description: ""
coverImage: "/assets/img/2024-05-17-MultimodalAdvertisingAnalysisandCreativeContentGenerationusingAnthropicClaude3onAmazonBedrock_0.png"
date: 2024-05-17 18:11
ogImage:
  url: /assets/img/2024-05-17-MultimodalAdvertisingAnalysisandCreativeContentGenerationusingAnthropicClaude3onAmazonBedrock_0.png
tag: Tech
originalTitle: "Multimodal Advertising Analysis and Creative Content Generation using Anthropic Claude 3 on Amazon Bedrock"
link: "https://medium.com/@garystafford/marketing-and-advertising-analysis-and-creative-content-generation-using-anthropic-claude-3-on-f522bd9943e6"
---

요즘은 산업 비즈니스 및 엔지니어링 리더들의 대규모 그룹 앞에서 "진화하는 생성형 AI 아키텍처 패턴: 광고기술, 마케팅기술 및 광고대행사"이라는 강연을 하는 영권을 가졌습니다. 이 강연 중에는 9가지 변화하는 산업별 생성형 AI 아키텍처 패턴을 소개했습니다. 이 게시물에서는 생성형 AI를 사용하여 기존 마케팅 및 광고 콘텐츠에서 통찰을 얻는 하나와 해당 분석 및 관련 데이터를 사용하여 새로운 창의적 컨셉을 개발하는 또 다른 보조적 아키텍처 패턴 두 가지를 탐구해 보겠습니다.

![이미지](https://miro.medium.com/v2/resize:fit:1400/1*F_6zO6yr3wEzDF8v78qaFQ.gif)

## 아키텍처 1: 생성적 통찰력 도출

첫 번째 아키텍처는 안토픽 클로드 3 패밀리와 같은 비전 가능한 모델이 문서 및 이미지를 이해하고 분석할 수 있는 능력을 강조합니다. 텍스트 기반 프롬프트 외에도 업로드된 문서 및 이미지를 분석할 수 있습니다. 프롬프트는 인간 피드백과 함께 프롬프트 라이브러리에 저장될 수 있습니다. 아마존 베드락의 가드레일은 여전히 미리보기 단계에 있지만 프롬프트 및 응답 모두에 대한 규정 준수를 보장합니다. 마지막으로, 동일한 기반 모델 또는 다른 모델을 사용하여 사용자의 초기 프롬프트를 최적화합니다.

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

![이미지](/assets/img/2024-05-17-MultimodalAdvertisingAnalysisandCreativeContentGenerationusingAnthropicClaude3onAmazonBedrock_0.png)

## 아키텍처 2: 창의적 광고 콘텐츠 생성

두 번째 아키텍처는 기초 모델이 창의적 디렉터, 카피 라이터, 디지털 아티스트 및 그래픽 디자이너가 새로운 컨셉, 레이아웃, 헤드라인, 광고 복사본 및 이미지를 개발하는 능력을 강조합니다. 기초 모델은 내용을 실제 디지털 광고로 바꾸는 컴퓨터 코드를 작성하는 데도 도움을 줄 수 있습니다.

![이미지](/assets/img/2024-05-17-MultimodalAdvertisingAnalysisandCreativeContentGenerationusingAnthropicClaude3onAmazonBedrock_1.png)

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

이 두 가지 패턴은 서로 보완하며 마케팅 및 광고 콘텐츠 분석 및 작성 능력을 향상시키는데 큰 역할을 합니다.

# Anthropik Claude 3 모델

이 게시물은 Anthropik의 최근 2024년 3월 클로드 3 기반 인공 지능 모델인 Claude 3 Haiku, Claude 3 Sonnet 및 Claude 3 Opus를 발표한 소식 없이는 불가능했습니다. 클로드 3 모델 패밀리는 단순한 패턴 인식이나 텍스트 생성을 넘어 복잡한 인지 작업을 수행하며, 필기 노트부터 그래프, 사진까지 거의 모든 정적 이미지를 전사 및 분석하며, 코드를 생성하고 복잡한 코드 베이스를 디버그하며, 다양한 언어 간 실시간으로 번역하고 문법을 연습하거나 다국어 콘텐츠를 생성할 수 있습니다. 이 게시물에 가장 중요한 것은 Anthropik에 따르면 "클로드 3 모델은 다른 주요 모델과 윤곽이 유사한 정교한 시각 능력을 갖고 있습니다. 사진, 차트, 그래프 및 기술도표 등 다양한 시각 형식을 처리할 수 있습니다."

# Amazon Bedrock

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

Anthropic가 발표한 지 하루도 채 되지 않은 시점에 AWS는 Claude 3 모델 패밀리가 Amazon Bedrock을 통해 이용 가능하다고 발표했습니다. AWS에 따르면 "Amazon Bedrock은 AI21 Labs, Anthropic, Cohere, Meta, Mistral AI, Stability AI 및 Amazon과 같은 주요 AI 기업들의 고효율 기본 모델(FMs)을 단일 API를 통해 제공하며, 보안, 개인 정보 보호 및 책임 있는 AI를 구축하는 데 필요한 다양한 기능을 함께 제공하는 완전 관리형 서비스입니다."

# 소스 코드

이 게시물의 모든 오픈 소스 코드는 GitHub에서 이용할 수 있습니다.

# 멀티모달 프롬프팅

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

Anthropic Claude 3 패밀리의 다중 모달 프롬프트 기능을 활용하기 위해 Amazon Bedrock에서 Streamlit 애플리케이션을 개발했어요. 현재 이 애플리케이션을 통해 하나 이상의 JPG, PNG, GIF, WEBP, PDF, CSV 또는 TXT 형식 파일을 업로드할 수 있어요. 또한 모델 (Sonnet 또는 Haiku)을 변경하고 max_tokens, temperature, top_p, top_k와 같은 추론 매개변수를 조절할 수 있는 기능도 제공돼요. 게다가 이 애플리케이션은 총 분석 시간(초)과 소비된 입력 및 출력 토큰 수를 반환할 거예요.

아래는 Streamlit 애플리케이션을 실행하는 방법이에요. 먼저 프로젝트 종속성을 설치할 파이썬 가상 환경을 생성하는 것을 제안해요.

```js
python3 -m pip install virtualenv -Uq
virtualenv claude-streamlit-venv
python3 -m venv claude-streamlit-venv

source claude-streamlit-venv/bin/activate

python3 -m pip install -r requirements.txt -Uq
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

가상 환경이 생성되고 종속성이 설치된 후, 다음 명령어를 사용하여 터미널에서 Streamlit 앱을 시작하세요:

```js
streamlit run app.py \
    --server.runOnSave true \
    --theme.base "light" \
    --theme.backgroundColor "#333333" \
    --theme.primaryColor "#CCC8AA" \
    --theme.secondaryBackgroundColor "#777777" \
    --ui.hideTopBar "true" \
    --client.toolbarMode "minimal"
```

# 다중 모달 프롬프트 예제

GitHub 저장소의 README 파일에는 제가 강연에서 보여준 다양한 다중 모달 프롬프트 예제가 포함되어 있습니다. 이 예제들에 사용된 브랜드와는 어떠한 제휴도 없음을 유의해 주세요.

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

## 예시 1: 기존 광고 분석

이 예시에서는 시각을 인식할 수 있는 Claude 3 기반 모델을 활용하여 인터넷에서 쉽게 찾을 수 있는 몇 가지 기존 메르세데스-벤츠 인쇄 광고 예시를 조사하고 창의적인 분석을 제공할 것입니다. 잘 설계된 예시 프롬프트는 다음과 같습니다:

영어로 된 메르세데스-벤츠 세단을 위한 네 개의 인쇄 광고를 분석하십시오. 이 중 두 개는 영어로, 두 개는 독일어로 되어 있습니다. 성공에 기여하는 적어도 다섯 가지 공통 창의적 요소를 식별하십시오. 시각적 디자인 및 이미지, 메시지 및 복사 쓰기, 색상, 타이포그래피 및 브랜드 활용, 상호작용 또는 멀티미디어 구성 요소, 메르세데스-벤츠 브랜드 정체성 및 포지셔닝과의 일치와 같은 요소를 검토하십시오.

각 요소에 대해 광고를 통해 효과적으로 활용된 방법을 설명하고 왜 이것이 강력한 창의적 선택임을 설명하십시오. 분석을 지원하기 위한 구체적인 예시와 통찰을 제공하십시오. 목표는 이러한 메르세데스-벤츠 광고를 매력적이고 효과적으로 만드는 주요 창의 전략을 발견하는 것입니다.

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

중요: 광고가 제공되지 않았다면 분석 결과를 생성하지 마십시오.

업로드하고 분석할 네 가지 광고 예시가 있습니다.

![advertising example](/assets/img/2024-05-17-MultimodalAdvertisingAnalysisandCreativeContentGenerationusingAnthropicClaude3onAmazonBedrock_3.png)

분석은 Anthropic Claude 3 Sonnet on Amazon Bedrock을 사용하여 약 13초가 소요되었습니다. 프롬프트는 5,463개의 입력 토큰을 사용하였고, 응답은 432개의 출력 토큰을 사용하였습니다. 모델의 창조적 분석은 중요한 디자인 요소를 세심하게 식별했습니다. 이 요소에는 1/ 강렬한 시각적 이미지, 2/ 미니멀하고 우아한 타이포그래피, 3/ 효과적인 색상 활용, 4/ 일관된 브랜딩과 태그 라인, 그리고 5/ 성능과 혁신에 대한 강조가 포함되어 있습니다.

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

![이미지](/assets/img/2024-05-17-MultimodalAdvertisingAnalysisandCreativeContentGenerationusingAnthropicClaude3onAmazonBedrock_4.png)

## 예시 2: 프롬프트 최적화

다음 예시에서는 Anthropic Claude 3 모델에게 사용자의 프롬프트 품질을 향상시키도록 요청할 것입니다. 이 기술은 프롬프트 엔지니어링 기술에 익숙하지 않거나 분석 주제에 익숙하지 않은 사용자에게 효과적일 수 있습니다. 다음은 예시 프롬프트입니다:

당신은 마케팅 분석가를 위한 대규모 언어 모델 프롬프트 최적화 전문가입니다. 다음 마케팅 분석가의 작업 설명과 수반하는 광고 샘플을 고려하면, 제공된 콘텐츠를 기반으로 대규모 언어 모델로부터 효과적인 응답을 제공할 수 있는 간결하고 효과적인 긍정적인 프롬프트를 작성해야 합니다. 분석에 초점을 맞출 요소들의 간결한 목록을 포함하십시오.

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

과제: 이러한 성공적인 영어와 독일어 인쇄 광고를 분석하고 효과적인 요소들을 발견해보세요.

Anthropic Claude 3 Sonnet을 사용하여 프롬프트 최적화 요청 작업에는 약 8.7초가 걸렸습니다. 프롬프트는 5,381개의 입력 토큰을 사용했으며, 응답은 141개의 출력 토큰을 사용했습니다. 새로운 프롬프트에는 광고 콘텐츠에 대한 사실들과 보다 완전한 분석을 보장하기 위한 추가 지시사항이 포함되어 있습니다.

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

## 예시 3: 분석을 통한 새로운 창의적 개념 생성

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

이전 예시에서 생성된 창의적인 분석을 사용하여, 우리는 기반 모델을 활용하여 새로운 광고 컨셉을 개발할 수 있습니다. 이 예시에서는 제품, 대상 고객 세그먼트, 광고 채널 및 광고 형식을 제공하고 모델에게 창의적 컨셉을 개발하도록 요청합니다. 다음은 예시 프롬프트입니다:

4개의 성공적인 메르세데스-벤츠 인쇄 광고에 대한 다음 분석을 기반으로, '모던 럭셔리 | 맨해튼' 웹사이트에 나타나는 '대형 리더보드' 사이즈 디지털 광고를 위한 새롭고 매력적인 디자인을 설명하십시오. 이 광고는 25~35세의 젊은 부유한 도시 부부를 대상으로 한 메르세데스-벤츠 C-클래스 세단을 타깃팅합니다. 레이아웃을 참조할 수 있도록 '모던 럭셔리 | 맨해튼' 온라인 광고 사양에 대한 PDF를 사용하십시오. 답변에 광고의 크기를 포함하세요.

여기서 분석 내용... 이 메르세데스-벤츠 인쇄 광고는 몇 가지 매력적인 창의적 요소를 보여줍니다...

프롬프트와 함께, 발행사 정보를 담은 PDF 파일과 발행사의 디지털 광고 사양을 담은 다른 PDF 파일을 업로드해 주세요.

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

![이미지](/assets/img/2024-05-17-MultimodalAdvertisingAnalysisandCreativeContentGenerationusingAnthropicClaude3onAmazonBedrock_6.png)

Anthropic Claude 3 소네트를 사용하여 작업은 약 14초가 걸렸습니다. 분석에는 4,145개의 입력 토큰과 516개의 출력 토큰이 사용되었습니다. 결과 광고 컨셉에는 요약, 이미지 제안, 헤드라인 텍스트, 광고 복사본, 로고 및 콜 투 액션이 포함되어 있습니다.

![이미지](/assets/img/2024-05-17-MultimodalAdvertisingAnalysisandCreativeContentGenerationusingAnthropicClaude3onAmazonBedrock_7.png)

우리는 이제, 같은 브랜드와 제품을 위한 기존 광고의 생성 AI 분석을 기반으로 한 새 광고 컨셉을 갖게 되었습니다.

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

## 4: 창의적 브리프 분석 및 새로운 창의적 컨셉 생성

이제 두 가지 이전 예제를 하나의 작업으로 결합하겠습니다. 첫 번째로, 인터넷에서 널리 이용 가능한 PayPal에 대한 창의적 브리프를 기반으로 모델에게 창의적 디렉터의 역할을 맡겨 새로운 디지털 광고 아이디어 세 가지를 생성하도록 할 것입니다. 우리는 아래의 예시 프롬프트를 사용하여 JSON 형식으로 잘 정리된 응답을 출력하고, 이를 API 호출이나 아마존 DynamoDB와 같은 데이터 저장소를 통해 신속하게 하부 서비스로 전송할 것입니다.

프롬프트 예시:

당신은 선두주자 광고 기관의 창의적 디렉터입니다. PayPal을 위한 다음 창의적 브리프를 기반으로 매력적인 온라인 디지털 광고 세 개를 개발하십시오. 응답에는 각 광고에 대한 고유한 광고 ID(UUID), 헤드라인, 광고 문구, 콜 투 액션, 그리고 이미지 설명을 포함하십시오. 다음 템플릿을 사용하여 응답 형식을 JSON 개체 시리즈로 만들어주십시오.

템플릿 여기에...''...

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

위의 프롬프트와 함께 창조적 브리프를 이미지 파일로 업로드해주세요. Foundation 모델은 이미지에서 텍스트를 추출하여 분석할 수 있습니다.

![Creative Brief](/assets/img/2024-05-17-MultimodalAdvertisingAnalysisandCreativeContentGenerationusingAnthropicClaude3onAmazonBedrock_8.png)

위는 스트림릿 애플리케이션에서 프롬프트 및 업로드된 파일이 나타나는 모습입니다.

![Streamlit](/assets/img/2024-05-17-MultimodalAdvertisingAnalysisandCreativeContentGenerationusingAnthropicClaude3onAmazonBedrock_9.png)

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

The model’s response is correctly formatted as a series of JSON objects.

![Image](/assets/img/2024-05-17-MultimodalAdvertisingAnalysisandCreativeContentGenerationusingAnthropicClaude3onAmazonBedrock_10.png)

The JSON-formatted output can be easily serialized and written to a downstream system without additional string manipulation.

```js
{
    "advertisements": [
        {
            "id": "9b1deb4d-3b7d-4bad-9bdd-2b0d7b3dcb6d",
            "headline": "자유롭게 수입해보세요, 언제든지",
            "ad_copy": "PayPal의 가상 지갑으로 재정적 독립을 누려보세요. 언제 어디서나 돈 받을 수 있어요.",
            "call_to_action": "지금 가입하기",
            "imagery": "광고에는 다양한 활동에 참여하는 다양한 청소년들이 나와 있습니다. 파트타임으로 일하거나 취미를 추구하거나 친구들과 어울려 시간을 보내는 모습들이 그려져, 그들의 다양한 삶을 상징합니다."
        },
        {
            "id": "3e67f83a-5f3e-4b4f-8653-b1c3c6b7147e",
            "headline": "당신의 돈, 당신이 관리하세요",
            "ad_copy": "PayPal로 재정을 자유롭게 통제하세요. 복잡함 없이 편리함만을 제공합니다.",
            "call_to_action": "앱 다운로드하기",
            "imagery": "광고에는 손에 스마트폰을 든 청소년의 손 모습이 그려져, 이동 중인 돈을 쉽게 관리하는 방법을 보여줍니다."
        },
        {
            "id": "c54f7d4d-b2d4-4b63-8b2b-d5a7b3c9e2f0",
            "headline": "수익 창출, 지출, 반복",
            "ad_copy": "용돈이든 파트타임으로 번 돈이든, PayPal이 돈의 움직임을 지원합니다.",
            "call_to_action": "지금 PayPal 가입하기",
            "imagery": "광고에는 한 손으로 돈을 받는 모습(수익을 나타냄)과 다른 손으로 지불하는 모습(지출을 나타냄)이 동적으로 그려져, PayPal로의 돈의 원활한 흐름을 상징합니다."
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

아래는 Amazon DynamoDB에 저장된 광고를 위한 새로운 창의적인 개념을 볼 수 있습니다. 그럼 광고 구성 시스템이 실제 디지털 광고를 만들 때 쉽게 읽을 수 있습니다.

![이미지](/assets/img/2024-05-17-MultimodalAdvertisingAnalysisandCreativeContentGenerationusingAnthropicClaude3onAmazonBedrock_11.png)

## 5: 새 광고를 위한 레이아웃 디자인

그래픽 디자이너의 역할을 가정하면, 이제 이 모델에게 이전에 생성된 헤드라인, 광고 복사본, 콜 투 액션 및 이미지 설명 중 하나를 기반으로 새로운 디지털 광고의 레이아웃을 디자인하도록 요청할 수 있습니다. 여기 예제 프롬프트가 있습니다:

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

당신은 선도적 광고 대행사의 능력 있는 그래픽 디자이너입니다. 다음 제목, 광고 복사본, 콜 투 액션 및 이미지 설명을 기반으로 매력적인 온라인 디지털 광고를 위한 디자인을 설명해주세요. 광고물은 세로 방향의 형식으로 디자인되어야 하며, 너비는 300픽셀, 높이는 600픽셀이어야 합니다. PayPal을 위한 창의적 브리프도 참고로 포함되어 있습니다.

창의적 개념이 여기에 있습니다…''…

다음은 응용프로그램에서 프롬프트와 업로드된 파일이 나타날 것입니다.

![이미지](/assets/img/2024-05-17-MultimodalAdvertisingAnalysisandCreativeContentGenerationusingAnthropicClaude3onAmazonBedrock_12.png)

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

알림에 따라 Foundation 모델은 제공된 헤드라인, 광고 복사, 호출-투-액션, 로고, 그리고 이미지 설명을 사용하여 레이아웃 세부 정보를 제공합니다. 이 접근 방식의 도전점은 여전히 이러한 쓰기된 레이아웃 설명을 실제 디지털 광고로 어떻게 전환할지입니다.

![이미지](/assets/img/2024-05-17-MultimodalAdvertisingAnalysisandCreativeContentGenerationusingAnthropicClaude3onAmazonBedrock_13.png)

## 6: 광고 컨셉에서 이미지 생성하기

저희의 새로운 광고 컨셉에는 이미지가 포함되어 있습니다. Amazon Bedrock와 최근 발표된 Amazon Titan Image Generator G1 foundation 모델을 사용하여, 이전 예시에서 생성된 이미지 설명에서 여러 이미지 변형을 만들 수 있습니다. 우리의 긍정적인 동기부여가 되겠습니다.

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

아래는 우리의 디지털 광고의 여러 변형을 생성하는 데 사용할 이미지 변형입니다.

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

## 7: 모두 함께하는 시간 — 프로그래밍적으로 광고 생성

우리는 이전 예제에서 생성된 디자인 컨셉과 이미지 변형을 사용하여 여러 광고 변형을 만들 수 있습니다. 우리는 Pillow 패키지를 사용하여 이 작업을 수행하는 Python 스크립트를 작성할 것입니다. Pillow은 Python Imaging Library (PIL)의 한 종류로, Python 인터프리터에 이미지 처리 기능을 추가합니다. 아래는 예시 프롬프트입니다:

당신은 선도적인 광고 대행사의 명문 Graphic Designer이자 Python 개발자입니다. 다음 헤드라인, 광고 복사본, 콜 투 액션 및 이미지 설명을 기반으로 매력적인 온라인 디지털 광고를 만들기 위해 PILLOW 패키지를 사용한 Python 프로그램을 작성하세요. 광고는 400픽셀의 폭과 600픽셀의 높이를 갖는 세로 형식으로 설계되어야 합니다. PayPal을 위한 크리에이티브 브리프가 참조용으로 포함되어 있습니다.

여기서 Kreative 컨셉이 나오네요...

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

다음은 응용 프로그램에서 프롬프트와 업로드된 파일이 보일 것입니다.

![Image](/assets/img/2024-05-17-MultimodalAdvertisingAnalysisandCreativeContentGenerationusingAnthropicClaude3onAmazonBedrock_17.png)

기반 모델은 디지털 광고를 만들기 위한 Python 스크립트를 작성할 수 있었습니다. 모델은 광고 요소를 구성하기 위해 Pillow 패키지를 올바르게 사용했으며 코드 옆에 주석도 추가되었습니다.

![Image](/assets/img/2024-05-17-MultimodalAdvertisingAnalysisandCreativeContentGenerationusingAnthropicClaude3onAmazonBedrock_18.png)

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

파이썬 스크립트는 모델의 응답에서 영감을 받아 Microsoft Visual Studio Code에서 수정되었습니다. 스크립트 내에서 생성된 이미지와 TrueType 글꼴의 기본 위치가 업데이트되었고, 레이아웃의 요소들이 약간 조정되어 전체 가독성과 영향력이 향상되었습니다. 마지막으로, 여러 이미지 변형으로부터 여러 광고 버전을 생성하도록 스크립트가 수정되었습니다. 개발 가속화를 위해 VS Code에서 Amazon CodeWhisperer를 사용했습니다. 최종 코드의 80% 이상은 기존 모델의 기반 응답에서 변경되지 않았습니다.

```js
from PIL import Image, ImageDraw, ImageFont

for idx, generated_image in enumerate(
    [
        "generated_images/paypal_generated_image_v1.png",
        "generated_images/paypal_generated_image_v2.png",
        "generated_images/paypal_generated_image_v3.png",
        "generated_images/paypal_generated_image_v4.png",
        "generated_images/paypal_generated_image_v5.png",
        "generated_images/paypal_generated_image_v6.png",    ]
):
    # 원하는 크기로 새 이미지 생성
    width, height = 400, 500
    img = Image.new("RGB", (width, height), color="white")
    draw = ImageDraw.Draw(img)

    # 글꼴 로드
    headline_font = ImageFont.truetype(
        "/Users/garystafford/Library/Fonts/Montserrat-Bold.ttf", 36
    )
    copy_font = ImageFont.truetype(
        "/Users/garystafford/Library/Fonts/Montserrat-Regular.ttf", 18
    )
    cta_font = ImageFont.truetype(
        "/Users/garystafford/Library/Fonts/Montserrat-SemiBold.ttf", 20
    )

    # 헤드라인
    headline = "Your Money,\nYour Control"

    headline_width, headline_height = draw.textbbox((0, 0), headline, font=headline_font)[2:]
    headline_x = (width - headline_width) / 2
    headline_y = 20
    draw.text((headline_x, headline_y), headline, font=headline_font, fill="#0C9C00", align="center")

    # 광고 복사본
    ad_copy = (
        "Take charge of your finances with PayPal.\nNo complexities, just convenience."
    )
    copy_width, copy_height = draw.textbbox((0, 0), ad_copy, font=copy_font)[2:]
    copy_x = (width - copy_width) / 2
    copy_y = headline_y + headline_height + 20
    draw.text((copy_x, copy_y), ad_copy, font=copy_font, fill="#000000", align="center")

    # 콜 투 액션
    cta = "Download the App"
    cta_width, cta_height = draw.textbbox((0, 0), cta, font=cta_font)[2:]
    cta_x = (width - cta_width) / 2
    cta_y = height - cta_height - 30
    draw.rectangle(
        (cta_x - 10, cta_y - 10, cta_x + cta_width + 10, cta_y + cta_height + 10),
        fill="#0C9C00",
        outline="#0C9C00",
    )
    draw.text((cta_x, cta_y), cta, font=cta_font, fill="white")

    # 이미지
    image = Image.open(generated_image)
    image = image.resize((400, 240))
    image_x = (width - image.width) / 2
    image_y = copy_y + copy_height + 20
    img.paste(image, (int(image_x), int(image_y)))

    # 전체 이미지 경계선
    draw.rectangle(
        [0, 0, width - 1, height - 1], outline="#999999", width=1
    )

    # 이미지 저장
    img.save(f"generated_ads/paypal_generated_ad_v1_{idx + 1}.png", format="PNG")
```

이전 예제에서 생성된 창의적인 개념과 이미지를 사용하여, 이 파이썬 스크립트를 사용하여 몇 초만에 생성된 수십 개의 디지털 광고 중 일부를 확인할 수 있습니다.

![2024-05-17-MultimodalAdvertisingAnalysisandCreativeContentGenerationusingAnthropicClaude3onAmazonBedrock_19.png](/assets/img/2024-05-17-MultimodalAdvertisingAnalysisandCreativeContentGenerationusingAnthropicClaude3onAmazonBedrock_19.png)

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

광고 결과물은 다양한 이미지, 헤드라인, 광고 복사본, 콜 투 액션 및 색상 팔레트를 사용합니다.

![이미지](/assets/img/2024-05-17-MultimodalAdvertisingAnalysisandCreativeContentGenerationusingAnthropicClaude3onAmazonBedrock_20.png)

## 8: 결과 분석

마지막으로, 우리는 다시 한 번 Claude 3 패밀리의 기본 모델을 사용하여 표 데이터를 분석할 수 있습니다. 예를 들어, 광고 예산과 에이전시 고객의 판매 내역이 포함된 스프레드시트가 있다면, 모델에 다양한 계산을 수행하도록 요청할 수 있습니다. 모델은 확실히 답변을 제공할 것이지만, 해당 답변이 정확한 가능성은 매우 낮습니다. 모델은 표 데이터에서 수학적 계산을 신뢰할 수 없지만, 선택한 언어로 컴퓨터 프로그램을 작성해 정확하게 계산하도록 요청할 수 있습니다. 여기 몇 가지 예시 문구입니다:

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

- 광고 예산 및 판매 CSV 파일을 기반으로 한 Python 프로그램을 작성하여 총 매출을 계산해보세요. Pandas 패키지를 사용해보세요.
- 광고 예산 및 판매 CSV 파일을 기반으로 한 Java 프로그램을 작성하여 총 매출을 계산해보세요.
- 광고 예산 및 판매 CSV 파일을 기반으로 한 Node.js 프로그램을 작성하여 TV, 라디오, 신문 광고 예산의 총 합계를 계산해보세요. csv-parser 패키지를 사용해보세요.
- 광고 예산 및 판매 CSV 파일을 기반으로 한 Python 프로그램을 작성하여 ID 100의 TV, 라디오, 신문 광고 예산의 총 합계 대비 매출의 비율을 계산해보세요. Pandas 패키지를 사용해보세요.

기본 모델은 요청된 Pandas 패키지를 사용하여 계산 작업을 수행하는 Python 스크립트를 성공적으로 작성했습니다. 해당 Python 스크립트는 정확한 답을 계산했습니다.

![이미지](/assets/img/2024-05-17-MultimodalAdvertisingAnalysisandCreativeContentGenerationusingAnthropicClaude3onAmazonBedrock_21.png)

# 결론

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

이 게시물에서는 진화하는 두 가지 생성적 AI 아키텍처 패턴을 살펴보았습니다: 하나는 기존의 창의적 콘텐츠에서 통찰을 얻는 데 사용되는 생성적 AI를 활용한 것이고, 다른 하나는 해당 통찰과 관련 데이터를 사용하여 새로운 창의적 개념을 개발하는 데 사용되는 생성적 AI를 사용한 것입니다. 우리는 생성적 AI를 활용하여 기존 광고를 분해하고 분석하고, 몇 가지 Python 코드와 함께 새로운 창의적 광고 개념을 작성했습니다.

Medium 회원이 아니고 나와 같은 작가들을 지원하고 싶다면 여기에서 가입하세요: https://garystafford.medium.com/membership.

이 블로그는 제 의견을 대표하며 제 고용주인 Amazon Web Services (AWS)의 의견을 대변하지 않습니다. 모든 제품 이름, 이미지, 로고 및 브랜드는 각 소유자의 재산입니다.
