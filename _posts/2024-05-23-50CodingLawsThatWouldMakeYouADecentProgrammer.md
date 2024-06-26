---
title: "50가지 코딩 법칙이 적절한 프로그래머가 되도록 만들어줄 거예요"
description: ""
coverImage: "/assets/img/2024-05-23-50CodingLawsThatWouldMakeYouADecentProgrammer_0.png"
date: 2024-05-23 13:27
ogImage:
  url: /assets/img/2024-05-23-50CodingLawsThatWouldMakeYouADecentProgrammer_0.png
tag: Tech
originalTitle: "50 Coding Laws That Would Make You A Decent Programmer."
link: "https://medium.com/@alexobidiegwu/50-laws-of-best-practices-in-python-6942c7cafd56"
---

## 이 법칙을 따르지 않으면 해고당할 수 있습니다.

![이미지](/assets/img/2024-05-23-50CodingLawsThatWouldMakeYouADecentProgrammer_0.png)

수백 또는 아마 수천 가지의 파이썬 최고의 관행들이 있습니다. 누구에게 물어도 다소 다른 실천 방법을 얻을 수 있습니다.

인터넷은 모든 사람에게 의견 표명의 권리를 부여했습니다. 심지어 저도 말이죠. 하지만 이 기사에서는 암호화된 50가지 파이썬 최고의 관행을 살펴보겠습니다.

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

신이라도 조작할 수 없는 기술들이 있어요. 이러한 기술들은 전문가와 아마추어를 구분하며 다양한 프로그래밍 언어에도 적용할 수 있어요.

대부분의 파이썬 개발자들은 코드를 빠르게 테스트하거나 오류를 디버그할 곳이 필요해요. 제가 만든 python-fiddle.com 이라는 웹사이트를 이용하면 코드를 빠르게 테스트하고 AI/LLMs를 사용하여 가능한 오류의 해결책을 찾아줄 수 있어요.

만약 웹 스크래퍼를 만들거나 데이터를 분석하거나 암호화폐 관련 프로젝트를 개발하거나 기계 학습 모델을 만들거나 Django 또는 Flask 웹사이트를 만들거나 작업을 자동화하거나 SQL 관련 프로젝트 등이 필요하다면, 이 사람에게 메시지를 보내보세요.

## 법칙 1: 가능한 한 주석을 피하세요.

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

코멘트는 종종 사실과는 다른 내용을 전달할 수 있는 방식을 갖고 있어요. 코드가 실제로 무엇을 하는지가 아닌 다른 사람이 말하는대로 어떤 일을 하고 있는지를 읽는 사람의 마음을 벗어낼 수 있어요.

시간이 흐르고 코드가 업데이트되거나 변경될 때 이 문제가 매우 심각해질 수 있어요. 어느 순간, 코멘트가 거짓이 되고 이제 모든 사람들은 거짓을 통해 진실을 관찰해야 할 수도 있어요.

모든 비용을 피해야 하는 것이 코멘트에 대한 태도예요. 코멘트는 독자가 당신의 과거적인 생각을 상속받도록 강요해요. 함수나 클래스가 변경되면 대부분 코멘트는 함께 변경되지 않을 가능성이 높아요. 대부분, 코멘트는 독자가 앞으로 생각하도록 막을 수 있어요.

코멘트는 작성자가 명확한 클래스, 함수 또는 변수 이름을 제시하지 못했다는 것을 나타냅니다. 이는 프로그래머의 태도의 부족을 드러내고 팀에 그러한 태도를 상속받도록 강요해요.

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

주석을 가능한 한 피해야 합니다.

로우 14와 15에서는 언제 주석을 사용해야 하고 언제 사용하면 안 되는지 알 수 있습니다.

## 로우 2: 변수에 타입 속성을 이름으로 사용하지 마세요

가끔 특정 변수가 문자열인지 정수형인지를 명시하고 싶을 때가 있습니다. 따라서 일부 개발자는 변수를 다음과 같이 지정할 수 있습니다: name_of_variable_str 또는 name_of_variable_int.

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

변수가 문자열인 것이 직관적으로 이해되는 경우와 같이, 변수가 결코 int(정수) 타입이 될 수 없는 경우에는 이것이 상당히 중복될 수 있습니다.

그러나 변수 타입이 직관적이지 않은 경우에는 변수명을 지정할 때 타입을 명시하는 대신 타입 어노테이션을 사용하는 것이 가장 좋은 방법입니다.

name_of_variable:str = value 대신 name_of_variable_str = value를 사용하는 대신 이 방법을 사용하면 모두가 변수가 문자열인 것을 알 수 있으며 코드를 깔끔하고 간결하게 유지할 수 있습니다.

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

## 법률 3: 클래스 이름은 명사여야 합니다.

클래스 이름을 명사로 유지하는 것이 항상 최선의 실천 방법입니다.

이는 대부분 클래스 객체가 특징과 동작을 식별하거나 표현하는 데 사용되기 때문입니다. 어떻게 양이 어떤 특징(뿔)과 행동(주변 사람에게 고개를 끄덕이다)을 나타내듯이요.

이는 코드를 매우 가독성 있고 중복되지 않게 만듭니다. 예를 들어, Goat.get_horn_length() 대신 GetGoat.get_horn_length()을 사용하는 대신 Goat.get_horn_length()을 사용합니다.

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

## 법칙 4: 함수 이름은 동사여야 합니다

함수는 인접한 개발자가 수행하는 작업을 명확하게 이해할 수 있도록 동사로 가장 잘 명명되어야 합니다.

이는 주석이 필요 없어지게 하고 어떤 개발자든 원시 코드를 확인하지 않고도 정신적으로 개념화할 수 있도록 해줍니다.

## 법칙 5: 함수는 매개변수와 반환 형식을 명시해야 합니다

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

