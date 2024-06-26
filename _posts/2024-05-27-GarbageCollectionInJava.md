---
title: "자바에서의 가비지 컬렉션"
description: ""
coverImage: "/assets/img/2024-05-27-GarbageCollectionInJava_0.png"
date: 2024-05-27 15:48
ogImage:
  url: /assets/img/2024-05-27-GarbageCollectionInJava_0.png
tag: Tech
originalTitle: "Garbage Collection In Java"
link: "https://medium.com/@ArvindVignesh/garbage-collection-in-java-b90a7f4b3ac7"
---

## 자바에서 쓰레기 수거가 이뤄지는 방식에 대한 기본적인 설명

![이미지](/assets/img/2024-05-27-GarbageCollectionInJava_0.png)

# 가비지 컬렉션이란?

변수를 선언하는 것은 프로그래머로서 생활의 일부입니다. 변수를 사용하는 것은 코드의 재사용성을 향상시키는 것뿐만 아니라 코드의 가독성을 크게 향상시키고, 이는 결국 코드의 유지보수성을 높이는 데 큰 도움이 됩니다. 또한 선언된 변수가 일정 공간을 차지하고 특정 범위와 연결되어 있다는 것은 비밀이 아닙니다. 최적화된 메모리 관리를 위해 JVM이 이러한 객체들이 더 이상 필요하지 않을 때 제거하거나 파괴하는 것이 합리적입니다. 이렇게 "원치 않는" 변수들을 제거하는 프로세스를 가비지 컬렉션이라고 합니다.

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

## 자바에서의 가비지 컬렉션

C, C++와 같은 언어에서는 가비지 컬렉션을 애플리케이션 개발자의 책임으로 간주됩니다. 이러한 언어에서는 쓰레기를 수동으로 처리하는 명시적 도구가 있습니다. 반면에 Java에서는 JVM이 가비지 컬렉션을 자동으로 처리합니다. JVM은 힙 메모리에서 언제 어디서 GC를 트리거할지 결정하는 일련의 프로세스를 거칩니다. 자바 개발자로서, 가비지 컬렉션의 기본 원리와 힙 메모리의 섹션을 이해하는 것이 매우 중요합니다. 이는 우리가 메모리를 효율적으로 사용하는 자바 애플리케이션을 개발하는 데 큰 도움이 될 것입니다.

## 가비지 컬렉션에 포함된 단계

자바 가비지 컬렉션은 일반적으로 2단계로 수행됩니다. 마크 단계와 스윕 단계입니다.

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

마크 단계: 마크 단계에서는 객체를 참조될 수 있는지 여부에 따라 두 가지 방식으로 분류합니다. 참조된 객체는 여전히 사용 중이며 범위에서 벗어나지 않은 객체입니다. 참조되지 않은 객체는 응용 프로그램에서 더 이상 필요하지 않은 객체입니다. 참조되지 않는 객체는 가비지 수집을 위해 "표시"됩니다.

스윕 단계: 스윕 단계에서는 "표시"된 객체가 삭제되거나 지워집니다. 게다가 때로는 힙(heap)에서 메모리 압축(Mark-compact 알고리즘)이라고 하는 작업도 초기화됩니다. 이 과정을 통해 남아 있는 객체가 연속된 메모리에 남아 있도록 메모리가 다시 할당됩니다. 이 과정을 통해 JVM이 새로 생성된 객체에 순차적으로 메모리를 할당할 수 있습니다. 메모리 압축 프로세스의 세부 내용은 이 글의 범위를 벗어나므로 생략하겠습니다.

효율적인 가비지 수집을 유지하기 위해 JVM이 따르는 일부 가비지 수집 전략이 있습니다. 가장 흔한 전략은 세대별 가비지 수집 전략입니다.

## 세대별 가비지 수집 전략

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

여기서는 일반적으로 사용되는 가장 흔한 가비지 컬렉션 형태인 세대별 가비지 컬렉션 전략을 탐구해 볼 거에요. 세대별 가비지 컬렉션은 주로 2가지 주된 이유로 선호됩니다.

- 메모리에 할당된 객체가 더 많아질수록 표시, 정리, 그리고 압축 과정이 굉장히 비효율적해집니다. 이는 표시, 정리, 그리고 압축하는 데 소요되는 시간을 증가시키고 극도로 큰 GC 시간을 야기할 수 있습니다.
- 분석 결과 자바 애플리케이션의 대부분 객체가 수명이 짧다는 것을 입증하였습니다.

위에서 언급된 이유들 때문에, GC 프로세스를 더 효율적으로 만들기 위해 자바의 힙 메모리는 세그먼트 또는 세대로 분할됩니다.

