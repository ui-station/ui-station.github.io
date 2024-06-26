---
title: "Uber는 어떻게 매일 페타바이트 규모의 Spark 셔플 데이터를 처리할까"
description: ""
coverImage: "/assets/img/2024-06-22-HowdoesUberhandlepetabytesofSparkshuffledataeveryday_0.png"
date: 2024-06-22 23:37
ogImage:
  url: /assets/img/2024-06-22-HowdoesUberhandlepetabytesofSparkshuffledataeveryday_0.png
tag: Tech
originalTitle: "How does Uber handle petabytes of Spark shuffle data every day?"
link: "https://medium.com/@vutrinh274/how-does-uber-handle-petabytes-of-spark-shuffle-data-every-day-223d8317cfb1"
---

## 원격 외부 서비스 (RSS)

# 목차

- Uber의 Apache Spark
- 원래의 셔플
- MapReduce에 대한 새로운 생각
- RSS 아키텍처

# TL;DR

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

스파크 셔플 데이터의 대규모 규모는 Uber에 약간의 문제를 일으킵니다. 이에 대응하여 Uber는 원격 셔플 서비스(RSS)를 사용하여 셔플 데이터를 원격으로 관리하기로 결정했습니다. RSS의 주요 아이디어는 스파크 실행자가 맵 작업에서 셔플 데이터를 원격 서버로 보내도록 한 다음 리듀서가 거기서 데이터를 가져오도록하는 것입니다. 게다가, Uber는 솔루션을 더 효율적으로 만들기 위해 원본 MapReduce 패러다임을 역전시켰습니다.

# 소개

내 글 쓰기 역사를 돌이켜보면, 내 첫 블로그는 BigQuery가 셔플 작업을 처리하는 방법에 대한 기사입니다. 그 블로그에서는 Google이 Dremel의 셔플 도전 과제를 해결하기 위해 전용 인메모리 셔플 서버를 구축할 때 사용한 흥미로운 접근 방식에 대한 메모를 공유했습니다. Dremel 맵 워커가 로컬 디스크에 셔플 레코드를 쓰는 대신 결과를 원격 서버에 쓰고 리듀스 워커가 데이터를 가져오도록 했습니다. Google은 이 방법이 셔플 스케일링 도전 과제에 대응하는 데 도움이 되었다고 말했지만, 원격 셔플 솔루션의 세부 사항에 대해서는 자세히 설명하지 않았습니다.

Dremel의 셔플 작업이 Google에 문제를 일으키는 요소 중 하나는 처리해야 하는 데이터의 거대한 양입니다. 이로 인해 나는 궁금해졌습니다: "셔플 작업에서 문제를 일으키는 거대한 규모의 데이터를 처리해야하는 다른 기업이 있는가?". 조사한 결과, Uber가 Dremel과 같은 문제를 겪고 있는 Spark에 문제가 있다는 것을 발견했습니다. Uber의 글에는 Spark 셔플 작업을 처리하기 위해 전용 서버를 개발했다는 내용이 있습니다. 다행이도 글은 솔루션이 어떻게 동작하는지에 대한 내용을 자세히 보여줍니다.

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

이번 주의 글은 우버(UBER)에서 공개한 훌륭한 블로그 게시물을 읽은 후에 작성한 제 노트입니다: 높은 확장성과 분산 셔플 서비스.

## 우버(Uber)에서의 아파치 스파크

스파크는 우버(Uber)의 주요 컴퓨팅 엔진으로, 라이드, 우버 이츠(Uber Eats) 또는 지도와 같은 운영을 지원합니다. 데이터 웨어하우징, 데이터 과학, 그리고 AI/ML에 있어서 필수적입니다. 우버의 스파크 사용량은 기하급수적으로 증가했으며, 하루에 수 백 페타바이트의 데이터를 처리하는 10,000대 이상의 프로덕션 노드에서 실행됩니다. 현재 스파크 작업은 분석 클러스터 계산 자원의 95% 이상을 사용하고 있습니다. 우버의 규모에 맞게 스파크를 운영하는 것은 데이터 이동과 관련된 도전에 직면하며, 특히 잘 알려진 셔플이라고 불리는 작업 단계 간 데이터 전송에 있어서 중요한 문제가 있습니다. 다음 섹션에서는 우버에게 중요한 원래의 스파크 셔플과 그것이 가지는 중요한 도전에 대해 알아보겠습니다.

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

