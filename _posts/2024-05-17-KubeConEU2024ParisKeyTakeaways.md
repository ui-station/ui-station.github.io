---
title: "KubeCon EU 2024 파리 주요 포인트들"
description: ""
coverImage: "/assets/img/2024-05-17-KubeConEU2024ParisKeyTakeaways_0.png"
date: 2024-05-17 18:20
ogImage:
  url: /assets/img/2024-05-17-KubeConEU2024ParisKeyTakeaways_0.png
tag: Tech
originalTitle: "KubeCon EU 2024 Paris: Key Takeaways"
link: "https://medium.com/@danielbryantuk/kubecon-eu-2024-paris-key-takeaways-ad4c1bb7fbfe"
---

## 회의 요약

최근 파리, 프랑스에서 열린 쿠베콘 유럽 행사에 다녀온 후에 다시 여행에 대해 생각하게 되었습니다. 행사에서 주요 강연과 세션들로부터 많은 것을 배우고, 플랫폼 엔지니어링에 대한 강조와 커뮤니티에서 많은 흥미로운 사람들을 만났습니다. 12,000명 이상의 참가자들과 100여 개의 후원사들이 참석하여, 이전보다 더 붐볐습니다.

다음은 쿠베콘 유럽 2024에 대한 내 주요 포인트들입니다:

- 나는 우리의 새로운(클라우드 기반) AI 지배자들을 환영합니다.
- 책임있는 혁신에 대한 요구, 즉 "비용과 지속 가능성을 잊지 말자"
- 플랫폼 엔지니어링이 중심에 서 있습니다.
- 제품 사고가 이깁니다!
- 개발자 경험 및 내부 및 외부 개발 루프에 대한 더 많은 관심
- 보안은 여전히 큰 사업입니다.
- 최종 사용자 이야기가 위로 올라갑니다.
- 도구와 프레임워크 번들링이 계속됩니다.
- Wasm: 핫한 주제, 그러나 불확실성이 있습니다.
- Dapr가 점점 클라우드 네이티브 ESB로 자리 잡고 있습니다(좋은 방법으로)

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

이러한 주요 사항을 자세히 살펴보겠습니다. 기사 맨 끝에는 보너스 "KubeCon EU를 위한 GTM 주요 사항" 섹션도 포함되어 있어요. 프랑스어로 말하자면, allons-y!

![KubeConEU](/assets/img/2024-05-17-KubeConEU2024ParisKeyTakeaways_0.png)

# 나는 새로운 (클라우드 기반의) AI 지배자들을 환영합니다

작년 12월 KubeCon NA Chicago에서 AI/LMM 콘텐츠의 부족에 놀라신 분들이 많았습니다. 그런데 KubeCon EU Paris에서는 이를 보완해줬어요... 그리고 더 더해졌습니다! 오프닝 데이 키노트 중 거의 모두가 AI에 초점을 맞춘 내용이었고(AI에 대한 조급한 마음까지!), 한 주 내내 전적으로 AI에 초점을 맞춘 AI 랩도 있었으며 다양한 각도에서 AI/LLM에 집중한 분과 세션이 많았습니다. 또한 클라우드 네이티브 트렌드와 "AI", "LLM", 또는 "Generative"이라는 단어가 섞인 스폰서 부스를 보는 것조차 무한했을 정도에요.

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

농담은 물론, 나는 CNCF 생태계가 AI에 대해 매우 열려 있다는 메시지를 주로 받았어. 행사 전체에서 본 사용자 이야기들은 주로 모델 훈련이 아니라 추론에 대해 클라우드 네이티브 기술을 사용하는 데 초점을 맞췄어; 특히 엣지 근처의 추론. 이는 이전에 클레이튼 콜먼이 한 말을 반영하고 있었어: "만약 추론이 새로운 웹 앱이라면, 쿠버네티스는 새로운 웹 서버야." 주요 무대와 후원사 홀에서 보여진 K8s 기반 AI 빌더들을 위한 관련 "도구"들도 풍부했어.

