---
title: "저장된 XSS를 통해 사용자 쿠키를 탈취한 방법"
description: ""
coverImage: "/assets/img/2024-07-01-HowIwasabletostealusercookiesviastoredXSS_0.png"
date: 2024-07-01 15:51
ogImage: 
  url: /assets/img/2024-07-01-HowIwasabletostealusercookiesviastoredXSS_0.png
tag: Tech
originalTitle: "How I was able to steal user cookies via stored XSS"
link: "https://medium.com/@0x_xnum/how-i-was-able-to-steal-cookies-via-stored-xss-c7f172fe114c"
---


테이블 태그를 마크다운 형식으로 변경해주세요.

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

저는 사용자 계정과 상점 계정 두 개를 만들었어요.

상점 계정에서 "Ahmeee"라는 랜덤 카테고리 아래에 "ahmed"라는 상점을 만들었어요.

새 제품을 추가하러 가서 새 제품을 추가했는데, 제품에 일반적인 이름 대신 XSS payload를 시도해 봤어요.

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

이렇게 사용했어요:


`img src="invalid-image" onerror="alert(document.cookie)"`


![image](/assets/img/2024-07-01-HowIwasabletostealusercookiesviastoredXSS_2.png)

그러고 나서 "제품 추가"를 클릭하고 내 사용자 계정으로 이동했어요.

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

사용자 계정에서 내 가게를 방문하여 "Ahmeee" 카테고리를 클릭했어요.

![image1](/assets/img/2024-07-01-HowIwasabletostealusercookiesviastoredXSS_3.png)

그런 다음 XSS 페이로드로 주입한 제품을 발견했어요!

![image2](/assets/img/2024-07-01-HowIwasabletostealusercookiesviastoredXSS_4.png)

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

제가 제품을 클릭한 후 "장바구니에 추가"를 클릭했더니, XSS 페이로드가 실행되어 사용자 세션 쿠키가 표시되었어요!

![이미지](/assets/img/2024-07-01-HowIwasabletostealusercookiesviastoredXSS_5.png)

영향: 이 취약점으로 인해 공격자가 사용자 쿠키를 획득하여 사용자 계정에 무단 액세스할 수 있습니다.

권장 사항: 이러한 공격을 방지하기 위해 적절한 입력 방지 및 출력 인코딩을 구현해야 합니다. 또한 콘텐츠 보안 정책(CSP)을 배포하여 이 취약점을 줄일 수 있습니다.

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

# 배운 교훈!

보이는 모든 입력란에 XSS 페이로드를 주입하세요; 아마도 작동할지도 몰라요!

**편집**

그 프로그램은 사기였고 신고에도 답변이 없어요. 발견하면 피하세요, "dukaan.com"이에요.

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

**수정**

<img src="/assets/img/2024-07-01-HowIwasabletostealusercookiesviastoredXSS_6.png" />

더 많은 Write-Ups을 보려면 소셜 미디어에서 팔로우해주세요

# 연락처: