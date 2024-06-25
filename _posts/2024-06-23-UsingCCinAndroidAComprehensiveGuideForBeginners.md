---
title: "초보자를 위한 안드로이드에서 C C 사용하는 방법 총정리"
description: ""
coverImage: "/assets/img/2024-06-23-UsingCCinAndroidAComprehensiveGuideForBeginners_0.png"
date: 2024-06-23 01:19
ogImage:
  url: /assets/img/2024-06-23-UsingCCinAndroidAComprehensiveGuideForBeginners_0.png
tag: Tech
originalTitle: "Using C C++ in Android: A Comprehensive Guide For Beginners"
link: "https://medium.com/proandroiddev/using-c-c-in-android-a-comprehensive-guide-for-beginners-8a870cf3dba6"
---

## 안드로이드 개발

![이미지](/assets/img/2024-06-23-UsingCCinAndroidAComprehensiveGuideForBeginners_0.png)

안드로이드 개발자로서, 우리는 아름다운 UI 및 기능을 구축하기 위해 Java 또는 Kotlin을 사용해 왔습니다. 앱 개발부터 실행까지 안드로이드 전체 스택은 JVM 및 Java-스러운 기능을 중심으로 돌아갑니다. 리눅스 커널을 제외하고, Java는 C로 작성되어 있는데요.

프로그래밍 언어인 Java는 앱 개발을 위한 최적의 선택지로 만들어 주는 많은 우수한 기능을 갖추고 있습니다. 가상 머신 실행으로 플랫폼 독립적이며, JIT 컴파일된, 멀티 스레딩 지원이 가능하며, 프로그래머에게 표현력이 뛰어나고 간단한 구문을 제공합니다. 플랫폼에 대한 중립적인 특성으로 인해 Java 패키지는 CPU 아키텍처에 따라 이식 가능하며, 이는 라이브러리 개발을 용이하게 하고 플러그인, 빌드 도구 및 유틸리티 패키지의 전반적인 생태계를 높일 수 있습니다.

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

특정 기능의 수와 성능 사이에는 상충 관계가 있습니다. 어셈블리와 같은 언어는 메모리 및 실행 오버헤드가 가장 적지만 프로그래머 관점에서는 가장 적은 수의 기능을 제공합니다. 계층을 올라가면서 C 및 C++과 같은 언어는 하드웨어에 더 가까운 위치를 유지하면서 좋은 기능 집합을 제공합니다. 그 위에는 플랫폼 의존성을 완전히 제거하기 위해 가상 머신을 사용하는 Java와 Python과 같은 언어들이 있습니다. 이러한 언어로 작성된 프로그램은 많은 오버헤드가 발생하지만 개발자에게는 천국입니다.

## 누군가가 Android 프로젝트에서 C/C++ 지원이 필요한 이유는 무엇인가요?

위에서의 토론에도 나와 있듯이, 우리 시스템에서는 성능이 개발자 친화성보다 더 중요하기 때문에 우리의 관심을 '네이티브 언어'인 C/C++로 이동시킵니다. 네이티브 코드의 역할과 성능 개선에 대해 이해할 수 있는 몇 가지 예를 살펴보겠습니다.

- 그래픽, 렌더링 및 상호작용: 사용자 인터페이스를 개발하고 매력적으로 보이게 만드는 작업은 Jetpack Compose와 같은 고수준 프레임워크에서는 간단한 작업으로 보일 수 있습니다. 하지만 픽셀 수준에서는 그림자의 강도, 조명 모드 및 객체의 질감을 계산하기 위해 수천 개의 계산이 수행됩니다. 이러한 계산은 벡터와 행렬과 같은 선형 대수 구조 및 해당 연산을 많이 사용합니다. 터치 상호작용을 처리할 때는 명령을 클릭, 더블 클릭, 드래그 또는 스와이프 제스처로 구분해야 하므로 많은 계산이 필요합니다. 이러한 계산은 하드웨어에 더 가까운 언어에서 수행하는 것이 더 나은데 추가적인 최적화가 가능합니다.
- 머신 러닝: C/C++의 역할은 PyTorch 및 TensorFlow와 같은 인기있는 프레임워크의 코드베이스의 주요 부분이 C/C++로 작성되어 있음으로써 쉽게 이해됩니다. TensorFlow는 C++로 작성된 연산을 사용하고 Python 코드에서 이러한 연산을 사용하기 위한 래퍼(인터페이스)를 제공합니다. C++의 채택은 선형 대수 연산, 병렬 처리에 사용되는 CUDA 코드베이스가 수년 전에 작성되었고 많은 시험을 거쳐 왔기 때문에 명백합니다. Python은 TensorFlow의 인터페이스 중 하나로 사용되며, C/C++ 작업을 깔끔하고 간편하게 만들어주어 비 프로그래머 사용자에게 편리합니다.

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

