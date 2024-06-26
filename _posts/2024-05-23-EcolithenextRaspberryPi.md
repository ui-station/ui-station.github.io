---
title: "Ecoli, 다음 라즈베리 파이"
description: ""
coverImage: "/assets/img/2024-05-23-EcolithenextRaspberryPi_0.png"
date: 2024-05-23 16:08
ogImage:
  url: /assets/img/2024-05-23-EcolithenextRaspberryPi_0.png
tag: Tech
originalTitle: "E.coli, the next Raspberry Pi?"
link: "https://medium.com/@rphlkim/e-coli-the-next-raspberry-pi-9c61a49454f8"
---

상상해보세요 — 작은 로봇들이 당신의 몸 속에서 작동하여 생명을 구할 수도 있는 약을 감지하거나 전달하거나, 바다에서부터 외부 공간까지 적대적인 환경으로 투입한다고 상상해보세요. 다시 말해서, 다양한 유용한 것들을 감지하고 전달하고 모니터링한다고 상상해보세요. 그런데 더 흥미로운 것은, 이 로봇들이 실제로 E.coli와 같은 살아있는 세균이었을 때 어떤 모습일지 상상해보세요? 이것이 어느 정도 우스꽝스러워 들릴 수 있지만, 실제로는 여러분이 생각하는 것보다 조금 더 현실적일 수 있습니다. IoT 영역에서, 더 정확하게 말하면, 미생물 사물 인터넷 (IoBT) 분야에서 연구진들은 이것을 실현하기 위한 진전을 이루고 있습니다.

IoBT는 생물학적 개체와 나노 규모 장치를 혼합하여 통신하는 네트워크를 사용하는 것입니다. 합성 생물학과 나노 기술의 발전 덕분에 IoBT는 다양한 가능한 용도를 갖게 되었습니다. 과학자들은 이러한 바이오-나노 구성 요소 중 하나로 세균을 사용하는 것을 탐구하고 있습니다. 예를 들어, 환경 지속 가능성 분야에서, 이러한 세균은 독소와 오염물질을 감지하고 정보를 수집하며 심지어 자연적인 과정을 통해 환경 정화에 도움을 줄 수 있도록 프로그래밍되어 다양한 장소로 보내질 수 있습니다.

마찬가지로, 의학 및 보건 분야에서 세균은 질병 치료에 도움이 되도록 공학적으로 개발될 수 있습니다. 이 모든 것은 좋고 아름다운데, 그리고 이른바 생물공학의 노동말들로 유용한 물질을 양조하는 데 세균을 사용하는 것은 새로운 것이 아닙니다. 그러나 IoBT의 맥락에서 세균을 커넥터로 취급할 때 흥미로운 일이 벌어집니다.

## 세균을 IoT 장치로 사용하기

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

DNA를 가지고 유용한 호르몬을 포함한 세균들은 인체 내에서 특정 영역으로 이동할 수 있습니다. 목적지에 도착하면 내부 센서에 의해 활성화되어 이러한 호르몬을 생성 및 방출할 수 있습니다. 이러한 잠재적 응용 프로그램은 세균의 감지, 활성화, 통신 및 생물학적 처리 능력을 활용하려는 것으로, 재미있게도 전형적인 컴퓨터 IoT 디바이스의 구성 요소와 유사한 것입니다. 이는 세균을 인터넷 오브 띵스(IoT) 장치의 생체 버전으로 볼 수 있다는 것을 시사합니다. 결과적으로, 세균에 대한 인식 변화는 바이오디자인 및 바이오-HCI(인간 컴퓨터 상호작용) 분야에서 탐구할만한 흥미로운 분야를 열어줍니다.

아마도 의미 있는 방식으로 IoT에서 세균의 역할을 탐색하는 가장 쉬운 방법은 해당 분야의 기존 컴퓨터화된 IoT 장치와 미생물을 비교하는 것입니다(그림 1). 이렇게 하면 세균의 특징을 표준화된 디지털 모델과 비교하여 종의 속성 및 단점을 더 잘 이해할 수 있습니다.

