---
title: "안드로이드 앱 부정 방지 프로모션 남용을 막기 위한 최적의 디바이스 ID 선택 방법"
description: ""
coverImage: "/assets/img/2024-06-22-Fraud-ProofinganAndroidAppChoosingtheBestDeviceIDforPromoAbusePrevention_0.png"
date: 2024-06-22 23:17
ogImage: 
  url: /assets/img/2024-06-22-Fraud-ProofinganAndroidAppChoosingtheBestDeviceIDforPromoAbusePrevention_0.png
tag: Tech
originalTitle: "Fraud-Proofing an Android App: Choosing the Best Device ID for Promo Abuse Prevention"
link: "https://medium.com/@talsec/fraud-proofing-an-android-app-choosing-the-best-device-id-for-promo-abuse-prevention-aa4a2459637f"
---


⚡주요 요점:

- 프로모션 남용은 비즈니스의 가입 보너스, 추천, 쿠폰 또는 프로모션을 악용하는 사기 유형입니다.
- 가능한 경우에는 MediaDRM을 디바이스 지문보다 선호해야 합니다.
- 우리의 연구 결과, 블록리스트에 가장 적합한 최고의 디바이스 ID는 MediaDRM+디바이스 모델의 결합입니다.
- 모바일 앱 백엔드 API를 보호하는 AppiCrypt(앱 보호), RASP(앱 쉴딩) 및 KYC 솔루션(고객 신원 확인)과 같은 여러 보안 계층을 항상 포함해야 합니다.
- 특정 시나리오에 따라 다른 접근 방식이 필요할 수 있다는 것을 염두에 두세요. info@talsec.app으로 메시지를 남기면 Talsec 보안 전문가가 도와드릴 것입니다.

# 사용자 파악 방법?

최근에 모바일 디바이스 식별에서 어려움에 부딪혔습니다 - 사용자 개인정보를 침해하지 않으면서 사기적인 디바이스를 식별하고 블록리스트에 올리는 방법은 무엇일까요. 이 문제는 결제 회피 또는 다양한 보너스를 악용하는 사용자들과 빈번히 대면하는 모바일 애플리케이션 소유자에게 특히 중요합니다.

<div class="content-ad"></div>

초기 가입 시 사용자들을 유혹하는 매력적인 보너스로 사용자를 유치하는 것은 흔한 일이지만, 이 전략은 내재적인 위험을 안고 있으며 남용될 수 있다는 점을 인지하는 것이 중요합니다. 이 악의적인 사용자들은 앱을 여러 번 다시 설치하여 계속해서 가입 보너스를 얻으려는 행위를 하는데, 이를 "멀티 인스턴싱"이라고 부릅니다.

안드로이드는 각 앱 인스턴스마다 일부 디바이스 ID를 변경하므로, 동일한 악의적 사용자의 디바이스를 차단하기가 어려워집니다. 이는 효과적인 해결책을 찾기 위한 우리의 노력이 복잡함을 강조합니다.

![이미지](/assets/img/2024-06-22-Fraud-ProofinganAndroidAppChoosingtheBestDeviceIDforPromoAbusePrevention_0.png)

# 좋은 ID는 고유하며 충돌 방지, 지속적이고 개인정보 친화적이어야 합니다. 그리고 위조할 수 없어야 합니다.

<div class="content-ad"></div>

과거에는 특별한 권한을 요청하지 않고 MAC 주소나 IMEI를 통해 기기를 식별할 수 있었어요. 오늘날 안드로이드 개인 정보 보호를 위한 다양한 변경 사항 이후, Android 기기에서 AndroidID, MediaDRM, GSF ID, FID 및 InstanceID와 같은 여러 반영구적 ID가 사용 가능해졌어요. 물론, 사용자에게 상승된 액세스 및 잠재적 보안 문제를 가진 권한을 요청하는 것은 현실적이지 않아요.

각 ID에는 장단점이 있어 다양한 시나리오에서 유용하게 사용될 수 있어요. 아래 예시 테이블을 참고하세요. Android 문서에서 ID에 대한 추가 정보를 찾을 수 있어요.

또 다른 방법으로는 다양한 디바이스 지문 라이브러리(예: fingerprintjs-android)를 사용하여 여러 기기 ID, 기기 상태, OS 지문 또는 설치된 앱을 기반으로 ID를 생성할 수 있어요.

<div class="content-ad"></div>

이 ID들을 더 자세히 살펴보겠습니다.

# AndroidID, GSF ID, FID, InstanceID, Google 광고 ID

