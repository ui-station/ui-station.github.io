---
title: "2024 OWASP 모바일 탑 10 변경 사항에 대한 소식이 뭐길래"
description: ""
coverImage: "/assets/img/2024-05-23-Whatsthebuzzaboutthe2024OWASPMobileTop10changes_0.png"
date: 2024-05-23 14:48
ogImage:
  url: /assets/img/2024-05-23-Whatsthebuzzaboutthe2024OWASPMobileTop10changes_0.png
tag: Tech
originalTitle: "What’s the buzz about the 2024 OWASP Mobile Top 10 changes?"
link: "https://medium.com/proandroiddev/whats-the-buzz-about-the-2024-owasp-mobile-top-10-changes-83c93f765bd3"
---

# 소개

먼저, 2024년 새해 복 많이 받으세요 🎉 이미 2월이 되었지만, 올해 첫 게시물이라 조금 늦었다는 점 이해해 주시면 감사하겠습니다.

그 외에도, 좋은 소식은 올해 모바일 보안 분야에서 이미 흥미로운 소식으로 시작되었다는 것입니다. 이 게시물에서는 2024년을 위한 OWASP Mobile Top 10의 변경 사항을 살펴보고, 그것이 보안에 민감한 개발자인 여러분에게 무슨 의미가 있는지 알아보겠습니다!

저의 OWASP Mobile Top 10 강의나 포스트에 이미 익숙한 분들은 당연히 "OWASP의 정상" 섹션으로 건너뛰어서 흥미로운 내용을 확인할 수 있습니다. 그러나 OWASP가 무엇이며 최근 변경 사항이 모바일 보안 분야에서 큰 영향을 미친다는 사실에 대해 잘 알지 못하거나 간단히 상기시키고 싶다면, 함께 머물러 주시면 OWASP란 무엇이며 최근 변경 사항이 모바일 보안 분야에서 왜 중요한지에 대해 다시 한 번 요약해 드리겠습니다.

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

# OWASP이 뭐죠? 🐝

모바일 보안에 새로 오신 분이나 제 블로그 게시물을 처음 보시는 분, 혹은 한동안 돌아본 적이 없는 분들께 질문드립니다. OWASP와 그들이 하는 훌륭한 작업에 대해 아시지 못할 수 있습니다.

2001년에 설립된 OWASP(Open Worldwide Application Security Project) 재단은 비영리 단체로, 소프트웨어 보안 관행에 대한 교육 자료, 도구, 교육 자원 및 다른 다양한 커뮤니티 기반 서비스를 제공합니다. 우리 분야에서 선두적인 어플리케이션 보안 커뮤니티로 널리 인정받으며 다양한 프로젝트에서 모바일 보안을 다루는 프로젝트를 비롯한 많은 헌신적인 자원봉사자들이 있습니다.

OWASP가 제공하는 안내 중 하나는 'Top 10' 위협 목록입니다. 이 목록은 OWASP가 특정 영역의 보안에 대한 자신의 상위 10위 위협 목록으로, 모든 개발자에게 매우 유용한 자원입니다¹.

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

저는 이전에 그들의 지난 안내에 대해 다양한 콘텐츠를 제공했습니다. 더 자세히 알고 싶다면, 제 발표 페이지와 이전 OWASP 관련 블로그를 확인해보세요. 그 곳에는 더 많은 자세한 내용과 관련 링크가 있습니다.

보다 실용적인 학습자라면, 제 OWASP 발표를 위한 동반 앱도 있습니다. 그 앱은 제가 소개한 주제 중 일부를 보여줍니다.

어쨌든, 2024년은 거의 10년 만에 가장 중요한 Mobile Top 10의 수정이 이루어지고 있습니다. 그래서 더 이상 말 할 필요 없이, 무엇이 변했는지 살펴보겠습니다! 👀

# “OWASP의 꼭대기" ✨

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

긴 협의 기간과 여러 차례의 수정을 거친 끝에, 최근 발표된 OWASP Mobile Top Ten 2024는 2014년 최초 발표 이후 세 번째이자 최신 주요 개정판입니다.

2024년 발표 버전은 2016년 버전을 대체하며, 현재의 모바일 보안 환경을 더 잘 반영하기 위해 네 가지 새로운 위협 범주와 위협의 완전한 재배열을 포함한 명백한 변경 사항을 가져왔습니다.

이 모든 것은 잘 된 일이지만, 실제로 무엇이 변경되었고 이 모든 의미가 무엇을 의미하는지 궁금하십니까? 😅 음, 진짜 Billboard 차트 스타일로 (이나 영국인으로서는 Top of the Pops 스타일로), 각 위협에 대해 순서를 거꾸로하여 번호 10부터 간단히 설명해 보겠습니다!

