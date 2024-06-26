---
title: "GA4 BigQuery 원시 데이터 이해하기 컨센트 모드 활성화된 상태"
description: ""
coverImage: "/assets/img/2024-05-20-UnderstandtheGA4BigQueryrawdatawithconsentmodeactivated_0.png"
date: 2024-05-20 21:34
ogImage:
  url: /assets/img/2024-05-20-UnderstandtheGA4BigQueryrawdatawithconsentmodeactivated_0.png
tag: Tech
originalTitle: "Understand the GA4 BigQuery raw data with consent mode activated"
link: "https://medium.com/@guillaumewagner/understand-the-ga4-bigquery-raw-data-with-consent-mode-activated-99fdf844fa18"
---

GA4 동의 모드가 BigQuery에 미치는 영향은 때로는 평가하기 어려울 수 있습니다. 반면 “쿠키 없는 핑”을 통해 수집된 데이터는 설계상 매우 복잡하여 결합하거나 분석하기 어렵습니다.

일부 또는 전체적으로 무시해야 할까요? 받아들여야 할까요? 아니면 모든 것을 파괴하러 가야 할까요? 이 문제를 해결하는 데 도움을 드리고, 두뇌가 녹아내리는 것을 방지하기 위한 잠재적인 솔루션을 제안해 드리겠습니다.