Akyildiz et al. [2]에서 일반적인 생물학적 세포와 전형적인 IoT 장치 간의 공통 요소를 보여주는 삽화가 제시되었습니다. 이제 이미지를 더 좁혀보겠습니다. 그림 1은 E.coli 세균(이하 '세균'이라고 함)과 라즈베리 파이에 추가 구성 요소를 붙인 것을 나타내어 생물학적 및 디지털 세계를 대표합니다. 본 논문에서는 이러한 선택을 의도적으로 한 것으로, DIY 생물학 및 오픈 소스 컴퓨팅의 공통 및 표준화된 '노동말'이기 때문입니다.

따라서 두 가지 모두 의미 있는 참여와 학습을 위한 비용 효율적이고 접근하기 쉬운 도구가 될 수 있는 이상적인 후보입니다. 전체적으로, 그림 1에서 디지털과 생물학적 세계의 과도한 단순화를 인정하지만, 해당 삽화는 세균이 어떻게 프로그래밍되고 실제 상황에서 어떻게 적용될 수 있는지에 대한 중요한 질문을 제기하기 위해 설계되었습니다. 이러한 질문은 안정성, 반응성, 상호작용의 안전성과 더불어 바이오 세균물물(Internet of Bacterial Things, IoBT)이 우리 사회에 미치는 미래적인 도덕적 영향에 대한 중요성을 내포하고 있을 수 있습니다.

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

<img src="/assets/img/2024-05-23-EcolithenextRaspberryPi_0.png" />

## 박테리아 센서 및 구동기

박테리아는 빛, 화학물질, 기계적 스트레스, 전자기장, 온도와 같은 다양한 자극에 반응할 수 있습니다. 이러한 자극에 대응하여 박테리아는 깃털을 이용한 이동(그림 2)이나 녹색 형광 단백질(GFP)과 같은 색소를 포함한 단백질 생산을 통해 상호 작용할 수 있습니다(그림 3).

박테리아 센서 및 구동기의 분자 수준에서의 작동 방식 때문에, 디지털 상대품들과 비교했을 때 그 반응 성, 민감도, 안정성에 큰 차이가 있을 수 있습니다.

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

<img src="/assets/img/2024-05-23-EcolithenextRaspberryPi_1.png" />

## 박테리아 제어 장치, 메모리 및 프로세서

박테리아 안의 DNA는 데이터 저장을 제공하고 실현 가능한 기능으로 해석될 수 있는 지시를 부호화하며, 컴퓨터의 제어 장치(예: '데이터 단위' 및 소프트웨어 조건 표현 단위를 관리하는 개체로서)와 유사한 역할을 합니다. 이러한 이유로, 이는 메모리(예: 포함된 시스템 데이터의 저장) 및 처리 장치(예: 소프트웨어 명령어 실행)와 유사한 역할을 제공합니다. 박테리아에 존재하는 DNA의 주요 형태로는 첫째로 세포 기능에 대한 대부분의 지시 사항을 포함하는 유전체 DNA와 두 번째로 플라스미드라고 불리는 보다 작은 원형 단위가 있습니다. 합성 생물학에서 플라스미드는 종 다양한 유전자를 생물체에 도입하는 데 자주 사용되며, 새 데이터의 비교적 저장 및 기능 맞춤화를 위한 다목적 도구로 사용됩니다.

## 박테리아 송수신기

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

통신의 송수신을 허용하도록 설계된 구성 요소로, 박테리아의 세포막은 송신기로 간주될 수 있습니다. 이는 세포 신호전달 경로의 일환으로 분자의 방출 및 수입에 관여합니다. 게다가 박테리아 필러스(그림 1)는 DNA 교환을 초래하는 두 세포 간의 쌍합 과정에 사용됩니다. 전반적으로 이러한 유형의 통신은 분자 통신으로 지칭되며, 이는 박테리아 나노네트워크의 기초를 형성합니다.

## 박테리아 나노네트워크

박테리아 나노네트워크는 분자 통신의 한 예로, IoT 커뮤니티에서 점차 주목받고 있습니다. 박테리아 나노네트워크는 분자 신호 및 최근에 발견된 전기장을 통해 박테리아 커뮤니티 간의 통신을 포함합니다. 통신의 또 다른 방법은 물리적 이동을 통해 이루어집니다. 즉, 대장균 등 박테리아의 이동 능력을 활용하여 정보를 운반합니다.

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

