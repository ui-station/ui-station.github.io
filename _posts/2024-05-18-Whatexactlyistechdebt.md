---
title: "기술 부채Tech Debt란 정확히 무엇인가요"
description: ""
coverImage: "/assets/img/2024-05-18-Whatexactlyistechdebt_0.png"
date: 2024-05-18 15:51
ogImage:
  url: /assets/img/2024-05-18-Whatexactlyistechdebt_0.png
tag: Tech
originalTitle: "What exactly is “tech debt”?"
link: "https://medium.com/atomic-engineer/what-exactly-is-tech-debt-45750ac27039"
---

저는 팀들이 기술 부채에 대해 이야기하는 것을 많이 듣고 온 경험이 있어요. 몇몇 팀은 이를 어떻게 관리해야 하는지 이해하고 있었고, 다른 팀 몇몇은 이로 인해 손실을 겪었어요. 그리고 어떤 기업은 실제로 처리되지 않은 기술 부채로 크게 실패했었죠.

![technical debt](/assets/img/2024-05-18-Whatexactlyistechdebt_0.png)

그렇다면 기술 부채란 무엇일까요?

부채 비유는 관련이 있는 것이에요. 처음으로 이 비유를 만든 엔지니어가 더 설명했던 내용에 대해 알아보죠.

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

기술적 부채가 발생하는 방법에 대해 언급해볼게요. 그 전에 기술적 부채의 원인과 성격에 대한 일반적인 오해를 이해하는 것이 중요합니다.

# 신화 1: "기술적 부채" == 나쁜 코드

어째서 "나쁜 코드"일까요? 좋은 코드는 아마 깔끔한 코드일 것이고, 그것은 미래에 특정 결정을 내리도록 강요하지 않는 코드라고 할 수 있습니다. 옵션을 남겨두는 코드라고 할 수 있겠네요. 나쁜 코드는 옵션을 남겨두지 않고, 그렇지 않았을 것 같은 구현 제약을 강요합니다.

"나쁜 코드"를 "나쁜 개발자"에서 거의 볼 수 없어요. 적어도 프로젝트의 제품에서는요. (그것이 코드 리뷰가 필요한 이유죠.) 내가 보는 "나쁜 코드" 대부분은 제약 조건 하에서 일하는 좋은 개발자에서 나옵니다.

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

## 신화 2: 기술 부채가 틀렸다

기술 부채는 재정적 부채와 마찬가지로 "틀린" 게 아닙니다. 아니요, 이상적이지는 않습니다. 필요한 것이 모든 현금이라면 좋겠지만, 제품을 만들 때 유효한 도구입니다.

## 신화 3: 만드는 순간, 무료다

이 오해는 소프트웨어가 건설과 같다는 믿음에서 비롯된 것 같아요. 건설에서 (및 이 비유에서) 작업은 순서대로 발생합니다:

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

- 건축가는 건물을 설계하고 계획을 제작하여 전달합니다.
- 작업자들은 기초를 다지고 벽을 세우며 전기 및 배관을 설치하고 가구를 추가합니다.
- 입주자들이 들어와 행복한 삶을 삽니다. 문제가 발생하면 유지 보수팀에 연락할 겁니다.

이건 이해하기 쉬운 비유예요. 하지만 소프트웨어에는 적용되지 않아요.

잘못된 건설 비유에서는 대부분의 비용이 처음에 발생합니다. 유지 보수 비용은 대개 이름만 언급하거나 우연히 한 번 발생한 것으로 간주됩니다.

정원 관리 비유에서, 기능을 구축하는 것은 (작물을 심는 것과 같이) 장기적인 작업의 단계 중 하나에 불과합니다. 정원이 커질수록 더 많은 유지 보수가 필요합니다. 이것은 레이아웃을 변경하기 위해 다시 심기(refactoring), 정원을 확장하고 새로운 작물을 심는 작업(새로운 기능 추가 - 이 또한 유지 보수가 필요합니다) 외의 작업입니다.

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

소프트웨어 총 비용의 90%는 유지보수와 관련이 있습니다. 몇 년 동안 이 지표를 알고 있었지만, 그것에 대해 생각하면 여전히 놀라워요.

# 어떻게 이 꼴을 타게 되었을까요?

하지만 정작 기술 부채는 어디서 유래하며, 피할 수 있을까요?

기술 부채의 또 다른 정의는 아마 이럴 것입니다: 현재 프로젝트의 위치와 우리가 그 동안 얻은 지식을 토대로 프로젝트를 깨끗한 상태에서 시작했다면 어디에 있을지의 차이입니다.

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

기술 부채는 두 가지 소스에서 나옵니다:

- 트레이드오프에 대한 결정(무모한 대책 vs 신중한 대책)
- 디자인 및 구현에 대한 결정을 내릴 때의 지식(또는 그 부족)

이상적으로는 제약 조건을 충족시키기 위해 타협하지 않고 충분한 지식을 바탕으로 결정을 내리는 것이 좋습니다. 하지만 현실적으로는 전체 프로그램을 어떻게 구현해야 하는지 완전히 이해하지 못한 채 프로젝트를 시작할 것입니다. 또한 이해관계자의 마감 기한이나 비용 제한과 같은 제약 조건을 바탕으로 트레이드오프를 해야할 필요가 있습니다.

기술 부채를 피하는 방법에 대한 교훈이 여기에 있습니다. 다음 게시물에서 자세히 다루겠습니다. 하지만 "기술 부채를 피할 수 있을까?"에 대한 간단한 답은 "네, 하지만 부채는 적이 아닌 도구입니다."

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

# 기술적 부채 측정

"얼마나 많은 기술 부채가 있는가?" 모든 기술 부채가 있다고 말할 수 있을까요? 이렇게 추상적인 개념을 정량화할 수 있을까요?

음,아뇨. 당신은 기술 부채를 신뢰할 수 없습니다. 엔지니어에게 특정 기능을 구현하거나 제품의 특정 부분을 수정할 것을 요청하면, 그들은 아마도 기술 부채를 갚을 필요가 있는 견적을 제시해야 할 것입니다. 그러나 시간이 흘러가면서 이것을 추적할 수는 없습니다.

기술 부채와 높은 상관 관계를 가지는 다른 측정 항목이 있습니다. (a) 정량화 가능하며, (b) 기술적 부채와 높은 상관 관계를 가지는 요소가 있습니다: 유지보수 부하.

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

유지보수 부담은 프로젝트의 연령과 구축 방법에 따라 달라집니다. 이는 지속적으로 개발자의 노력으로 측정됩니다.

제가 확실하게 말하고 싶은 것은, 유지보수 부담 ≠ 기술 부채입니다. 그러나 시스템 내에 얼마나 많은 기술 부채가 존재하는지를 나타내는 좋은 근사치 메트릭스입니다. 시스템이 붕괴되지 않도록 유지하기 위해 엔지니어를 추가로 필요로 한다면 기술 부채가 많은 것일 수 있습니다. 12명의 사람으로 10억 달러 가치의 회사를 창업할 수 있다면, 기술 부채를 잘 다루고 있는 것일 겁니다.

많은 것들처럼, 제 생각 중 많은 부분은 다른 우수한 산업 지도자들에 의해 영감을 받았습니다. 이 게시물은 특히 Mozilla의 스태프 엔지니어인 Chelsea Troy와 Meta의 CTO인 Andrew Bosworth에 의해 영감을 받았습니다. (Mozilla의 블로그에는 엔지니어들을 위한 훌륭한 자료가 가득합니다.)