함수를 정의할 때는 항상 인수의 유형 및 함수의 결과가 반환하는 데이터 유형을 명시해야 합니다.

이렇게 하면 당신과 팀의 개발자들 모두 print 문을 계속 사용하지 않고도 예상되는 결과를 알 수 있게 됩니다.

![이미지1](/assets/img/2024-05-23-50CodingLawsThatWouldMakeYouADecentProgrammer_1.png)

![이미지2](/assets/img/2024-05-23-50CodingLawsThatWouldMakeYouADecentProgrammer_2.png)

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

## 법칙 6: 함수는 한 가지 기능만 수행해야 합니다

주니어 개발자들은 종종 이 규칙을 어기기 좋아합니다. 함수가 한 가지 기능만을 수행하는 것은 버그가 어디에 있는지 노출시키고 재사용성을 높이며, 함수 이름이 하는 일을 정확히 수행하도록 해줍니다.

다음과 같은 일을 하고 싶지 않을 것입니다...

![Img](/assets/img/2024-05-23-50CodingLawsThatWouldMakeYouADecentProgrammer_3.png)

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

주소가 유효한지 확인하고 확인 후 위도와 경도를 반환합니다. 이 함수는 두 가지 작업을 수행합니다. 주소가 유효한지 확인하고 해당 주소의 지리적 위치를 반환합니다.

다음은 더 나은 방법입니다.

![Better Way](/assets/img/2024-05-23-50CodingLawsThatWouldMakeYouADecentProgrammer_4.png)

위의 함수들은 하나의 일만을 하고 그 이상의 일을 하지 않습니다. 덜 간결해 보일 수 있지만 훨씬 간결하고 가독성이 높습니다.

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

함수의 "하나" 기능을 정확히 알아내는 것은 새로운 개발자들에게는 약간 어려울 수 있어요. 함수가 무엇을 수행해야 하는지를 매우 구체적으로 명시해야 해요.

보통은 함수 내에서 일부 작업을 추출하거나 그룹화하여 다른 하나의 함수로 만들 수 있다면, 아마도 함수가 한 가지 이상의 일을 수행하고 있는 것일지도 모르겠어요.

또 다른 방법은 함수가 여러 수준의 추상화를 갖는지 여부를 확인하는 것이에요...

## LAW 7: 함수는 동일한 추상화 수준에 있어야 해요

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

함수가 동일한 추상화 수준에 있을 때 언급하는 것은 함수가 단일하고 명확한 작업을 수행해야 한다는 아이디어를 가리킵니다. 해당 작업은 함수 전체에서 일관된 추상화 수준에 있어야 합니다.

다시 말해, 함수는 특정한 세부 사항이나 복잡성에 집중해야 하며, 모든 함수의 작업은 동일한 수준에서 작동해야 합니다.

![image](/assets/img/2024-05-23-50CodingLawsThatWouldMakeYouADecentProgrammer_5.png)

이 함수는 낮은 추상화 수준의 명령문을 가지고 있습니다. sum, len 등과 같은 것들이 포함되어 있습니다.

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

<img src="/assets/img/2024-05-23-50CodingLawsThatWouldMakeYouADecentProgrammer_6.png" />

예를 들어, 이 함수에는 여러 수준의 추상화가 있습니다. get_numbers()는 높은 수준의 추상화, 리스트 내포(list comprehension)는 중간 수준의 추상화이며 sum은 낮은 수준의 추상화입니다.

## 제8의 법칙: 함수와 인수는 형제 자매처럼

함수 이름은 매우 밀접하게 그 인수와 관련되어야 합니다. 함수 이름과 관련성이 없는 인수를 전달하는 것은 좋은 방법이 아닙니다.

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

아래와 같이 코드를 Markdown 형식으로 변경해보세요.

write(True)

write(name)

두 번째 예시가 함수가 정확히 무엇을 하는지 더 잘 설명하고 있어요. 이것을 읽는 사람에게 이름을 작성하고 있다는 사실이 명확해요.

첫 번째 예시는 두 번째 예시만큼 명시적이지 않아요. 추측을 하거나 함수 전체를 살펴봐야 할 수도 있어요.

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

## 법칙 9: 함수는 작아야 합니다

함수는 재사용 가능하도록 설계되었습니다. 그리고 함수가 커질수록 재사용 가능성이 낮아집니다. 이는 함수가 한 가지 일만 해야 하는 이유와 관련이 있습니다. 한 가지 일만 하면 함수가 작을 가능성이 높습니다.

## 법칙 10: 불필요한 단어 및 중복 단어 피하기

개발자가 변수나 함수의 의미를 더 명확하게 해주는 단어가 아닌 단어를 사용하는 시간도 있습니다. 이런 목록화:

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

![Image](/assets/img/2024-05-23-50CodingLawsThatWouldMakeYouADecentProgrammer_7.png)

함수의 구현에 대한 사전 지식이 없으면 이를 본 개발자는 어떤 함수를 사용해야 하는지 알 수 없습니다.

## LAW 11: 더러운 프로그래머가 되지 말라

시니어 개발자라면 그가 코드가 깨끗할 때에만 정신적으로 맑다고 말할 것입니다. 이것은 더러운 코드를 작성하는 것이 더러운 프로그래머를 만들기 때문입니다. 깨끗한 코드는 팀의 모든 이들에게 깨끗한 코드를 계속 작성하도록 장려합니다. 항상 깨끗한 코드를 작성하도록 노력해야 합니다.

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

하지만 깨끗한 코드란 무엇일까요? 깨끗한 코드는 잘 구조화되어 정리되어 있습니다.

깨끗한 코드는 버그를 숨기지 않습니다. 프로그래머가 버그가 숨을 수 있는 어떤 곳이든 드러내며, 완전한 리팩토링 없이 쉽게 수정할 수 있는 공간을 마련해줍니다.

