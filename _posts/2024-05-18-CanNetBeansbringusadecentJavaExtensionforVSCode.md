---
title: "넷빈스가 VS Code용으로 괜찮은 자바 확장 기능을 제공할 수 있을까요"
description: ""
coverImage: "/assets/img/2024-05-18-CanNetBeansbringusadecentJavaExtensionforVSCode_0.png"
date: 2024-05-18 15:15
ogImage:
  url: /assets/img/2024-05-18-CanNetBeansbringusadecentJavaExtensionforVSCode_0.png
tag: Tech
originalTitle: "Can NetBeans bring us a decent Java Extension for VS Code?"
link: "https://medium.com/itnext/can-netbeans-bring-us-a-decent-java-extension-for-vs-code-836b339f6109"
---

작년 말, 오라클은 Java 개발자들을 위한 Visual Studio Code용 새로운 확장 프로그램을 발표했습니다. 이 확장 프로그램은 Java 플랫폼 지원이라고 불리며, 오라클의 발표에 따르면,

이 글에서는 이 VS Code 확장 프로그램을 살펴보고, Java 개발의 미래에 대한 올바른 방향으로의 한 걸음인지 아닌지를 비교해봅니다. 그리고 VS Code에서 Java 개발을위한 두 가지 인기 있는 확장 프로그램인 Microsoft Extension Pack for Java와 Spring Boot Extension Pack과 비교해보겠습니다.

![이미지](/assets/img/2024-05-18-CanNetBeansbringusadecentJavaExtensionforVSCode_0.png)

# 오라클이 왜 Visual Studio용 Java 확장 프로그램을 만들기로 선택했는가

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

전통적으로 NetBeans는 오라클이 자바 개발을 위해 공식 IDE로 지원했던 것이지만, IntelliJ의 인기가 증가함에 따라 특히 IntelliJ IDEA Community Edition이 무료 오픈 소스 IDE로 출시된 후에 NetBeans는 약화되었고 약화되었으며, 오라클이 전체 NetBeans 프로젝트를 아파치 재단에 기부하기로 결정했습니다.

Red Hat은 Eclipse JDT Core를 Java 언어 서버로 사용하기 위해 VS Code에 포장했고, Microsoft 및 스프링 팀은 그 위에 몇 가지 확장 기능을 더 덧붙였습니다. 이렇게 함으로써 IntelliJ 이외의 Java 개발을 위한 VS Code에서 사용할 수 있는 것을 적어도 무언가를 제공해 주었습니다.

이 배경을 알고 나면, Eclipse IDE의 핵심 부분을 개선하고 VS Code의 Java 언어 서버로 다시 소개하려고 한 Red Hat과 유사하게, 오라클도 NetBeans 언어 서버로 같은 시도를 하고 있으며 이를 통해 IntelliJ와의 경쟁에서 자신들을 유지하려고 하고 있습니다. 인기와 VS Code의 기능 상의 확장 기능을 개발하기 위해, 다음과 같은 기능이 포함됩니다:

- 다중 언어 지원
- 브라우저에서 실행 가능
- 빠르고 가벼움
- 매우 강력한 원격 개발 기능
- 그리고...

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

<img src="/assets/img/2024-05-18-CanNetBeansbringusadecentJavaExtensionforVSCode_1.png" />

## VS Code를 이용한 자바 개발 경험

나에게 있어서 의심의 여지 없이 IntelliJ IDEA Community Edition이 자바 개발을 위한 최고의 선택이라고 생각해요. 빠르고 믿을 수 있고 무료이며 기능이 풍부하며 그 외 여러 장점들이 있어요.

VS Code의 많은 기능 중에서도 저에게 흥미로운 두 가지 핵심 기능은 있어요. IntelliJ에서 부족하다고 느끼는 것은 첫째는 VS Code 원격 개발 기능과 두번째는 자바 이외의 다른 프로그래밍 언어(특히 Go와 JavaScript) 지원입니다. 그래서 항상 VS Code 확장 프로그램을 사용하여 실제 프로젝트를 테스트하고 IntelliJ과 비교하려고 노력해요. 지금까지 IntelliJ과 동일한 기능을 수행할 수 있는 VS Code Java 확장 프로그램을 찾지 못한 것 같아요.

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

# 오라클 자바 플랫폼 익스텐션은 무엇을 제공하나요?