오프닝 첫날 두 번째 주요 연설로 NVIDIA가 "Kubernetes에서 GPU로 AI 워크로드 가속화"를 주제로 성공적으로 등장한 것이 정말 흥미로웠어. 그들이 쿠버네티스 이야기를 잘 얽었지만, 이것은 주로 하드웨어/인프라스트럭처 플레이일 것 같았는데, 이는 이 대중에 조금 불편하게 느껴졌을지도 모르겠어. 또한 NVIDIA가 GPU와 AI 칩 분야에서 현재 주도적인 위치를 차지하고 있다는 것을 강하게 상기시켜 주었어.

관련 주제로, 주말에 All-in 팟캐스트를 듣다가, 베타들이 AI 스택의 네 가지 계층 중 어디에서 혁신과 가치 창출이 발생할지 의문을 제기했어: 인프라스트럭처, (기초) 모델, 개발 도구, 애플리케이션. 만약 NVIDIA가 인프라스트럭처 계층을 확보했다면, 우리의 다음 주요 연설은 모델 및 개발 도구 계층을 확보하려는 다른 회사를 강조했어: Microsoft.

우리가 나중에 다룰 주제를 시작으로, Microsoft가 행사에서 첫 번째로 "Kubernetes AI Toolchain Operator (Kaito)"라는 AI 주제의 "번들"을 발표했어. Kaito는 Kubernetes 클러스터에서 AI/ML 추론 모델 배포를 자동화하고 falcon 및 llama2와 같은 모델을 대상으로 하고 있어.

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

이어서, Ollama는 프로그램에서 많이 소개되었고, 대형 언어 모델을 로컬에서 실행하는 사실상의 방법으로 보였습니다. 우리가 파리에 있었기 때문에 Mistral AI 팀도 칭찬을 받았어요. KubeCon에 참석하지 못했다 하더라도 AI 주제의 몇 가지 모임이 근처에서 열렸어요:

AI 분야에서 혁신이 빠른 속도로 일어나고 있음을 부정할 수 없어요. 후원사 홀에서 이야기를 나눈 많은 최종 사용자들은 발전에 발맞추기 위해 노력하고 있었어요. 리더십이 AI를 수용하도록 장려했지만, 그들은 소규모 실험만을 실행하고 관련 발전과 따라가려 애를 쓰고 있었어요.

이 점을 더 확실하게 보여주기 위해, 회의가 진행되는 동안 나의 X/Twitter 뉴스피드에는 지금까지 언급된 두 회사의 더 많은 발전상황이 소개되었어요. 지난 주 NVIDIA의 Blackwell 칩 및 Microsoft의 AI 분야 최근 인재 영입에 대해 더 알고 싶다면, Ed Sim의 뉴스레터를 확인해보세요.

키노트와 3일간의 부스 대화에서 떠날 때, AI 미래가 드디어 KubeCon에 왔다는 생각을 하지 않을 수 없었어요 — 그러나 소문처럼 고르게 퍼져 있지 않다는 게 분명해요; 특히 최종 사용자들 사이에서 그렇습니다. 이제 클라우드 네이티브 군중이 다음 세대의 AI 앱을 개발하기 시작할 때인 것은 분명해요. 그리고 저는 우리의 새로운(클라우드 기반) AI 지배자를 환영합니다.

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

# 비용과 지속 가능성을 잊지 말아 담당하는 혁신을 요청합니다.

AI에 대한 다양한 언급과 함께, 오프닝 키노트에서는 "책임있는 혁신"을 촉구했습니다. 이 구문은 전시회 전체에서 여러 차례 사용되었습니다. 맥락을 고려하면 "혁신을 지속하되 오픈 소스를 활용하고 비용 및 에너지 절감을 염두에 두어야 한다"는 의미로 받아들였습니다.

CNCF가 이벤트를 주최하고 있기 때문에 OSS를 수용하고, 이상적으로는 CNCF 생태계에서 기술을 활용하라는 명백한 요구가 있었습니다. Redis가 같은 날 오프닝 키노트에서 이중 라이선스로 전환한다고 발표함으로써 청중에 불편함을 느낀 장면이 있었는데, 이는 최근의 이러한 변경 사항을 모두 생각나게 했습니다.