이러한 경우에는 디지턈 정보가 DNA로 번역되어 박테리아 세포로 변환되고, 나중에 디지턈 형식으로 다시 해독될 수 있습니다. 이 방법의 구현 예로는 비운동성 및 운동성 박테리아 간의 교합을 이용하여 DNA 데이터를 저장하고 전송하는 것이 Tavella 등에 의해 시연되었습니다. (그림 4, 5).

![이미지](/assets/img/2024-05-23-EcolithenextRaspberryPi_3.png)

## 도전과 가능한 방향

박테리아가 HCI 조사에 가져다 주는 풍부한 프레임워크에도 불구하고, 미생물과 직접 작업하는 것은 실제적 및 윤리적 도전이 될 수 있습니다. 생물학적 성질 때문에 다루고 조작하는 것은 신중한 고려가 필요한데, 컴퓨터와 전자제품과 작업할 때 고려할 필요가 없는 문제들입니다. 이에는 전문적 감독이 필요할 수 있는 가능성, 일부 미생물을 안전하게 법적으로 다룰 수 있는 라이선스, 비용 측면의 영향과 발생할 수 있는 생명 윤리 및 생물안전 도전이 포함됩니다.

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

더 나아가서, HCI 조사원들은 박테리아에 익숙하지 않을 수 있으며, 이 분야에 이전의 작업 경험이 없을 수도 있어 의미 있는 참여에 어려움이 있을 수 있습니다. 이는 과거 지식이 없는 사람들에게 추상적으로 보일 수 있는 박테리아 생리학의 기본 개념 중 일부로 악화될 수 있습니다. 이러한 도전을 극복하기 위해, DIY 생물학 운동 및 게이미피케이션에 대한 잠재적인 HCI 조사를 활용하기로 제안합니다. 아래에서 이에 대해 자세히 기술하고 이유를 설명하겠습니다.

## DIY 생물학

DIY 생물학 운동은 생물 기술의 도구, 데이터 및 재료의 접근성과 가용성을 증대시키는 데 공헌합니다 [9]. 현대 생물 기술 산업의 경제적 환경 변화를 활용하며, 지속적으로 하락하는 DNA 합성 및 서열 분석 비용을 대표합니다. 현재, 미생물과 소규모 실험을 수행하는 도구와 기술이 일반 대중에게 다양한 채널을 통해 널리 제공되고 있으며, 이는 메이커 스페이스를 포함합니다.

게다가, 지원하는 키트도 있습니다. Amino Labs와 같은 교육용 제품은 특히 E.coli 종을 조작하고 유전적 엔지니어링에 관심 있는 사람들을 위해 고안되었습니다 [3]. 이는 다양한 환경 자극으로부터 트리거될 수 있는 유전 회로를 구축하여 박테리아로부터 맞춤형 색상을 만들 수 있도록 하여 사용자가 사용하는 것을 허용합니다.

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

세균 중 특히 대장균은 획기적인 생명해킹 프로젝트를 위한 이상적인 도구로, 획득, 배양 및 유지하기 쉽다는 장점이 있습니다. 예를 들어 산화된 랩에서 사용되는 업계 표준 균주 K-12 E.coli는 비교적 안전하게 취급할 수 있습니다. 이 균주는 비병원성으로 개량되어 실험실 환경 이외에는 쉽게 전파되지 않도록 되어 있습니다. 대부분의 세균 종들과 달리 K-12 균주는 미국 및 유럽 대부분 지역에서 비교적 쉽게 구입할 수 있습니다.

## 세균의 게임화

IoT 분야에서 게임화는 여러 가지 이점을 보여주고 있습니다. 예를 들어, IoT 응용 프로그램의 참여를 증가시키고[4], 긍정적인 인간 행동 변화 (예: 스마트 시티 계획의 일환으로 여행 행동)를 유도해 왔습니다[12]. 비슷하게, Wood 등의 GPS Tarot은 참가자들이 즐기고 예술적인 도구로, 존재하는 글로벌 항법 위성 시스템(GNSS)을 활용하여 '숨겨진 기술'에 대해 배우고 인식하는 기회를 제공합니다[18,19].