이 기사의 나머지 부분에서는 오라클 자바 플랫폼 익스텐션의 기능에 대해 깊이 있게 살펴보고, Red Hat Java Language Server를 기반으로 한 Microsoft Extension Pack for Java와 비교해 볼 것입니다. 이 비교과정에서 특정 도서에서 PolarBookShop 클라우드 네이티브 애플리케이션을 열어보려고 합니다:

GitHub 저장소에서 전체 프로젝트에 액세스한 다음 PolarBookshop 폴더를 열어보세요. 이 클라우드 네이티브 앱은 Spring Boot로 구현된 여덟 가지 다른 마이크로서비스로 구성되어 있습니다.

## Netbeans를 기반으로 한 새로운 언어 서버

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

제가 Java로 프로그래밍을 시작했을 때에는 NetBeans 4.5를 IDE로 사용했고 정말 좋았어요. 여러 해 동안 NetBeans IDE를 계속 사용했는데, 일부 특정 프로젝트를 위해 Eclipse를 잠깐 사용한 적도 있어요. IntelliJ로 전환하는 것을 망설였지만, 결국 NetBeans를 영원히 작별하고 IntelliJ IDEA Community Edition으로 이주하기로 결정했어요.

이제 VS Code 확장에 이 NetBeans에 기반한 새 언어 서버가 있는 것을 보니, 새로운 리뷰를 위해 적어도 고려할 가치가 있겠다 싶어요. 이전까지는 Eclipse JDT Core에 기반한 Red Hat 언어 서버가 유일한 선택지였는데, 이제는 다른 대안이 생겼네요.

이 두 언어 서버를 실제로 비교하기 전에, 이 언어 서버의 주요 이점을 언급할 가치가 있어요:

NetBeans 언어 서버의 주요 제한 사항은 Java 11 이상을 기반으로 하는 프로젝트를 지원한다는 것이에요. 그러나 Red Hat 언어 서버는 Java 1.5 이상을 지원하고 있어요!

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

넷빈스 언어 서버의 장점 중 하나는 독립적으로 작동한다는 것이며, 언어 서버만 설치하면 Java 프로젝트를 열고 실행할 수 있지만, 레드햇 언어 서버를 실제로 사용하려면 Microsoft Java 언어 팩을 설치해야 합니다.

## 메모리 사용량 및 성능 비교

이 두 확장 프로그램을 사용하여 PolarBookshop 프로젝트를 열어 메모리 사용량 및 인덱싱 성능을 확인해보기로 합시다.

오라클 Java 확장 프로그램 ⛔️:

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

- 색인 시간: 약 70초
- 메모리 사용량: 약 2.2 GB

Microsoft Java Extension Pack ✅:

- 색인 시간: 약 40초
- 메모리 사용량: 약 1.5 GB

이클립스 ™ JDT Language Server(마이크로소프트 확장팩)를 사용할 때, 각 파일을 열 때마다 언어 서버는 해당 파일을 색인하며 어느 정도는 점진적으로 색인이 발생하는 것으로 보입니다. 그러나 NetBeans Language Server는 프로젝트를 한 번에 열고 색인하므로 RedHat 언어 서버의 색인 시간과 메모리 사용량이 조금 더 나은 것으로 보입니다.

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

## 자바 프로젝트 열거나 생성하기

두 확장 프로그램은 Maven과 Gradle을 기반으로 하는 Java 프로젝트를 기본으로 지원합니다. 전에도 언급했듯이, Microsoft 확장 프로그램의 색인 프로젝트가 더 빠릅니다.

일반적으로 Microsoft Java 확장 팩이 더 성숙하다고 생각했습니다. 예를 들어 Oracle Java 확장 프로그램은 Gralde 프로젝트에서 간단한 문제가 있습니다.

Oracle Java 확장 프로그램의 흥미로운 기능 중 하나는 Project Explorer 패널입니다. 이 패널은 NetBeans Project Explorer 패널을 연상시킵니다. 해당 패널에서 루트 폴더의 각 프로젝트를 별도 노드로 볼 수 있습니다. 이것은 논리적 프로젝트 구조의 개요를 제공하여 프로젝트 탐색을 쉽게 할 수 있습니다. Java 프로젝트를 빌드, 테스트 및 실행하는 데 활용할 수 있습니다.

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