CNCF가 지속 가능성에 대한 일부 어려움을 인정하는 것을 볼 수 있어서 좋았습니다. 세 번째 날의 키노트에서 Gualter Barbas Baptista가 독일 철도에서의 실제(기업) 사례인 "IT 그린 구축: Deutsche Bahn에서의 플랫폼, 데이터 및 개발자 권한 부여의 여정"에 지속 가능성을 요구했습니다.

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

이 주제는 중요합니다. 키노트 시간을 충분히 할애하는 것을 보아 좋았어요.

# 플랫폼 엔지니어링이 중심 무대에 서다

2022년 KubeCon EU에서 몇 명이 "플랫폼 엔지니어링"의 신흥 트렌드에 대해 이야기했습니다. 2024년에는 플랫폼 엔지니어링이 주류로 자리 잡았습니다.

스폰서 쇼케이스는 플랫폼, 플랫폼 엔지니어링, 개발자 경험에 대한 언급으로 넘쳤으며, Solomon Hykes인 Dagger와 Docker의 공동 창시자가 진행한 훌륭한 키노트 세션도 있었습니다. "10년의 변곡점: 컨테이너화된 세계에서 애플리케이션 전달의 미래" (말하자면, 최근 InfoQ 팟캐스트에서 Solomon과 이야기를 나누었는데, Dagger에 대해 많이 배웠어요!)

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

인프라스트럭처 레이어 도구 및 프레임워크인 Kubernetes, 서비스 메쉬, 게이트웨이, CI/CD 등이 잘 발전하여 대부분의 사람들에게는 "지루한 기술"로 여겨집니다 (적어도 이들에게는 그렇습니다). 지금 큰 도전은 퍼즐 조각들을 조합하여 내부 고객인 개발자들에게 가치를 전달하는 것입니다. 위 트윗에 댓글로 역동적으로 관찰한 Betty Junod는 "우리는 그 시대의 모든 PaaS를 해체했고... 모든 개별 레고로 놀아보았으며... 새로운 레고도 얻었고... 이제 그것들을 다시 조립하려고 노력하고 있다"고 언급했습니다.

최근 Syntasso 블로그에서 플랫폼 구축 레이어에 대한 내 정신적 모델을 논의했습니다. 애플리케이션 코레오그래피, 플랫폼 오케스트레이션 및 인프라스트럭처 구성이라는 세 가지 레이어 중에서, 애플리케이션 (포털) 레이어에 과도하게 집중하는 위험이 있다고 봅니다.

마치 사람들이 AI 레이어에서 가치 창출 및 확보가 어디에서 일어날지에 대해 논쟁하는 것처럼, 플랫폼 공간 레이어에서도 같은 경향이 보인다고 생각합니다.

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

인프라스트럭처 레이어는 이윤이 많이 나오는 것으로 보이지만, 주로 후발주자들이 참여하는 영역입니다. 최근 Adam Jacob이 언급한 바에 따르면, OpenShift는 연간 매출이 10억 달러 이상을 올립니다. 그러나 동시에 HashiCorp와 Terraform에 대한 시장에서의 어려움을 볼 수 있습니다 (관련해서 OpenTOFU가 행사에서 많은 관심을 받았습니다).

많은 기업이 내부 개발자 포털을 통해 애플리케이션 코레오그래피 레이어의 도구 및 프레임워크를 홍보하고 있습니다. Backstage, Port, Cortex 등과 같은 워크로드 사양 언어(Score, Radius) 등도 활발히 활용되고 있습니다.

제 친구 Abby Bangser와 Whitney Lee는 플랫폼 사용자 경험에 관한 훌륭한 강연을 발표했습니다: "가끔 돼지에게 필요한 건 립스틱일 수도 있어요!"

![이미지](/assets/img/2024-05-17-KubeConEU2024ParisKeyTakeaways_2.png)

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

애플리케이션 코레오그래피 레이어에서는 명확한 가치가 창출될 수 있습니다. 그러나 이번 KubeCon에서 Backstage에 대해 최고와 최악의 순간이 겹쳐보였어요. Backstage 커뮤니티가 여러 훌륭한 세션을 제공했고, 분명히 많은 역동성이 있었습니다 (CNCF 최종 사용자에게 가장 많은 기여를 한 프로젝트였습니다). 그러나 채택자들로부터 "외부에서 제공되는" 기능 부재와 포털을 운영하고 유지하기 위해 필요한 노력에 대한 불평이 많았습니다.

