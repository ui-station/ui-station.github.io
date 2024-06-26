---
title: "왜 당신의 마이크로서비스 아키텍처가 아마도 지나치게 복잡한 지에 대해 그리고 어떻게 다시 단일체를 사랑하게 되었는지 알아보겠습니다"
description: ""
coverImage: "/assets/img/2024-06-19-WhyYourMicroservicesArchitectureisProbablyOverkillAndHowILearnedtoLovetheMonolithAgain_0.png"
date: 2024-06-19 12:09
ogImage:
  url: /assets/img/2024-06-19-WhyYourMicroservicesArchitectureisProbablyOverkillAndHowILearnedtoLovetheMonolithAgain_0.png
tag: Tech
originalTitle: "Why Your Microservices Architecture is Probably Overkill (And How I Learned to Love the Monolith Again)"
link: "https://medium.com/@PurpleGreenLemon/why-your-microservices-architecture-is-probably-overkill-and-how-i-learned-to-love-the-monolith-dfdb93b0511c"
---

작은 독립적인 서비스들이 각각 완벽하게 자신의 일을 하는 것, 개발자의 낙원 같지 않아요? 하지만 알아요? 가끔, 이건 정말 엄청난 고통일 수 있어요.

저는 이 허세에 홀렸었어요.

우리는 새 시스템을 구축하고 있었는데, '올바른' 방식으로 하겠다고 다짐했어요. 익숙한 거대한 단일체는 밖으로, 작은 마이크로서비스 떼가 들어왔어요. 처음에는 놀랍게 느껴졌어요 — 너무 깨끗하고, 너무 모듈화됐잖아요!

하지만 현실이 닥쳤어요.

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

결과는, 복잡성이 사라지지 않았습니다; 단지 변했습니다. 이것이 마이크로서비스가 당신의 영혼을 죽일 수 있는 이유입니다:

# 수다스러운 캐시 문제

이전에는 동일한 코드베이스에서 함수를 호출할 수 있었던 것을 기억하시나요? 이제 여러분의 절반 서비스는 네트워크 상에서 수다 떨고 있습니다. 그 중 하나가 성을 내면? 그 난장판을 디버깅하는 데는 행운이 필요할 겁니다.

한 번은 간단했던 함수 호출이 교차 서비스 요청의 끝없는 사가로 변모하고 있습니다. 지연과 관련된 머리 아픔, 네트워크 어딘가에서 뭔가가 고장날까 두려워하는 늘스런 공포의 상태로 변해가고 있습니다.

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

일렇게 REST API와 메시지 큐를 사용하여 처리할 수 있을 것이라 생각할 수도 있지만, 사실은 그 소중한 밀리초가 누적됩니다. 작은 데이터 조각을 가져와야 하나요? 그것은 네트워크 호출입니다. 간단한 작업을 처리해야 하나요? 또 다른 호출이 필요합니다. 여러분의 시스템은 실제 일을 하는 대신 수다에 더 많은 시간을 낭비합니다.

그런 다음, 불가피한 일이 벌어집니다. 여러분의 서비스 중 하나가 오동작하면 네트워크 타임아웃을 발생시키거나 엉망으로 된 데이터를 뱉어 냅니다. 그 속을 해체하는 과정을 즐기세요. 분산 디버깅은 분실된 양말을 찾는 것처럼 쉽지 않습니다. 각 네트워크 호프는 또 다른 용의자, 여러분의 비통의 근원이 될 수 있는 복잡성의 또 다른 층입니다.

# 배포 지옥

코드를 배포했을 때 직업을 바꾸어 야만한 일이 되려고 하거나 라마 농부가 되고 싶어졌던 기억을 여전히 갖고 계시나요? 네, 그런 날들은 이미 멀리 떠났습니다. 마이크로서비스로, 관리 가능한 프로세스였던 것이 불길한 기분을 내뿜는 다두와 여러 머리를 지닌 괴물로 변모했습니다.

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

갑자기 어플리케이션 하나가 아니라 십여 개의 작은 괴물을 다루게 되었네요. 각각이 자체 빌드 프로세스, 테스트 슈트 및 신중한 조율이 필요해요. CI/CD 파이프라인은 이렇게 엄청난, 꼬인 루브 골드버그 장치가 되어 새롭고 흥미로운 실패로 매번 고장나는 것처럼 보여요. 잘못된 구성 하나, 맞지 않는 종속성 하나로 모든 것이 터져버릴 수 있어요. 배포 오류와 싸우면서 실제 기능 개발은 멈춰있게 되네요. 코드 복잡성을 운영 복잡성과 바꾸었군요.

# 관찰성 부담

과거 몇몇 전략적으로 배치된 로그 라인으로 무엇이 문제인지 알 수 있던 시절을 기억하시나요? 마이크로서비스로, 그런 시기는 이미 멀리 사라진 기억이 됐어요. 이제는 시스템을 이해하는 데 상당한 투자가 필요할 거예요.

