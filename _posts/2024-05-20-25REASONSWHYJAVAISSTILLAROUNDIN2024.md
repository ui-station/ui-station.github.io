---
title: "25가지 이유 2024년에도 왜 자바가 여전히 인기 있는지"
description: ""
coverImage: "/assets/img/2024-05-20-25REASONSWHYJAVAISSTILLAROUNDIN2024_0.png"
date: 2024-05-20 15:42
ogImage:
  url: /assets/img/2024-05-20-25REASONSWHYJAVAISSTILLAROUNDIN2024_0.png
tag: Tech
originalTitle: "25 REASONS WHY JAVA IS STILL AROUND IN 2024"
link: "https://medium.com/javarevisited/25-reasons-why-java-is-still-around-in-2024-452c582d55d0"
---

![Java Image](/assets/img/2024-05-20-25REASONSWHYJAVAISSTILLAROUNDIN2024_0.png)

“프로그래밍 언어는 두 종류밖에 없어: 사람들이 불평하며 사용하는 것과 아무도 사용하지 않는 것.” — 바르네 스트롭스트룹.

제가 Java를 배웠을 때, 객체지향 프로그래밍 열풍이 불었고 (90년대 후반), Java는 그 개념을 실제로 구현한 유일한 언어였던 것 같아요 (이전에 C++도 공부했지만, 정말로 한계라고 생각했죠).

제가 Java가 어떻게 발전해 왔는지 정말 좋아했어요. Python을 살펴봤었는데, 사람들에게 열정을 일으키는 것 같지만, 저는 딱히 그 열풍에 휩싸이지 않았어요; 다시 말해서, 제가 충분히 깊은 수준으로 들어가지 못했을 수도 있어요. 제가 정말 큰 Java 프로젝트에서 일한 적이 있는데, 그들은 (합리적으로) 꽤 잘 다뤘어요; Python은 그 규모에서 상상도 못 해요. 하지만, 최근의 프로젝트들은 미니 서비스 방향으로 향하고 있는 것 같아요 — 아마 Python은 그것에 적합할지도 모르겠지만, 제게는 잘 모르는 분야예요. Java는 그 부분에 대해서는 알고 있어요.

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

자바를 배울 때는 90년대 후반에 OOP 열풍이 한창이었고, 자바는 실제로 그 개념들을 구현한 언어로 빛이 났죠 (전에 C++을 배웠었지만, 정말). 저는 자바의 플랫폼 독립성을 감사히 여겼어요.

제 생각에는 간결함보다는 구조와 일관성을 선호하는 편이기 때문에 자바를 계속 사용하고 있네요.

제 경력 동안에는 이상적이지 않은 자바 코드베이스와 여러 번 마주치게 되었는데, 때로는 자바에 조금 심심해하기도 했어요. 하지만 다른 많은 훌륭한 프로젝트들에 참여하면서 자바를 다시 사랑하게 되었죠.

자바에 대해 비판하고 불평하는 사람들은 종종 자바스크립트에 더 노출된 젊은 세대인 것 같아요. JS와 비교하면 자바는 조금 무겁고 제한적으로 느껴질 수 있어요 — 보일러플레이트가 많고, 컴파일러에 의해 엄격하게 시행되는 타입 시스템 등이 그 예시죠. 하지만 선택이 주어진다면, 저는 분명 자바 코드베이스가 서브옵티멀하더라도 JS보다는 자바를 선택할 것입니다.

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

현실 세계에서 일정 경험을 쌓은 후 수백 개의 파일에 흩어진 코드를 처리하면서, 자바의 이른바 "한계"는 실제로 발목을 다치지 않도록 방지하는 안전장치라는 것을 깨닫게 됩니다.

"C++의 창시자인 바네 스트롭스트룹이 할 말을 인용하면, '프로그래밍 언어는 두 가지 종류뿐이다: 사람들이 불평하는 것과 아무도 사용하지 않는 것'"입니다.

자바에 대한 불평이 많다고 생각하십니까? C++를 보세요. 사람들은 그 언어를 사랑하거나 미워하며 마치 피할 수 없는 학대적인 관계인 것처럼 대합니다.

파이썬도 불평을 많이 받았습니다.

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

사람들은 계속해서 GIL(글로벌 인터프리터 락)이 진정한 효율적인 멀티스레딩을 방해한다고 말합니다. Python 코어의 많은 부분이 GIL에 의존하기 때문에 아마도 결코 제거되지 않을 것입니다. 그 결과로 사람들은 여러 프로세스를 사용하고 프로세스 간에 통신하기 위해 메시지 전달 체계를 만들어 이를 해결해야 했습니다. Python의 성능 또한 C/C++로 hotpaths를 다시 작성하지 않으면 굉장히 느립니다. 물론 Python 2에서 3으로의 전환도 그렇죠.

한 번 Django 프로젝트를 작업했을 때(그때는 뜨거운 주제였던 시절) Python이 유형화된 언어보다 더 나은 것으로 생각했습니다(사용 용도에 따라 다르겠지만 1000개 이상의 클래스가 있는 복잡한 시스템에서는 아닙니다).

이 프로젝트가 저 혼자 개발자인 상태에서 여러 사람이 참여하고 코드가 10,000줄 이상이 된 순간, 유지보수가 복잡해졌습니다.

Java로 전환하고 다시 발견했을 때 놀랍다는 생각이 들었습니다. Java와 그 생태계를 사랑하는 것을 깨달았죠. 그래서 Java 생태계에 대해 좋아하는 몇 가지를 메모해 보기로 했습니다. 그래서 Java를 욕하는 누군가가 있다면, 그들이 틀렸다는 이유를 25가지로 설명해 줄 수 있습니다.

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

# 1. 생태계가 성숙합니다

자바는 25년 이상 되었습니다. 이 생태계에서 개발자로 일해온 경험을 토대로, 이 생태계가 몇 년 동안 얼마나 성숙해졌는지 되돌아보는 것이 흥미롭습니다.

자바의 넓은 생태계 중 가장 좋은 점은 다양한 라이브러리, 빌드 도구 및 프레임워크를 선택할 수 있다는 것입니다.