![이미지](https://miro.medium.com/v2/resize:fit:768/1*9ESds38QHiOyqz_CkOXM7A.gif)

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

## #10: 부적절한 암호화 ⬇️

2016년 5위에서 내려온 부적절한 암호화는 모바일 앱이 현대 암호화 최상의 방법을 채택하지 못해 겪는 위협입니다. 이는 안전하지 않은 알고리즘(SHA-1, MD5 등)을 구현하거나 안전한 데이터 전송(HTTPS)을 사용하지 않거나 심지어 키에 맛있는 소금을 쓰지 않는 것으로 나타납니다 🧂.

암호화를 사용할 때, 특정 필요에 맞는 최상의 방법을 따르도록 하십시오. 예를 들어, AES와 같은 알고리즘을 사용하면서 최소 128비트 블록 크기 또는 최소 2048비트 RSA를 사용하는 것이 좋습니다. 의문이 생길 경우, 전문가에게 질의하거나 Google의 tink 라이브러리와 같은 신뢰할 수 있는 도구를 활용하십시오.

앞으로 제 블로그에서 관련 포스트가 올라올 예정입니다 (희망적으로) 📝.

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

## #9: 안전하지 않은 데이터 저장 ⬇️

2016년 목록에서 크게 순위가 변동한 항목 중 하나는 안전하지 않은 데이터 저장이었어요. 2위에서 9위로 내려왔네요. 아파요 😣

만약 데이터(네트워크 호출 포함)를 로그에 출력하거나, 비밀번호나 토큰을 저장하거나, 임시 파일을 생성하거나, 또는 SQL 데이터베이스나 Shared Preferences와 같은 표준 저장 기술을 사용하고 있다면, 당신의 앱은 안전하지 않은 데이터 저장에 취약할 수 있습니다.

저는 이전에도 안전하지 않은 데이터 저장에 대해 자세히 다뤘었어요. 그래서 제 글을 꼭 확인하고, 앱의 데이터가 안전하고 안전하게 저장되어 있는지 확인해보세요.

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

언제나 강조해야 할 점은 가능한 경우 기기에 민감한 데이터를 저장하는 것을 피하는 것이 최선의 실천 방법입니다!

## 8번: 보안 구성 오류 ⬆️

10위에서 상승한 이 ‘보안 구성 오류’라는 새로운 명칭의 ‘Extraneous Functionality’에 대해 이야기해 보겠습니다.

‘RTFM’ 개념을 알고 있다면, 이 위협이 무엇인지 알 수 있을 것입니다. 이는 종종 개발자가 제품 빌드에서 잘못된 설정을 사용하거나 필요하지 않은 상승된 액세스나 권한을 요청하거나 원래는 애플리케이션 내부로 의도된 기능을 노출하는 데 발생합니다.

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

문서화는 종종 친구가 될 수 있어요. 읽는 걸 소홀히 하지 마세요. 🥲 커피를 가져다가 실수에 갇히세요!

## #7: 바이너리 보호 미비 ⬆️

크림, 오디오스레이브 또는 아톰 포 피스와 같이 유명한 밴드들처럼, 2016년 8위와 9위(코드 위조 및 역공학)가 합쳐져 7위로 올라왔어요. 😎

바이너리 보호는 앱의 바이너리(즉, 안드로이드의 .apk/.aar 또는 iOS의 .ipa 파일)가 정보를 유출하지 않거나 다시 패키징되지 않도록 하는 데 초점을 맞춥니다. 앱을 제대로 난독화하지 않거나 무결성 검사를 제대로 하지 않으면 공격자가 악성 코드를 주입하여 앱을 역공학하거나 재분배할 수 있는 가능성이 있습니다.

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

내 좋은 친구 Guardsquare과 그들의 훌륭한 도구(예: DexGuard 및 Proguard Playground)를 적극 추천합니다. 이 도구들은 특정 위협으로부터 앱을 안전하게 유지하는 데 도움이 됩니다. 또는 R8 및 Google Play 무결성 API에 대해 더 알아보는 데 투자하는 것도 도움이 될 수 있어요!

## #6: 불충분한 개인정보 보호 기능 🆕

6위로 새롭게 입성한 '불충분한 개인정보 보호 기능'입니다.

당신의 앱이 사용자의 개인 식별 정보(예: 전체 이름, 정확한 위치, 금융 상세 정보, 성향 등)를 다룬다면, 이 정보들이 잘못된 손에 들어가면 해당 사용자를 사칭하거나 괴롭히거나 사기를 저질 수 있는 경우가 발생할 수 있습니다. 이 경우 이것이 해당될 수도 있어요! 🥸

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

애플리케이션이 저장하거나 기록하는 개인 식별 정보(PII)를 로컬에 저장하지 않도록하고 사용자로부터의 정보 최소한만 요청/전송하십시오. 이렇게 하면 저장소나 데이터 전송의 취약점을 통해 PII가 노출될 가능성이 크게 줄어듭니다.

예를 들어, 영화 예매 앱에서 성적 지향이나 정확한 위치가 필요합니까? 아마 아니죠! 대신 코스 위치를 요청하여 인근 영화관을 찾고 사용자가 추천을 받기 위해 추가 개인 정보를 제공할 수 있도록 요청하고, 나중에 그것을 선택적으로 거부할 수 있도록 하는 것이 훨씬 나을 것입니다.

## #5: 통신 불안전 ⬇️

또 다른 이동! 2016년 3위인 '통신 불안전'이 5위로 두 단계 하락했습니다.

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

데이터 전송이나 수신과 관련된 위협을 다루고 있어요. 대부분의 응용프로그램은 인터넷을 통해 이를 수행하지만, 여러분의 앱은 NFC나 블루투스와 같은 다른 통신 방법을 사용할 수도 있어요. 데이터 통신이 있으면, 거기에는 위험이 연결되어 있을 거라고 확신할 수 있어요!

이 문제에 대해 더 자세히 다루지는 않겠어요. 이전에 보안 통신에 대해 블로그를 썼었기 때문에 그 부분도 꼭 확인해보시길 바래요.

## #4: 충분하지 않은 입력/출력 유효성 검사 🆕

4번 항목의 새로운 주제는 사용자 입력 및 출력을 다뤄봅니다.

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

모바일 앱 및 API가 사용자 입력을 통신하거나 출력할 때 올바로 처리되도록 하는 것이 매우 중요합니다 🛁 그렇지 않으면 SQL 인젝션 또는 교차 사이트 스크립팅 (XSS)과 같은 위협이 발생할 수 있으며 이는 민감한 사용자 데이터를 노출하거나 더 심각한 경우 기기가 위협받을 수 있습니다. 원하는 값 및 형식을 얻고 해당 기준에 충족하지 않는 경우는 버려야 합니다.

WhatsApp에서 유포된 ‘Effective Power’ 또는 ‘Black Spot’ 메시지를 기억하시나요? WhatsApp 메시지에 특별히 디자인된 입력이 출력될 때 발생한 문제였는데, 이는 일종의 ‘서비스 거부’로 설계된 것이었습니다. — 여기서 살균 처리가 문제를 해결했을까요? 아마도 가능합니다!

요약하면, 입출력 조심하면 ‘작은 바비 테이블’이 문제를 일으키지 않아도 안심할 수 있습니다 ⁶.

## #3: 안전하지 않은 인증/인가 ⬆️

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

In the new ranking, insecurities related to authentication and authorization have moved up to the 3rd place, combining aspects from the previous 4th and 6th spots.

To prevent confusion due to similar names, let's clarify that authentication confirms the user's identity, while authorization verifies if the user has the necessary roles or credentials to access specific resources.

It's crucial to perform authentication and authorization checks on the server side to prevent vulnerabilities from potential binary modifications or other methods.

When interacting with APIs that need authorization, make sure to use revocable tokens linked to the device. This way, users can revoke tokens if their device is lost or stolen. Remember to refresh tokens regularly and ensure your backend team authenticates properly when authorizing access to restricted resources!

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

다시, Insecure Authentication에 대한 자세한 내용은 해당 주제에 대한 이전 블로그를 확인해 보세요 🤠 이어서 Insecure Authorization에 대한 더 많은 정보를 확인할 수 있을 거에요.

## #2: 부적절한 공급망 보안 🆕

제 2위에 새로운 항목이 추가되었는데, 이는 우리 앱을 구축하는 데 사용하는 도구와 프로세스에 초점을 맞추고 있어요.

'공급망 공격'은 사용하는 도구에 대한 공격을 말하며, 감지되지 않고 도구에 취약점, 보안 위협 또는 악의적인 코드를 도입하는 것을 의미해요. 조직 내부에서는 부정직한 직원에 의해, 또는 시스템이나 도구에 특권 액세스를 얻은 악의적인 행위자에 의해 외부에서 이루어질 수 있어요.

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

안녕하세요! 보안을 강화하기 위해 코드 검토 프로세스를 철저히 실시하는 것이 중요합니다. 뿐만 아니라 공급망의 액세스 제어에 대한 정기적인 감사도 수행해야 합니다. 앱의 종속성을 모니터링하고 이들이 정기적으로 검토되도록 하여 보안 취약성을 방지해야 합니다.

이미 확인하지 않았다면, 저는 이전에 공급망 공격과 Gradle 작업 시 어떻게 완화할 수 있는지에 대해 블로그 게시 및 강의를 진행했습니다.

## #1: 부적절한 자격 증명 사용 🆕

대망의 순간입니다 🥁 OWASP에 따르면 모바일 보안의 가장 큰 위협은... 부적절한 자격 증명 사용입니다.

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

이것은 사용자 자격 증명, API 키부터 그 사이의 모든 것에 적용되는 보안 부적절로 인한 케이스를 포괄하는 범주입니다!

알람이 울리는 2021 보고서에 따르면 추정 200개 애플리케이션 중 1개가 하드코딩된 API 키를 통해 AWS 자격 증명을 유출한 것으로 추정되었습니다 😅 이처럼 API 키 또는 하드코딩된 자격 증명이 노출되면 파괴적인 결과가 초래될 수 있습니다. 이러한 종류의 자격 증명 유출은 이전에 Uber, Verizon, Accenture 등 주요 기업들에게 피해를 입히고, 고객 데이터(포함된 개인 식별 정보 및 결제 정보)가 노출된 데이터 침해를 발생시켰습니다 🙀 일반적으로 로컬에 저장해야 하는 민감한 데이터는 항상 암호화되어야하며, 암호화에 사용되는 키는 하드웨어 보호 키 저장소에 안전하게 저장되어야 합니다.

이 모든 소리들이 겁나게 들리지만, 자신이 부적절한 자격 증명 처리에 걸리지 않는 방법은 꽤 간단합니다. 사용자 자격 증명(비밀번호 등)을 장치에 저장하지 않으며, 제3자 API 키를 정기적으로 교체하고, 언제나 HTTPS를 통해 자격 증명을 안전하게 전송해야 합니다. 간단하죠! 🙌

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

# 마무리로... 🐝

마지막으로, 2016년과 2024년 목록 사이에 상당한 변화가 있었지만, 보안에 민감한 개발자로서 여러분은 항상 응용 프로그램을 잠재적인 위협에 대해 지속적으로 모니터링해야 합니다. 상위 10개가 변경되었을 수 있지만, 많은 개발자들이 모바일 보안을 제대로 이해하지 못한다는 현실은 여전합니다. 더 많이 배우고 이 지식을 공유함으로써 이를 바꿀 수 있습니다!

이 글에서 하나의 교훈을 얻는다면, 모바일 애플리케이션에 대한 잠재적인 위협에 대해 여러분과 팀원들이 숙지되어 있는지 확인하고 (MASVS와 같은 훌륭한 자원이나 저의 블로그를 읽는 것처럼) AppSweep, MobSF, Snyk, 또는 SonarQube와 같은 도구를 통합하여 코드/바이너리를 이러한 문제에 계속적으로 점검해보시기 바랍니다.

마지막까지 완주하셨네요! 2024년이 멋지고 안전한 한 해 되길 바랍니다 🥰 🔐

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

# 감사합니다 🌟

언제나 읽어 주셔서 감사합니다! 이 포스트를 재밌게 보셨으면 @Sp4ghettiCode로 피드백을 트윗해 주세요. 반응이나 좋아요, 트윗, 공유, 스타 등도 잊지 말고 해 주세요 🙂

## 더 읽기

- OWASP Top Five Companion App (2016 버전)
- OWASP Mobile Top Ten 2024
- Mobile Application Security Verification Standard: MASVS
- Mobile Application Security Testing Guide: MASTG

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

## 각주

[1]: 보안 중심의 모바일 개발자이시라면, 업데이트된 MASVS 및 MASTG에 특히 주의를 기울여 모바일 앱의 보안 모델을 적용하고 테스트해야 합니다.

[2]: 염화 나트륨과는 아무 상관이 없습니다. 이 문맥에서의 "솔트(salt)"는 동일한 평문이 여러 번 입력되더라도 다른 해시값이 생성되도록 보장하기 위해 평문에 연결된 임의의 데이터를 의미합니다.

[3]: 불편하기는 해도, 2024년 Top 10이 5위에 있을 때 대부분 완성된 "Insufficient Cryptography" 글은 최종적으로 10위가 아니라는 것을 알았습니다. (그런데 누가 10부작 블로그 시리즈를 생각해냈을까요!?)

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

[4]: 제발 제품에서 네트워크 호출을 로깅하는 것을 그만둬주세요. 이미 2024년이에요. 🥲

[5]: 'F'는 정말 옳은 것을 가리키는 것이에요 😅

[6]: https://xkcd.com/327/