![image](/assets/img/2024-06-23-UsingCCinAndroidAComprehensiveGuideForBeginners_1.png)

Many such systems uphold performance compromising readability and some other factors. Next, we’ll have a short discussion on Instruction Set Architectures (ISAs) and how program execution changes with changing CPU architectures.

# Understanding How C++ Is Integrated in Android Apps

![image](/assets/img/2024-06-23-UsingCCinAndroidAComprehensiveGuideForBeginners_2.png)

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

위의 그림에서 안드로이드에서 C/C++ 코드를 사용하는 방법에 대해 나와 있습니다. 여기에는 C/C++ 코드용 빌드 프로세스와 Java/Kotlin 코드용 또 다른 독립적인 빌드 프로세스가 있습니다. 이 블로그에서는 C/C++ 코드 빌드 프로세스에 중점을 두고 코드가 함수 호출을 위해 JVM과 어떻게 통신하는지 살펴볼 것입니다.

먼저 C/C++ 및 Java 프로그램이 컴파일되는 간단한 개요를 살펴볼 것이며, 주로 C/C++ 컴파일의 플랫폼별 특성을 강조할 것입니다. 그 다음, C/C++과 Java 코드 간의 접착제 역할을 하는 JNI에 대해 논의할 것입니다. 빌드 프로세스의 가장 하단 구성 요소인 CMake, 공유 라이브러리 및 ABI에 대한 논의로 마무리할 것입니다.

자, 시작해봅시다 🚀

## 1. C++ 및 Java 프로그램의 컴파일

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

➡️ C++은 컴파일된 언어로, 소스 코드가 실행 가능한 이진 코드로 변환됩니다. 실행 파일에는 소스 프로그램의 이진 버전, 필요한 상수 및 라이브러리 코드가 포함되어 있습니다.

➡️ 이 실행 파일은 운영 체제의 구성 요소인 로더에 의해 구문 분석되며, 프로그램의 실행을 위해 메모리를 할당하고 실행 파일에서 명령을 읽습니다. 예를 들어, hello-world C++ 프로그램이 Ubuntu에서 사용 가능한 g++로 컴파일되었다면, x86 또는 x86_64 명령 세트를 이해하는 한 다른 Linux 배포판에서도 실행될 것입니다.

➡️ 모바일 기기는 arm 또는 arm64 명령 세트로 작동하기 때문에 x86용으로 컴파일된 프로그램은 작동하지 않습니다. 두 실행 파일은 로더가 보는 것과 같이 완전히 다른 언어로 작성되어 있기 때문입니다.

![이미지](/assets/img/2024-06-23-UsingCCinAndroidAComprehensiveGuideForBeginners_3.png)

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

![Android Architecture](/assets/img/2024-06-23-UsingCCinAndroidAComprehensiveGuideForBeginners_4.png)

Android 기기는 주로 네 가지 아키텍처에서 작동할 수 있습니다 - arm64-v8a, armeabi-v7a, x86 및 x86_64. ARM 기반 프로세서에서 사용되는 arm- 아키텍처는 대부분의 Android 휴대폰에서 사용되며, Intel 또는 AMD 프로세서에 사용되는 x86- 기반 아키텍처는 Windows 에뮬레이터와 Chromebook에 사용됩니다.

## Java

➡️ 만약 어느 시점에 Java를 배웠다면, 동영상이나 블로그에서 자주 강조되는 특징 중 하나는 플랫폼 독립성 또는 한 번 작성하고 어디서든 실행하는 것입니다. 소스 코드를 기계 의존적 실행 형식으로 변환하는 대신 Java는 코드를 중간 표현 (IR)으로 변환합니다.

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

➡️ IR은 플랫폼에 독립적이며, x86 또는 arm 플랫폼에서 생성된 IR은 명령어 세트의 차이에 관곧하게 동일합니다. IR은 플랫폼 의존 구성 요소인 Java 가상 머신에 의해 구문 분석되며, 해당 가상 머신은 IR에서 명령어를 읽어서 기존 CPU에서 실행합니다. JVM은 IR을 처리하고 동시에 기존 CPU에서 명령어를 실행하기 때문에 플랫폼에 독립적이 아닙니다.

➡️ JVM은 거의 모든 CPU 아키텍처에서 실행되고, 생성된 IR이 플랫폼에 독립적이기 때문에 모든 플랫폼에 작성된 Java 코드를 실행할 수 있습니다. 유일한 종속성은 대상 기기에 JVM이 설치되어 있어야 한다는 것입니다.