서비스 사이를 건너는 요청을 추적하려면 분산 추적 솔루션이 필요할 건데요. 서비스가 생성하는 로그의 해일을 해석하려면 로그 집계 도구가 기다리고 있어요. 그리고 멋진 대시보드와 경보 시스템을 잊지 마세요.

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

갑자기 예산과 엔지니어링 시간이 시스템이 제대로 작동하는지 확인하는 데 사용되는 것으로 바뀌었네요. 모순적인 점은, 이 모든 복잡성을 더한 결과물로 인해 문제의 근본 원인을 찾기가 이전보다 더 어려워졌다는 느낌을 자주 받습니다. 당신은 가시성의 환상에 큰 대가를 지불하고 있는 것 같습니다.

# 그들은 그리 독립적이지 않아요

마이크로서비스의 전체 약속은 느슨하게 결합되고 서로 교환 가능한 조각들의 아름다운 비전인데, 사실은 종종 많은 허세였던 것으로 밝혀졌어요. 실제로 "독립적"인 서비스들이 의외로 얽혀있는 방식으로 복잡해 집니다.

한 서비스의 API를 조정하면 어떤 결과도 없을 것이라고 생각하시나요? 한 번 더 생각해보세요. 숨겨진 가정, 문서화되지 않은 의존성, 그리고 행동의 미묘한 변화들이 불량한 음식 중독과 같이 시스템 전반을 퍼져나갈 수 있습니다. 당신이 알게 모르게, 여러 팀에 걸쳐 다시 작업에 뛰어들어서 왜 이러한 열광을 사들인 걸까 궁금해할 수도 있어요. 민첩성에 대한 약속에 대해 말할 수 없다 — 이제 당신은 아무것도 바꾸기를 두려워할 정도로 두려워하게 되었습니다.

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

# 단일체만큼 좋은 경우도 있어요

잠시 동안 독단적인 견해를 내려 놓아 보죠. 구성 요소들이 본질적으로 연결되어 있는 작은 프로젝트, 팀 또는 시스템의 경우, 단일체는 생명보호병이 될 수도 있어요. 이는 단순함을 추구하여 유행성을 포기하는 것과도 같아요.

생각해 보세요: 네트워크 지연 문제가 없고, 디버깅이 간단하며 울고 싶지 않게 배포할 수 있는 장점이 있어요. 확실히 성장하면 조금 엉망일 수 있지만, 코드베이스 내 신중한 모듈화로 그것을 완화할 수 있어요. 그리고 솔직히 말하자면, 잘못 설계된 마이크로서비스 시스템이 보잘것없이 퍼져나가는 모습은 그다지 즐겁지 않아요.

나쁜 말 안 하겠습니다. 마이크로서비스가 나쁜 것은 아니에요. 그것들은 대규모 시스템에서 빛을 발하거나, 구성 요소 간에 완전한 독립성이 필요한 경우에 유용해요. 하지만 맹목적으로 트렌드를 따라가서 모든 것을 마이크로서비스로 분리하는 것은 정말로 무분별한 복잡성과 개발자의 탈진으로 이어지는 결과를 초래할 수도 있어요.

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

가끔 "옛날 방식"의 단일체가 더 현명한 선택일 수 있습니다. 인프라와 무쟁하기보다는 가치 전달에 집중할 수 있게 해줍니다.

나는 반드시 마이크로서비스를 비판하는 것은 아닙니다. 특히 확장성이 중요한 대규모 복잡한 시스템에서 유용할 수 있습니다. 그러나 기술 세계는 항상 최신 유행을 지나치게 홍보하여 검증된 솔루션을 낡은 것으로 여기게 만드는 나쁜 버릇이 있습니다.

쿨한 것을 하고 있는 친구들이 그렇게 하고 있다고 해서 우리도 모든 애플리케이션을 마이크로서비스로 나누는 것을 맹목적으로 따라갈 필요는 없습니다. 한 발 물러나서 프로젝트의 필요를 정직하게 평가하고, 복잡성 대비가 정말 그만한지 고려해보세요. 잘 설계된 단일체가 작업 부담이 적게 더해도 동일한 기능을 제공할 수 있는 경우에는 왜 그것을 선택하지 않을까요?

마이크로서비스가 디폴트일 필요는 없습니다; 이는 의도적인 결정이어야 합니다. 독단적인 사고를 버리고 더 실용적인 접근 방식을 채택합시다 — 적합한 도구가 승리하는 아키텍처. 가장 유행하는 것이 아니더라도. 아니, 혹시 서면이 잘 갖춰진 단일체의 간결함에 대한 새로운 감사함을 발견할지도 모릅니다.
