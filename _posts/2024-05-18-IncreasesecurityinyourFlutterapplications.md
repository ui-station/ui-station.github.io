---
title: "플러터 애플리케이션에서 보안 강화하기"
description: ""
coverImage: "/assets/img/2024-05-18-IncreasesecurityinyourFlutterapplications_0.png"
date: 2024-05-18 17:10
ogImage:
  url: /assets/img/2024-05-18-IncreasesecurityinyourFlutterapplications_0.png
tag: Tech
originalTitle: "Increase security in your Flutter applications"
link: "https://medium.com/@raphaelkennedy/increase-security-in-your-flutter-applications-348d6532e021"
---

![보안 강화하기](/assets/img/2024-05-18-IncreasesecurityinyourFlutterapplications_0.png)

현재의 디지털 세계에서 애플리케이션 보안은 절대적인 우선 순위입니다. 견고하고 안전한 Flutter 앱을 개발하는 것은 사용자 데이터를 보호하고 제품에 대한 신뢰를 유지하는 데 중요합니다. Flutter 애플리케이션 보안 여정을 시작하는 경우, 제 이전 기사를 읽어 보시기를 권장합니다. 해당 기사에서는 Gray Box 모드에서 응용 프로그램의 보안을 보장하기 위한 필수적인 실천 방법을 탐구했습니다(기사 링크).

# 보안 분석: 주요 결론

Flutter 애플리케이션의 보안 평가 중에 시장 경험을 토대로 가능한 취약점을 찾을 수 있는 여러 단계를 식별했습니다. 아래에서 자세히 이야기하도록 하겠습니다.

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

## 인증 검토

애플리케이션에서 사용되는 인증 메커니즘을 조사합니다. 인증 중 강력한 보안 메커니즘의 부재로 사용자 토큰에 민감한 데이터가 평문으로 포함되는 문제가 발생했다고 발견했습니다. 명확히 말하자면, JWT 토큰은 올바른 방식으로 발급되지 않으면 복호화될 수 있습니다. jwt.io 웹사이트에서는 토큰을 붙여넣으면 모든 내부 데이터를 제시해줍니다.

![이미지1](/assets/img/2024-05-18-IncreasesecurityinyourFlutterapplications_1.png)

![이미지2](/assets/img/2024-05-18-IncreasesecurityinyourFlutterapplications_2.png)

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

Figure 1에서는 웹 사이트에 추가된 JWT 토큰을 볼 수 있고, Figure 2에서는 분해된 데이터가 나타납니다. 이 토큰을 해독하는 데 도움이 되는 보안 키의 사용 방식을 강조할 가치가 있습니다. 이 키는 서버만 사용하여 컴파일하고 앱-클라이언트가 분해하는 데 사용됩니다. Figure 3에 표시된 대로입니다.

![Image](/assets/img/2024-05-18-IncreasesecurityinyourFlutterapplications_3.png)

## 액세스 제어의 평가와 문제 해결 방지

또 다른 중요한 점은 프론트엔드의 액세스 제어이며, 여러 중요한 결함을 발견할 수 있는데, 애플리케이션이 안전하지 않은 기기에서 액세스되도록 허용합니다. 즉, 탈옥 또는 루팅된 것으로 간주되는 특정 기기에서 허용됩니다.
이러한 기기는 너무 허용되어 있어 일반적인 운영 체제에서 적용된 보안 제약 사항 없이 작업을 수행하고 응용 프로그램을 설치할 수 있는 것을 의미합니다. 이러한 환경에서는 앱 보안 메커니즘이 쉽게 우회될 수 있어 민감한 데이터 및 중요한 기능이 잠재적인 공격자에게 노출될 수 있습니다. 이는 미약한 기기로부터의 액세스를 차단하고 탐지하기 위해 프론트엔드에 보다 엄격한 보안 제어를 구현해야 함을 강조합니다.
Flutter에서 액세스 제어 결함을 극복하고 안전하지 않은 기기에서 애플리케이션을 보호하기 위해 여러 전략을 채택할 수 있습니다. 이 기기들을 감지하는 것이 중요합니다. Flutter에는 기기의 탈옥 또는 루팅 여부를 확인하는 데 도움이 되는 특정 라이브러리가 있습니다. 탈옥 또는 루팅된 기기를 감지하면 사용자에게 경고 메시지를 표시하고 액세스를 차단하여 앱이 작동하지 않도록 할 수 있습니다.
또 다른 중요한 조치는 애플리케이션이 실행되는 환경의 무결성을 확인하는 것입니다. 이는 에뮬레이터를 감지하고 응용프로그램 바이너리가 변경되지 않았는지 확인하는 것을 포함할 수 있습니다. 이를 위해 디지털 서명 및 체크섬 확인 기술을 사용하여 애플리케이션 코드가 변경되지 않도록 보장할 수 있습니다.

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