![이미지](/assets/img/2024-05-18-CanNetBeansbringusadecentJavaExtensionforVSCode_2.png)

Microsoft Java Extension 팩의 프로젝트 탐색기 패널과 비교해서 오라클 것이 훨씬 더 좋고 더 편리하며 더 깔끔하다고 생각했어요.

![이미지](/assets/img/2024-05-18-CanNetBeansbringusadecentJavaExtensionforVSCode_3.png)

Spring Boot 프로젝트의 경우, Spring Boot 익스텐션 팩 프로젝트 뷰와 대시보드를 이 두 가지보다 더 좋아해요. Spring Boot 프로젝트에 대해 훨씬 더 유용하고 정보를 잘 제공하며 쉽게 관리할 수 있도록 도와줍니다.

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

## 코드 완성

코드 완성 기능에서 두 확장 프로그램은 동일한 수준입니다. 다만 오라클 자바 익스텐션의 코드 완성이 마이크로소프트 자바 익스텐션 팩보다 약간 더 느린 것을 알 수 있습니다. 또한, 마이크로소프트 익스텐션은 코드 완성 대화상자가 조금 더 읽기 쉬운 것 같습니다 (적어도 제 경험 상):

![이미지](/assets/img/2024-05-18-CanNetBeansbringusadecentJavaExtensionforVSCode_4.png)

![이미지](/assets/img/2024-05-18-CanNetBeansbringusadecentJavaExtensionforVSCode_5.png)

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

마이크로소프트 자바 익스텐션 팩의 코드 완성 기능 중 한 가지 장점은 Visual Studio IntelliCode 익스텐션이 있어요. 이는 코드 컨텍스트를 이해하고 기계 학습을 활용한 인사이트를 제공하는 AI 지원 개발 기능을 제공해요.

두 익스텐션 모두 Source actions 기능을 가지고 있어요. 이 기능은 코드 생성에 도움이 돼요.

## 실행 및 디버그

이 두 익스텐션의 이러한 기능은 거의 동일해요. 오라클 자바 익스텐션의 한 가지 장점은 실행 구성 패널을 제공한다는 것이에요. 이는 흥미롭고 프로그램 인수, VM 옵션, 환경 변수 및 프로젝트의 작업 디렉토리를 설정할 수 있게 해줘요. 이 패널을 이용하면 일부 고급 사용 사례를 제외하고는 launch.json 파일을 수동으로 설정할 필요가 없어요.

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

아래는 Markdown 형식으로 변경된 내용입니다.

![image](/assets/img/2024-05-18-CanNetBeansbringusadecentJavaExtensionforVSCode_6.png)

Oracle Java extension pack supports Native Image Debugger, which allows Java-style debugging of Ahead of Time compiled native images produced by GraalVM.

Another advantage of the Oracle Java Extension is that it supports run main with continuous mode for gradle projects.

## Testing view

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

이 기능에서는 Microsoft Java Extension Pack이 훨씬 좋습니다. Oracle Java Extension에는 기본으로 테스트 패널이 없지만 Microsoft Java Extension에는 있으며 각 프로젝트를 개별적으로 표시합니다. 또한 프로젝트를 실행할 때 테스트를 실행하면 전용 뷰가 있습니다.

![image](/assets/img/2024-05-18-CanNetBeansbringusadecentJavaExtensionforVSCode_7.png)

## 기타 기능

리팩터링이나 Java Doc 생성 및 편집과 같은 일부 유용한 기능은 두 확장 프로그램 모두 동등하며 두 확장 중 어느 것에 대해서나 특별한 이점을 창출하지 않습니다.

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

# 마지막으로 생각해 볼 것

경쟁은 항상 소비자에게 좋을 수 있지만, 오라클 자바 익스텐션에 대한 초기 인상은 놀랄만한 것은 아니었습니다. 이 제품은 미래에 Microsoft 자바 익스텐션과 경쟁할 수 있는 기본 제품이라고 생각했습니다.

물론, 네이티브 이미지 디버거나 프로젝트 탐색기 패널, 실행 구성 패널과 같은 고유한 기능도 있습니다.

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

그런데 개선 가능한 부분이 많습니다. VS Code를 사용하여 Java 개발 경험에 대해 의견을 남겨주세요.

다가오는 이야기를 위해 저를 팔로우해주세요:

트위터에서 나의 짧은 기술 포스트를 읽어보세요.