## Android ART와 DEX 바이트코드

운영 체제로서 Android는 Java 코드를 실행하기 위해 표준 JVM을 사용하지 않습니다. 패키지된 애플리케이션인 APK에는 DEX 파일(.class 파일과 유사)과 네이티브 코드 및 리소스가 포함되어 있습니다. DEX 파일은 실행 전에 OS에 의해 네이티브 실행 코드로 미리 컴파일되어 사용자가 앱을 열 때 빠르게 인스턴스화될 수 있습니다.

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

# 2. JNI을 사용하여 C++ 소스 코드 래핑하기

JNI 또는 Java Native Interface는 JVM과 네이티브 코드(C, C++ 또는 어셈블리 코드) 간의 통신을 쉽게 할 수 있게 해주는 프레임워크입니다. 일반적으로 말해, 이는 외부 함수 인터페이스(FFI)를 제공하여 다른 언어로 작성된 코드가 주로 함수 호출을 통해 다른 언어로 작성된 코드와 통신할 수 있게 합니다. Java 소스 코드는 C++ 모듈에 존재하는 함수 정의를 찾아서 JVM에서 사용할 수 있도록 플래그를 달게 합니다.

JNI에는 jclass, jobject, jfloat, jstring 등과 같은 클래스들이 있으며, 이들은 각각 C++에서 해당하는 자바 원시 자료형 (클래스, 객체, float 및 String)을 나타냅니다. 예를 들어 C++에서 정의된 JNI 함수는 다음과 같습니다.

```cpp
// C++ 소스 파일
extern "C" JNIEXPORT jstring JNICALL
Java_com_projects_ml_samplecppdemo_MainActivity_compute(
        JNIEnv* env,
        jobject instance ,
        jstring message ,
        jlong length
) {
    // 메소드 블록이 여기에 위치합니다
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

MainActivity에서 Kotlin 함수 compute에 해당하는 것이 존재할 것입니다.

```kotlin
// Kotlin 소스 파일
external fun compute(message: String, length: Long): String
```

MainActivity.kt를 컴파일하는 동안, JVM은 코드에서 선언한 compute 함수의 정의를 찾아야 합니다. 정의는 C++ 소스 파일 안에 포함되어 있기 때문에, Java 프로그램에 이를 어떻게 제공할까요? 우리는 C++ 코드를 컴파일하고, JVM이 JNI 함수의 정의를 찾을 수 있는 공유 라이브러리로 패키징합니다.

## 3. CMake과 Android NDK

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

<img src="/assets/img/2024-06-23-UsingCCinAndroidAComprehensiveGuideForBeginners_5.png" />

## Android NDK and Toolchains

저희는 Windows, macOS 또는 Linux 기반 운영 체제에서 Android 앱을 개발합니다. 이러한 시스템 대부분은 Android 특정 ARM 아키텍처를 보유하고 있지 않으며 Android 기기에서 코드를 컴파일하는 것은 불가능합니다. 그렇다면 모바일 폰에서 사용하는 Android 특정 ARM 아키텍처를 위해 코드를 어떻게 컴파일할까요?

<img src="/assets/img/2024-06-23-UsingCCinAndroidAComprehensiveGuideForBeginners_6.png" />

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

안녕하세요! Android NDK(안드로이드 네이티브 개발 키트)를 사용하고 있습니다. 이 키트는 Android-ARM 라이브러리 및 실행 파일을 x86 또는 다른 arm 장치(애플 실리콘 또는 라즈베리 파이)에서 빌드하기 위한 컴파일러 및 링커를 제공합니다. 이렇게 다른 타겟(예: Android-ARM)을 위해 코드를 빌드하는 프로세스는 현재 다른 타겟(예: x86_64)을 실행 중인 시스템에서 이루어지는 교차 컴파일이라고 합니다. 그래서, Windows 기기에서 Android NDK의 컴파일러를 사용하여 앱을 위한 공유 라이브러리를 빌드할 수 있습니다. 이 라이브러리는 ARM 기기인 모바일 장치에서 완벽하게 실행될 것입니다.

Android NDK에는 CMake에게 사용할 컴파일러를 알려주는 CMAKE_TOOLCHAIN_FILE이 존재합니다. 위키피디아에 따르면, 툴체인은 복잡한 소프트웨어 개발 작업을 수행하거나 소프트웨어 제품을 만들기 위해 사용되는 프로그래밍 도구의 모음입니다. Android NDK는 다양한 Android API 레벨을 위해 다양한 툴체인을 제공하여 C/C++ 프로그램을 빌드하고 컴파일할 수 있습니다.

## CMake란 무엇인가요?

간단한 C++ hello-world 프로그램을 컴파일한다면, 대부분의 리눅스 배포판에 미리 설치된 GNU의 g++ 컴파일러를 사용했을 것입니다.

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

```bash
g++ main.cpp -o main
```

➡️ 하나의 소스 파일 main.cpp의 경우, 하나의 명령어로 작업을 처리할 수 있어요. 더 큰 코드베이스는 여러 모듈과 많은 C/C++ 소스 파일을 포함하고 있을 수 있는데, 이들은 공유 혹은 정적 라이브러리로 컴파일하거나 빌드해야 합니다. 해당 코드베이스의 의존성들, 즉 다른 C++ 프로젝트들도 잘 통합되어야 해요. 이런 방대한 코드베이스는 컴파일에 많은 시간이 소요될 거예요.

➡️ 이러한 문제들에 대처하기 위해 GNU의 Make 도구를 사용할 수 있는데, 이는 여러 대상을 관리하거나 증분 빌드, 헤더 파일 포함 및 다양한 언어 지원 기능을 제공해줘요. 따라서 컴파일을 위해 여러 명령어를 실행하는 대신, 단일 Make 스크립트가 효율적으로 컴파일을 수행할 거예요.

```bash
cmake_minimum_required(VERSION 3.22.1)