이러한 식별 방법은 다른 맥락에서는 잘 작동할 수 있지만, 우리의 시나리오에서는 부정한 다중 인스턴싱에 견딜 수 있는 견고함이 부족합니다. 이러한 ID들은 비교적 쉽게 변경할 수 있으므로 각각에 대한 단점에 대해 간단한 설명만 제공하겠습니다.

- AndroidID는 다시 패키징되거나 기기의 다른 사용자로 설치되는 경우 변경됩니다.
- GSF ID (Google Play 서비스 프레임워크 ID)는 Google 기기에만 제한되며 XPrivacyLua에 의해 상대적으로 쉽게 위조될 수 있습니다. 또한 다른 사용자에 대해 변경됩니다.
- FID (Firebase 설치 ID)는 재설치를 견뎌내지 못합니다.
- InstanceID (GUID, UUID.randomUUID().toString())는 사용자 정의 생성 및 내부 저장 ID입니다만, 재설치를 견딜 수 없습니다.
- Google 광고 ID는 전혀 적합하지 않습니다.

<div class="content-ad"></div>

요약하면, 우리의 시나리오에서 나쁜 행위자를 식별하는 데 충분히 견고한 ID는 없습니다.

# fingerprintjs-android 라이브러리 기반의 지문 ID

참고: 전체 분석에서 우리는 상태 없는 오프라인 지문 ID를 위해 종합적인 신호 목록을 사용하는 fingerprintjs-android 라이브러리를 사용했습니다. 다른 지문 ID 라이브러리의 결과는 다를 수 있습니다. 이는 그들의 데이터 인사이트, 지리 위치, IP 위치, TEE 및 다른 요소에 기반한 더 많은 신호와 휴리스틱을 포함할 수 있습니다. Talsec는 fingerprintjs-android V3 및 StabilityLevel.OPTIMAL을 수집합니다. 안정성 수준 (STABLE — OPTIMAL — UNIQUE) 사이의 차이점은 여기에서 찾을 수 있습니다.

위의 표를 다시 한 번 살펴보세요. 첫눈에는 하드웨어 지문 (위의 표 참조)가 최선의 선택일 수 있습니다. 이것은 인스턴트 앱 이벤트를 제외하고는 무엇이든 견딜 수 있습니다. 그러나 이 ID에는 한 가지 심각한 단점이 있습니다 — 충돌입니다. 이 충돌은 ID가 계산되는 방식에 의해 발생합니다. ID는 디바이스의 하드웨어에만 기반하기 때문에 발생합니다. 예를 들어, 조립 라인에서 직접 나오는 모든 삼성 갤럭시 Z 플립이 동일한 ID를 갖게 될 것입니다. 이 유형의 지문은 STABLE 지문이라고 불립니다.

<div class="content-ad"></div>

![2024-06-22-Fraud-ProofinganAndroidAppChoosingtheBestDeviceIDforPromoAbusePrevention_2.png](/assets/img/2024-06-22-Fraud-ProofinganAndroidAppChoosingtheBestDeviceIDforPromoAbusePrevention_2.png)

한편, 고유한 지문(fingerprint)이 있습니다. 이것은 기기의 ID를 계산하기 위해 상당량의 신호를 사용합니다. 이 방식으로 생성된 ID는 하나의 사용자에게만 매칭될 가능성이 높습니다(충돌이 적음), 그러나 신호가 많기 때문에 ID가 자주 변경될 수 있습니다(예: 설정 변경시). 따라서 하나의 사용자에게 많은 ID가 생길 수 있어 우리의 사용 사례에는 유용하지 않습니다. 예: 설치된 앱이 변경되거나 데이터 로밍이 활성화/비활성화될 때 ID가 변경될 수 있습니다.

세 번째 유형의 지문은 STABLE과 UNIQUE 지문 사이의 절충안인 OPTIMAL 지문입니다. 이 지문은 STABLE 지문보다는 덜 안정적이지만 더 고유하며 Talsec SDK에 의해 수집됩니다. 그러나 이 유형의 지문조차 이 기사에서 나중에 보여줄 것처럼 최적적이지 않습니다. 예: 사용자가 12시 및 24시간 형식으로 전환하거나 개발자 설정 또는 ADB가 활성화/비활성화될 때 ID가 변경될 수 있습니다.

지문 예시: `f37fc958dc6d566a8f4bf1e0fd25b510`

<div class="content-ad"></div>

# MediaDRM