이 애플리케이션 레이어는 일컬어 말하는 "[개발자]의 손에 [플랫폼]의 길이 만난다" 곳이며, 아마도 개발자 경험이 여기서 제작됩니다. 그러나 제 소프트웨어 아키텍처 경험에 근거하여, 제는 올바른 추상화를 생성하는 찬성자이며, 포털은 플랫폼의 목표를 실현하기에는 필수적이지만 충분하지는 않다고 생각합니다. 플랫폼 오케스트레이션 레이어는 내년에 많은 혁신이 일어날 것으로 보입니다. 저는 현재 오픈 소스 Kratix 플랫폼 오케스트레이터를 구축 중인 Syntasso 팀과 조금 편향되어 있는 것 같습니다만, Humanitec, Upbound (Crossplane), Massdriver, Mia-Platform, Qovery 등 다른 많은 회사들도 이 공간에 투자하고 있습니다.

플랫폼 구축 공간의 상대적으로 미숙함을 상기시키기 위해, 가장 많은 관람자가 참여한 내용 중 일부는 클라우드 네이티브 앱을 제공하는 기본원리에 중점을 둔 것이었습니다. 예를 들어, Adrian Mouat은 "현대적인 방식으로 컨테이너 이미지 빌드하기"를 가득 찬 키노트 룸에 제시했습니다. Docker 부스는 Docker Build Cloud에 중점을 두고 있었고, Docker 관련 인사들이 1년 만에 행사에 돌아온 것을 보는 것이 좋았습니다. 그리고 많은 101-레벨 세션이 가득 차서 서 있었습니다.

마지막으로, AI가 플랫폼 엔지니어링에 큰 영향을 미치는 것 같지는 않았어요! 대부분 애플리케이션, 플랫폼 및 인프라 구성 요소를 오케스트레이팅하고 라이프사이클을 관리하기 때문에, "AI ROI"는 다른 곳에서 더 높아 보입니다.

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

# 제품 사고 FTW!

이전에 이야기한 내용과 밀접하게 관련된 주제로, 이번 KubeCon에서는 플랫폼, 프레임워크 및 개발자 도구에 "제품 사고"가 훨씬 더 적용되는 것을 보았어요.

저는 Platfom Engineering Day에서 함께하는 이벤트와 Spotify의 Samantha Coffman이 하는 "제품 사고를 통한 개발자 플랫폼 팀의 지원 강화"라는 발표를 정말 즐겼어요. 다른 몇몇 플랫폼 개발자들도 이 발표가 제일 좋았다고 했어요. 물론, 우리 모두가 Spotify가 될 수는 없겠지만, Samantha은 매우 유용한 사고 모델을 제시하고 Marty Cagan의 우수한 작업에 강한 참조를 했어요(가치, 생존 가능성, 실행 가능성, 사용 가능성과 같은 네 가지 큰 위험에 대해서).

NatWest의 Chris Plank가 하는 발표 "혁신 발휘: NatWest Bank가 클라우드 네이티브 도구를 활용하여 제품으로서의 플랫폼 제공"도 저에게 큰 인상을 주었어요. Chris는 규제가 엄격한 환경에서 플랫폼을 전개하기 위한 도전을 잘 소개하고, 이러한 장애물을 극복하기 위해 제품 사고와 기관 간 협력을 적용하는 이점을 제시했어요.

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

관련해서, Aviatrix의 Mitch Connors가 "Product Market Misfit: Adventures in User Empathy"를 발표했어요. 제 친구이자 전 동료인 Open Credo의 Nicki Watt도 "To K8S and Beyond — Maturing Your Platform Engineering Initiative"를 발표했는데, CNCF App Delivery TAG의 Platform Maturity Model에 대한 유용한 안내와 제품에 대한 생각의 적절한 조화를 제공해 주었어요.

만약 KubeCon의 미래가 플랫폼 모양이라면, 제품 중심으로도 지속됐으면 좋겠어요!

# 개발자 경험 및 내부, 외부 개발자 루프에 대한 더 큰 관심