project("samplecppdemo")

# CMake에게 주어진 소스 파일 native-lib.cpp에 대한 공유 라이브러리(.so)를 빌드하도록 지시합니다.
# native-lib.cpp에는 JNI 함수도 포함되어 있어요.
add_library(
        ${CMAKE_PROJECT_NAME}
        SHARED
        native-lib.cpp)

# CMake은 현재 빌드에 다른 라이브러리를 링크할 수도 있어요.
# android와 log는 각각 안드로이드 특정 루틴과 로깅을 제공하기 위해 사용됩니다.
target_link_libraries(
        ${CMAKE_PROJECT_NAME}
        android
        log)
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

➡️ CMake는 컴파일러에 독립적인 방법으로 Make 스크립트를 생성할 수 있으며, 의존성, 헤더 및 컴파일 시에 링크해야 하는 다른 라이브러리를 추가할 수 있는 고유한 구문을 갖고 있습니다. CMake은 Gradle과 유사하여 둘 다 빌드 시스템입니다.

간단히 읽으려면 이 스택오버플로우 답변을 참조하세요: 코드를 컴파일하기 위해 Makefile과 CMake 중 어떤 것을 사용해야 하는지에 대한 차이는 무엇인가요?

# 4. 공유 라이브러리와 ABI

➡️ C/C++ 코드의 컴파일은 실행 파일 또는 라이브러리 중 하나의 이진 표현물을 생성할 수 있습니다. 실행 파일에는 실행이 시작되는 main 함수의 주소와 ELF 형식을 따르는 추가적인 세부 정보가 포함되어 있습니다. 라이브러리는 다른 프로그램에서 호출할 수 있는 함수를 제공하며, 프로그램의 오브젝트 코드와 라이브러리를 링크하여 사용됩니다.

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

<img src="/assets/img/2024-06-23-UsingCCinAndroidAComprehensiveGuideForBeginners_7.png" />

➡️ 안드로이드에서 C/C++ 파일은 .so (shared object) 확장자로 끝나는 공유 라이브러리로 컴파일됩니다. 이러한 라이브러리는 (2)에서 extern으로 표시된 JNI 함수를 노출합니다. JVM은 .so 파일의 코드를 검토하고 해당 기능의 이진 코드를 장치에서 실행하기 위해 사용할 수 있습니다.

➡️ 소스 코드와 라이브러리 코드 간의 상호 작용은 이진 레벨에서 발생하므로 일반적으로 응용 프로그램 바이너리 인터페이스 (ABI)를 통해 발생합니다. 반대로 응용 프로그램 프로그래밍 인터페이스 (API)는 컴파일이 발생하기 전 소스 코드 수준에서 이러한 상호 작용을 용이하게 합니다.

ABI에 대한 직관적인 설명을 보려면 LinkedIn 게시물을 확인해보세요. 두 개의 소프트웨어 조각이 소스 코드에서 통신해야 하는 경우 API를 사용합니다. 만약 두 이진 모듈이 통신하려면 어떻게 해야할까요?

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

# 결론

JVM은 이제 공유 라이브러리에 노출된 함수에 액세스할 수 있으며, OS가 필요에 따라 실행합니다.

이 기사가 흥미로웠고 새로운 것을 배우셨기를 바랍니다. 아래 댓글에 궁금한 점이나 제안을 공유해 주세요. 즐거운 하루 되세요!