MediaDrm은 프리미엄 콘텐츠 재생을 위해 암호화 키를 안전하게 제공하는 안드로이드 API입니다. Google의 Widevine 및 Microsoft의 PlayReady와 같은 DRM 공급자를 사용합니다. 초기 DRM 사용 중에는 장치 프로비저닝을 통해 장치의 DRM 서비스에 저장된 고유한 인증서를 획득합니다.

이 API에서 제공되는 MediaDRM은 장치 상의 모든 사용자에게 동일하지만, 저희 상황에서는 위조하기 어렵고 많은 공격에도 견딜 수 있습니다. 이 ID를 얻으려면 권한이 필요하지 않습니다.

하지만 여전히 제한 사항이 있습니다. MediaDrm을 지원하지 않는 장치에서는 사용할 수 없을 수 있습니다. 또한 동일한 제조업체의 장치 간에는 충돌이 많이 발생할 수 있습니다.

<div class="content-ad"></div>

MediaDRM 예시: `e3af1aa4dacb6b6637846488b511e7643c6ac20b65c95baad164b122ecb036b6`

# MediaDRM vs 지문 ID?

우리는 다섯 대의 기기와 에뮬레이터를 여러 개의 다중 인스턴싱 시나리오에서 테스트하고 ID가 변경되었는지 여부를 확인했습니다. 가장 흥미로운 부분은 MediaDRM과 Fingerprint (V3 & V5 Optimal)입니다. 그래서 우리는 특히 이 부분에 주의를 기울였습니다.

다중 인스턴싱 시나리오:

<div class="content-ad"></div>

- 앱의 첫 설치
- 앱 재설치
- 작업 프로필에 설치
- Island 앱을 사용하여 앱의 복제
- Parallel Space를 사용하여 앱의 복제
- 병렬 애플리케이션을 사용하여 앱의 복제
- 두 번째 공간(Xiaomi)을 사용하여 앱의 복제
- 손님 프로필에 설치
- 공장 초기화
- 안드로이드 에뮬레이터 대 실제 기기

이 지루한 작업은 훌륭한 지문 OSS 데모 도구 덕분에 더 쉽게 진행되었습니다.

<img src="/assets/img/2024-06-22-Fraud-ProofinganAndroidAppChoosingtheBestDeviceIDforPromoAbusePrevention_3.png" />

가장 중요한 관찰 결과를 여기에 나열하였습니다. 모든 테스트를 항상 수행할 수는 없었기 때문에 사소한 부분들은 모두 다시 시도했습니다 - 이는 공격자들이 시도할 것이기 때문입니다.

<div class="content-ad"></div>

유지한 것 (= 좋은):

- 미디어 DRM은 처음 설치 시 OnePlus 8 Pro의 Island App에서 동일하게 유지되었습니다.
- 미디어 DRM은 Redmi Note 10 Pro의 두 번째 공간에서 동일하게 유지되었습니다.
- 미디어 DRM은 OnePlus 8 Pro에서 공장 초기화 후에도 동일하게 유지되었습니다.
- 미디어 DRM은 OnePlus 8T에서 처음 설치, 작업 프로필 및 여러 사용자에 대해 동일하게 유지되었습니다.
- 첫 설치 및 병렬 공간에서의 OnePlus 8T의 Fingerprint V5 Optimal이 동일하게 유지되었습니다.
- Redmi Note 10 Pro에서 다시 설치 후에도 미디어 DRM 및 Fingerprint V3 및 V5 Optimal이 동일하게 유지되었습니다.
- OnePlus 8T의 첫 설치, 작업 프로필 및 병렬 공간에서 Fingerprint V5 Optimal이 다시 설치 후에도 동일하게 유지되었습니다.

변경된 것 (= 나쁜):

- 처음 설치 및 Island App에서 Fingerprint V5 Optimal이 변경되었습니다.
- OnePlus 8 Pro에서 공장 초기화 후 Fingerprint V5 Optimal이 변경되었습니다.
- Redmi Note 10 Pro의 두 번째 공간에서 Fingerprint V5 Optimal이 변경되었습니다.
- OnePlus 8T의 병렬 공간에서 미디어 DRM이 변경되었습니다.
- OnePlus 8T의 작업 프로필 및 게스트 사용자에서 Fingerprint V5 Optimal이 변경되었습니다.

<div class="content-ad"></div>

다른:

- 에뮬레이터에서 FingerprintV3 Optimal이 Fingerprint V5 Optimal보다 성능이 더 좋았습니다.
- Emulator 1과 Emulator 2에서 MediaDRM이 다르게 동작했는데 둘 다 동일한 Windows 기계에서 실행되었습니다.