저는 당연히 주관을 가지고 있지만, 부속으로 이루어진 App Developer Con에서 발표한 "Testing Cloud Apps: Mocks vs. Service Virtualization vs. Remote Tools" (아직 비디오는 없어요!) 를 보았어요. 그러나 행사 전체에서 개발자 경험 및 내부, 외부 개발자 루프에 집중하는 중요성에 대한 언급이 많이 있었어요.

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

![KubeConEU2024ParisKeyTakeaways](/assets/img/2024-05-17-KubeConEU2024ParisKeyTakeaways_3.png)

안녕하세요! 제 친구 AtomicJar(이제는 Docker)와 Diagrid의 Oleg Šelajev, Alice Gibbons은 "간단화된 내부 및 외부 클라우드 네이티브 개발 루프"를 발표했어요. 그리고 멋진 분들인 GitPod 팀에서 클라우드 개발 환경 (CDEs)에 대한 이야기를 많이 들었어요. 또한 CDE 분야의 새로운 흥미로운 소식을 Daytona팀에서 들었죠.

행사에서 고위 리더들과 이야기할 때, 모두 개발자 경험의 가치를 알고 있지만 그것을 리더십에 "팔기"에 어려움을 겪는다는 이야기를 자주 들었어요 (특히 후기 ZIRP 시대에서). 일반적인 의견은 DORA/SPACE 지표에 초점을 맞추고 조직간의 협업을 통해 도움을 받을 수 있다는 거예요. 이전에 SNS에서 공유했듯이 Abi Noda와 함께 DX Engineering Enablement 팟캐스트 몇 에피소드를 듣는 것은 언제나 좋은 선택이라는 걸 상기시켜 드립니다.

# 보안은 여전히 중요한 비즈니스입니다.

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

키노트는 상대적으로 보안에 대해 가볍게 다뤄졌지만, 세션과 후원 쇼케이스는 분명히 다르더군요. "보안"이라는 용어로 세션 카탈로그를 싹 훑어보면 수십 개의 이야기가 나옵니다. 안전한 공급망부터 네트워크 침입 및 플랫폼 보안까지 다양한 주제가 잘 다뤄졌어요.

특정 세션들은 보안 구현 및 도구에 명확한 초점을 맞추며, Kyverno, Falco 및 OPA가 쇼를 훔쳤습니다. 안전한 공급망은 컨테이너 빌드 도구(SBOMS 및 SLSA와 함께), Keptn, Harbor 등을 자세히 다뤘죠. 또한 네트워크 보안에 많은 주목이 갔는데, Cilium(및 이 문맥에서의 eBPF에 대한 흥미로운 언급), Linkerd(Cloudflare의 Pingora에 대한 의문이 있는 상태), Istio가 언급되었습니다.

# 종단 사용자 이야기가 업계에서 주목받고 있어요

한번 더 강조하면, 프로그램에 참여한 종단 사용자 키노트와 세션을 보는 것이 좋았습니다. 이러한 세션 중 많은 것들이 "업계에서 주목받고 있어요"는 방향으로 이동하고 있었습니다. 그들이 Kubernetes(또는 다른 CNCF 기술)를 도입한 방법을 소개하는 대신에, 확장, 지속가능성, 비용 절감, 또는 개발자 생산성 향상과 같은 메시지를 주로 다뤘어요. 앞서 언급한 Deutsche Bahn의 지속가능성에 대한 키노트는 이를 잘 보여주는 좋은 예시였습니다.

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

저는 닉 루티글리아노와 다니엘 드 레프렌티뇨가 진행한 “How Spotify Re-Created Our Entire Backend Without Skipping a Beat”라는 Spotify 토크도 즐겁게 감상했어요. 이 토크는 제품 사고를 사용하여 제품화되고 유지되는 K8s 기반 환경에 대해 다뤘죠. 이 분야에서 다른 좋은 토크들로는 다음과 같은 것들이 있어요:

- 노르웨이 공공 부문 플랫폼 성숙도 현황
- 레고 그룹의 제조용 플랫폼 엔지니어링 접근 방식: 기본 블록 유지하기
- 인투잇의 서비스 메시 확장: 300개 이상 클러스터를 넘어서는 셀프 서비스
- 블랙록의 KEDA를 사용해 몇 년 동안 수백만 달러를 절약하는 법
- TikTok의 Edge Symphony: 멀티 클러스터 컨트롤러로 범위를 넓히는 방법