![이미지](/assets/img/2024-06-22-HowdoesUberhandlepetabytesofSparkshuffledataeveryday_0.png)

과거의 Spark 셔플러에는 두 가지 종류의 작업이 있었습니다: 맵과 리듀스. 첫 번째는 셔플 데이터를 생성하고, 후자는 이를 사용합니다. Spark에서 작업 교환은 풀 모델을 사용하여 데이터를 섞습니다. 맵 작업은 셔플 데이터를 로컬 디스크에 작성합니다. 그런 다음 리듀스 작업은 여러 맵 작업에게 해당 데이터를 가져오기 위해 연결합니다.

## 셔플 쓰기

![이미지](/assets/img/2024-06-22-HowdoesUberhandlepetabytesofSparkshuffledataeveryday_1.png)

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

우버의 초기 Spark 셔플 구현은 맵 작업이 셔플 데이터를 실행자의 로컬 디스크에 쓸 수 있게 했습니다. 먼저 데이터를 메모리 버퍼에 씁니다. 버퍼가 가득 차면 데이터를 임시 파일로 디스크에 흘립니다. 나중에 모든 흘림 파일을 최종 셔플 파일로 병합합니다. 우버는 이 프로세스가 최적화되지 않았음을 깨달았습니다. 많은 경우에, 장애 파일에서 여러 디스크 작업(읽기 및 쓰기)을 수행해야 하여 지연을 초래합니다.

## 셔플 읽기