JVM 생태계는 완벽하게 다양하며, 최상의 라이브러리로 거의 모든 문제에 대한 해법이 있으며 모두 고성능이며 잘 유지됩니다. 빌드 도구를 선택할 때 다양한 선택지가 있습니다. 예를 들어 빠르고 재현 가능한 빌드를 위해 Gradle, Maven 및 Bazel을 사용할 수 있습니다. 아직 이 생태계에 익숙하지 않은 경우, 자바는 기본 구현체로 로깅, 데이터베이스 연결, 메시징 및 응용 프로그램 서버와 같은 다양한 기능을 제공하여 좋은 출발점이 됩니다.

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

예를 들어, 애플리케이션에 로깅이 필요한 경우를 생각해보겠습니다. 걱정하지 마세요. Java는 JDK에 기본 로깅 옵션이 내장되어 있어 여러분을 지원해줍니다. 기본 옵션을 좋아하지 않거나 충분하지 않다면 어떻게 할까요? 기본 로깅은 로깅 API에 대한 참조 구현에 불과합니다. 선택할 수 있는 다른 훌륭한 로깅 라이브러리들이 있습니다.

그리고 로깅 뿐만 아니라, Java 생태계는 데이터베이스 연결, 메시징, 애플리케이션 서버, 서블릿 등 다양한 옵션을 제공해줍니다.

## 2. 한 번 작성하면 어디서나 실행하기 (WORA)

이것은 Java 언어의 크로스 플랫폼 이점을 가리키는 우리가 자주 사용하는 슬로건입니다. 여기서 원한 말고 말하자면, 요즘 Java를 배우는 대부분의 개발자들은 이 기능이 소프트웨어 개발에 어떤 혁신적 영향을 미쳤는지 모를 수도 있습니다.

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

Java가 탄생하기 10년 전, C++가 주를 이루던 프로그래밍 언어였습니다. 그러나 개발자들이 직면한 문제 중 하나는 C++의 플랫폼 의존적인 성격이었습니다. C++로 작성된 코드는 다른 운영 체제나 하드웨어 아키텍처에서 실행되려면 재컴파일 및 종종 수정이 필요했습니다.

![image](/assets/img/2024-05-20-25REASONSWHYJAVAISSTILLAROUNDIN2024_1.png)

### 3. 하위 호환성

만약 새로운 Java 버전이 나올 때마다 프로그램을 위한 코드를 다시 작성해야 한다면 어떨까요? 특히 대규모 기관들에게는 매우 비용이 많이 들고 시간이 많이 소요될 것입니다.

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

자바는 오랜 시간동안 사용되어 왔기 때문에 예전 버전의 자바를 기반으로 한 소프트웨어 제품이 많이 존재하며, 다양한 분야에서 중요한 기능을 제공하며 기업의 주요 기반을 형성하고 있습니다.

대규모이고 복잡한 프로젝트가 진행되는 기업 개발에서는 이러한 시스템을 최신 자바 버전으로 이관하는 작업은 신중한 계획과 실행이 필요합니다.

자바의 하위 호환성은 개발자나 조직이 시스템 개발에 많은 투자를 한 상태라도 시스템이 계속 운영되며 완전한 재작성을 필요로하지 않고 유지 관리할 수 있음을 보장해줍니다. 자바(JVM)의 하위 호환성은 또한 마이그레이션 프로세스를 간소화시키며, 기존 시스템의 안정성을 저해하지 않으면서 새로운 기능과 개선 사항을 도입하는 것을 용이하게 합니다.

# 4. 자바 강한 타입 시스템

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

자바는 파이썬과 같이 느슨하게 유형이 지정된 언어와는 달리 강력하게 유형이 지정된 언어입니다. 파이썬과 함께 일해본 적이 있다면, 서로 다른 유형의 값을 동일한 변수에 할당할 수 있는 유연성을 즉시 느낄 수 있으며, 언어가 동적으로 적응된다는 것을 느끼게 될 것입니다.

int age = 25;

String name = “John”;

하지만 이러한 유연성은 대가가 따릅니다. 제가 기억하는 한, 다양한 숫자 데이터 유형을 사용하는 복잡한 계산을 다루는 금융 응용 프로그램에서 작업했었습니다. 자바의 강력한 유형 지정으로 인해, 컴파일러는 호환되지 않는 데이터 유형을 혼합하거나 데이터 손실이나 예기치 않은 결과로 이어질 수 있는 작업을 수행하려는 모든 시도에 경고를 표시했습니다. 이러한 명백한 버그 중 일부는 파이썬과 같은 언어로 작업할 때 런타임까지 눈에 띄지 않았을 수 있습니다.

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

이것이 왜 Java가 기업 애플리케이션을 개발하는 데 흥미로운 이유 중 하나인데요, 특히 은행 및 금융과 같은 산업에서 신뢰성과 보안이 중요한 분야입니다. Java의 강력한 타입 시스템은 런타임 오류를 줄이는 데 도움이 될 뿐만 아니라 변수, 매개변수 및 반환 값의 의도된 데이터 유형을 이해하기 쉽게 해서 코드의 가독성을 향상시킵니다.

## 5. RELEASE CYCLE — 지속적인 개선

![이미지](/assets/img/2024-05-20-25REASONSWHYJAVAISSTILLAROUNDIN2024_2.png)

일반적으로 Java 개발자로서, 몇 년마다 주요 릴리스에 이어 새로운 Java 기능을 얻는 데 익숙했습니다. 그러나 현대 프로그래밍 요구 사항에 따라 Java의 릴리스 주기가 Java 9 릴리스 후 6개월로 바뀌었습니다. 그러나 새 버전으로 넘어가지 않아도 되는 기업 기관을 위해, Oracle은 3년마다 LTS(Long-Term Support) 버전을 릴리스할 것을 제안했습니다.

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

더 자주 발생하는 작은 릴리스는 새로운 자바 버전으로 업그레이드할 때 발생하는 복잡성과 위험을 줄입니다. 점진적인 변경 사항은 역호환성을 더 고려하여 설계되어 있기 때문에 개발자들이 주요 호환성 문제에 직면할 가능성이 적어집니다.

