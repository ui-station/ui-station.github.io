---
title: "AWS Rekognition은 어떻게 아이들을 지킴에 도움을 주나요"
description: ""
coverImage: "/assets/img/2024-05-27-HowAWSRekognitionHelpsProtectChildhood_0.png"
date: 2024-05-27 16:59
ogImage:
  url: /assets/img/2024-05-27-HowAWSRekognitionHelpsProtectChildhood_0.png
tag: Tech
originalTitle: "How AWS Rekognition Helps Protect Childhood"
link: "https://medium.com/aws-in-plain-english/how-aws-rekognition-helps-protect-childhood-2386eddda9d0"
---

## 어린이 안전을 혁신하다

![이미지](/assets/img/2024-05-27-HowAWSRekognitionHelpsProtectChildhood_0.png)

내 아들이 다니는 유치원에서 모든 것이 시작되었어요. 팬데믹 전날들, 외부 활동을 하고 사진을 찍어 SNS에 올리고 가족들에게 보내는 것이 흔했어요.

이 맥락에서, 데이터 보호법은 미성년자의 신원이 노출되지 않도록 현명하게 규정하고 있습니다. 이를 위해 가족/부모의 명시적 허락을 받지 않으면 이러한 이미지를 배포해서는 안 된다는 요구 사항을 비롯한 다양한 제약이 설정되어 있습니다.

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

이 모든 과정은 감정 표현 아이콘을 겹쳐 놓거나 잘라내거나 삭제하는 귀찮은 익명화 수동 프로세스로 이어집니다. 수동 프로세스이기 때문에 오류가 없고 규모에 비해 잘 작동하지 않습니다.

이것은 아이들의 얼굴을 찾아 숨기기 위해 자동화된 형태를 사용하여 그들의 익명성을 보장하고 교사들과 교육진들의 작업을 쉽게 만드는 아이디어로 이끈 것입니다. 지속 가능한 비즈니스 모델도 아울러 구체화할 수 있다면 한 발자국 더 나아갈 수 있겠죠.

# 창의적인 프로세스

이 아이디어와 함께, 나는 그것을 구현하기 시작했습니다: 간단한 웹 응용 프로그램과 서버 측 API. 나는 단순한 것을 준비했고, 준비가 끝난 후 도메인을 등록하고 처음 발표 전에 첫 번째 테스트 중에 이것이 다소 인위적인 단계임을 깨달았습니다.

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

사진을 찍고 공유하려면 대개 모바일 폰에서 하게되는데, 사진을 다듬기 위해 여러 애플리케이션을 거치고 싶지 않을 때가 있습니다 (적어도 대부분의 사람이 그러하지는 않지만), 아예 타사 웹사이트를 통해 지나치게 다듬는 것은 훨씬 더 그렇습니다. 이러한 까칠함은 애플리케이션을 받아들이기 어렵게 만들어, 다른 방법을 시도해보기로 결정했습니다: 텔레그램 봇.

기존에 이미 이뤄진 작업에 대한 많은 이점을 취할 수 있게 해주었기에, 리팩토링을 거쳐 봇을 발표하고 조금씩 사용자를 모았습니다. 개발 중이었을 때 4명의 분들께서 친절하게 테스트해 주셔서 그것이 저를 격려해 다양한 기능을 추가하게 이끌었습니다. 광고 없이 3개월 만에 400명의 사용자에 도달했습니다. 여러 분야에서 공유한 후, 총 1034명의 사용자가 있는 애플리케이션을 종료했습니다.

![AWSRekognitionHelpsProtectChildhood](/assets/img/2024-05-27-HowAWSRekognitionHelpsProtectChildhood_1.png)

## 아키텍처 단계

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

각 건축 설계의 근거가 아래에 자세히 나와 있습니다. 이것은 솔루션의 진화를 보여줍니다.

## 첫 번째 반복: 서버리스

콘텐츠 배포는 CloudFront와 S3를 사용하여 이루어졌으며, 로직은 람다에서 처리되어 GW API를 통해 액세스되었습니다. 비즈니스 로직은 Rekognition과 S3 호출을 통해 지원되었습니다. 모든 것이 예상대로 작동했지만, 이미지를 생성하는 프로세스에서 Lambda에서 많은 CPU/RAM을 필요로 했습니다.

![AWS Rekognition Helps Protect Childhood](/assets/img/2024-05-27-HowAWSRekognitionHelpsProtectChildhood_2.png)

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

## 두 번째 반복: EC2에서 IaaS

두 번째 버전은 우분투가 실행되는 EC2에 직접 배포되었습니다. 처음에는 PEM을 사용하고 나중에는 SSM을 사용했습니다. CodeCommit, Rekognition 및 DynamoDB에서 코드를 다운로드하는 역할이 있는 인스턴스였습니다.

![이미지](/assets/img/2024-05-27-HowAWSRekognitionHelpsProtectChildhood_3.png)

## 최종 후보 아키텍처 (실행되지 않음)

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

진화와 수요가 발생하는 경우 알맞은 버전은 필요 시 스케일 업할 수 있는 ALB가 있는 작은 Fargate Spot Fleet를 생성하는 것이었습니다. 이 버전은 예비적이며, 개발된 것은 없었으며, 폴링 대신 웹소켓을 사용하고 노드 밸런싱을 위해 외부 스토리지인 Redis에 세션 관리를 포함시키기 위해 코드를 리팩토링해야 했습니다.

![이미지](/assets/img/2024-05-27-HowAWSRekognitionHelpsProtectChildhood_4.png)

# 유용한 코드 조각

코드의 흥미로운 부분은 Rekognition 응답을 처리하는 섹션입니다:

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

그리고 그와 함께, 얼굴을 익명화하기 위해 가우시안 블러 효과를 적용하는 섹션을 변경하겠습니다:

마지막으로, 궁금해하시는 분들을 위해, 여기에는 봇의 상태 머신, 애플리케이션의 나머지 비즈니스 로직 및 API 호출을 처리하는 데 필요한 헬퍼가 포함된 완전한 코드 저장소가 있습니다.

# 배운 점

이 섹션은 예기치 않은 도전에 직면하거나 다양한 기술을 실험하며, 무엇보다도 사이드 프로젝트로 디지털 제품을 출시하려는 노력에서 배운 교훈을 공유하는 데 전념합니다.

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

- 프리미엄이나 기부 기반 모델을 구현하는 것은 쉬운 일이 아닙니다.
- 사람들은 스마트폰 상에서 봇과 상호 작용을 매우 잘합니다.
- 사용자 경험과 명확한 지침은 플랫폼과 상호 작용하는 데 중요합니다.
- 사용자 피드백은 반드시 필요하며, 소프트웨어를 테스트한 친구들로부터 얻은 일부 최고의 제안이 있었습니다.

읽어주셔서 감사합니다! 마음에 드셨으면 여기 다른 AWS 관련 기사들이 있어요!

# 쉽게 이해하는 AWS 관련 기사들 🚀

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

In Plain English 커뮤니티에 참여해 주셔서 감사합니다! 떠나시기 전에:

- 반드시 작가를 클랩하고 팔로우해 주세요! 👏️️
- 팔로우하기: X | LinkedIn | YouTube | Discord | 뉴스레터
- 다른 플랫폼 방문하기: Stackademic | CoFeed | Venture | Cubed
- PlainEnglish.io에서 더 많은 콘텐츠를 확인해 보세요.
