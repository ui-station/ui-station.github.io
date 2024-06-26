---
title: "안녕하세요 안드로이드 프로젝트에서 자주 마주치는 성가신 문제들을 살펴보겠습니다 함께 해요"
description: ""
coverImage: "/assets/img/2024-05-23-AnnoyingproblemswithAndroidmobileanyprojects_0.png"
date: 2024-05-23 15:00
ogImage:
  url: /assets/img/2024-05-23-AnnoyingproblemswithAndroidmobileanyprojects_0.png
tag: Tech
originalTitle: "Annoying problems with Android (mobile   any?) projects"
link: "https://medium.com/@azazellj/annoying-problems-with-android-mobile-any-projects-8b6f37a7f424"
---

한 회사에서 오래 일하지 않으면 다양한 단계의 여러 프로젝트에 참여할 기회가 많지 않을 수 있습니다. 이러한 경험을 통해 작은 개인 프로젝트나 대규모 기업의 어려움을 보게 될 수 있습니다.

많은 문제가 종종 무시되고 이로 인해 예상치 못한 문제나 버그가 발생할 수 있습니다. 이 중 일부는 매우 쉽게 해결할 수 있고 프로젝트를 조금 더 쉽게 이어나갈 수 있게 해줍니다. 여기에는 대부분 안드로이드 애플리케이션 개발과 관련된 권장 사항이 포함되어 있지만 대부분의 내용은 어떤 기술의 프로젝트에도 적용됩니다. 이것은 대부분 개인 경험과 실패의 모음인데, 다른 사람의 실수로부터 배우는 것이 더 재미있지 않을까요?

# 프로젝트 구조

IDE가 처음부터 새 프로젝트를 생성하면 프로젝트에 불필요하거나 방해가 되는 많은 파일과 폴더가 생성될 수 있습니다. 여기서 기본 구조와 작업해야 하는 구성 요소를 이해하는 것이 중요합니다: 모듈, 구성, 아무도 작성하지 않을 테스트 😁

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

.gitignore 파일을 살펴보면, 대략 20-30개의 규칙이 있을 겁니다. 대부분이 다섯 가지 경우에 사용되지만, 그 중에도 종종 부적절하게 기재된 경우가 많습니다. local.properties가 프로젝트에 추가된 적이 있다면, 실제로는 무시 목록에 포함되어 있지만 IDE가 신경을 쓰지 않을 수도 있습니다. 그런 소프트웨어를 믿을 이유가 있을까요?

언제나 더 나은 방법이 있을 수 있습니다. 한 회사에서 첫 근무일에 안드로이드(1.5GB)와 iOS(4.5GB) 저장소를 다운로드해야 했던 적이 있었습니다. 문제는 선배들이 Google Play에 업로드된 애플리케이션의 모든 버전을 프로젝트에 커밋했기 때문에 저장소가 비합리적으로 커진 것이었습니다. 이 프로젝트를 빌드하고 몇 가지 테스트를 실행해야 할 때마다 CI/CD가 이 프로젝트를 클론해야 하는 상황을 생각해보세요 😉

내가 알기로는 각 빌드에는 대략 40분이 걸렸던 것 같네요. 가장 흥미로운 점은 git 히스토리를 정리함으로써 해결할 수 있다는 것이었습니다. 실제로 이것을 시도해 보았고, 그 결과 프로젝트 크기가 1.5GB대 대신 100MB가 되었습니다. 그러나 이것이 최종적으로 해결되었는지 여부는 저에게는 알 수 없습니다.

![안드로이드 모바일 프로젝트에서의 귀찮은 문제들](/assets/img/2024-05-23-AnnoyingproblemswithAndroidmobileanyprojects_0.png)

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

지난에 개발한 기능 중 하나로 프로젝트에서 천 개 이상의 파일을 제거했어요. 클릭베이트 제목으로 좋을 거예요, 하지만 실제로는 훨씬 간단해요 — 이전 담당자가 TypeScript 프로젝트를 Android 프로젝트로 복제했을 때, 전체 node_modules 폴더를 커밋하지 않고 얼마 전 사용한 노드 모듈 폴더(.gradle이나 build 폴더와 유사한)를 포함했어요. 왜냐고요? 다행히 매달 또는 두 달에 한 번 특별한 파일을 생성하고 그 파일을 프로젝트에 사용할 수 있게 하기 위해서예요. 왜 별도의 IDE에서 프로젝트를 열고 거기서 코드 생성을 하지 않을까요? 저도 잘 모르겠어요.