# 6. 최고의 IDE

Java는 다양한 변경과 기능으로 현대적인 개발에 탁월하게 맞는 언어로 발전해 왔습니다. 그러나 IntelliJ IDEA, Eclipse, NetBeans와 같은 강력한 통합 개발 환경(IDE)의 지원을 받지 않는다면 이는 유용하지 않을 수도 있습니다.

지능적인 코드 완성, 자동 리팩터링, 원활한 버전 관리 통합 등과 같은 기능이 없는 환경에서 코드를 작성하는 것은 상상하기 어렵습니다. 그러나 특히 초기의 자바 시대에는 항상 그랬다고 당당히 주장하는 것도 중요합니다.

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

최신 IDE인 IntelliJ와 Eclipse로 빠르게 이동하면, 이들은 Java 개발을 간편하게 만들어 주었습니다. 이 IDE들은 Maven 및 Gradle과 완벽하게 통합되어 컴파일, 의존성 해결 및 프로젝트 관리를 처리합니다. 지능적인 코드 완성 기능과 내장 정적 코드 분석 도구를 통해 Java가 덜 장황하게 느껴지며, 플러그인을 통한 액세스로 환경을 원하는 대로 사용자 정의할 수 있습니다.

# 7. GRAALVM 네이티브 이미지 지원

이미 JVM이 Java를 영광으로 이끌어 주었던 원츄r 개론 자 애플리케이션에서 실행 시간이 지연되므로 애플리케이션이 JVM 상에서 실행되는 것보다 빠르게 시작하는 것입니다. 젠하는 것입니다. 이 이있는데, 나와 같은 개 개발자는 대부분 마이크로서비스, 서버리스 컴퓨팅 및 빠른 시작 및 최적화된 리소스 사용이 중요한 환경으로 이동하고 있으므로, 특히 이제는 프로그램 정보와 시작 시간을 빠르게 시작하기 위해 노력을 기울이고 있습니다. 사용하기 위한 해법 중 하나로 Oracle GraalVM의 고성능 JDK이며, Graal 컴파일러라는 대체 JIT(Just-in-Time) 컴파일러를 사용하여 Java 및 JVM 기반 애플리케이션의 성능을 농도로vey합니다.

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

GraalVM에는 Java 바이트코드를 미리 컴파일하는 네이티브 이미지 유틸리티가 포함되어 있어서 애플리케이션이 거의 즉시 시작될 수 있습니다. Graal 컴파일러는 AOT 컴파일러로도 작동하여 네이티브 실행 파일을 생성합니다. 입력은 Java 바이트코드이고 출력은 네이티브 실행 파일입니다.

여기 재귀를 사용하여 문자열을 뒤집는 작은 Java 프로그램 예제가 있습니다.

```js
public class Example {
    public static void main(String[] args) {
        String str = "Native Image is awesome";
        String reversed = reverseString(str);
        System.out.println("The reversed string is: " + reversed);
    }
    public static String reverseString(String str) {
        if (str.isEmpty())
            return str;
        return reverseString(str.substring(1)) + str.charAt(0);
    }
}
```

이 Java 클래스에서 컴파일하고 네이티브 이미지를 빌드할 수 있습니다.

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

```js
javac Example.java
native-image Example
```

네이티브 이미지 빌더는 Example 클래스를 사전 컴파일하여 현재 작업 디렉토리에 독립 실행 파일인 "example"로 생성합니다. 그런 다음 해당 실행 파일을 실행할 수 있습니다.

```js
./example
```

# 8. 오픈 소스 라이브러리 및 프레임워크.

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

오픈 소스 라이브러리와 프레임워크는 Java가 내 도구상에서 특별한 자리를 차지하는 주요 이유 중 하나입니다.

![이미지](/assets/img/2024-05-20-25REASONSWHYJAVAISSTILLAROUNDIN2024_3.png)

이러한 라이브러리와 프레임워크는 내 프로젝트에 원활하게 통합할 수 있는 구성 요소와 같습니다. 개발자로서 일반적인 기능을 새롭게 만들 필요 없이 바퀴를 다시 발명할 필요가 없습니다. 마치 잘 작성되고 테스트된 코드 저장소를 편하게 사용할 수 있는 것과 같습니다.

이러한 라이브러리의 양이 많아서 한 가지 솔루션에 갇히지 않습니다. 내 요구에 가장 적합한 것을 항상 선택할 수 있습니다. 이러한 라이브러리의 오픈 속성은 투명성과 책임성을 장려합니다. 또한 소스 코드를 살펴볼 수 있고 내부 작동 방식을 이해할 수 있으며 개선 작업에 기여할 수 있습니다.

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

Java에서는 Jackson 및 Gson과 같은 JSON 파싱 라이브러리, Log4j, SLF4j 및 LogBack와 같은 로깅 라이브러리, JUnit, Mockito 및 PowerMock를 포함한 유닛 테스트 라이브러리, 데이터베이스 연결 라이브러리, 메시징 라이브러리 등이 포함된 오픈 소스 라이브러리 목록이 있습니다.

또한 Java는 다양한 프레임워크의 확장성이 있어서 널리 알려진 프로그래밍 언어입니다. Spring과 Springboot는 제가 좋아하는 조합 중 하나입니다. 또 다른 제가 작업한 프레임워크로는 Jakarta Faces, Struts, Hibernate 및 Quarkus 등이 있습니다.

# 9. 멀티스레딩

Java는 멀티스레딩을 지원하여 데이터 처리, 사용자 상호작용 처리, 백그라운드 계산 관리 등을 동시에 처리할 수 있는 애플리케이션을 설계할 수 있습니다. Java는 Runnable 인터페이스를 구현하거나 Thread Class를 확장함으로써 멀티스레딩을 지원합니다.

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

자바의 java.util.concurrent 패키지는 ExecutorService, ScheduledExecutorService, Future, CyclicBarrier 등을 포함한 고수준 동시성 유틸리티를 제공하여 동시성 응용 프로그램을 개발하는 데 도움을 줍니다.

