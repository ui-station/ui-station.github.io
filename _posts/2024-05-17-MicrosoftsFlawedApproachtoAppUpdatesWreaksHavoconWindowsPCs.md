---
title: "마이크로소프트의 응용 프로그램 업데이트 접근 방식의 문제가 윈도우 PC에 혼란을 야기합니다"
description: ""
coverImage: "/assets/img/2024-05-17-MicrosoftsFlawedApproachtoAppUpdatesWreaksHavoconWindowsPCs_0.png"
date: 2024-05-17 18:58
ogImage:
  url: /assets/img/2024-05-17-MicrosoftsFlawedApproachtoAppUpdatesWreaksHavoconWindowsPCs_0.png
tag: Tech
originalTitle: "Microsoft’s Flawed Approach to App Updates Wreaks Havoc on Windows PCs"
link: "https://medium.com/pcmag-access/microsofts-flawed-approach-to-app-updates-wreaks-havoc-on-windows-pcs-8afb2555b61a"
---

![image](/assets/img/2024-05-17-MicrosoftsFlawedApproachtoAppUpdatesWreaksHavoconWindowsPCs_0.png)

안녕하세요! WinRAR을 설치해 두셨나요? PC가 취약할 수 있습니다. 이 문제의 근본은 Windows 8로 돌아갑니다.

Chris Hoffman 작성

여전히 적극적으로 악용되고 있는 방대한 보안 결함을 가진 WinRAR은 자동 업데이트를 지원하지 않는 많은 Windows 애플리케이션 중 하나입니다. 개발자는 전 세계적으로 5억 개 이상의 WinRAR 설치를 자랑하며, 따라서 수억 대의 PC가 오늘날 악성 ZIP 파일로부터 취약할 수 있습니다.

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

2023년에 세계에서 가장 인기 있는 데스크톱 운영 체제가 설치된 응용 프로그램을 쉽게 업데이트할 수 있는 방법을 제공하지 않는다니, 어떻게 된 일인가요?

Windows 업데이트는 보안 패치를 설치하지만 여기서 그치고 있어요. 다운로드한 많은 응용 프로그램은 스스로 업데이트를 수행하지만 일부는 업데이트를 확인할 생각조차 못 합니다. Microsoft가 Windows 8을 만드느라 많은 시간을 낭비하고 나서 그 이후 Windows 10으로 넘어가며 거의 모든 Windows 사용자가 원하지 않았던 유형의 애플리케이션을 위한 스토어를 구축하려고 했으면, PC 사용자는 현명한 결정을 할 수 있었을 것입니다. Windows 스토어에 대해 잃어버린 십 년이었죠.

# WinRAR이 공격 받고 스스로 업데이트하지 않아요

파일 아카이빙 프로그램 WinRAR의 결함은 특수하게 디자인된 ZIP 아카이브를 다운로드하고 열면 공격자가 PC에서 원하는 코드를 실행할 수 있다는 것을 의미합니다.

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

공격자들이 2023년 초부터 이 버그를 악용해왔습니다. WinRAR 개발사 RARLAB은 8월에 이 취약점을 수정한 업데이트를 출시했지만, 수개월 후에도 여전히 이 버그에 대해 언급 중입니다. 왜냐하면 정부 지원 사이버 범죄 조직을 포함한 기관들이 여전히 이를 악용하고 있기 때문입니다. 이는 구글의 위협 분석 그룹에 따르면 "여러 국가의 그룹들"이라고 합니다.

문제는요: WinRAR은 자동으로 업데이트되지 않습니다. 심지어 업데이트를 확인하고 사용자에게 중요한 보안 업데이트가 가능하다고 알리지도 않습니다. 많은 사람들이 오래된 업데이트되지 않은 버전의 WinRAR을 사용하고 있으며 이 패치를 설치하지 않을 것입니다. 새로운 PC를 구입하고 다시 설치할 때에만 안전한 버전의 WinRAR을 얻을 것입니다. 소망하기를 그들이 악의적인 ZIP 파일을 열지 않았으면 좋겠네요.

# WinRAR이 자동 업데이트되지 않는 이유는?

그렇다면 왜 자동 업데이트 기능이 없는 걸까요? 저는 RARLAB에 연락을 취해보았고 WinRAR 개발자인 Eugene Roshall은 Windows가 웹사이트에서 다운로드한 데스크톱 앱을 자동으로 업데이트할 수 있는 방법을 제공하지 않는다고 말했습니다. "모든 개발자는 모든 보안 및 기술적 문제를 고려하여 바퀴를 다시 발명해야 합니다."

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

로샬은 RARLAB이 업데이트 알림을 구현하는 것을 고려해 왔지만 기업 시스템 관리자들이 이 아이디어를 좋아하지 않고 사용자 컴퓨터에 팝업 알림이 나타나는 대신 소프트웨어 업데이트에 대해 중앙 집중식 접근을 선호한다고 말했습니다.

하지만 RARLAB은 어쨌든 이 기능을 추가할 수도 있으며 해당 회사는 "Avast, Kaspersky 및 기타 업데이트 프로그램과 긴밀히 협력하고 있다"고 말했습니다.

평균적인 Windows PC의 업데이트 상황은 정말 난잡합니다. 어도비, 구글, PC 제조업체 및 다른 개발자들로부터 별도의 업데이트 서비스를 가질 가능성이 매우 높습니다. 그것이 작동하더라도 각 개발자에게는 고통이 될 뿐만 아니라 CPU 및 메모리 자원을 소모하는 많은 불필요한 백그라운드 프로세스가 있습니다.