![2024-05-23-AnnoyingproblemswithAndroidmobileanyprojects_1.png] (/assets/img/2024-05-23-AnnoyingproblemswithAndroidmobileanyprojects_1.png)

좋은 방법 중에 템플릿이 언급될 수 있어요. 이 기능 덕분에 프로젝트를 한 번 생성하고 필요에 딱 맞게 만들어진 구조를 사용할 수 있어요. 특히 많은 마이크로서비스를 보유한 회사에게 관련이 있어요.

# 읽어볼 내용이 없어요

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

<img src="/assets/img/2024-05-23-AnnoyingproblemswithAndroidmobileanyprojects_2.png" />

프로젝트를 열 때 빌드되지 않는 문제에 대한 이야기를 남겨보세요. 또는 몇 시간 동안 그 문제를 해결하기 위해 애쓰게 되는 경험에 대해 이야기해보세요.

한 번만 개발 환경을 설정하는 데 도움을 받을 수도 있지만, 새 노트북을 사용하거나 새로운 동료가 합류할 때마다 동일한 문제가 반복되곤 합니다.

README를 만들기 위해서만 만드는 것은 가치가 없죠. 종종 적절한 명명법으로 이미 명확한 모듈 설명이나 2년 전에 유효했던 문서들을 볼 때가 있습니다.

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

누군가가 모바일 개발자가 아닌 사람이 모바일 프로젝트를 열면, 반대의 경우도 마찬가지지만, 무언가를 빌드할 수 없다면 먼저 README를 확인할 것입니다. 특히 iOS 프로젝트에서는 여전히 pods를 사용하는 경우나 Docker 또는 다른 설정이 많이 필요한 프로젝트의 경우에 이는 특히 흔합니다.

꽤 좋은 습관은 그 공정들과 모든 스크립트들을 설명하는 것입니다. 그 공정들이 뒷담화처럼 작동하는 것에 대해, 그것들을 자동화하기 위해 한 주 동안을 쏟아부었지만 한 시간 안에 할 수 있는 일을 한 주 쏟아 부을 수도 있습니다.

# 설정 파일 없음

불일치하는 간격으로 커밋을 자주 보곤 했나요? 동료들이 코드를 마음대로 서식을 맞추다보면 모든 것이 엉망이 되고, 다음 코드 리뷰에 +100500줄을 추가하는 일이 벌어지곤 했나요? 또는 변경된 파일마다 imports가 뒤죽박죽이 되어 충돌을 수동으로 해결해야 하는 경우가 있긴 했나요?

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

모든 이슈는 코드 스타일, EditorConfig 및 정적 분석과 함께 Reformat 코드 기능으로 쉽게 해결할 수 있어요.

가장 중요한 점은 귀하의 팀(또는 권한이 충분하다면 개인적으로)이 특정 규칙을 모두가 동일하게 준수해야 한다고 결정하고 이를 따르아야 한다는 것입니다. 이러한 구성은 프로젝트에 추가되어 IDE가 모두에게 동일한 설정을 지정할 수 있게 합니다. 코드 포맷팅 덕분에 수입, 들여쓰기, 형식 지정 및 일반적으로 코드 리뷰 중에 거슬리는 다른 사항들에 대한 문제가 더 이상 발생하지 않을 거예요.

# 의존성 업데이트

![의존성 업데이트](/assets/img/2024-05-23-AnnoyingproblemswithAndroidmobileanyprojects_3.png)

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

이 귀찮은 일에 대해 신경 쓸 필요는 없을 수도 있지만, 고객은 생산성에서 안정성을 요구할 것입니다... 기술적 부채 티켓을 만들어 동료 / 관리자 / 고객에게 이 단계의 중요성을 설명해 보는 게 좋겠죠. 이런 문제가 있는 여러 프로젝트를 상기하며, 개인적으로 문제를 겪었던 한 가지 사례가 있었어요 :)

Google은 단순히 targetSDK를 올리지 않으면 업데이트를 거부했는데 (안드로이드 개발자들에게 큰 고통이었습니다), 이 지원에 상당한 시간이 필요했죠. 그래서 모든 노력이 새로운 OS 버전 지원이 추가된 앱의 새 버전을 준비하는 데 집중되어, 클라이언트는 제때 업데이트를 받지 못했어요.