```java
public class MyRunnable implements Runnable {
   public void run() {
       // 새로운 스레드에서 실행될 코드
   }
}
MyRunnable myRunnable = new MyRunnable();
Thread myThread = new Thread(myRunnable);
myThread.start();
```

# 10. 자바의 객체지향적 특성

여러분이 생각하는 것을 알고 있어요: 자바는 유일한 객체지향 언어가 아니기 때문에, Python이나 C와 같은 언어와 무엇이 다른 점을 만드는 걸까요? 실제로 어떤 프로그래밍 언어들은 객체지향의 요소를 채택하거나 객체지향 개념을 지원하는 기능을 도입한 반면, 자바는 객체지향 원칙을 처음부터 고려하여 설계되었습니다.

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

자바는 추상화, 상속, 다형성, 캡슐화와 같은 객체 지향 원칙을 준수하여 복잡하고 확장 가능하며 유지보수가 용이한 소프트웨어 시스템을 구축하는 데 좋은 선택지입니다. 자바가 OOP 패러다임을 지원하는 데서 얻을 수 있는 많은 혜택이 있습니다. 이러한 혜택으로는 모듈화, 유연성, 가독성, 유지보수 용이성 및 확장 가능성이 포함됩니다.

# 11. 메모리 관리와 가비지 컬렉션

![이미지](/assets/img/2024-05-20-25REASONSWHYJAVAISSTILLAROUNDIN2024_4.png)

출처: Digital Ocean

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

솔직히 말해서, 쓰레기를 버리는 걸 싫어해요. 그 일은 항상 귀찮게 느껴지는 가사일 중 하나에요. 많은 분들이 공감하실 거예요. 수동 메모리 관리에서는 개발자가 메모리를 할당하고 해제해야 하는 책임이 있어요. 마치 쓰레기를 버릴 때와 같이 언제 어떻게 각 조각을 처분할지를 결정하는 것과 비슷해요. 쓰레기를 버리는 것을 잊어버리면 혼란스러운 상황이 생기듯, 메모리를 해제하는 것을 잊어버리면 메모리 누수와 성능 문제가 발생할 수 있어요.

자바의 자동 메모리 관리를 생각해보세요. 신뢰할 수 있는 쓰레기 수거 서비스가 있는 것과 같아요. 자바에서는 쓰레기 수거자가 성실하게 메모리를 식별하고 폐기하는 역할을 합니다.

저는 자바로 전향하기 전 C++을 사용한 경험이 있어요. C++는 메모리 관리에 대한 막연한 통제를 제공하지만 메모리 누수가 없는지 확인하기 위한 개발자로서의 책임도 크지요.

반면 자바에서는 저수준 시스템 기술적인 세부사항이나 수동 쓰레기 수거, 기본 OS 또는 메모리 할당 및 해제 추적에 대해 걱정할 필요가 없어요. 쓰레기 수거자가 더 이상 필요하지 않은 메모리를 자동으로 식별하고 회수합니다. 이로 인해 메모리 누출의 위험을 줄일 수 있어요.

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

표준 프로파일러.

VisualVM.

VisualVM은 Java 애플리케이션의 내부 작동을 이해하는 데 필수적인 도구입니다. JConsole 및 VisualGC를 통합하여 쓰레드, 힙 사용량 및 CPU 프로파일링을 모니터링할 수 있는 시각적인 플레이그라운드를 제공합니다. 또한 다양한 JDK 유틸리티와 원활하게 작동하여 신뢰할 수 있는 도구로 유용합니다.

YourKit.

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

이 프로파일러는 내 도구 상자에 비밀 요원이 있는 것 같아요. 이것은 메소드 수준의 세부 정보를 깊이 파고들어 실행 시간과 메모리 할당을 드러냅니다. 이것은 클라우드, 컨테이너 및 클러스터 환경에서 프로파일링하는 간단하고 쉽고 안전한 방법을 제공합니다.

APM 도구.

뉴 렐릭.

뉴 렐릭은 어플리케이션 성능 모니터링 (APM)에 가장 좋은 선택지예요. 내 애플리케이션을 24시간 내내 감시하는 개인적인 비서가 있는 느낌입니다. 실시간 통찰력부터 자세한 트랜잭션 추적까지. 경고 기능은 예기치 않은 동작에 대해 알림을 받도록 하는 안전망이에요.

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

아래는 Markdown 형식으로 표로 변경해주세요.

AppDynamics.

AppDynamics, 나의 성능 아티스트! 데이터베이스 및 서버부터 클라우드 네이티브 및 하이브리드 환경까지 전체 기술 스택을 모니터링, 관찰 및 시각화하는 전체적인 방법을 취합니다. 성능이 최종 사용자에 미치는 영향을 이해하는 데 도움을 주어 감사합니다. 이로써 이는 모니터링 도구뿐만 아니라 사용자 만족 도구가 됩니다.

Logging Solutions.

Log4j.

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

이것은 로깅 프레임워크의 노인 같은 존재입니다. 믿고 의존할 수 있게 이벤트와 오류를 충실하게 기록해 왔습니다. 애펜더와 필터를 구성하는 유연성 덕분에 로깅 필요에 신뢰할 수 있는 일말의 일력으로 떠드는 중입니다.

SLF4J.

SLF4J는 바로 내 로깅 스위스 아미 나이프 같은 존재입니다. 직접 로그를 남기지 않고 퍼사드 역할을 하며, 서로 다른 로깅 구현체들을 편리하게 연결할 수 있게 해줍니다. 이 유연성 덕분에 라이브러리의 로깅 기본 사양을 준수하는 다양한 작업 시 슬기로운 선택지가 됩니다.

Java에서의 관측 가능성.

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

다이그마: Continuous Feedback (FC).

코드가 실제 세계에서 어떻게 실행되는지 보지 못하면, 설계 결정을 내릴 수 없고 변경 사항의 영향을 평가할 수 없습니다. 관측 가능성과 코드 사이의 루프를 닫음으로써, 다이그마는 새로운 개발 방법을 열어줍니다.