이러한 관찰을 바탕으로 Fingerprint V3과 V5 Optimal은 MediaDRM과 비교했을 때 많은 다중 인스턴스 사기 시나리오에서 실패했습니다. 이러한 테스트에서 우리는 MediaDRM이 더 나은 것으로 결론지었습니다.

# 원시 데이터 분석

<div class="content-ad"></div>

결과를 양적으로 파악하기 위해 우리는 데이터를 살펴보고 해당 ID들의 행동을 평가하여 데이터를 기반으로 가장 적합한 ID를 찾아냈습니다. 사용자 기반의 기기와 비교하여 우리의 데이터가 왜곡되어 있고 대표적이지 않을 수 있다는 점을 기억해 주세요.

## 2주간의 데이터 수집

우리는 2주 동안 freeRASP 데이터를 수집하여 분석했습니다. 이 기간이 비교적 짧기 때문에 다시 설치하는 경우가 많지 않다고 가정합니다. 다시 한 번 강조하지만, 특정 응용프로그램의 카테고리/사용 사례에 따라 실제 재설치 비율이 달라질 수 있으므로 이 창을 선택하는 데 실제 재설치 비율에 대한 연구가 없다는 점을 유의해 주세요.

아래에서 각 ID의 고유 값 수와 이 데이터에서 캡처된 고유 디바이스 모델 수를 확인할 수 있습니다.

<div class="content-ad"></div>

AndroidID: 13 402 601

FingerprintV3: 22 525 265

MediaDRM: 13 285 081

InstanceId: 13 740 706

<div class="content-ad"></div>

다양한 기기 모델: 14,175 (예: Pixel 4, SM-G973N, ONEPLUS A5000, LG-H930, ...)

처음에 봤을 때 다른 ID들의 수보다 훨씬 높은 FingerprintV3의 수를 알아차렸어요. 이것은 사용자가 32가지 관측된 지문 신호 중 일부를 변경할 때마다 변하는 FingerprintV3의 행동에 의한 것일 수 있어요.

## ID들은 어떻게 관련이 있을까요?

그 후, 우리는 ID들 간의 동시 발생을 살펴보았습니다. 이를 통해 그들 간의 관련성을 파악해 보았어요.

<div class="content-ad"></div>

아래 테이블을 읽는 방법입니다. 하나의 AndroidID에는 1.00557개의 고유한 MediaDRM이 있으며, 고유한 AndroidID 중 0.54%가 하나 이상의 MediaDRM을 가지고 있습니다.

## ID 충돌: 같은 ID지만 다른 기기

데이터에 따르면, 우리는 여전히 "최적" 식별자를 찾고 있기 때문에 "다른 기기"가 무엇인지 말할 수 없습니다.

<div class="content-ad"></div>

아이디 당 평균 모델 수를 살펴봐요. ID마다 모델이 하나만 있는 것이 이상적입니다 — 서로 다른 디바이스에 동일한 ID가 있는 충돌이 최소화되기를 바랍니다. 아래 표를 빠르게 살펴보면 진정한 불일치가 있음을 알 수 있어요.

![표](/assets/img/2024-06-22-Fraud-ProofinganAndroidAppChoosingtheBestDeviceIDforPromoAbusePrevention_5.png)

## 결과

데이터 분석 결과, 다음과 같이 진술할 수 있어요:

<div class="content-ad"></div>

- FingerprintV3는 다른 ID들에 비해 값이 너무 많아서 우리 시나리오에서는 덜 유용합니다.
- 하나의 AndroidID/MediaDRM/InstanceID에는 보통 여러 개의 FingerprintV3가 있습니다.
- AndroidId와 MediaDRM은 대략 1:1 관계이며, 일부 MediaDRM 인스턴스에는 AndroidID보다 더 많은 AndroidID (AndroidId보다 더 많은 MediaDRM을 가지고 있는 경우도 있습니다).
- 경우에 따라 하나의 AndroidID에는 여러 개의 InstanceID (MediaDRM보다 더 자주 발생), MediaDRM과 InstanceID 사이의 관계와 유사합니다.
- InstanceID는 MediaDRM보다 AndroidID와 더 밀접한 관계에 있습니다.
- AndroidID는 하나의 모델만 가지고 있습니다 (이상치의 양은 극히 적음).
- MediaDRM은 일반적으로 하나의 모델을 가지나, 몇 가지 충돌이 있을 수 있습니다 (AndroidID의 경우보다 더 많음).
- InstanceId는 이 두 모델 사이 어딘가에 위치합니다.