## LAW 12: 개방폐쇄 원칙

개방폐쇄 원칙(Open Closed Principles, OCP)은 클래스, 메서드 또는 함수가 확장을 위해 열려 있지만 수정에는 닫혀 있어야 한다고 말합니다. 이는 정의된 모든 클래스, 메서드 또는 함수가 코드를 변경하지 않고 여러 인스턴스에 재사용하거나 확장할 수 있도록 만든다는 것을 의미합니다.

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

위 예제를 들어보겠습니다. address라는 클래스가 있다고 가정해 봅시다.

![address class](/assets/img/2024-05-23-50CodingLawsThatWouldMakeYouADecentProgrammer_8.png)

이것은 OCP를 준수하지 못한 것입니다. 새로운 국가가 추가될 때마다, 해당 국가를 보충하기 위해 새로운 if 문을 작성해야 합니다. 지금은 간단해 보일 수 있지만, 상상해 보세요. 100개 이상의 국가를 고려해야 한다면 어떻게 될까요?

여기서 OCP가 중요한 역할을 합니다.

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

![image](/assets/img/2024-05-23-50CodingLawsThatWouldMakeYouADecentProgrammer_9.png)

이제 클래스나 함수를 수정할 필요가 없어서 보다 견고한 해결책입니다. 어떤 나라와 그 나라의 수도를考え하고 싶을 때 capital 사전만 조정하면 됩니다.

또 다른 흔한 예는 클래스 상속을 사용하는 것입니다.

예를들어:

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

아래는 다른 지불 방법을 추가할 때마다 항상 PaymentProcessor 클래스를 수정해야한다는 것이 잘못된 방법입니다.

더 나은 방법은 다음과 같습니다:

![이미지](/assets/img/2024-05-23-50CodingLawsThatWouldMakeYouADecentProgrammer_11.png)

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

이렇게 하면 새로운 결제 옵션을 추가해야 할 때마다, 예를 들어 암호화폐나 페이팔 같은 것, 이를 달성하기 위해 어떤 클래스도 수정할 필요가 없습니다. 단순히 다음과 같이 하면 됩니다:

<img src="/assets/img/2024-05-23-50CodingLawsThatWouldMakeYouADecentProgrammer_12.png" />

## 법칙 13: 리스코프 치환 원칙

이 전 원칙을 살펴보면, 암호화폐를 사용하여 결제할 때 우리는 정확히 어떤 암호화폐를 보내는지 명시적으로 지정하지 않습니다. 금액만 명시합니다. 그래서 만약 우리가 암호화폐를 명시하고 싶어한다면, 일반적으로 다음과 같이 할 것입니다:

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

```python
from abc import abstractmethod

class PaymentProcessor:

   @abstractmethod
   def pay_tax(amount, crypto):
      pass
```

그런 다음 각 결제 프로세서를 호출할 때 crypto 인수를 None 타입으로 선언하거나 필요하지 않은 경우 인수를 전달하지 않도록 기본값을 지정합니다. 이 두 경우 모두 Liskov Substitution Principle을 준수하지 못합니다.

이는 부모 클래스 또는 추상 클래스가 대부분의 하위 클래스에게 관련이 없는 인수를 포함하고 있기 때문입니다.

리스코프 치환 원칙(LSP)은 "슈퍼클래스의 객체는 하위 클래스의 객체로 교체해도 프로그램의 정확성에 영향을 미치지 않아야 한다"는 것을 명시합니다.

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

LSP를 준수하기 위해 CryptoPaymentProcessor 클래스 내에서 암호화폐를 정의해야합니다. 이렇게 하면 다른 하위 클래스와의 불필요한 충돌을 방지할 수 있습니다.

```js
class CryptoPaymentProcessor(PaymentProcessor):
   def __init__(self, crypto):
      self.crypto = crypto

   def pay_tax(amount):
      print(f'당신의 {self.crypto} 지갑으로 세금 지불이 진행됩니다')
      print(f'{amount}을(를) 청구할 예정입니다.')
```

## LAW 14: 언제 코멘트를 사용해야 할지 알기

코멘트를 사용해야 할 때마다 코드로 표현하지 못한 것에 대해 부끄러워해야 합니다. 그러나 댓글을 사용하면 실제로 코드 자체보다 코드의 기본 작업을 잘 설명하는 데 도움이 되는 경우도 있습니다. 여기 "좋음" 코멘트의 5가지 좋은 예제가 있습니다.

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

친절한 메모
코드를 읽는 사람에게 코드를 더 잘 전달하는 데 도움이 되는 정보가 있는 메모를 작성하는 것은 언제나 좋습니다. 예를 들어 함수의 반환 값을 강조하는 메모는 더 많은 명확성을 제공할 수 있습니다. 그러나 이러한 메모는 명확한 함수 또는 변수 이름을 사용하여 불필요하게 만들 수 있습니다.

할 일 메모
이러한 메모는 다른 프로그래머들이 이 함수/작업이 아직 미완성이거나 수정이 필요하다는 것을 알 수 있도록 도와줍니다. 특정 함수를 구현하는 더 나은 방법이 있을 수도 있습니다. 때로는 코드가 주기적으로 실패할 수 있습니다.

당신의 이유에 상관없이, 이러한 메모는 가져가는 것보다 더 많은 가치를 제공합니다. 할 일 메모는 일반적으로 과업이 완료되었거나 제대로 수정된 후에 제거되어야 한다는 것을 기억하기 때문에 코드가 변경되거나 개선될 때 거의 손대지 않는 경향이 있습니다.