# 도구 번들링이 계속됩니다

이전 KubeCon 요약에서 본 것처럼, 도구와 프레임워크의 번들링이 계속되었습니다. 몇 가지 예외를 제외하고는, 공급업체들이 “클래스 최고의 솔루션”이 되려고 싸우던 시절은 사라졌어요.

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

Could you please change the table tag to Markdown format?

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

흥미를 끌기 위해, 제 플랫폼 엔지니어링에 초점을 맞춘 친구들과 얘기를 나눠본 결과, 여전히 Wasm에 대한 "핵심 사용 사례"를 찾고 있었어요. 이야기에 따르면 컨테이너 기반 및 서버리스 앱의 조합이 필요한 세분화와 리소스 사용 제어를 제공했습니다. 그들은 사무실로 돌아가서 AI 도구에 대해 더욱 탐구하고 싶어했습니다.

일반적으로 Wasm에 대해 긍정적입니다. 예전에 프록시와 API 게이트웨이 작업을 많이 해왔기 때문에 항상 Lua와 같은 것을 대체할 수 있는 Wasm의 플러그인 사용 사례를 볼 수 있었습니다. 하지만 미래에는 기조 발표 시간 대신 뒷장에서 더 많은 사용 사례를 보게 될 지 궁금합니다.

# Dapr는 클라우드 네이티브 ESB(좋은 방식으로)입니다

Dapr 프로젝트에 대해 오랫동안 긍정적이었던 것은 비밀이 아닙니다. 이 프레임워크는 10년 전 초기 클라우드 네이티브 앱을 개발할 때 접근할 수 있었으면 좋았을 것이라고 생각했습니다. 이번 이벤트 중에서도 기술에 관한 많은 언급을 볼 수 있어 기쁘었고, 예상치 못한 세션에서도 잘 통합된 것과 관련한 집중이 자주 있었습니다.

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

평소처럼, Diagrid의 Bilgin, Mauricio 및 Mark와 흥미로운 토론을 나눴어요. 개발자 패턴, 클라우드 네이티브 응용 프로그램 미들웨어의 미래, 그리고 "서버리스 시대 이후의 클라우드 컴퓨팅: 현재 트렌드 및 이상"에 대해 이야기했죠.

여러 명이 Dapr을 옛날 스타일의 엔터프라이즈 서비스 버스에서 언급할 때 웃음을 자아내며, Dapr을 "클라우드 세대용 ESB" 또는 "그렇게 되어야 했던 ESB"로 프레임하고 있었어요. 전 엔터프라이즈 자바 개발자로 일할 때 여러 ESB를 다뤘던 경험이 있어서 이 소리들을 들으면서 웃음과 고개를 끄덕였죠! 희망히, 새로운 세대의 개발자들은 우리가 ESB에 대해 했던 장난을 기억하지 않았으면 좋겠어요.

# 보너스: KubeCon EU를 위한 GTM 요약

이번에 제 일상 업무에서 여러 개발자 도구 회사들에게 Go-To-Market, 제품 마케팅 및 개발자 관계에 대한 조언을 제공하고 있어요. 그래서 한두 문단을 공유하고 싶어요. 후원사 쇼케이스를 둘러보면 현재 핫하고 판매 가능한 트렌드를 몇 가지 알 수 있었죠.

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

- AI 기반의 모든 것
- 플랫폼 엔지니어링
- 보안
- 관찰 가능성
- 비용 절감 (또한 흥미로운 것은 "FinOps" 라벨이 일관되게 적용되지 않았다.)

나는 이번에 쿠베콘 정기 참가자 중에 부스를 운영하지 않는 사람들이 여러 명 있다는 것도 처음 알게 되었다, 특히 VC 지원을 받은 A/B 시리즈 펀딩 공간에 속한 사람들 중. 창립자와 GTM 직원들과 이야기를 나누면서 비용 절감, EU 시장을 대상으로 지향하지 않음, 그리고 이전 쿠베콘에서의 부스 스캔이 $$$/€€€로 전환되지 않았다는 이유로 그들의 ICP(즉, 이전 쿠베콘의 부스 스캔이 돈으로 전환이 되지 않았다는 것)의 리드 품질이 낮다고 믿는 것을 발견했다.