총평으로, 이러한 식별자 중에서 AndroidID가 가장 우수해 보이며, MediaDRM이 그 뒤를 이어갑니다. InstanceId도 유용할 수 있지만 AndroidID만큼은 아닙니다. FingerprintV3은 우리 시나리오에서는 쓸모 없습니다. AndroidID는 다시 설치 후 변경되고 상대적으로 쉽게 위조될 수 있기 때문에 사기 탐지에는 MediaDRM이 가장 적합해 보입니다.

다만, MediaDRM은 충돌이 꽤 많이 발생하는 것 같습니다 (모델 분석을 기반으로 확인한 결과). 같은 제조업체의 기기끼리(즉, 동일 제조업체의 기기일수록 다른 제조업체의 기기보다 더 같은 MediaDRM을 가질 가능성이 훨씬 높음) 충돌이 가장 자주 발생하는 것으로 밝혀졌습니다. 여기에 전반적인 개요를 제공해 드리겠습니다:

- MediaDRM 중 0.005%가 여러 제조업체를 가지고 있음
- MediaDRM 중 0.55%가 같은 제조업체의 여러 모델을 가지고 있음
- 한 MediaDRM 당 평균 제조업체 수: 1.000085
- 한 제조업체 당 MediaDRM의 평균 모델 수: 1.006362

<div class="content-ad"></div>

## MediaDRM를 개선할 수 있을까요?

우리는 많은 시도(자세히 설명하지는 않겠습니다) 끝에 MediaDRM+모델의 조합을 잠재적 ID로 실험해 보았습니다.

구글 Pixel 4의 MediaDRM+모델의 결합 예시: e3af1aa4dacb6b6637846488b511e7643c6ac20b65c95baad164b122ecb036b6+Pixel 4

아래는 위와 동일한 공현 테이블이며, 이제 MediaDRM+모델과의 관계를 포함하고 있습니다:

<div class="content-ad"></div>


![Image](/assets/img/2024-06-22-Fraud-ProofinganAndroidAppChoosingtheBestDeviceIDforPromoAbusePrevention_6.png)

우리는 MediaDRM+ 모델이 원래의 MediaDRM보다 더 나은 성능을 보여준다는 것을 확인할 수 있습니다. 각 MediaDRM+ 모델에는 연관된 다른 ID의 수가 더 적습니다. 이는 몇 가지 충돌을 피했다는 것을 의미합니다 (정확한 숫자를 측정하기는 어렵지만, 최소한의 경계는 MediaDRM과 MediaDRM+ 모델의 숫자로 추정됩니다).

두 가지 특성의 조합으로 ID를 만들 때, 한 기기가 더 많은 ID를 가지는 문제가 발생할 수 있습니다. 그러나 MediaDRM+ 모델에서는 이런 경우가 발생하지 않아야 합니다. 한 기기에는 하나의 모델만 연결되어야 하기 때문에 (즉, 구글 픽셀 4 전화기의 물리적 단위는 항상 "Pixel 4" 모델 이름만을 가져야 함).

그러므로 데이터를 기반으로, 사기 탐지 사례를 검토할 때 MediaDRM과 기기 모델의 간단한 조합을 ID로 사용하는 것을 제안합니다.


<div class="content-ad"></div>

# 요약

저희는 모바일 기기 식별에 대한 도전에 직면하고 있습니다 — 사용자 개인 정보를 침해하지 않고 사기꾼 기기를 차단하는 방법에 대해 고민하고 있습니다. 모바일 앱 소유자들은 사용자들이 결제를 회피하거나 보너스를 악용하는 문제에 직면하고 있는데, 이는 여러 번 앱을 재설치함으로써 이루어지는 다중 인스턴스화로 인한 문제입니다. 신뢰할 수 없는 기기 ID로 인해 꾸준한 악의적인 사용자를 식별하는 것이 어려워지고 있습니다. 저희의 연구 결과는 효과적인 차단을 위해 미디어 DRM을 기기 지문보다 우선시하는 것을 권장하며 (또는 더 나아가 미디어 DRM과 기기 모델 조합을 권장합니다). 또한 AppiCrypt, RASP 및 KYC 솔루션과 같은 추가적인 보안층으로 보호 강화를 잊지 마세요. 모든 시나리오는 유니크합니다. 맞춤형 조언이 필요하시면 info@talsec.app 으로 Talsec 보안 전문가에게 문의하세요.

저자: Dáša Pawlasová, Matúš Šikyňa, Tomáš Soukal