또한 특정 사용자들이 구글 라이브러리 버전의 버그로 인해 충돌이 발생하기도 했었는데, 매우 드문 일이었지만 실제로 발생했습니다. 그 버그는 스택 트레이스를 남겨두었는데 원인이 명확하지 않았어요. 라이브러리를 업데이트하고 나서 그 충돌은 발생하지 않았습니다.

한 번은 프로젝트에서 난독화가 활성화되어 있었는데, 긴급하게 업데이트를 릴리스해야 해서 GSON을 업데이트하지 않고 AGP만 업데이트했던 적이 있었어요. 라이브러리를 업데이트하려면 특정 이주가 필요했습니다. 문제는 테스터들이 조금 다른 앱 버전을 가지고 있었고, 그것이 나중에 Google Play에 릴리스된 것이었지만 라이브러리는 새 AGP 규칙을 준비하지 못했단 거죠. 그 결과, 난독화로 네트워크 모델의 필드가 제거되었습니다. 충돌은 없었지만 앱의 주요 기능이 중단되어 데이터의 절반이 손실된 채로 남았습니다.

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

# 건축의 부재/“건축”의 존재

아, 파트타임 직업 또는 더 나쁜 경우, 본 업무에서 프로젝트를 열고 난 후, 필요한 파일을 찾을 수 없거나 프로젝트에 있는 전반적인 기능을 이해하는 데 고난을 겪고 있는 상황이군요. 다중 모듈 아키텍처, 추상화, 깔끔한 코드? 그런 건 잊어버리세요. 나중에 아무도 읽지도 유지 보수하지도 않을 거고, 일 년 후에는 버려지고 처음부터 다시 쓰여질 가능성이 높습니다. 그렇게 된다면 괜찮겠지만, 분명히 당신 다음에 누군가 새로운 기능을 추가할 것이고 그 후에 젠장할 것입니다.

한 번, 고객이 6개월 동안 6명의 다른 사람들에 의해 작성된 약 열 개의 문제를 고치라고 요청했던 적이 있었습니다. 어려웠나요? 그런 것은 별로 어렵지 않았습니다. 왜냐하면 프로젝트 자체가 기술적으로 복잡하지 않았기 때문이죠. KMM에서 상업적 경험이 전혀 없는 사람들이 작성했으며, 앱 자체는 주요 복잡성이 항상 비즈니스 로직에 있었습니다. 그래서 대부분은 보이기-편집하기-보내기-다시 모든 것을 보이는 종류의 앱이었습니다.

이 프로젝트에서 가장 큰 문제는 유증의 열망이 놀랍게도 있었다는 것이었습니다. 매우 단순하고 명백한 기능마다 함수 입력 매개변수를 사용할 수 있는데도, 사용 사례가 만들어지고 한 번만 사용되었습니다. 더 나쁜 점은 수량조차 것이 아니라 여러 사용 사례의 카스케이드 사용이었는데, 디버깅을 믿을 수 없을 정도로 어렵게 만들었으며, 브레이크포인트의 수가 IDE를 넘도록 압도했습니다. 그 뿐만 아니라 이러한 것들은 비동기 함수조차 아니라 예전의 콜백 지옥이었습니다.

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

![image](/assets/img/2024-05-23-AnnoyingproblemswithAndroidmobileanyprojects_4.png)

# 댓글

지혜로운 사람들은 코드가 주석 없이도 명확해야 한다고 말합니다. 그렇게 해야 한다는 조언은 귀중하지만 비즈니스에서 요구사항이 나타나면 완벽한 코드에 큰 지팡이를 삽입해야 한다는 요청이 옵니다. 지금 이 작업을 기억하지만 한 달 후 성공적으로 잊을 겁니다.

무언가를 문서화해야 한다고 생각된다면, 그것이 다소 명백하다 해도 수행해야 합니다. 그리고 올바른 Git 전략과 관련하여, 나중에 요구사항을 쉽게 파악하고 코드를 작동 상태로 유지하는 지팡이가 무엇인지 이해할 수 있게 됩니다.

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

<img src="/assets/img/2024-05-23-AnnoyingproblemswithAndroidmobileanyprojects_5.png" />

# 정적 분석