이는 소소한 예기지만, 나는 또한 이번 이벤트에서 훨씬 더 많은 영업 사원들을 발견했다. 내가 커리어 초반(2000년대 후반의 Java 공간)에 컨퍼런스에 참석하기 시작했을 때, 스폰서 쇼케이스에서 과도하게 열정적인 영업 사원들에게 접근당하는 것은 완전히 정상적이었다. 이것은 소프트웨어 툴링이 제품 중심으로 발전하고 구매력이 개발자로 변하며 개발자 관련 역할이 나타남에 따라 서서히 변화했다(Stephen O'Grady의 "The New Kingmakers: How Developers Conquered the World" 참조).

쿠베콘 부스는 역사적으로 개발자 관계, 기술적인 Go-To-Market, 그리고 창립자들이 중심에 있었다. 이들은 여기에도 있었지만, 나는 부스에서 만남에서 만남으로 걸어갈 때 말할 것을 찾는 영업 사원들에게 비유적으로 (가끔은 실제로도) 당한 것을 발견했다. 한 영업 사원은 나가 바쁘다고 대답하고 영업 설명에 관심이 없다고 답했을 때 거의 적대적이었는데, 그 정도로 나는 내 대답을 확인하고 입맛을 감췄다(그는 몇 가지 근거 없는 공격으로 나를 완전히 당황하게 했지만, 행동 규범은 양쪽 모두에게 적용된다!)

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

시간이 까다롭고 영업 목표가 어려울 수 있다는 것을 알지만, 대상 체르의 문화를 이해해야 합니다. KubeCon 스폰서 홀에서 강하게 팔아넣는 것은 효과적이지 않다고 생각해요.

저는 개인적으로 Shomik Ghosh의 Software Snack Bites 팟캐스트에서 처음 알게 된 기술 SDR 역할에 대해 열정을 가지고 있어요. 또한 개발자 관계자들이 제품 중심 역할에 더 많이 눈길을 띄게 될 것이라고 믿어요. 이에 대해 더 자세히 썼으니 링크를 참조해주세요:

# 마무리: 커뮤니티, 커뮤니티, 커뮤니티!

KubeCon을 포함한 모든 행사에서 이벤트를 더 포괄적으로 만들기 위해 커뮤니티의 노력에 감명을 받았어요. 저는 중요한 강연 및 다른 세션에 수어 통역자를 확보한 청각장애 및 장애인 작업 그룹에 박수를 보내요.

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

니키타 라그나스와 아파르나 수브라마니안이 행사를 공동 주최한 데에서 훌륭한 일을 해냈어요. 또한, 처음으로 행사 공동 주최한 클라우드 네이티브 친구 카스퍼 보그 니센에게도 큰 박수를 보냅니다! 포용에 관한 주제를 더 깊이 다루기 위해, 카스퍼는 클라우드 네이티브 커뮤니티의 포용적 성장의 10년을 주제로 한 주요 토론 패널 "다양성 속의 통일: 클라우드 네이티브 커뮤니티의 포용적 성장"을 주관했어요.

나는 KubeCon EU에서 보낸 시간을 철저히 즐겼어요. 커뮤니티 여러분과 많은 소통을 나눌 수 있어 정말 좋았고, O'Reilly 부스에서 "API 아키텍처 마스터링" 책 서명 행사를 열면서 즐거운 시간을 보냈어요. 또한, PlatEngDay의 공동 주최자인 Syntasso 크루와 같이 현재 함께 일하고 있는 분들과 시간을 보낸 건 정말 즐거웠어요. 이 협력 행사의 다음 단계가 기대돼요!

아직 모든 배운 것을 정리 중이라 놓친 중요한 주제나 주요 인물 언급이 있었다면 알려주세요!

만나지 못한 분들과 만날 기회가 없었다면 소셜 미디어나 평소의 연락처로 연락해주세요. 11월에 솔트레이크시티에서 만나요!
