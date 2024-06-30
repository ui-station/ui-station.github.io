---
title: "GSoC 2024  OpenMRS 5주차  환자 생성 및 리소스 동기화"
description: ""
coverImage: "/assets/img/2024-06-30-GSoC2024OpenMRSWeek5PatientCreationSyncedResources_0.png"
date: 2024-06-30 19:30
ogImage: 
  url: /assets/img/2024-06-30-GSoC2024OpenMRSWeek5PatientCreationSyncedResources_0.png
tag: Tech
originalTitle: "GSoC 2024 @ OpenMRS: Week 5 | Patient Creation , Synced Resources🔄"
link: "https://medium.com/@panchalparth_91743/gsoc-2024-openmrs-week-5-patient-creation-synced-resources-47d7b16516d5"
---


안녕하세요 여러분! 🌟

모두가 멋진 하루를 보내고 계시길 바랍니다! 이번 주는 몇 가지 중요한 발전이 있는 흥미로운 여정이었습니다. 강조할만한 부분을 알아보도록 하죠!

![이미지](/assets/img/2024-06-30-GSoC2024OpenMRSWeek5PatientCreationSyncedResources_0.png)

# OpenMRS에서 FHIR 엔드포인트를 활용한 환자 생성 🏥

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

환자를 만드는 도전에 도전 중이었어요. 특히 FHIR 엔드포인트를 사용하는 것이 너무 어려웠어요. 이유는 Patient 리소스에 고유한 환자 식별자를 제공해야 했기 때문이에요. 저희의 여정을 간략히 소개할게요:

# 시도한 방법:

- Idgen 엔드포인트 방법: 이 방법은 idgen 엔드포인트를 호출하여 고유한 ID를 생성하고 이를 환자 리소스 페이로드와 함께 포함하는 방법이었어요. 웹 앱에선 간단하지만 오프라인 우선 애플리케이션에는 최적이 아니었어요.
- HSU ID 방법: 이 방법은 요청 페이로드에 위치 ID를 사용하여 엔드포인트를 호출하여 세션 ID를 가져오는 것을 요구했어요. 이 세션 ID에는 사용자의 위치 정보가 포함되어 있어 쿠키로 전달되어야 했어요. 그러나 RESTful API에 대한 세션 유지는 이상적이지 않았어요.
- Legacy-ID 식별자 솔루션: 마침내 legacy-id 식별자를 사용하기로 결정했어요. 이 방법은 값만 필요로 하기 때문에 UUID를 제공하여 고유성을 보장할 수 있어요. 연간 약 10만 명의 사용자에게 안전한 방법이에요.

# 예시 FHIR 환자 리소스:

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

엔드포인트:

![img](/assets/img/2024-06-30-GSoC2024OpenMRSWeek5PatientCreationSyncedResources_1.png)

리소스:

```js
{
  "resourceType": "Patient",
  "meta": {
    "versionId": "1"
  },
  "name": [
    {
      "use": "official",
      "family": "Jane Doe",
      "given": [
        "10000032"
      ]
    }
  ],
  "gender": "female",
  "birthDate": "1928-05-06",
  "identifier": [
    {
      "use": "official",
      "type": {
        "text": "Legacy ID"
      },
      "value": "dd1819e6-b0b7-4286-8941-a82a0a0fceb0"
    }
  ],
  "address": [
    {
      "use": "home",
      "city": "Unknown City",
      "state": "Unknown State",
      "postalCode": "Unknown PostalCode",
      "country": "Unknown Country"
    }
  ],
  "active": true,
  "deceasedBoolean": false
}
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

# 동기화된 자원 기능 🔄

저희 애플리케이션에서 환자가 등록되면 로컬에 저장됩니다. 사용자가 서버와 동기화할 때만 환자가 업로드됩니다. 데이터 손실을 피하기 위해 사용자가 환자 데이터가 동기화되었는지 알 수 있어야 합니다.

![이미지](/assets/img/2024-06-30-GSoC2024OpenMRSWeek5PatientCreationSyncedResources_2.png)

# 구현 단계:

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

- Unsynced 환자 식별: 저는 FhirEngine의 localChanges API를 사용하여 동기화되지 않은 환자를 식별하기 위한 로직을 개발했습니다. 이 API는 FHIR 자원에 적용된 로컬 변경 목록을 반환합니다. 환자 목록을 가져온 후에는 각 환자에 대한 로컬 변경 목록이 비어 있는지 확인하여(동기화된 자원을 나타냄) 이 정보를 PatientItem의 isSynced 플래그로 저장합니다.
- UI 업데이트: 이 데이터를 사용하여 UI가 동기화 상태를 적절하게 반영합니다.

# PR 링크:

여기서 구현 상세 내용을 확인하세요.

# 다음 주 계획 🔧

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

- 환자 생성 테스트 및 통합: 환자 생성 프로세스가 매끄럽게 작동하는지 확인합니다.
- 환자 편집 기능: 환자 편집 기능을 추가 및 개선합니다.
- UI 업데이트: 더 많은 화면에서 사용자 인터페이스를 개선하여 더 나은 사용자 경험을 제공합니다.

더 많은 업데이트를 기대해주시고, 이 흥미진진한 여정을 따라와 주셔서 감사합니다! 🎉

독자 여러분, 읽어 주셔서 감사합니다! 저에게 문의할 사항이 있으시면 언제든지 연락해주세요! 특히 헬스케어, 풀스택 개발, FHIR, OpenMRS Android, Open Health Stack, 노래 추천 등이라면 더욱 환영합니다. 🎵

LinkedIn이나 OpenMRS Talk에서 저를 찾아보시고 GitHub에서 제 프로젝트를 확인해보세요.