![이미지](https://miro.medium.com/v2/resize:fit:960/1*Bq0dEk71wL82uIknalortw.gif)

# 왜 이것에 대해 이렇게 많은 소란이 있는 걸까요?

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

개인 정보 보호 규정이 적용되는 국가에서 활동하고 계신 경우, 데이터 수집 전에 사용자의 동의를 요청하는 것이 법적으로 의무화됩니다. GA4는 이러한 데이터 손실에 강하게 영향을 받습니다. 그래서 그들은 "쿠키 없는 핑"을 소개했습니다. 이제 사용자들이 동의를 제공하지 않더라도 당신은 그들의 데이터를 수집할 수 있습니다. 사용자가 동의를 제공하지 않는 경우, 익명 이벤트를 수집하여 GA4가 웹사이트 활동의 현실을 모형화하는 데 도움을 줄 수 있습니다. 이로 인한 데이터에 대한 영향 몇 가지를 살펴보겠습니다:

# 사용자 의사 ID가 없음

▶ 해당 이벤트에는 "user_pseudo_id"가 없을 것입니다. 일반적으로 쿠키를 기반으로 사용자를 계산하는 데 사용하는 것입니다. 만약 사용자 ID가 있다면, 수집하지 않도록 규칙을 마련해야 할 것입니다. 이 부분은 귀하의 법률팀이 결정하도록 하겠습니다.

![이미지](/assets/img/2024-05-20-UnderstandtheGA4BigQueryrawdatawithconsentmodeactivated_0.png)

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

# 세션 ID가 없음

▶ 해당 이벤트에는 ga_session_id 매개변수가 포함되어 있지 않습니다. 참 좋죠?

![이미지](/assets/img/2024-05-20-UnderstandtheGA4BigQueryrawdatawithconsentmodeactivated_1.png)

# 신뢰할 수 없는 session_start 및 first_visit

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

▶ 각 페이지 다시로드는 새로운 first_visit 및 session_start를 발생시킵니다. 이는 해당 이벤트로 새 사용자 또는 세션을 계산할 수 없음을 의미합니다.

![이미지](/assets/img/2024-05-20-UnderstandtheGA4BigQueryrawdatawithconsentmodeactivated_2.png)

# 싱글 페이지 앱의 경우

▶ ... Singe Page Apps의 경우, 기술적으로 페이지 컨텍스트가 페이지를 변경할 때 다시로드되지 않습니다. 그러나 사람들이 수동으로 페이지를 새로 고치거나 "새 탭" 내부 링크를 통해 이동하는 경우 다시로드될 수 있습니다. TLDR: 여전히 신뢰하기 어렵고, "비슷하지만 다름"입니다.

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

![이미지](/assets/img/2024-05-20-UnderstandtheGA4BigQueryrawdatawithconsentmodeactivated_3.png)

# 방문 지표를 비교할 때 무슨 일이 벌어질까요?

같은 기간 동안 두 시스템에서 사용하는 간단한 쿼리를 아래에서 확인할 수 있습니다. 사용자 ID를 수집하지 않는 속성에 대해 GA4는 세션, 사용자 및 신규 사용자를 추정하며, 분명히 원시 데이터만 사용하지 않습니다.

![이미지](/assets/img/2024-05-20-UnderstandtheGA4BigQueryrawdatawithconsentmodeactivated_4.png)

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

아래처럼 해드릴게요:

<img src="/assets/img/2024-05-20-UnderstandtheGA4BigQueryrawdatawithconsentmodeactivated_5.png" />

GA4이 이벤트에 기반한 메트릭(new_users 및 세션)을 계산할 때, 상황이 약간 복잡해집니다. 이벤트 수를 알고 있는데도 불구하고 실제 데이터가 아니라는 것을 인지합니다. 데이터베이스의 고유 사용자 키에 기반한 메트릭인 경우, 데이터가 부족하다는 점을 감안해 그 수치를 팽창시킵니다.

# 특정 이벤트를 비교하면 무슨 일이 벌어질까요?

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

# 문제 해결 방법은 무엇인가요?

빅쿼리(BigQuery)에서 이벤트와 방문 메트릭을 조심스럽게 사용하는 벙법
첫 번째 황금 규칙은 아마도 빅쿼리에서 사용자와 세션을 생각하는 것을 그만두는 것입니다. 그렇지만 한번 그렇게 생각한다면 조심해야 하고 절대 숫자가 아닌 추세만 보도록 해야 합니다. 하지만 추세조차도 조심해서 봐야 하는데, 쿠키 동의율의 변화가 추세에 영향을 줄 수 있기 때문입니다. 예를 들어, 배너에서 팝업으로 전환하면 동의율이 확실히 높아져서 더 많은 방문자가 표시될 수 있습니다.

보고서에서 동의하지 않은 사람들 제외하기
두 번째 가능성은 이벤트 수집에 동의하는 사람들만 분석하는 것입니다. 이 것은 구현하기 가장 쉬운 해결책입니다("where user_pseudo_id is not null"을 사용하면 됨). 그러나 동의율이 대중 중에 통계적으로 동일하지 않다는 점을 이해해 주세요. 연구에 따르면 브랜드/웹사이트를 이미 알고 있는 사람들의 경우 동의율이 더 높아진다는 것을 보여주며 새로운 편향을 고려해야 합니다. 게다가 최종 사용자들이 "GA4 인터페이스와 동일한 숫자를 얻는 것"에 대해 걱정한다면(해석해서는 안 되지만, 믿어줘요 모두에게 쉽게 이해되지 않는 것입니다), 이 기술만으로는 작동하지 않을 것입니다.

일부 메트릭을 위해 API 사용하기
사용자 흐름 분석이나 대부분의 전자상거래 보고서에는 원시 데이터를 계속 사용하고, 트래픽 획득과 같은 보고서를 추출하기 위해 API를 사용합니다. 이것은 대개 수치가 거의 (아래 참조) 인터페이스와 일치한다는 것으로 사업 사용자들에게 많은 신뢰감을 줍니다. Airbyte나 Meltano과 같은 도구들은 매우 쉽게 이 작업을 수행하지만 유지할 파이프라인을 추가해야 합니다... 누가 새로운 파이프라인을 유지하는 것을 좋아할까요?

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

또한 API(및 인터페이스)에는 매우 기뻐할만한 단점이 하나 있습니다. 마음이 편안해지도록 알려드리기 전에: 보고서의 모든 라인 값의 합계가 보고서가 표시하는 총합보다 항상 높습니다. 네, 이것은 끔찍한 문제입니다.

![이미지](/assets/img/2024-05-20-UnderstandtheGA4BigQueryrawdatawithconsentmodeactivated_7.png)

더 알고 싶다면 저에게 연락해주세요. 결국 이에 대해 기사를 작성할 예정입니다.

BigQuery에서 GA4 모델 복제
당신의 솔루션 중 가장 흥미로우면서 복잡한 부분입니다. 그들이 하는 것을 왜 당신도 할 수 없을까요? 데이터도 있으니 그렇게 복잡하지 않을 것입니다, 맞죠?

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

저도 아직 시도해보지 않았어요, 하지만 그렇다고 둘려쌓인 일을 두려워하거나 그만큼 열망하지도 않아요. 이미 이야기를 알고 있어요: 합리적인 시간 내에 흥미로운 첫 결과물을 얻으면 흥분되고 자신감을 얻게 되면서 거의 사용할 수 있을 거라고 느끼지만, 실제로 제품으로 출시하기 위한 마지막 20%의 노력은 사실 프로젝트 시간의 80%에 해당한다는 것을 알아요. 악마는 세부 사항에 있다는 거죠.

![이미지](/assets/img/2024-05-20-UnderstandtheGA4BigQueryrawdatawithconsentmodeactivated_8.png)

또 하나: 모델에서 Google이 내 소유 데이터만 사용하는지 정말로 알 수 없어요. 그들은 지구상의 대다수 웹사이트 데이터에 접근할 수 있어요. 아마 그들은 자신의 모델을 훈련시키는 데 사용했을지도 몰라요? 그건 블랙박스죠.

요즘 LinkedIn 게시물에서 POC(Concept of Proof)를 본 적이 있는데, 기초 통계 계산을 사용하여 그런 지표를 모델링한다는 내용이었어요. 예를 들어, 동의 데이터의 각 세션과 사용자에 대한 이벤트 수를 가져와서 비동의 데이터의 지표를 추론해요. 통계적으로 허용되려면, 동의한 방문자와 동의하지 않은 방문자가 같은 행동을 한다는 것을 확인해야 해요. 이미 논의한 바 있는데, 연구에 따르면 그들은 다르다는 걸 보여줬어요. 그래서 그 가치가 무엇인지 알기 어려워요 ¯\_(ツ)\_/¯

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

# 결론

![이미지](/assets/img/2024-05-20-UnderstandtheGA4BigQueryrawdatawithconsentmodeactivated_9.png)

이 문제에 대해 완벽한 해결책이 없다는 것은 실망스러울 수 있지만, 이 글은 해당 주제에 대한 제 경험에 대해 좋은 개요를 제공해줍니다.

본문에서 강조한 해결책들은 상황과 팀의 성숙도에 따라 모두 함께 사용하는 것이 좋습니다. 다시 한 번 강조하지만, 너무 깊게 생각하지 마세요. 웹 분석뿐입니다. 데이터를 그대로 받아들이고, 이 지식을 비즈니스 사용자와 공유하며, 분석을 진행할 때 편향을 고려하면 훨씬 더 효율적일 것입니다.