유사한 맥락에서, 대장균의 게임화가 HCI 및 IoT 연구에 대한 통합에서 참여도, 학습, 태도 변화에 도움이 될 것으로 가정합니다. 미생물은 이전에 생물 게임의 형태로 게임화되어왔으며, 실제 미생물을 컴퓨터 게임 플랫폼에 통합된 하이브리드 바이오 디지털 게임인 활자 게임으로 개발되어왔습니다[14]. 전반적으로, 세균의 게임화는 참여도, 플레이 경험 및 학습 측면에서 성공적으로 입증되었습니다[7,8,10].

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

## 윤리적 담론

잠재적인 IoT 응용 프로그램과 마찬가지로, 세균 주도 IoT 시스템의 경우에도 사용자 데이터와 관련된 윤리적 고려 사항과 개인 정보 보호 문제가 적용될 것입니다. 그러나 흥미로운 점은 이러한 시스템이 작동할 수 있는 생물학적 성격으로 인해 추가적인 윤리적 도전 과제가 제시된다는 것입니다. 먼저, 세균이 작동할 수 있는 자율적인 성격에서 문제가 발생합니다. 세균은 진화하고 자율적으로 행동할 수 있기 때문에 자연 생태계에 위협을 가할 수 있고 병원성이 될 수도 있습니다. 물론 이는 교육적으로 배포된 K12 대장균 균주에는 적용되지 않을 수 있지만, 넓은 시각에서 고려할 가치가 있는 가능성입니다.

둘째로, 세균 나노 네트워크는 DNA에 인코딩된 데이터를 공유하기 위해 교반 및 세포 운동이라는 자연 과정을 통해 의존합니다. 고도로 공학화된 세균이 효율적인 통신 시스템을 제공할 수 있지만, 그들은 결과적으로 예기치 않은 결과(예: 돌연변이)를 일으킬 수 있는 생물적 실체입니다. 모든면에서, IoT 및 HCI에서 세균 사용은 흥미로운 기회를 제공하면서도 새로운 윤리적 도전 과제를 제시하여 토론의 새로운 길을 열어줍니다.

## 결론적으로...

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

이 논문에서는 E. coli 박테리아의 특징을 강조하며 IoT 및 HCI 문맥에서 미생물 탐구를 가능하게 할 수 있는 기능을 강조했습니다. 이는 이 생물체를 전통적인 컴퓨터 기반 IoT 장치와 비교함으로써 시작되었습니다. 더 나아가, 현재 IoT 및 HCI 분야의 연구자들이 박테리아에 대한 접근 및 실험을 할 수 있는 현실적인 인프라의 부재를 강조했습니다. 이러한 문제에 대한 잠재적인 해결책으로 DIY 생물학 운동과 게임화 기술을 활용하여 사용자 참여와 박테리아 소개를 촉진하는 것을 제안했습니다. 마지막으로, 박테리아 중심의 IoT 시스템이 제기할 수 있는 윤리적 도전 과제를 설명했습니다. 이는 박테리아의 자율적이고 생물학적 (따라서 예측할 수 없는) 성격에 의해 야기되는 것입니다. 이러한 도전 과제들은 박테리아 중심의 IoT 시스템이 가져다 줄 광범위한 영향에 대한 풍부한 토론 영역을 제공합니다.

## 참고문헌

[1] Akan et al., 2017.

[2] Akyildiz et al., 2015.

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

[3] Amino Labs.

[4] Charlton and Poslad. 2016.

[5] Felicetti et al., 2018.

[6] Gotovtsev and Dyakov, 2016.

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

[7] Hossain et al., 2017.

[8] Kim et al., 2016.

[9] Landrain et al., 2013.

[10] Lee et al., 2015.

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

[11] Pataranutaporn et al., 2018.

[12] Poslad et al., 2015.

[13] Prindle et al., 2015.

[14] Riedel Kruse et al., 2011.

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

- Ron and Rosenberg. 2014.
- Tavella et al., 2018.
- Wood et al., 2017a.
- Wood et al., 2017b.

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

[19] Zhang et al., 2015.