![이미지](https://miro.medium.com/v2/resize:fit:1400/1*f9fRJZZBKeHhtBtZsVFTyw.gif)

맵 호스트에서는 여러 파티션에서 많은 데이터 파일이 오는데, 호스트는 또한 각 파일이 어느 파티션에 속하는지 추적하기 위해 파티션 오프셋을 유지하는 인덱스 파일을 유지합니다.

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

reduce 작업은 각 맵퍼 호스트에서 실행 중인 셔플 서비스와 통신하여 해당 호스트에서 셔플 파티션 출력을 가져옵니다. 셔플 서비스가 요청을 받으면 인덱스 파일에서 오프셋을 읽고 reduce 작업에 필요한 데이터를 찾아 반환합니다. 그 후에 reduce 작업은 데이터를 메모리 버퍼로 가져와서 리듀서 프로세스에서 사용할 이터레이터를 생성합니다. 이 과정은 비효율적입니다. 왜냐하면 리듀서가 파티션 데이터를 가져오기 위해 많은 맵퍼 호스트에 요청해야 하며, 이로 인해 네트워크 오버헤드가 발생하고 맵퍼 호스트가 많은 디스크 작업을 수행해야 합니다.

## 도전과제

Uber가 대규모로 Spark 셔플을 운영할 때 여러 도전 과제가 있었습니다:

- 하드웨어 신뢰성: 매일 SSD에 쓰이는 대량의 셔플 데이터로 인해 초기 설계에서 예상한 것보다 더 빨리 Uber 디스크가 소모됩니다. 3년간 유지될 것으로 예상되었던 디스크는 대신 6개월 만에 소모됩니다.
- 셔플 실패: 리듀서가 동일한 기계에서 모든 맵퍼 작업의 데이터를 가져올 때, 서비스가 사용 불가능해지며, 이로 인해 많은 셔플 실패가 발생합니다.
- 노이즈 이슈: 더 많은 셔플 데이터를 작성하는 응용 프로그램은 해당 기계의 디스크 공간을 모두 차지하여 다른 응용 프로그램이 디스크가 가득 찼다는 예외로 실패할 수 있습니다.
- 셔플 서비스 신뢰성 문제: Uber는 Spark에서 YARN 및 Mesos를 사용하여 외부 셔플 서비스를 사용합니다. 그들은 종종 일련의 노드에서 셔플 서비스를 사용할 수 없다는 경험을 가졌습니다.

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

## Uber가 문제를 해결하기 위한 노력

Uber는 지역 데이터 저장이 위에서 언급한 문제의 근본 원인 중 하나라는 것을 깨달은 후, 원격 기기에 셔플 데이터를 저장하는 여러 가지 접근 방식을 시도해 보았습니다:

- 다른 저장소 플러그인 사용: Uber는 Spark를 위한 셔플 관리자를 작성하여 다양한 저장소 플러그인을 지원하도록 하고, 셔플 파일을 HDFS 또는 NFS에 쓸 수 있는 플러그인을 개발했습니다. 그러나 테스트 결과, Spark 작업의 실행 시간이 2배에서 5배 증가했습니다.
- 스트리밍 쓰기: Uber는 Spark 실행기로부터 스트림을 수락할 수 있는 스트리밍 서버를 구축했습니다. 이들은 이러한 스트림의 싱크 대상으로 HDFS 및 로컬 저장소를 사용했지만, 작업 지연이 1.5배에서 3배 증가했습니다.

# MapReduce에 대한 새로운 접근 방식

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

여러 번의 실험 끝에 Uber는 매퍼에서 원격 서버로 스트리밍 쓰기를 통해 문제를 해결할 수 있다는 것을 발견했습니다. 그러나 이 방식의 성능은 현재 지역 머신 성능(맵 태스크가 데이터를 로컬 디스크에 쓰는 곳)과 비교할 수 없다고 합니다. 따라서 Uber는 원격 쓰기를 위해 맵-리듀스 패러다임을 역전시켰습니다.

처음에 맵 태스크는 데이터를 로컬 머신에 기록하고, 그 후 리듀스 태스크가 단일 파티션의 데이터를 얻기 위해 여러 매퍼 머신에 도달하였습니다. 이렇게 함으로써 리듀서는 각 맵 머신으로 이동하여 데이터를 가져오고 이를 최종적으로 병합하여 리듀스 프로세스를 수행하는 데 많은 시간이 소요되었습니다.

Apache Spark를 위해 Remote Shuffle Service(RSS)를 구현하기 위해, 매퍼는 셔플 데이터를 원격 서버로 써야 합니다. Uber는 매퍼가 동일 파티션의 데이터를 고유한 RSS 서버로 쓰도록 지정하여, 리듀서가 하나의 RSS 서버에서만 데이터를 가져오도록 했습니다. 이를 통해 Uber는 이제 리듀서가 여러 매퍼에 통신할 필요가 없어져서 원격 셔플 서버에서의 Spark 작업 지연을 완화할 수 있었습니다.

# RSS 아키텍처

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

![RSS architecture](https://miro.medium.com/v2/resize:fit:1400/1*SthOAIxcxzMl_pM5yqVdlw.gif)

다음은 RSS의 전반적인 아키텍처입니다:

- RSS에서 모든 Spark executor들은 서비스 레지스트리와 RSS 서버와 통신하기 위해 클라이언트를 사용합니다.
- 초기에 Spark 드라이버는 Zookeeper를 활용하여 동일한 파티션에 대한 고유한 RSS 서버 인스턴스를 식별합니다.
- 드라이버는 이 정보를 모든 매퍼와 리듀서에게 전달합니다.
- 매퍼와 리듀서는 이 정보를 사용하여 셔플 데이터 프로세스를 처리합니다.

## RSS 클라이언트

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

RSS는 클라이언트 측 jar 파일을 가지고 있으며 Apache Spark가 제공하는 Shuffle Manager 인터페이스를 구현합니다. 드라이버는 서비스 레지스트리를 쿼리하여 Shuffle을 처리할 RSS 서버 목록을 선택합니다. 그런 다음 드라이버는 이 메타데이터를 Shuffle 핸들 내에 인코딩합니다. Spark 머신은 Shuffle 핸들(다른 인터페이스)을 맵-리듀스 작업에 전달할 것입니다.

위에서 언급한대로, 모든 매퍼는 특정 파티션의 Shuffle 데이터를 단일 RSS 서버로 보냅니다. 따라서 리듀서는 특정 파티션의 Shuffle 데이터를 읽기 위해 단일 RSS 서버에 연결하기만 하면 됩니다.

RSS 클라이언트는 Shuffle 데이터의 복제 팩터를 여러 RSS 서버에 지정할 수도 있습니다. 매퍼는 단일 서버에 기록하는 대신, 서버 다운에 대한 내고장성을 추가하기 위해 두 개 이상의 서버로 데이터를 기록할 것입니다.

전체 클라이언트는 Shuffle Manager 인터페이스에서 구현되므로 Spark 코드베이스에는 코드 변경이 없습니다. Spark 작업 관점에서는 모든 것이 같지만, 매퍼가 이제 로컬 디스크에 쓰기 대신 Shuffle 데이터를 RSS 서버로 보내야 합니다.

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

## 서비스 레지스트리

RSS에서 서비스 레지스트리는 사용 가능한 RSS 서버 목록을 유지합니다. Uber는 서비스 레지스트리로 Zookeeper를 사용합니다. 각 RSS 서버는 주기적으로 상태를 업데이트하기 위해 ZooKeeper와 장시간 연결을 유지합니다. 셔플 등록 중에 Spark 드라이버는 Zookeeper에서 모든 사용 가능한 RSS 서버 목록을 검색합니다. 그런 다음 드라이버는 지연 시간, 셔플 파티션, 활성 연결 등 여러 요소를 기반으로 서버를 선택합니다. 이 프로세스를 마치면 드라이버는 사용 가능한 서버 목록을 셔플 핸들 내의 매퍼 및 리듀서에 전달합니다.

이 접근 방식은 각 RSS 인스턴스가 자체를 서비스 레지스트리에 등록해야 한다는 것을 필요로 합니다. 인스턴스는 실패하고 서비스 레지스트리에 연결을 잃으면 자동으로 등록 해제됩니다. 익스큐터는 사용 가능한 인스턴스 및 데이터 셔플 위치에 대한 정보를 얻기 위해 서비스 레지스트리와 통신합니다.

## 셔플 매니저

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

**Shuffle Manager** in RSS는 Spark의 셔플 관리자 인터페이스를 구현하는 구성 요소입니다. 이는 원격 셔플 서비스 인스턴스를 선택하고 추적하여 (매퍼) 데이터를 업로드하고 (리듀서) 다운로드하기 위한 책임을 가지고 있습니다.

## RSS 서버

![RSS Server](/assets/img/2024-06-22-HowdoesUberhandlepetabytesofSparkshuffledataeveryday_2.png)

각 매퍼는 셔플 데이터를 고유한 RSS 서버에 작성합니다. 클라이언트로부터 복제된 설정이 있는 경우 데이터는 내결함성을 위해 하나 이상의 서버에 작성됩니다.

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

셔플 데이터 스트림은 각 레코드가 파티션 ID와 실제 데이터를 가지고 있는 셔플 레코드의 시퀀스입니다. RSS 서버가 매퍼로부터 셔플 레코드를 받으면, 해당 파티션 ID를 기반으로 로컬 파일을 선택하고 레코드를 파일 끝에 추가합니다.

RSS 서버에서는 미접근된 셔플 데이터를 주기적으로 확인하고 36시간 이내에 접근되지 않은 데이터를 삭제하는 정리 스레드가 실행됩니다. Uber는 RSS 서버 당 동시 연결 수를 제한하여 RSS 서버가 과중되지 않도록 합니다.

# 고장 허용

- RSS 서버가 바쁠 때: 클라이언트가 RSS 서버에 도달하지 못할 경우, 서버에 부담을 주지 않도록 지수 함수적 백오프 재시도를 수행합니다.
- RSS 서버 다시 시작: 서버는 로컬 디스크로 셔플 데이터 파일을 읽어 다시 시작한 후 이전 상태를 복구합니다. 이러한 상황에서 맵 태스크가 실패하면 Spark 애플리케이션이 새로운 태스크 시도 ID로 이러한 실패한 태스크를 재시도합니다. RSS 서버는 파일에 저장된 각 셔플 레코드의 시도된 태스크를 유지합니다. 서버는 최종으로 성공한 태스크 시도 ID만 저장합니다. 리듀서는 마지막으로 성공한 시도가 있는 셔플 레코드만 수락합니다.
- 서버 다운: RSS 서버를 사용할 수 없는 경우 복제는 유용합니다. 셔플 데이터는 하나 이상의 RSS 서버에서 제공될 수 있습니다. 이러한 상황에서 필요하면 Spark 드라이버가 영향을 받는 매퍼/리듀서 스테이지를 다시 예약합니다.

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

# 하드웨어 신뢰성

거의 10 PB의 디스크 쓰기를 원격 서버로 오프로딩하여 최적화된 I/O-하드웨어로, RSS를 사용하여 SSD의 내구 시간을 3개월에서 36개월로 연장된다. 원래의 셔플 구현에 있어 3개월의 수명주기를 갖고 있던 SSD 디스크는 이제 RSS 서버를 도입한 이후 거의 3년 동안 사용할 수 있다.

# 애플리케이션 신뢰성

운영 환경에 RSS를 배포한 후, Spark 작업의 실패율이 크게 감소했다; 셔플 실패로 인한 실패율은 거의 95%로 줄었다. 또한, RSS 서버가 다운되는 경우를 대비해 장애 허용 기능을 추가함으로써 Uber는 99.99% 이상의 신뢰성을 달성했다.

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

# 확장성

현재 우버의 RSS는 하루에 약 220k 개의 애플리케이션을 처리하며, 약 80k 번의 셔플 및 일일 10 PB의 데이터를 처리합니다. 하나의 셔플마다 약 40 TB의 데이터를 처리하는 프로덕션 작업을 갖고 있습니다. RSS는 셔플 프로세스에 더 많은 RSS 서버를 참여시킴으로써 이러한 대규모 셔플을 쉽게 처리할 수 있습니다. 이는 스파크의 기본 외부 셔플에서는 불가능했으며, 각 기계의 디스크 크기에 의해 제한되었으며, 일반적으로 1 TB SSD를 사용했습니다.

# 마무리

이 기사를 통해 우버 블로그 게시물에서 얻은 주요 통찰을 문서화했습니다. 스파크의 원래 셔플 작업의 도전과제를 논의한 뒤 우버가 원격 셔플 서비스를 도입함으로써 이를 극복한 방법을 살펴보았습니다. 셔플 데이터의 원격 쓰기 및 읽기로의 전환은 네트워크를 통해 이제 이러한 작업을 수행해야 하므로 추가된 대기 시간의 가능성을 도입합니다.

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

하지만, 이 변경 역시 상당한 혜택을 가져다줍니다. 예를 들어, 줄어든 worker는 이제 입력 데이터를 읽기 위해 여러 map worker와의 통신이 필요 없어졌으며, 하나의 서버에서만 데이터를 읽으면 됩니다. 셔플된 데이터를 로컬 디스크로 이동하는 것은 Uber에게 다양한 엔지니어링 기회를 제공합니다. 예를 들어, map worker에 장애가 발생하더라도, 데이터가 RSS 서버에 원격으로 저장되어 있기 때문에 처리된 데이터에는 영향을 주지 않습니다.

이번 주는 여기까지 입니다. 읽어 주셔서 감사합니다.

다음 주에 뵙겠습니다.

# 참고문헌

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

테이블 태그를 Markdown 형식으로 변경해 드릴게요.