![가비지컬렉션](/assets/img/2024-05-27-GarbageCollectionInJava_1.png)

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

젊은 세대:

젊은 세대는 세 지역으로 나뉩니다. 에덴 공간, 생존자 공간 제로(S0) 및 생존자 공간 원(S1). 우리는 응용 프로그램 부팅 시 가비지 컬렉션 프로세스를 논의해 보겠습니다.

처음에는 생성된 모든 객체가 에덴 공간에 채워집니다. 생존자 공간(S0 및 S1)은 모두 비어 있을 것입니다. 에덴 공간이 가득 차면 소규모 가비지 컬렉션 주기가 트리거됩니다. 에덴 공간의 모든 참조되지 않은 객체가 삭제되고 모든 참조된 객체가 S0로 이동됩니다. 이것이 gc(0)입니다. 이어지는 가비지 컬렉션 주기에서 에덴 공간 및 S0 공간의 모든 참조된 객체가 S1로 이동됩니다. 이후 모든 참조되지 않은 객체가 삭제됩니다. gc(1) 이후의 객체 할당은 다음과 같이 보일 것입니다.

![image](/assets/img/2024-05-27-GarbageCollectionInJava_2.png)

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

위 이미지에서 볼 수 있듯이 모든 GC 주기마다 객체의 나이가 1씩 증가합니다. 나이가 특정 임계값에 도달하면 객체들은 힙 메모리의 다음 섹션으로 이동됩니다. 또한 모든 마이너 GC는 Stop the world 이벤트입니다. 이는 응용 프로그램 스레드가 GC가 완료될 때까지 중지된다는 것을 의미합니다. 일반적으로 마이너 GC는 메이저 GC와 비교했을 때 소요 시간이 더 적습니다.

늙은 세대

결국 객체의 연령이 특정 임계값에 도달하면 젊은 세대에서늙은 세대로 이동됩니다. 언젠가는 오래된 세대도 채워집니다. 오래된 세대가 가득 차고 정리되어야 할 때 메이저 GC가 트리거됩니다.

메이저 GC와 마이너 GC의 차이점

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

Minor GC와 Major GC의 주요 차이 중 하나는 Minor GC와 Major GC가 완료되는 데 걸리는 시간입니다. Major GC는 Minor GC와 마찬가지로 멈춤 현상(stop the world) 이벤트이지만 Major GC를 완료하는 데 걸리는 시간은 모든 활성 객체를 포함하기 때문에 Minor GC보다 훨씬 많이 소요됩니다. Major GC를 완료하는 데 필요한 시간은 주요 GC 공간이 증가할수록 선형적으로 증가하며 공간이 많을수록 메모리 압축에 더 많은 시간이 소요됩니다. 이는 응답 시간을 우선시하는 응용 프로그램에서 주요 GC의 빈도를 낮게 유지해야 한다는 것을 의미합니다. 또한 주요 GC의 멈춤 현상 이벤트의 길이는 사용 중인 GC 유형에 따라 달라집니다.

**Full GC**

자바 가비지 컬렉션에서 Full GC란 young generation과 old generation GC가 모두 트리거되는 경우입니다. full garbage collection은 Minor GC나 Major GC보다 훨씬 더 많은 시간이 소요됩니다. Full GC가 발생하는 이유는 여러 가지가 있을 수 있습니다. 그 중 몇 가지 이유는 다음과 같습니다:

- Heap 공간이 가득 차는 경우: 모든 Java 힙 메모리가 고갈되기 전에 Full GC가 발생할 수 있습니다. 이는 일련의 영역을 연속으로 찾아야 하는 필요로 인해 발생할 수 있습니다.
- 영역 크기 변경: 메모리 수요에 따라 영역 크기(영구 또는 올드)가 변경되면 일반적으로 Full GC에 의해 영역 변경 크기가 시작됩니다.

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

## Metaspace

힙 메모리의 일부였고 전체 가비지 수집에 포함되었던 추가 메모리 세그먼트가 있습니다. 이 메모리 세그먼트를 영구 세대(Permanent generation)라고 했습니다. Java 8에서 영구 세대는 Metaspace로 변경되었으며 힙 메모리에서 분리되었습니다. Metaspace의 주요 이유는 클래스 메타데이터를 저장하는 것입니다. 클래스가 더 많은 공간을로드하고 메타데이터를 저장해야 할 경우 Metaspace는 자동으로 증가하는 기능을 갖추고 있습니다.

Java 가비지 컬렉션을 이해하는 것은 자바 애플리케이션을 보다 효율적으로 만들기 위해 매우 중요하며 시스템에서 잠재적인 메모리 누수를 해결하고 문제 해결하는 데 도움이 됩니다.