저는 개인적으로 항상 7-Zip을 선호해 왔지만, 그것도 내장된 업데이트 확인기가 없습니다!

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

# 윈도우 8 문제의 시작

윈도우 8에 '앱 스토어'가 포함될 예정이라고 처음 들었을 때, 저는 흥분했습니다.

데스크톱 리눅스에 대한 경험이 있는 사람으로서, 제가 가장 좋아하는 것 중 하나는 패키지 관리자입니다. 리눅스에서는 개발자 웹사이트에서 각 애플리케이션을 다운로드하는 대신 패키지 관리자를 통해 애플리케이션을 얻습니다. 업데이트가 나오면 패키지 관리자가 업데이트를 찾아 설치해줍니다. 중앙에서 관리되며 한 애플리케이션이 모든 애플리케이션 업데이트를 확인하고 설치합니다.

2011년 마이크로소프트 빌드에서 최초로 발표된 Windows Store는 윈도우 8에서 그런 종류의 경험을 제공할 수 있었을 것입니다. 그러나 마이크로소프트는 새로운 '메트로 앱'을 위한 새로운 스토어만을 제공하기로 결정했습니다. (그러나 개발자들은 전통적인 데스크톱 앱에 대한 목록을 게시하고 직접 PC 사용자를 웹사이트로 이동할 수 있었습니다.)

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

그보다 나쁜 것은, Windows 8 스토어가 한 때 사기 가득했다는 것이었습니다. "VLC" 같은 것을 검색하면 VLC를 다운로드할 위치를 보여주는 애플리케이션을 지불하라는 저금급한 결과물들로 가득찼습니다. 이 문제를 강조한 첫 번째 사람은 아니었지만, 2014년에 이 사기에 대해 강조한 것은 어디서나 Windows 사용자들의 불만을 대변했습니다. 결국, Microsoft는 2015년에 스토어를 개선하기로 약속하며 일반인들의 압력에 반응했습니다.

# Microsoft은 Windows 10에서 시간을 낭비했습니다

Windows 10은 Windows 8의 많은 문제를 해결했지만, 여전히 Microsoft의 불명확한 앱 전략의 피해자였습니다.

Windows 10이 출시된 시점에도 스토어는 "Universal Windows Platform" 애플리케이션만 허용했습니다. 이것은 합리적이지 않았습니다. 그때쯤에는 Windows Phone이 사라졌으므로, 유니버설 플랫폼은 데스크톱 PC, Xbox 및 HoloLens가 되는 것인가요?

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

Windows 8과 Windows 10에서 PC 사용자들은 상점을 무시하도록 훈련을 받았습니다.

# Windows 11은 너무 늦게 나왔습니다

좋은 소식은 Windows 11이 이를 뒤집었다는 것입니다. 네, Windows 8 출시 9년 후에 마이크로소프트가 마침내 전통적인 Windows 데스크톱 애플리케이션을 데스크톱 PC 운영 체제의 앱 스토어에서 허용해야 한다고 결정했습니다.

이제 개발자들은 전통적인 Windows 데스크톱 앱을 Windows 스토어에 넣을 수 있으며, PC 사용자들은 전통적인 Windows 데스크톱 앱을 설치할 수 있습니다. 스토어를 통해 응용프로그램을 중앙 집중식으로 업데이트할 수 있습니다. 하지만 우리는 다 무시하도록 훈련되었습니다.

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

더 나쁜 것은 최신 Windows 11 PC에서 앱 스토어를 둘러보다가 스토어에 있는 VLC 같은 앱들이 "제공 및 업데이트한 사람"이라고 되어 있는 걸 발견했어요. 그래서 스토어에서 앱을 설치해도, 해당 앱이 여전히 자체 업데이트를 설치할 확률이 높아요.

진짜 안타까운 일이에요. 새 PC를 설정하고 모든 앱을 자동으로 설치할 수 있으면 좋겠고요. 몇십 년 전 데스크톱 리눅스에서 할 수 있었던 것처럼 앱을 업데이트할 수 있는 단일 장소가 있으면 더 좋겠죠.

# 전문가들은 다른 옵션을 가지고 있어요

전문가들은 소프트웨어 업데이터 유틸리티나 winget, Chocolatey 같은 패키지 매니저를 활용할 수 있어요. 그러나 일반 Windows PC 사용자들은 여전히 웹사이트에서 프로그램을 다운로드하고 설치하고 있어요.

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

차트 태그를 마크다운 형식으로 변경해 주세요.

벌써 윈도우 데스크톱에서 이 문제들을 해결하는 최선의 방법이 스토어인지는 아마도 아닐 거예요. 하지만 만약 마이크로소프트가 지난 10년 동안 스토어를 진지하게 대하고 일반 PC 사용자와 그들이 실제로 사용하는 애플리케이션을 위해 사용할 만한 수준으로 만들려고 노력했다면, 우리는 해결책에 훨씬 가까웠을 거에요.

적어도 윈도우 11은 RAR, 7Z 및 다른 아카이브 형식에 대한 내장 지원을 얻었기 때문에 WinRAR 및 7-Zip과 같은 애플리케이션을 업데이트하는 걱정 없이 제거할 수 있게 됐어요. 제 생각에는 그것이 발전이에요.

원문 게시 위치: https://www.pcmag.com.