하지만 모든 동료가 코드를 완벽하게 서식 지정하기 위해 두 개의 버튼을 누를 것은 아닙니까? 그래서 프로젝트에 린터를 설정하는 가치가 있습니다. 전반적으로 그들은 좋은 목적을 갖고 발명되었으며 릴리스 시에 끄지 않도록 해야 합니다.

정적 코드 분석 덕분에 사람 눈으로는 보이지 않거나 단순 부주의로 인해 발생할 수 있는 다양한 종류의 버그를 예방할 수 있습니다. 프로젝트에서 표준 Android 린터를 사용하여 리소스 관련 문제에 대해서는 꽤 좋거나 코드에 대한 추가적인 린터를 사용할 수 있습니다. 저는 현재 ktlint를 사용 중이며, 프로젝트마다 동반되는 제 자체 gradle 플러그인과 .editorconfig를 사용하여 코드 분석을 수행합니다.

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

팀원 전체에 이메일을 보내고, 모든 빌드가 초록색이 될 때까지 코드 리뷰를 하지 말겠다는 합의를 한다는 것은 코드 청결도에 대한 좋은 동기부여가 될 수 있습니다.

![AnnoyingproblemswithAndroidmobileanyprojects](/assets/img/2024-05-23-AnnoyingproblemswithAndroidmobileanyprojects_6.png)

# 프로젝트 속 쓰레기

프로젝트를 여는데, 알 수 없는 개발자가 넣은 무서운 라이브러리가 있고, 이를 이 프로젝트로 끌어들인 동료는 이제 오랫동안 프로젝트를 떠난 자이며, 당신의 차례로 영어로 된 메뉴얼을 읽어야 할지도 모릅니다. 이를 경험하지 못한 경우 정말 운이 좋습니다! 저는 그렇게 운이 좋지 않았으니, 최근 기억에 남는 몇 가지 사례를 소개해 드릴게요.

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

현재 프로젝트에서는 난독화가 없으며, 앱은 다이어트를 해야 할 것 같아요. 호기심에 한 번 앱의 용량을 확인해보았더니 익숙하지 않은 폴더가 5-6MB 가량 있었는데 APK 자체는 52MB만 있더라구요. 의존성을 확인하고 구글링을 좀 해보니 PDF 파일을 표시하기 위한 라이브러리라는 것을 알게 되었는데, 추가 의존성도 몇 메가 바이트에 달하는 크기였어요. 만약 고유한 프로젝트이거나 최대한 유연성이 필요한 경우였다면 납득할 만했겠지만, 안 들리는데로 PDF를 표시하기 위한 기능은 이미 안드로이드 SDK에 있는 것을 이미 알고 있는 거죠. 총알 문서를 읽고 프로그래밍을 한 시간 했더니 모든 것이 정말 원활하게 작동했어요. 한 시간 뒤에는 삭제된 컴포넌트를 찾아내게 되어 앱이 총 12MB나 다이어트를 성공한 셈이 되었어요.

작년 이맘때부터 White Label 원칙에 따라 만든 하이브리드 애플리케이션에 대해 일해오고 있는데, 이 해결책은 거의 모든 고객에게 적용할 수 있지만 특정 구성을 통해 다른 클라이언트를 위해 기능을 맞춤화하고 확장할 수 있어요. 많은 도전이 이미 있었는데, 그 중 하나는 Compose를 사용하여 화면 탐색을 하는 라이브러리였어요. 이 솔루션을 사용하는 것이 간단하다고 말하기는 조금 과장되었을 것이고, 조금 전에 팀이 해당 라이브러리를 프로젝트에서 제거할 계획이라는 것을 알게 되었어요. 안드로이드에서 탐색을 한 번 다뤄본 적이 있다면, 단계적으로 할 수 있는 것이 아니라 한꺼번에 모든 것을 이주해야 하는 과정이 필요하다는 것을 이해하실 겁니다. 알 수 있는 한, 프로젝트에 대한 부적절한 의존성 선택은 장기적으로 도태되는 손해를 일으킬 수 있습니다.

![annoyingproblemswithAndroidmobileanyprojects](/assets/img/2024-05-23-AnnoyingproblemswithAndroidmobileanyprojects_7.png)

### 난독화 없음 / 리소스 축소

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