후행 작용 경고
가끔은 다른 개발자들에게 잠재적인 위험을 알리고 싶을 때가 있습니다. 이 위험에 발을 딛게 되면 예기치 못한 결과가 생길 수 있습니다. 우리는 모두 하루를 생존하고 싶어합니다. 이 상황에서 메모가 문제를 해결하는 데 도움이 될 수 있습니다.

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

특정 코드가 시간이 걸리거나 특정 시스템을 과부하시킬 가능성이 있는 경우, 독자나 다른 프로그래머에게 "#COMPUTING RESOURCES를 많이 소비함"과 같은 경고가 도움이 될 수 있습니다.

## 법칙 15: 언제 주석이 나쁜가요?

소음 주석
이러한 주석들은 당연한 것을 다시 강조하는 주석입니다. 추가 정보를 제공하지 않고 코드의 더 많은 길이만 늘립니다. 많은 시간, 우리는 이러한 주석을 건너뛰곤 합니다. 소음 주석의 예시는 다음과 같습니다:

```js
# 동물 리스트에 추가합니다
animal.append(dog)
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

비 로컬 정보
프로그래머들이 주석을 사용할 때 범하는 실수 중 하나는 전역 정보를 로컬에서 제공하는 것입니다. 주석을 작성할 때는 해당 함수나 문을 참조하는 것에만 관련되도록 유지하십시오. 그 외의 부분은 제거해야 합니다.

명확하지 않은 주석
우리가 알기에는 분명한 주석을 작성하기 쉽습니다만 다른 사람에게는 명확하지 않을 수 있습니다. 주석과 함수 간의 연결은 명확해야 합니다. 둘 다 동일한 단계나 절차를 따라야 합니다. 주석이 또 다른 주석을 필요로 하지 않도록 해야 합니다.

짧은 함수
짧은 함수에 대해 주석이 필요하지 않을 가능성이 높습니다. 함수가 짧을수록 좋은 이름으로 설명할 수 있는 가능성이 높습니다. 따라서 이러한 함수는 보통 자기 설명적입니다.

## 법칙 16: 소스 파일을 짧게 유지하기

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

소스 파일은 최대 500줄이지만 100–200줄 사이로 유지하는 것이 좋습니다. 매우 좋은 이유가 없는 한 다른 방법을 선택하는 것을 권장하지 않습니다. 소스 파일을 짧게 유지하면 재사용성과 가독성과 같은 다양한 명백한 이점이 있습니다. 또한 연결할 내용을 찾느라 스크롤하고 시간을 낭비하는 일이 줄어들기 때문에 유지 및 업데이트하기가 더 쉽습니다.

## 법칙 17: 빈 줄 사용 시점을 알아두세요

빈 줄은 새로운 개념과 분리된 부분으로 진행하고 있다는 것을 독자에게 알려주는 방법입니다. 각 줄 그룹은 완전한 생각을 나타냅니다. 이는 독자가 생각이 끝났는지를 이해하는 데 도움이 됩니다.

## 법칙 18: 관련된 코드/함수/클래스를 가까이 유지하세요

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

테이블 태그를 마크다운 형식으로 변경해보세요.

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

두 가지가 강하게 관련이 없음을 나타내기 위해 공백을 사용하고, 강하게 연관된 것들은 공백을 사용하지 않습니다. 예를 들어 함수를 정의할 때...

```js
def create(name):
    print(name)
```

함수와 이름 변수 사이에 공백이 없습니다. 만약 공백이 있다면, 매우 조화롭지 않고 조직되지 않은 모습이 될 것입니다...

```js
def create (name):
    print (name)
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

함수에 전달되는 인수는 구별되어야 합니다.

## 법칙 20: 팀 규칙 준수

거의 모든 개발자는 자신만의 스타일을 갖고 있습니다. 파일 이름 짓는 방식부터 print 문 작성 방식까지.

하지만 다른 개발자들과 협업할 때는 개인적인 취향을 내려놓고 팀의 선호도를 받아들이는 것이 좋습니다. 다른 사람들이 당신의 코드에서 아름다움을 느끼지 못할 수도 있습니다.

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

## LAW 21: 마법 숫자 피하기

마법 숫자란 나중에 변경될 수 있는 하드 코딩된 값으로, 그래서 업데이트하기 어려울 수 있습니다.

예를 들어, "나의 주문" 개요 페이지에서 마지막 50개 주문을 보여주는 페이지가 있다고 가정해봅시다. 여기서 50은 마법 숫자입니다. 왜냐하면 표준이나 규약으로 설정되지 않았으며, 명세서에 기술된 이유로 임의로 정한 숫자입니다.