![이미지](/assets/img/2024-05-20-25REASONSWHYJAVAISSTILLAROUNDIN2024_5.png)

다이그마는 Continuous Feedback(CF) 도구로, OTEL 관측 가능성 소스에서 코드에 대한 데이터를 수집하고 처리하는 작업을 간소화하도록 설계되었습니다. 다이그마는 IDE 플러그인으로 로컬에서 실행되며, 코딩하는 동안 추적부터 로그 및 지표까지 코드에 대한 데이터를 수집합니다. 이는 실시간으로 문제를 발견하고 통찰력을 얻을 수 있음을 의미합니다.

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

프로메테우스와 그라파나.

프로메테우스와 그라파나, 나의 다이내믹 듀오! 프로메테우스는 나의 애플리케이션에서 메트릭을 수집하고, 그라파나는 이러한 메트릭을 아름답고 사용자 정의 가능한 대시보드로 변환해줍니다. 이 도구들 주변의 활발한 커뮤니티와 오픈 소스 성격 때문에 이 둘은 나의 다이내믹한 관측성 듀오입니다.

Elastic Stack (ELK).

인공 지능과 검색 기술을 기반으로 한 오픈형, 확장 가능한 풀 스택 관측성입니다. 일라스틱서치, 로그스태시, 키바나는 함께 로그를 검색, 분석 및 시각화하기 위한 강력한 도구를 구성합니다. 로그, 메트릭 및 추적을 상호 연관시킬 수 있는 능력은 나에게 완전한 조사 툴킷을 제공해줍니다.

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

# 13. 함수형 프로그래밍 지원

자바는 자바 8부터 지원하는 또 다른 프로그래밍 패러다임인 함수형 프로그래밍을 제공합니다. 함수형 프로그래밍에서는 함수를 일급 시민으로 취급하여 변수에 할당하거나 인수로 전달하고 반환할 수 있습니다.

이 패러다임의 일부 기능으로 자바는 매력적인 프로그래밍 언어가 되었습니다. 자바의 함수형 프로그래밍 기능을 받아들이면서 개발자로서 제 삶에 큰 영향을 주었습니다. 람다 표현식과 함수형 인터페이스의 도입으로 코드를 더 간결하고 표현력 있게 작성할 수 있었습니다. 멀티코어 프로세서에서 병렬 처리를 활용할 수 있는 Stream API 덕분에 제 애플리케이션의 성능을 향상시킬 수 있었습니다.