많은 개발자들이 이러한 설정을 조정하는 것을 정말 싫어합니다. 앱을 심각하게 망가뜨릴 수 있기 때문에 그것들을 비활성화하고 테스트를 시작할 때까지 어디가 문제인지 알 수 없을 수도 있습니다. 또한, 여러 사용하지 않는 자원을 제거할 수 있게 해주어서 애플리케이션의 크기에 상당한 영향을 미칩니다. 특히 Compose를 사용할 때 그 효과가 좋습니다.

![이미지](/assets/img/2024-05-23-AnnoyingproblemswithAndroidmobileanyprojects_8.png)

# Git 전략이 없음

Particular Software의 분산 시스템 디자인 기초 과정에서는 기업의 창립자가 개발 경험이 15년 이상인 사람들에게 커밋의 이름 짓는 것이 매우 중요하다고 얘기합니다. 아니요, 농담이 아닙니다. 이 문제는 일반적으로 자주 언급되지 않지만 개발 과정에서 매우 중요합니다. "수정"이라는 텍스트로 커밋을 푸시할 수 있는 상황에서 꼭 신중하게 작성해야 할까요? 좀 더 자세히 살펴보겠습니다.

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

먼저, 브랜치 명명에 대해 알아보겠습니다. 프로그래머들은 종종 대량의 코드 개정을 동시에 다루는데, 현재 보고 있는 코드 버전이 무엇인지 정확히 이해하는 것이 중요합니다. 올바른 브랜치를 찾아야 하거나 비슷한 이름을 가진 여러 개의 브랜치 중 하나를 선택해야 할 때, 때로는 필요한 종류를 찾아야 할 수도 있는데, 이는 혼란스럽고 시간이 오래 걸릴 수 있습니다. 팀이 특정 유형의 구조에 합의했다면, 모든 것을 간단하게 만들 수 있습니다. 이 경우 cicd/sonar-cube-check-implementation 또는 bugfix/superuser-right-are-not-properly-assigned, 또는 feature/property-detail-screen과 같은 네이밍 규칙은 각 브랜치에 무엇이 있는지 논리적으로 이해하는 데 도움이 됩니다.

또한 GitHub, JIRA 또는 다른 도구와 같은 이슈 추적 시스템과 버전 관리 시스템 간의 강력한 통합에 대해 이야기해볼 가치가 있습니다. cicd/123-sonar-cube-check-implementation과 같이 브랜치를 명명하면, 여기서 123은 이슈나 티켓 번호이며, 시스템은 자동으로 이를 연결하여 해당 이슈 범위 내에서 어떤 변경 사항이 있었는지와 현재 브랜치 상태를 확인하는 데 도움을 줍니다. 항상 혼란을 예방하는 것은 아니지만, 한 번 EM이 티켓에서 브랜치가 병합되었음을 보고 클라이언트에게 전달했지만, 프로젝트가 무언가 문제를 일으키는 git 전략을 가지고 있어 잘못된 위치로 병합되었음을 못 봤던 적이 있었죠. 하지만 상황을 빠르게 이해하는 데 도움이 되죠.

커밋 메시지에 대해 이야기해보자면 - 주로 한 두 단어만 적힐 때가 많은데도 50개의 파일이 변경될 수 있습니다. 이제 한 브랜치에 10개의 커밋이 있다고 상상해보세요. 그리고 10개의 커밋이 모두 "fixes"라고 한다면 어떨까요? :)

순전히 실용적인 관점에서, 동료들을 통해 경험적으로 테스트한 결과, 다음 유형의 통합이 상당히 잘 작동하는 것으로 밝혀졌습니다:

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

- 브랜치 가시성: 작업 X를 나타내는 브랜치가 사라지지 않아요. 매니저는 개발자가 작업 상태를 업데이트하는 것을 잊어도 언제든지 작업이 진행 중인지 완료된 상태인지 확인할 수 있어요.
- 작업 기록: 작업 X에서는 6개월 지난 후에도 완료하기 위해 변경된 모든 파일을 찾을 수 있어요. "이 작업 기억 나요? 다시 모두 다시 해야 해"라는 비즈니스 요구가 오더라도 변경된 로직을 찾는 데 시간을 낭비할 필요가 없어요. 나중에 이 작업을 수행하는 다른 사람들이 있더라도 작업 기록에 액세스하여 관련 히스토리를 확인하기 쉽게 해요.
- 명확한 커밋 메시지: 각 커밋은 명확하게 설명되고 작업에 연결돼요. 작업이 커밋 하나로 완료됐더라도, 나중에 개발자가 변경이나 추가 사항이 필요하다는 것을 깨달아 작업 X의 일부인 경우가 있어요. 그럼 이런 경우에는 히스토리에 작업 X와 관련된 두 개의 커밋이 남아 있어요.
- 커밋 사이의 이동이 쉬워요: 큰 기능이 포함된 10개의 커밋이 있는 상황을 상상해보세요. 어떤 중요한 부분을 찾아야 할 때나 기능이 어디서 망가졌는지 확인해야 할 때 유용해요. 특히 큰 기능이나 오랜 기간동안 중요한 정보를 찾아야 할 때는, 구조화된 메시지에서 한 단어 메시지보다 요지를 파악하기 훨씬 쉬워요.