이제 50을 서로 다른 곳에 넣으시는 것입니다 — SQL 스크립트 (SELECT TOP 50 \* FROM orders), 웹사이트 (마지막 50개 주문), 주문 로그인 (for (i = 0; i ` 50; i++)) 그리고 가능한 다른 많은 장소에.

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

# 나쁨

SELECT TOP 50 \* FROM orders

# 좋음

NUM_OF_ORDERS = 50
SELECT TOP NUM_OF_ORDERS \* FROM orders

## LAW 22: 깊은 중첩 피하기

루프, 조건문 또는 함수 내의 중첩 수준을 제한하여 가독성을 향상시킵니다.

# 나쁨

if x:
if y:
do_something()

# 좋음

if x and y:
do_something()

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

## LAW 23: 임시 변수 피하기

```js
# 나쁜 예
temp_result = calculate(x, y)
final_result = temp_result * 2

# 좋은 예
final_result = calculate(x, y) * 2
```

## LAW 24: 암호적 줄임말 피하기

가독성을 높이기 위해 암호적 줄임말 대신 설명적인 이름을 사용하세요.

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

# 나쁜 예시

def calc(x, y):
pass

# 좋은 예시

def calculate_total_price(quantity, unit_price):
pass

## 법칙 25: 경로 하드코딩 피하기

파일 경로나 URL을 하드코딩하지 말고, 대신 구성 파일 또는 환경 변수를 사용해주세요.

# 나쁜 예시

file_path = "/path/to/file.txt"

# 좋은 예시

import os
file_path = os.getenv("FILE_PATH")

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

## LAW 26: 항상 Try-Catch-Finally 문 사용하기

코드를 작성할 때, 오류 처리를 반드시 포함하는 것이 가장 좋습니다. 이렇게 하면 디버깅 프로세스를 가속화하고 코드의 정교성을 높일 수 있을 뿐만 아니라 코드를 깔끔하고 관리하기 쉽게 유지할 수도 있습니다.

특정 코드에서 오류가 발생할 가능성이 높은 경우 try-catch 문을 사용하고 싶어할 것입니다.

API 요청, 파일 처리 등의 작업은 어떤 이유로든 실패하거나 오류를 일으킬 가능성이 높습니다. 반면에 곱셈이나 나눗셈과 같은 작업에 대해 try-catch 문을 사용하는 것은 오히려 문제를 더 만들어내기 때문에 지양해야 합니다.

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

## 법칙 27: 예외와 함께 컨텍스트 제공하기

예외가 발생했을 때는 해당 예외가 발생한 위치와 디버깅할 수 있도록 충분한 컨텍스트를 제공해야 합니다.

예외와 함께 유용한 오류 메시지를 생성해야 합니다. 오류를 출력할 때 해당 작업이 실패한 컨텍스트와 실패 유형을 명시해야 합니다.

## 법칙 28: 여러 예외 클래스 사용 피하기

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

아래와 같은 코드를 본 적이 있나요?

```js
try:
    pass
except ValueError:
    pass
except TypeError:
    pass
except IndexError:
    pass
except KeyError:
    pass
except FileNotFoundError:
    pass
```

이 코드는 매우 부적절하며, 오류 처리에 추가 도움을 제공하는 대신 가독성, 복잡성 및 유지보수 측면에서 문제를 일으킵니다.

우리가 마주칠 수 있는 모든 종류의 오류를 처리하기 위해 보다 일반적인 예외를 사용하는 것이 종종 더 나은 방법입니다. 기본적으로 이 유형의 예외는 우리가 받은 오류의 유형을 포함합니다.

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
try:
 pass
except Exception:
 pass
```

특정 유형의 오류를 잡고 싶을 때 다른 모든 오류가 통과될 수 있도록 해주세요.

## 법칙 29: 함수는 변이하거나 값을 반환해야 하나 둘 다 하면 안 된다.

함수를 작성할 때 해당 함수가 정확히 무엇을 해야 하는지 유의해야 합니다. 인수를 변이시킬까요? 아니면 반환해야 하나요?

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

만약 함수가 전달된 인수를 변형시키면, 그 함수 안에서만 그 작업을 하면 됩니다. 그 이외의 곳에서는 건드릴 필요가 없어요.

하지만 여기서 변형이 무엇을 의미하는 걸까요? 함수가 인수의 내용을 변경하거나 인수의 데이터 유형을 변경하는 경우 변형됩니다.

```js
def changed(array):
    array.append('hello')
```

만약 인수가 다른 변수를 만들기 위해 사용된다면 그것은 변형이 아닙니다. 예를 들어, 시간이라는 인수가 거리를 계산하는 데 사용된다면, 그것은 변형이 아니기 때문에 거리는 그 함수에서 반환될 수 있습니다.

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
def calculate_distance(time, speed):
    distance = speed * time
    return distance
```

하지만 두 마리 토끼를 모두 잡는 방법이 있어요. 함수의 인수를 복사하고 그에 대한 변이(mutation)를 수행할 수 있어요. 이렇게 하면 부작용을 피할 수 있어요.

```js
def changed(array):
    array_copy = array[:]
    array_copy.append(4)
    return array_copy
```

## 법칙 30: 모든 함수 이름이 동사일 필요는 없어요.

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

네, 알겠어요. 이전에 제가 언급했던 것처럼 모든 함수명은 일반적으로 동사여야 한다고 했었는데, 때로는 함수명이 명사 형태여야 하는 경우가 있습니다. 이것이 언제 그런지 아는 것은 이전 법칙/법칙 29에 근거합니다.

인수를 변형하지 않고 무언가를 반환하는 함수라면, 함수명은 명사여야 합니다. 반면에 인수를 변형하고 반환하지 않는 함수는 동사여야 합니다.

이것은 파이썬 자체에 내장된 일반적인 관례입니다. sort나 append와 같은 메서드는 데이터 유형을 변형하고 None을 반환하기 때문에 동사입니다. 반면에 sorted, sum, product와 같은 메서드들은 전달된 인수를 변형하지 않고 데이터의 새 복사본을 반환하기 때문에 모두 명사입니다.

물론 이에는 예외가 있고, 한 번 예외 상황을 마주했을 때는 언제든지 동사를 사용하도록 되돌아가도 괜찮습니다.

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

## 법칙 31: 클래스는 작아야 합니다

그렇습니다! 클래스는 가능한 작아야 합니다. 함수와 마찬가지로요.

함수에서는 크기가 함수 내의 줄 수에 의해 결정되지만 클래스에서는 책임의 수에 따라 결정됩니다.

일반적으로 클래스 이름은 해당 클래스가 가질 수 있는 책임의 종류를 나타냅니다. 그러나 이름이 모호하거나 너무 일반적인 경우, 대부분 너무 많은 책임을 부여하고 있는 것입니다.

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

이는 클래스가 하나의 이유, 즉 변경할 책임이 하나만 있어야 한다는 SRP(단일 책임 원칙)로 되돌아가게 됩니다.

## LAW 32: 클래스는 인스턴스 변수의 개수를 적게 가져야 합니다.

인스턴스 변수는 클래스가 정의되거나 인스턴스화될 때 정의된 변수입니다.

```js
class Animal:
    def __init__(self, name):
        self.name = name #인스턴스 변수
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

만약 모든 우리의 함수가 클래스 책임과 관련이 있다면, 많은 인스턴스 변수를 가질 이유가 없어요.

수많은 인스턴스 변수가 생기기 시작하는 건, 클래스의 핵심 역할에서 벗어난 함수 때문입니다.

이러한 함수들은 다른 함수가 필요하지 않은 자체 변수를 가지게 됩니다.

## 법칙 33: 당신의 클래스는 응집력이 있어야 합니다.

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

클래스 내의 모든 함수는 하나 이상의 인스턴스 변수를 포함해야 합니다. 함수가 클래스 내의 인스턴스 변수와 관련이 많거나 해당 변수를 포함하면 클래스의 응집력이 더 높아집니다.

## 법칙 34: 자원 관리를 위해 with 문 사용하기

파일이나 데이터베이스 연결과 같은 자원을 자동으로 관리하려면 with 문을 사용하여 해당 자원이 제대로 닫히거나 해제되도록 합니다.

```js
# 나쁜 예
file = open("example.txt", "r")
data = file.read()
file.close()

# 좋은 예
with open("example.txt", "r") as file:
    data = file.read()
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

## 법칙 35: 복잡한 삼항 표현식 피하기

과도하게 복잡한 삼항 표현식 사용을 삼가하고, 코드를 더 잘 이해할 수 있도록 간결함보다 가독성을 선호하세요.

```js
# 나쁨
result = "even" if number % 2 == 0 else "odd" if number % 3 == 0 else "neither"

# 좋음
if number % 2 == 0:
    result = "even"
elif number % 3 == 0:
    result = "odd"
else:
    result = "neither"
```

## 법칙 36: 정체성 비교에 ‘is’와 ‘is not’ 사용하기

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

대부분의 경우, 우리는 두 변수 사이의 비교를 확인하기 위해 ==를 사용합니다. 불변 데이터 유형인 문자열이나 정수와 같은 경우에는 보통 두 변수가 동일한 메모리 위치에 저장되기 때문에 메모리 위치 확인은 필요하지 않습니다.

그러나 list, dict 및 사용자 정의 객체와 같은 가변 데이터 유형과 작업할 때는 종종 변수의 서브 유형과 메모리 위치를 확인하는 is 비교 연산자를 사용하는 것이 더 좋습니다.

가변 객체의 메모리 위치는 Python의 작동 방식 때문에 보통 같지 않습니다. Python은 가변 객체를 서로 다른 메모리 위치에 저장합니다. 이것은 언제든지 변경될 수 있으며 각 객체가 다른 객체와 독립적이어야 하기 때문입니다.

문자열, 튜플 및 정수는 생성된 후에 변경할 수 없습니다.

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
아래 링크를 사용하여 이 코드를 실행해보세요:
https://python-fiddle.com/saved/nV6iEIyBuHm2mevD9Bhg

# 예시 2: 두 리스트가 동일한 객체를 참조하는지 확인
list1 = [1, 2, 3]
list2 = [1, 2, 3]

# 선호되지 않는 방법: == 사용
if list1 == list2:
    print("리스트는 동일한 값을 가집니다")

# 선호되는 방법: is 사용
if list1 is list2:
    print("리스트는 동일한 객체를 참조합니다")

# 참고: 이 경우에, list1과 list2는 동일한 값을 가진 다른 객체이므로,
# `is`를 사용하면 `==`와 다른 결과가 나옵니다.
```

## LAW 37: 의존 역전 원칙

의존 역전 원칙(Dependency Inversion Principle, DIP)은 객체 지향 설계의 중요한 원칙으로, 컴포넌트 간의 느슨한 결합을 촉진하고 소프트웨어 시스템의 보다 쉬운 유지보수와 확장을 돕습니다.

고수준 모듈은 저수준 모듈에 의존해서는 안 되며, 둘 다 추상화에 의존해야 합니다.

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

다시 말해서, 클래스는 구체적인 구현이 아닌 인터페이스 또는 추상 클래스에 의존해야 합니다.

```js
# 나쁜 예
class Logger:
    def log(self, message):
        with open('log.txt', 'a') as f:
            f.write(message + '\n')

class Calculator:
    def __init__(self):
        self.logger = Logger()

    def add(self, x, y):
        result = x + y
        self.logger.log(f"{x}와 {y}를 더했습니다. 결과 = {result}")
        return result
```

위 예시에서 Logger 클래스를 정의하고 Calculator 클래스에서 직접 인스턴스를 생성합니다. 이로 인해 Calculator는 이제 Logger 클래스에 의존하며, Logger 클래스를 변경하면 Calculator 클래스도 수정해야 합니다.

또한 이는 개방-폐쇄 원칙(확장에는 열려 있고 수정에는 닫혀 있음)을 지키지 못하는 것을 보여줍니다.

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
# 좋은 코드
from abc import ABC, abstractmethod

class LoggerInterface(ABC):
    @abstractmethod
    def log(self, message):
        pass

class Logger(LoggerInterface):
    def log(self, message):
      with open('log.txt', 'a') as f:
          f.write(message + '\n')

class Calculator:
    def __init__(self, logger: LoggerInterface):
        self.logger = logger

    def add(self, x, y):
        result = x + y
        self.logger.log(f"Added {x} and {y}, result = {result}")
        return result
```

이 기능은 테스트하기가 더 어려울 수 있지만, 가짜 로거 클래스를 사용하여 테스트할 수 없게 만든다는 문제가 있습니다.

이렇게 하면 인터페이스가 일관성을 유지하는 한 한 요소의 변경이 다른 요소에 변경을 요구하지 않기 때문에 모듈화를 촉진합니다.

이 모듈성은 코드베이스를 더 쉽게 이해, 수정 및 확장할 수 있도록 만듭니다.

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

## 법칙 38: 데이터 유효성 검사에 'assert' 사용을 피하십시오

'assert' 문은 디버깅 및 개발 목적으로만 사용하고, 제품 코드에서 데이터 유효성 검사에는 사용을 피하십시오.

```js
# 안 좋은 예
assert x > 0, "x는 양수여야 합니다."

# 좋은 예
if x <= 0:
    raise ValueError("x는 양수여야 합니다.")
```

## 법칙 39: 하드 코딩된 숫자를 피하십시오

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

명확성을 높이고 코드 수정을 쉽게 만들기 위해 의미 있는 이름으로 상수를 작성하십시오.

```js
DISCOUNT_RATE = 0.1

def calculate_discount(price):
    discount = price * DISCOUNT_RATE
    return price - discount
```

위 예제는 10% 할인을 나타내는 하드 코딩된 숫자 0.1을 사용합니다.

이로 인해 숫자의 의미를 이해하기 어렵고 다른 부분에서 필요시 할인율을 조정하는 것이 어려워집니다.

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

```python
def calculate_discount(price):
    TEN_PERCENT_DISCOUNT = 0.1
    discount = price * TEN_PERCENT_DISCOUNT
    return price - discount
```

개선된 코드는 숫자를 하드코딩하는 대신 TEN_PERCENT_DISCOUNT라는 이름이 지정된 상수로 대체합니다. 이 이름은 값의 의미를 즉시 전달하여 코드를 자체 문서화하는 데 도움이 됩니다.

## LAW 40: DRY (Don’t Repeat Yourself) 원칙을 따르세요

같은 코드를 한 번 이상 작성하지 않도록 합니다. 대신 함수, 클래스, 모듈, 라이브러리 또는 기타 추상화를 사용하여 코드를 재사용하세요. 이렇게 하면 코드가 더 효율적이고 일관되며 유지 보수가 용이해집니다.

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

위의 표를 변경하여 마크다운 형식으로 변환해보세요.

```js
# 나쁜 예

def calculate_book_price(quantity, price):
    return quantity * price
def calculate_laptop_price(quantity, price):
    return quantity * price

# 좋은 예

def calculate_product_price(product_quantity, product_price):
    return product_quantity * product_price
```

## LAW 41: 존중할 만한 코딩 기준을 따르세요.

공백, 주석, 그리고 명명에 대한 일반적으로 인정받은 컨벤션을 따르는 것이 중요합니다. 대부분의 프로그래밍 언어에는 커뮤니티에서 인정하는 코딩 표준과 스타일 가이드가 있습니다. 예를 들어, Python의 경우 PEP 8가 있습니다.

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

일반적으로 사용되는 관례들은 다음과 같습니다:

- 변수, 함수 및 클래스 이름에는 snake_case를 사용합니다.
- 들여쓰기에는 탭 대신 공백을 사용합니다.
- 들여쓰기 단계마다 4개의 공백을 사용합니다.
- 모든 줄을 최대 79자로 제한합니다.
- 2진 연산자 앞에 줄바꿈을 넣습니다.

## 법칙 42: 데메테르의 법칙

데메테르의 법칙은 간단히 말하면 모듈/함수/클래스는 주변 모듈/함수/클래스에 대한 지식이나 참조를 가질 수 있지만 그 이상의 지식은 가져서는 안 된다는 것을 의미합니다.

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

인접한 이웃이라 함은 직접 액세스할 수 있는 메소드, 함수 또는 변수를 의미합니다.

예제를 통해 설명해드리겠습니다...

```js
class Order:
    def __init__(self, customer):
        self.customer = customer

    def get_customer_name(self):
        # 위반 사항: 주문이 고객의 구조에 대해 너무 많이 알고 있습니다
        return self.customer.get_profile().get_name()
```

이 예제에서 Order 클래스는 고객의 프로필에 직접 접근하여 고객의 이름을 검색합니다. Order는 고객 객체의 내부 구조에 액세스하여 프로필 및 이름에 접근하고 있으므로 이는 데메테르 법칙을 위반합니다.

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

주변 이웃을 넘어서고 이제 고객 개체에 대해 너무 많이 알게 되었습니다.

```js
class Order:
    def __init__(self, customer):
        self.customer = customer

    def get_customer_name(self):
        # 준수 사항: 주문은 직접적인 협력자와만 상호 작용합니다
        return self.customer.get_name()
```

이 준수 사례에서 Order 클래스는 해당 고객 개체와만 상호 작용하고 고객의 이름을 검색하기 위해 직접적으로 메서드를 호출합니다.

고객 개체의 내부 구조에 접근하지 않으므로 Demeter의 법칙을 따릅니다.

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

## 법칙 43: 간결함보다 가독성이 중요합니다

코드는 기계가 해석할 수 있어야 합니다. 그러나 다른 개발자들도 코드를 이해할 수 있어야 합니다. 특히 여러 명이 참여하는 프로젝트에서 작업할 때는 더욱 중요합니다.

소프트웨어 개발에서 가독성은 항상 코드의 간결성보다 중요합니다.

만약 다른 개발자들이 이해할 수 없는 간결한 코드를 작성한다면, 그것은 별 의미가 없습니다.

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

## 법칙 44: Import를 깨끗하게 유지하세요

필요한 모듈과 심볼만을 가져와서 import 섹션을 깔끔하게 유지하고 가독성을 향상시키세요. 모듈에서 모든 (\*) 것을 가져올 때, 모든 변수, 함수 및 클래스도 가져오게 되어 특정 함수/클래스가 어디서 온 것인지 알기 어려워지며, 최신 IDE를 사용할 때 번거로울 수 있습니다.

예를 들어, get_file이라는 함수를 작성하고 싶다고 상상해보세요. g를 클릭하면 IDE가 g로 시작하는 함수/클래스/변수 목록을 추천해줍니다. 이렇게 되면 꽤 혼란스러워질 수 있습니다.

이것이 더 큰 문제로 변하는 경우가 있습니다. 함수를 호출하려고 할 때 더욱 문제가 될 수 있습니다. 함수 이름이 추천 목록 사이에 잃어버릴 수 있고, 이제 IDE가 효율적인 해결책보다는 문제로 변할 수 있습니다.

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

# 나쁜 예

from module import \*

# 좋은 예

from module import symbol1, symbol2

## LAW 45: Null/None을 반환하지 마세요

보통 함수를 정의할 때, 기본적으로 반환 값이 지정되지 않은 경우 None이 반환됩니다. 그렇지만 우리가 명시적으로 None을 반환할 때는, 해당 함수가 None 이외의 다른 값을 반환할 수 있다는 것을 간접적으로 읽는 사람에게 알리는 것입니다.

만약 이게 사실이 아니라면, 많은 오해를 불러올 수 있습니다.

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

## 법률 46: 건설과 사용 분리하기

관심사의 분리는 소프트웨어 공학에서 매우 기본적인 원칙이 되어왔습니다. 우리는 소프트웨어의 건설 방식을 사용 방식으로부터 분리하는 방법을 알아야 합니다.

이는 종종 시작 과정, 즉 의존성 및 객체들이 결합되는 때와 실행 시간 로직, 즉 응용 프로그램 로직이 사용자 입력이나 다른 트리거로부터 실행되는 경우와 같은 것들을 분리하는 것을 의미합니다.

건설과 사용을 분리하는 일반적인 방법은 main이라는 파일/함수/모듈에서 응용 프로그램 로직을 구성하는 것입니다.

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

주요 기능은 응용 프로그램이 원활하게 실행되도록 필요한 객체를 구축합니다. 이렇게 함으로써 다른 모듈이 응용 프로그램에 강하게 결합되지 않도록하고 재사용성과 모듈성을 증진시킵니다.

## LAW 47: 간단한 디자인에는 모든 이러한 규칙이 포함됩니다

모든 테스트 실행: 시스템은 문서상으로 완벽한 디자인을 가질 수 있지만, 시스템이 의도한 대로 작동하는지 확인할 수 있는 방법이 없다면, 문서상의 디자인은 의문스러워집니다.

중복이 포함되지 않음: 중복은 잘 설계된 시스템의 주요 적인 적수입니다.

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

프로그래머의 의도를 표현합니다

클래스와 메소드의 수를 최소화합니다

## LAW 48: 중첩된 Try-Except 블록 피하기

너무 복잡한 오류 처리 논리를 방지하기 위해 try-except 블록을 과도하게 중첩하는 것을 삼가세요.

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
# 나쁜 예시
try:
    try:
        # 오류가 발생할 수 있는 코드
        pass
    except ValueError:
        # ValueError 처리
        pass
except Exception as e:
    # 다른 예기치 않은 오류 처리
    pass

# 좋은 예시
try:
    # 오류가 발생할 수 있는 코드
    pass
except ValueError:
    # ValueError 처리
    pass
except Exception as e:
    # 다른 예기치 않은 오류 처리
    pass
```

## 법칙 49: 필요할 때만 동시성 사용하기

동시성 기능을 구현할 때 나쁜 코드를 작성하기가 매우 쉽습니다.

또한, 매우 결함이 많은 동시성 기능을 구현할 때 깔끔한 코드를 작성하는 것도 매우 쉽습니다. 보통 시스템에 많은 스트레스가 가해질 때까지 잘못되었다는 것을 인식하지 못할 수도 있습니다.

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

너는 매우 현명하게 전투를 선택하길 원해.

너의 동시성 코드가 실패할 수 있는 여러 이유들이 있다. 여기 몇 가지 예시가 있다:

굶주림(Starvation): 굶주림은 스레드나 프로세스가 공유 자원에 접근할 수 없어 계속해서 시도해도 영원히 실패하는 경우 발생한다. 이는 다른 스레드나 프로세스가 계속해서 자원을 확보하고 보유하여 굶주는 스레드가 진행하지 못하게 하는 경우에 발생할 수 있다.

교착상태(Deadlocks): 교착상태는 두 개 이상의 스레드나 프로세스가 상호적으로 서로 자원을 해제하기를 무한정 대기하고 있는 경우 발생한다. 이는 각 프로세스가 한 자원을 보유하고 다른 프로세스가 보유한 다른 자원을 기다리며 순환 의존성을 만들어 서로 대기하는 경우에 발생할 수 있다.

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

## LAW 50: 49가지 법칙을 따르세요

이 법칙들은 당신을 소프트웨어 엔지니어로서의 여정에서 안내하기 위해 존재합니다. 필요할 때마다 이를 준수해야 합니다.

그러나 경험과 기술이 쌓일수록, 특정 규칙을 따를 때와 그렇지 않을 때를 판단할 수 있는 능력을 가지고 싶을 것입니다.

이 직관은 자신의 기술을 숙달한 사람들에게만 주어지며, 만약 초보자이거나 2년 전에 경력을 시작한 경우라면, 이 법칙을 하늘로 가는 유일한 티켓으로 여기는 것이 가장 좋습니다.

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

대부분의 파이썬 개발자들은 코드를 빠르게 테스트하거나 오류를 디버깅하는 곳이 필요합니다. 저는 python-fiddle.com이라는 웹사이트를 개발했습니다. 여기에서 빠르게 코드를 테스트하고 AI/LLMs를 활용하여 가능한 오류의 해결책을 찾을 수 있습니다.