```java
import java.util.ArrayList;
import java.util.Arrays;
import java.util.List;
public class Main {
    public static void main(String[] args) {
        List<Integer> list = new ArrayList<>(Arrays.asList(1, 2, 3, 4, 5));

        int sum = list.stream().reduce(0, (a, b) -> a + b);
        System.out.println(sum);
    }
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

델리클러티브 스타일을 채택하면 함수형 프로그래밍에서 권장하는 대로 코드가 더 읽기 쉽고 이해하기 쉬워집니다. 불변성을 강조하고 부작용을 피하는 것이 실제로 영향을 미쳤으며, 코드를 더 예측 가능하고 유지보수 가능하게 만들었습니다. 그리고 테스트에 있어서 순수 함수의 보편성은 테스트하기 쉬워졌다는 것을 알았어요.

## 14. 자바의 풍부한 문서화

팀 프로젝트에 참여할 기회가 있다면 문서화의 중요성을 분명히 이해하게 될 것입니다. 저는 개인적으로 문서화를 코드가 무엇을 하는지, 어떻게 하는지, 그리고 왜 그렇게 하는지 설명해주는 사용자 매뉴얼로 간주합니다. 그리고 자바를 배우는 초보자라면, 그것은 마치 옆에서 멘토가 함께하는 것과 같습니다.

문서에는 다양한 코드 샘플, 튜토리얼, 개발자 가이드, API 문서 등이 포함되어 있어서 프로토타입을 빠르게 개발하고 실제 응용 프로그램으로 스케일을 확장할 수 있습니다.

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

문서는 최신 상태가 아니면 별로 도움이 되지 않습니다. Java의 문서는 생태계의 새로운 개발 내용을 개발자와 전문가들이 반영하기 위해 정기적으로 최신 상태로 유지되며 개정됩니다. 또한 문서는 특정 클래스, 메소드 또는 개념에 대한 정보를 쉽게 찾을 수 있도록 구조화되어 있습니다.

# 15. 빌드 도구 및 의존성 관리

평균적인 Java/Spring 부트 프로젝트는 직접 및 간접 의존성이 수십 개에서 수백 개가 될 수 있습니다. 특히 대규모 기업 프로젝트를 처리할 때 버전 호환성과 같은 문제로 인해 이러한 의존성을 수동으로 관리하는 것은 머리아플 수 있습니다. 빌드 도구를 활용하면 여러 개발자가 로컬에서 빌드를 실행하는 상황에서 이산된 빌드를 통합할 수 있습니다.

![이미지](/assets/img/2024-05-20-25REASONSWHYJAVAISSTILLAROUNDIN2024_6.png)

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

그러나 Maven 및 Gradle과 같은 빌드 도구는 빌드, 테스트 및 종속성 관리를 간소화하여 개발자로서 일을 더 쉽게 만듭니다. 이러한 도구들은 시작부터 여러분이 다양한 종속성에 대한 업데이트와 보안 패치에 대해 알아야 하는 불편함을 덜어줍니다. 저장소에서 종속성을 가져와 자동으로 업데이트를 확인하므로 직접 업데이트 정보를 찾아가지 않아도 됩니다.

빌드 도구는 또한 프로젝트 구조 및 구성에 대한 관례와 표준을 강제하므로 다른 Java 개발자 팀과 함께 작업하기 쉽고 이해하기 쉽습니다.

# 16. 견고한 테스트 기능

우리 개발자들이 버그를 해결하고 QA 엔지니어와 협업하는 것을 꺼리지만, 포괄적인 테스트는 응용 프로그램이 가능한 한 버그가 없도록 보장하는 가장 효과적인 방법 중 하나라고 인정합니다.

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

견고한 테스트 기능을 갖춘 언어를 선택하면 부담이 줄어들고 더 신뢰할 수 있고 유지보수가 쉬운 코드베이스를 구축하는 데 도움이 됩니다. 그것이 왜 나는 Java를 버그가 없는 소프트웨어를 만들기 위한 기본 프로그래밍 언어로 지지하는 이유 중 하나입니다.

단위 테스트, 통합 테스트, 또는 종단간 테스팅이든 Java는 포괄적인 테스트를 작성하기 위한 도구 세트를 제공합니다. JUnit은 Java의 단위 테스트의 표준입니다. 테스트를 작성하고 실행하기 위한 간단하고 우아한 방법을 제공합니다. JUnit은 @Test, @Before, @After, @BeforeClass, @AfterClass와 같은 어노테이션을 사용하여 테스트 메서드의 라이프사이클을 정의합니다. 이를 통해 테스트를 실행하기 전에 사전 조건을 설정할 수 있습니다.

개발자들이 버그를 해결하고 QA 엔지니어들과 협업하는 것을 꺼리지만, 아플리케이션이 가능한 한 버그가 없는 것을 보장하는 가장 효과적인 방법 중 하나가 포괄적인 테스트임을 인정합니다.

견고한 테스트 기능을 갖춘 언어를 선택하면 부담이 줄어들고 더 신뢰할 수 있고 유지보수가 쉬운 코드베이스를 구축하는 데 도움이 됩니다. 그것이 왜 나는 Java를 버그가 없는 소프트웨어를 만들기 위한 기본 프로그래밍 언어로 지지하는 이유 중 하나입니다.

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

단위 테스트, 통합 테스트 또는 엔드투엔드 테스트든, Java는 포괄적인 테스트를 작성할 수 있는 풍부한 도구 세트를 제공해요. JUnit은 Java에서 단위 테스트의 표준이며 간단하고 우아한 방식으로 테스트를 작성하고 실행할 수 있어요. JUnit은 @Test, @Before, @After, @BeforeClass, @AfterClass와 같은 주석을 사용하여 테스트 메서드의 라이프사이클을 정의합니다. 이를 통해 테스트 실행 전 사전 조건을 설정할 수 있어요.

```java
// EmployeeServiceTest.java
import org.junit.jupiter.api.Test;
import org.mockito.InjectMocks;
import org.mockito.Mock;
import org.springframework.boot.test.context.SpringBootTest;
import java.util.ArrayList;
import java.util.List;
import java.util.Optional;
import static org.junit.jupiter.api.Assertions.assertEquals;
import static org.mockito.Mockito.*;
@SpringBootTest
public class EmployeeServiceTest {
    @Mock
    private EmployeeRepository employeeRepository;
    @InjectMocks
    private EmployeeService employeeService;
    @Test
    public void testGetAllEmployees() {
        // Mocking repository response
        when(employeeRepository.findAll()).thenReturn(new ArrayList<>());
        // Test case for getAllEmployees method
        List<Employee> result = employeeService.getAllEmployees();
        // Assertion
        assertEquals(0, result.size());
        // Verify that the repository method was called
        verify(employeeRepository, times(1)).findAll();
    }
}
```

JUnit을 사용하여 테스트를 작성하는 것은 간단하고, Eclipse, IntelliJ IDEA, NetBeans와 같은 인기있는 Java IDE(통합 개발 환경)와 원활하게 통합됩니다.

Java 생태계에서 테스트하는 데 유용한 다른 도구로는 통합 테스트용 TestNG, 행동 주도 개발에 대한 Cucumber, 기능 및 회귀 테스트 케이스를 자동화하는 Selenium이 있습니다. Mockito는 JUnit 및 TestNG와 함께 사용할 수 있는 강력한 mocking 프레임워크입니다.

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

# 17. 대규모 커뮤니티

자바 커뮤니티는 나의 자바 개발자로서의 여정에서 중요한 역할을 해왔습니다. 자바 커뮤니티로부터 도움과 지침을 여러 차례 구했는데 그 횟수를 셀 수 없을 정도입니다. 버그에 막혔을 때, 새로운 라이브러리를 탐구할 때, 새로운 솔루션을 구현할 때 최고의 실천 방법을 찾을 때, 커뮤니티는 매우 가치 있는 정보의 근원임이 증명되었습니다.

자바 커뮤니티는 가장 응답이 빠른 커뮤니티 중 하나이며 거의 즉각적으로 도움을 받을 수 있습니다. 예를 들어, 저는 수천 명의 자바 개발자로 이루어진 레딧의 r/java 커뮤니티의 일원입니다. Stack Overflow와 GitHub와 같은 다른 플랫폼의 커뮤니티 또한 초보자부터 베테랑 전문가까지 다양한 개발자들이 포함되어 있습니다. 이 다양성은 각종 도메인과 경험 수준을 다루는 다양한 전문성이 있음을 의미하여 강점으로 작용합니다.

온라인 커뮤니티 외에도 여러 자바 이벤트, 컨퍼런스, 그리고 밋업을 통해 개발자들이 직접 만나 경험을 공유하고 서로 배우는 기회를 제공합니다. 이러한 모임들은 네트워킹과 협업을 촉진하여 자바 개발자 커뮤니티 감각에 기여합니다.

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

# 18. 자바 어노테이션 지원

자바 어노테이션은 매우 인기 있는 주제이지만 논란이 많습니다. 어노테이션은 자바 5에서 소개되었고, 우리 모두 흥분했습니다. 개인적으로도 이 열정을 나눴습니다. 어노테이션의 도입은 확장된 Hibernate 또는 Spring XML 구성 파일 시대에서 떠났음을 나타냈습니다. 대신, 어노테이션을 사용하여 우리는 필요한 곳에 정확하게 정보, 지시 또는 구성을 직접 내장할 수 있게 되었습니다.

Predefined annotations
@Deprecated
@Override
@SuppressWarnings
@SafeVarargs
@FunctionalInterface 2. Meta-annotations
@Retention
@Documented
@Target
@Inherited
@Repeatable 3. Custom annotations
우리만의 사용자 정의 어노테이션을 만들 수도 있습니다.

자바 어노테이션은 확실히 외부 문서를 참조할 필요성을 줄이는 것으로 코드의 명확성과 표현력을 향상시켰습니다. 어노테이션은 반복적인 작업이나 구성을 캡슐화하여 일부 보일러플레이트 코드를 줄이는 데 도움이 될 수도 있습니다. 예를 들어, 어노테이션을 사용하여 의존성 주입, ORM 매핑, 트랜잭션 범위와 같은 측면을 정의할 수 있습니다.

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

일부 개발자들은 주석이 편리하고 여러 이점이 있지만, 일부 주석에 대해 의심을 품고 있는 것 같아요.

# 보안 기능

우리가 작성하는 프로그램은 코드 라인뿐만 아니라, 개인 사용자 데이터, 금융 세부 정보 및 독점 비즈니스 정보와 같은 민감한 정보도 처리합니다. 사용자는 우리에게 그들의 정보를 보호하고 안전한 환경을 제공하기를 기대합니다. 소프트웨어 개발이나 혁신에 관여하는 비즈니스에 대해, 의지재산 침탈을 예방하기 위해 애플리케이션을 보호하는 것이 중요합니다. 소스 코드, 알고리즘 및 독점 정보를 보호하는 것은 경쟁 우위를 유지하는 데 중요합니다.

Java는 안전한 애플리케이션을 개발하기 쉽게 하는 많은 기능을 가지고 있어요. 이러한 기능 중 일부에는 암호화, 공개 키 인프라, 안전한 통신, 인증 및 접근 제어가 포함됩니다. 암호화에서부터 스마트 카드 I/O 및 안전한 통신을 보장하는 인증 프로토콜에 이르기까지, Java 애플리케이션에 보안 조치를 원활하게 통합할 수 있는 다양한 API 및 도구에 접근할 수 있어요.

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

이 목록은 완전하지 않아요. 공식 Java 보안 가이드에서 더 많은 기능을 배울 수 있어요.

# 20. RICH API SET

![이미지](/assets/img/2024-05-20-25REASONSWHYJAVAISSTILLAROUNDIN2024_7.png)

Java는 풍부한 응용 프로그램 프로그래밍 인터페이스(APIs) 세트로도 알려져 있어요. 이들은 여러 소프트웨어 구성요소, 라이브러리 및 서비스와 상호 작용하는 표준화된 방법을 제공해요. 이 API에는 준비된 기능을 제공하는 미리 컴파일된 클래스, 인터페이스 및 메소드들의 풍부한 컬렉션이 포함되어 있어요.

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

차를 처음부터 만들고 있다고 가정해보겠습니다. Java API를 사용하면 이미 제조업체로부터 입수한 부품들을 조립하는 것과 같은 느낌을 받게 됩니다. 즉, 부품을 직접 제조하는 대신 필요한 부품들을 조립하는 방식입니다. 이 경우, 필요에 딱 맞는 API를 선택할 수 있는 유연성이 있습니다. 이 API들은 표준화되어 있고 잘 문서화되어 있어 사용하기 쉽습니다.

Java의 API가 가지고 있는 장점은 구성 요소 빌드의 복잡한 세부 사항들을 추상화하여 완전히 기능적인 자동차를 조립하는 데 집중할 수 있게 해준다는 점입니다. 이 API들을 사용하여 네트워킹, IO, 파일 처리, 데이터베이스 연결, 멀티스레딩 등 다양한 도메인에서 다양한 작업을 수행할 수 있습니다.

Java API는 여러 패키지로 구성되어 있습니다. 가장 일반적인 것들 중 일부는 java.lang, java.util, java.io, java.net 등이 있습니다.

# 21. JAVA의 성능

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

다른 이유는 Java를 오랜 기간 동안 사용하게 한 것은 Java의 성능이 지속적으로 향상되는 것을 목도하면서, 이것이 주 프로그래밍 언어로서의 선택을 유효하게 만들었습니다.

성능 면에서의 이러한 개선은 문제를 해결하는 데 큰 도움을 주었으며, 현대 고객을 대상으로 하는 성능 우수한 응용 프로그램을 개발할 수 있는 기회를 제공했습니다. 몇 가지 주목할 만한 것도 있습니다.

예를 들어, Java 가상 머신(JVM)은 각 새로운 릴리스마다 상당한 최적화가 이루어졌습니다. Just-in-time(JIT) 컴파일러의 개선, 가비지 수집 기능의 향상, 그리고 더 나은 런타임 프로파일링이 함께 빠른 실행과 낮은 메모리 소요를 기여했습니다.

Project Valhalla는 값 타입을 도입하여 더 효율적이고 간결한 데이터 구조를 정의할 수 있게 했습니다. 이는 메모리 소모를 줄이고 캐시 지역성을 향상시키며, 특히 대량의 데이터가 있는 시나리오에서 상당한 성능 향상을 가져왔습니다.

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

최근에 JDK 21에서 키 캡슐화 메커니즘 API, 가상 스레드, 및 문자열 템플릿 및 구조화된 동시성 미리보기와 같은 15개의 기능이 도입되었습니다. 이러한 변경 사항은 자바를 크게 향상시킵니다.

# JDK 21에서 가상 스레드와 기타 기능으로 한 발 LEAP FORWARD

# 22. 구조화된 동시성

제안된 구조화된 동시성을 위한 API는 JDK 21의 주요 기능으로 유지되며 관련 작업 그룹을 다른 스레드에서 단일 작업 단위로 다루어 동시 프로그래밍을 간단하게 하는 것을 목표로 합니다. 이 접근 방식은 오류 처리, 취소, 신뢰성 및 관측성을 향상시킵니다. 이 기능은 java.util.concurrent의 기존 동시성 구조를 대체할 목적이 아닙니다.

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

이 기능을 통해 ExecutorService 및 Future와 같은 기존 구조를 활용하여 병행 프로그래밍에서 작업 및 하위 작업을 관리하는 복잡성을 제거할 수 있습니다. 작업과 하위 작업 간의 내재적인 관계 부재로 오류 처리, 취소 및 관찰에 어려움이 있습니다. 제안된 구조화된 동시성 접근 방식은 코드의 구문 구조를 작업의 실행 계층 구조와 일치시켜 주어 병행 프로그래밍을 더 가독성 있고 유지보수가능하며 신뢰할 수 있도록 합니다.

# 23. 가상 스레드

처음에는 JDK 19 및 JDK 20에서 미리보기 기능으로 소개되었지만 이제 JDK 21에서 공식적으로 도입된 가상 스레드입니다. java.lang.Thread의 각 인스턴스는 일반적으로 여타 플랫폼 스레드에 연결되어 수명 주기 동안 기본 OS 스레드에 바인딩됩니다.

그러나 가상 스레드는 패러다임 변화를 가져왔습니다. 여전히 java.lang.Thread의 인스턴스가 존재하지만 이제 Java 코드를 백그라운드 OS 스레드에서 실행하여 해당 스레드를 독점하지 않는 방식으로 작동합니다. 이 혁신은 여러 가상 스레드가 효율적으로 단일 OS 스레드를 공유할 수 있도록 합니다. 플랫폼 스레드와는 달리 가상 스레드는 소중한 OS 스레드를 제한하지 않으며, 그 숫자는 OS 스레드의 제약을 크게 초월할 수 있습니다.

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

가상 스레드는 JDK 21의 매력적인 새로운 기능입니다. 이는 특히 동시 작업이 매우 많은 시나리오에서 애플리케이션 성능에 상당한 긍정적인 영향을 미칠 수 있습니다. 이러한 시나리오에서는 종종 수천 개 이상의 동시 작업이 발생합니다.

이제 애플리케이션의 특정 요구 사항에 따라 가상 스레드와 전통적인 플랫폼 스레드 중에서 선택할 수 있는 유연성이 생겼습니다.

# 24. SWITCH 문을 위한 패턴 매칭

이 흥미로운 기능은 JDK 17 제안에서 시작되어 JDK 18, JDK 19 및 JDK 20에서 일련의 개선 작업을 거친 후, 이제 Java 커뮤니티의 피드백과 경험을 바탕으로 JDK 21의 일부로 정식으로 도입되어 추가로 발전했습니다.

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

이 기능의 주요 목적은 switch 표현식과 문장의 기능과 다양성을 확장하는 것입니다. 또한 경우 레이블에서 패턴이 더 중요한 역할을 하도록하여, null 값 처리에 대해 더 유연성을 제공하고, 패턴 switch 문을 사용하여 가능한 모든 입력 값에 대한 포괄적인 커버리지를 요구함으로써 switch 문을 더 안전하게 만드는 데 관한 것입니다.

이미 switch 표현식과 문장을 사용하고 있다면 걱정하지 마세요. 목표는 어떠한 변경도 필요하지 않게 현재처럼 작동하도록 보장하는 것입니다.

# 25. 문자열 템플릿

문자열 템플릿이 Java에 도입되어, 개발자가 오래 전부터 바라왔던 문자열 보간을 지원하게 되었습니다. 지금까지 여러 문자열을 결합하거나 string.format을 사용해야 했는데, 솔직히 말해서 귀찮았습니다. 그러나 Java 21에서 우리는 즐거움을 느낄 것입니다. 이 새로운 기능은 개발자들에게 Java의 문자열 리터럴과 텍스트 블록을 문자열 템플릿으로 채울 수 있는 기능을 부여합니다.

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

int x = 10;
int y = 20;
String s = STR."\{x} + \{y} = \{x + y}";

주요 목표는 런타임에서 컴파일될 수 있는 값들을 표현하는 데 있어서 동적 문자열 생성을 간소화하는 것입니다. 또 다른 목표는 StringBuilder 및 StringBuffer 클래스와 관련된 장황함을 해결하여 가독성을 향상시키는 것입니다. 마지막으로, 문자열 템플릿은 다른 프로그래밍 언어의 존재하는 문자열 보간 기술과 관련된 보안 문제를 극복하는 데 도움이 될 것입니다.

# 요약

자바는 더 쉽지는 않지만 더 나은 것이죠! 또한, 현대적인 자바는 매우 빠릅니다. 읽기 쉽고 - 유지 보수가 쉬워요. 자바에서 똑똑해지려고 애쓰는 것은 어렵습니다 - 록스타 동료들과 싸우지 않아도 됩니다. 그리고 모두 솔직해지자면, 우리는 급여에 관심이 있죠. 자바 개발자들은 높은 급여를 받습니다. 대부분의 기업과 대규모 조직에서 주 언어로 자바를 사용하기 때문에 많은 일자리가 있습니다. 아직 자바 8을 사용하고 행복해하는 사람들도 알고 있어요.

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

무료로 Digma를 설치하세요!

# 자주 묻는 질문 (FAQ)

Java가 인기 있는 프로그래밍 언어로 손꼽히는 이유는 무엇인가요?

Java는 플랫폼 독립성, 견고함, 그리고 방대한 커뮤니티 지원으로 인해 인기를 얻었습니다. 웹 개발부터 기업 애플리케이션에 이르기까지 다양한 분야에서 널리 사용됩니다.

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

대용량 엔터프라이즈 애플리케이션에 Java를 사용하는 이점은 무엇인가요?

Java는 안정적이고 확장 가능한 환경을 제공하며, 멀티스레딩을 지원하며, Spring Boot와 같은 많은 라이브러리와 프레임워크로 엔터프라이즈 애플리케이션 개발을 단순화합니다.

Java는 어떻게 크로스 플랫폼 개발을 지원하나요?

Java는 "한 번 작성하고, 모두에서 실행" 원칙을 통해 크로스 플랫폼 호환성을 달성하며, Java 코드를 Java Virtual Machine (JVM)을 사용하는 모든 장치에서 실행할 수 있도록 합니다.