당연히 이 방법이 여러분과 팀에 적합하지 않을 수도 있지만, 어떤 형태로든 존재함으로써 팀의 작업을 크게 간소화할 수 있어요.

![Annoying problems with Android mobile any projects](/assets/img/2024-05-23-AnnoyingproblemswithAndroidmobileanyprojects_9.png)

# CI/CD 설정 오류 및 미구성

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

한 번 인터뷰 중에, 어떤 개발자가 CI/CD가 필요하지 않다고 주장했어요. 그 이유는 자신의 노트북에서 어플리케이션을 원활하게 빌드할 수 있도록 도와주는 작은 스크립트가 있기 때문이라고 했어요. 실제로 그의 주장은 옳았습니다. 그가 이 프로세스를 최적화하고 완벽한 빌드를 격리된 환경에서 작업할 수 있었으니까요. 그러나 보다 심각한 프로젝트에 대해서는 CI/CD가 꼭 필요하고 시간을 절약할 수 있어요.

예를 들어, 하나의 화이트라벨 제품을 다루는 회사가 있었는데, 이 제품은 여러 가지 설정을 가지고 있었고 수작업으로 배포 과정이 매우 느렸어요. 어플리케이션을 빌드하고 필요한 곳에 배포하는 것을 상상해보세요. 일반적으로 이 프로세스는 수작업으로 약 10분이 소요되었습니다. 그런데 만약 20개, 50개, 100개 이상의 어플리케이션이 필요하다면 어떨까요? 그리고 이미 테스트된 것이 출시 준비가 된 상태인데도 개발자가 휴가 중인 경우 어떡하죠? CI/CD 덕분에 이 프로세스를 자동화하고 최대한 빠르게 만들었어요. 병렬화를 통해 모든 클라이언트가 15분 내에 배포를 받을 수 있게 되었죠. 비즈니스에게는 모든 것을 자동화하는 것이 편리하고, 개발자에게는 단조로운 작업을 하지 않고 해낼 수 있는 점이 편리합니다.

또한 모바일 데브옵스 같은 사람들이 이러한 프로세스를 설치하는 경우가 있다는 것도 언급할 가치가 있어요. 멋진 회사에서는 이들이 모바일 작업방식을 잘 이해하고 필요한 모든 작업을 처리하는 똑똑한 사람들입니다. 그런데 모든 사람이 그만큼 능숙한 것은 아닐 수 있어요. 미국 스타트업에서 안드로이드 아키텍트로 일했을 때, 빌드 컴파일 시간에 상당한 영향을 미치는 문제를 발견했습니다. 기억하시다시피, assemble이라는 gradlew 작업이 있어요. 기본적으로 모든 버전의 어플리케이션을 빌드합니다. 한 번에 빌드하는 데 15분이 걸린다면, 어플리케이션이 2가지 설정이 있다면 이 작업은 필요한 하나만을 빌드하는 데 1시간이 걸릴 거예요. CI/CD 파이프라인 관점에서 모든 것이 잘 설정되어 있었지만, 이 필요성을 이해하고 올바른 구성 조정을 할 수 있는 사람은 모바일 개발자만 알 수 있는 사실이죠.

<img src="/assets/img/2024-05-23-AnnoyingproblemswithAndroidmobileanyprojects_10.png" />

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

지금까지 여기까지입니다. 목록이 완전하지 않고 무작위이며 추가할 주제가 있습니다. 개발자가 APK를 이메일로 보내거나 슬랙에 게시하는 프로젝트를 진행해본 적이 있다면 댓글에 작성해 주세요. 또한 다른 프로젝트에서 자주 마주치는 문제를 공유해주세요.