또한, 로컬에 저장된 모든 민감한 데이터를 암호화하는 것이 중요합니다. 안전한 암호화 라이브러리를 사용하면 데이터가 평문으로 저장되지 않아 기기가 침해당해도 민감한 정보를 보호할 수 있습니다. 예를 들어, flutter_secure_storage 패키지는 Flutter에서 데이터를 안전하게 암호화하여 저장하는 방법을 제공합니다.

마지막으로, 엄격한 서버 측 보안 정책을 시행하는 것이 중요합니다. 서버가 클라이언트의 확인이나 조치와는 독립적으로 모든 요청을 유효성을 검사하도록 하는 것이 필수적입니다. 서버는 설정된 보안 기준을 충족하지 않는 요청을 거부해야 하며, 가능한 공격에 대비한 추가적인 보호층을 추가해야 합니다.

## 방지 방탈림 메커니즘의 실패

또 다른 중요한 점은 안드로이드 애플리케이션을 디컴파일할 때 소스 코드를 변경하고 애플리케이션을 다시 컴파일할 수 있는지 확인하는 것입니다. APKTOOL과 같은 도구를 사용하여 APK를 디컴파일하고 획득한 Java 코드를 수정할 수 있습니다. 이 과정 이후에 텍스트 편집기인 Sublime Text와 같은 도구를 사용하여 변수를 변경할 수 있었습니다. 코드를 변경한 후에는 같은 APKTOOL을 사용하여 애플리케이션을 다시 컴파일하고 서명할 수 있었습니다. 이러한 프로세스가 완료된 후 수정된 소스 코드가 재컴파일된 애플리케이션에 적용되었는지 확인할 수 있습니다.

이 취약점은 공격자가 역공학을 통해 애플리케이션의 내부 작업을 이해할 수 있게 하여 보안 메커니즘에 대한 공격을 개발할 수 있는 것을 의미합니다. 공격자는 왓츠앱이나 유튜브와 같은 앱에서 현재 매우 일반적인 사용자가 보낸 메시지를 삭제하지 못하도록 막거나 광고를 표시하는 등의 미인증된 새로운 기능을 추가할 수 있으며, 데이터 유출, 회사의 평판 및 이미지 훼손, 사기까지 발생할 수 있습니다.

이러한 위험을 완화하기 위해 응용 프로그램이 컴파일 중에 수행한 코드 변경을 런타임에서 감지할 수 있어야 합니다. 응용 프로그램은 침해된 환경을 식별하고 서버에 위반 사항을 보고하거나 응용 프로그램을 종료함으로써 적절히 대응해야 합니다. 이러한 조치를 구현하는 것은 방탈림 시도에 대한 응용 프로그램의 보호와 사용자 데이터의 무결성 및 보안을 보장하는 데 중요합니다.

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

이제까지 찾아낸 몇 가지 포인트들이 있어요. Flutter 애플리케이션에서 보안에 대해 더 알아보기를 권유합니다. 제공되는 자원을 활용하고 사이버보안 워크샵, 강좌 및 포럼에 참여해보세요. 보안은 공동 책임이며, 디지털 세계의 계속 변화하는 도전에 대비하기 위해 우리 모두가 교육받고 준비되어야 합니다.
기억하세요, 보안은 목적지가 아니라 계속되는 여정입니다. 함께 하면 Flutter 앱을 모든 사용자들을 위해 더 안전하고 안전하게 만들 수 있습니다.
공부하고 배우고, 애플리케이션을 통해 더 안전한 디지털 미래를 만들어 봅시다.

도움이 되었나요? 커피 사주시겠어요?

저를 팔로우해주세요.

- Linkedin: raphaelkennedy
- Youtube: raphaelpontes
