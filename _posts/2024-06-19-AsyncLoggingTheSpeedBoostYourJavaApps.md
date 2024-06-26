---
title: "비동기 로깅 자바 앱에 제공되는 속도 향상"
description: ""
coverImage: "/assets/img/2024-06-19-AsyncLoggingTheSpeedBoostYourJavaApps_0.png"
date: 2024-06-19 10:10
ogImage:
  url: /assets/img/2024-06-19-AsyncLoggingTheSpeedBoostYourJavaApps_0.png
tag: Tech
originalTitle: "Async Logging: The Speed Boost Your Java Apps"
link: "https://medium.com/@knowledge.cafe/async-logging-the-speed-boost-your-java-apps-d9d161ad1fe4"
---

<img src="/assets/img/2024-06-19-AsyncLoggingTheSpeedBoostYourJavaApps_0.png" />

소프트웨어 개발의 급속한 영역에서 밀리초가 문제가 되며 시스템 응답성이 품질 요건인 곳에서 전통적인 동기식(sync) 로깅에서 비동기식(async) 로깅으로의 여정은 게임 체인저로 입증되었습니다. 경험 많은 개발자로써, 특히 고도의 환경에서 성능을 최적화하는 중요성을 알고 계실 것입니다. 본격적인 투자 은행 응용 프로그램을 작업해왔을 때, 최적의 응답 시간을 달성하는 것에 대한 과제를 직면했습니다. 그 경계를 넘어 올 수 있는 것 같았던 것 — 400 TPS. 감사 작업은 중요했지만, 전통적인 로깅은 속도를 늦추는 앵커였습니다. 그런 다음, 우리는 대담한 결정을 내렸습니다: 비동기식 로깅. 그리고 말씀드리지만, 그것은 저희가 필요했다는 것을 전혀 모르고 있는 게임 체인저였습니다.

우리가 어떻게 성취했는지 궁금하신가요? 준비됐나요? 이 블로그는 즐거운 비동기식 로깅의 세계로 다가갑니다. 저는 개발 이야기의 중요한 순간에 대해 더욱 자세히 살펴볼 것이며, 비동기식 로깅으로의 전환과 전략적인 응용 프로그램 및 인프라 조정이 예상을 충족시키는데 그친 것이 아닌 더 초과했던 순간에 대해 알아볼 것입니다.

간단한 Java 응용 프로그램을 만들어 비동기식 로깅의 영향을 시연해보겠습니다. 저는 log4j-api.2.17.1, log4-core.2.17.1 및 lmax disruptor.4.0.0 같은 종속성을 가진 JDK 17을 사용할 것입니다.

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

위 코드는 GitHub에서 사용할 수 있습니다.

주 Java 클래스는 아래와 같이 보일 것입니다. 두 가지 메서드인 syncConsoleLogging()과 asyncLo4j2Log()를 포함하고 있습니다. syncConsoleLogging() 메서드는 메시지를 동기적으로 Log4j2를 사용하여 콘솔에 로깅하며, asyncLo4j2Log() 메서드는 메시지를 비동기적으로 로깅합니다. 이 예제는 비동기 로깅이 성능을 향상시킬 수 있는 방법을 이해하는 데 도움이 됩니다, 특히 로깅 작업이 많은 시나리오에서.

아래 log4j2.xml 스니펫은 두 개의 appender인 RandomAccessFile과 Console을 정의합니다. RandomAccessFile appender는 특정 패턴 레이아웃을 사용하여 "target" 디렉토리의 "async.log" 파일에 메시지를 로깅하며, Console appender는 다른 패턴 레이아웃으로 시스템 콘솔에 메시지를 로깅합니다. 파일 appender는 성능을 향상시키기 위해 일괄로 flush되도록 설정되어 있습니다. 추가로 루트 로거는 "info" 레벨의 메시지를 파일 appender에 로깅하도록 구성되어 있고, "syncLogger"라는 별도의 로거가 콘솔 appender에 메시지를 로깅하도록 정의되어 있습니다.

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

        <!-- Async Loggers will auto-flush in batches, so switch off immediateFlush. -->
        <RandomAccessFile name="RandomAccessFile" fileName="target/async.log" immediateFlush="false" append="false">
            <PatternLayout>
                <Pattern>%d %p %c{1.} [%t] %m %ex%n</Pattern>
            </PatternLayout>
        </RandomAccessFile>

        <Console name="Console" target="SYSTEM_OUT">
            <PatternLayout pattern="%d{HH:mm:ss.SSS} [%t] %-5level %logger{36} - %msg%n"/>
        </Console>

위 코드를 실행하면 Async 로깅의 영향을 확인할 수 있습니다. 제 개인 노트북에서 실행 후 아래 출력을 확인했습니다. 이 시나리오에서 Async 로깅이 동기 로깅보다 약 72.98% 빠름을 보여줍니다.

=== 처리 시간 요약 ===
동기 로깅 시간 2184 ms
Async 로깅 시간 590 ms

## Async 로깅의 장점

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

자바 애플리케이션에 대한 비동기 로깅은 성능 및 응답성 향상부터 높은 로깅 양을 처리하고 확장성을 향상시키는 장점을 제공합니다.

성능 향상: 애플리케이션의 주 스레드는 로깅 I/O가 완료될 때까지 기다리지 않으므로 응답성이 빨라집니다.

응답성: 로깅이 사용자 상호작용이나 다른 중요한 작업을 차단하지 않습니다.

확장성: 로깅 인프라는 더 많은 로그를 수용하도록 순조롭게 확장되어 애플리케이션의 핵심 기능에 영향을 주지 않습니다. 로깅을 주 애플리케이션 스레드로부터 분리함으로써 비동기 로깅은 수평 확장을 가능케 합니다. Disruptor 풀에 더 많은 워커 스레드를 추가함으로써 로깅 성능을 확장하여 더 높은 로그 양을 처리할 수 있습니다.

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

자원 사용량 줄이기: 주 스레드의 컨텍스트 전환 오버헤드와 메모리 소비를 최소화하세요.

고장 허용성 향상: 메인 스레드로부터 로깅을 분리하여 애플리케이션 충돌을 방지하세요. 디스크 공간이 가득 찼을 때 애플리케이션이 멈추거나 정지된 적이 있나요?

## 고려 사항:

- 복잡성: 비동기 로깅은 동기 로깅보다 더 많은 설정과 이해를 요구할 수 있습니다.
- 오류 처리: 잠재적인 로깅 오류와 큐 오버플로우를 처리하는 강력한 전략을 구현하세요.
- 로그 메시지의 순서: 비동기 로깅은 특히 여러 스레드가 동시에 로깅할 때 로그 메시지의 순서가 뒤죽박죽이 될 수 있습니다. 로거는 로그 메시지의 순서를 보존하려 노력하지만 로깅의 비동기적인 특성으로 인해 결정론적이지 않은 동작을 가져올 수 있습니다.
- 디버깅 및 문제 해결: 비동기 로깅 문제를 디버깅하는 것은 도전적일 수 있습니다. 특히 레이스 컨디션, 데드락 또는 예상치 못한 로깅 동작과 관련된 경우에는 신중한 분석과 테스트가 필요합니다.

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

## 결론

축하해요! 이제 비동기 로깅을 이해하고 활용할 지식으로 무장했어요. 앞으로 가서 느린 시스템을 뒤로 한 채 빠르고 반응성 있는 어플리케이션을 만들어보세요!

이 블로그는 비동기 로깅의 매력적인 세계를 탐험하며, 로깅을 주 애플리케이션에서 분리함으로써 가치 있는 리소스를 확보하는 방법을 보여줬어요. 비동기 로깅이 전통적인 동기 로깅과 비교했을 때 성능을 놀라운 72% 향상시킨 실제 자바 예제를 살펴봤어요. 네 맞아요, 더 빠른 로깅, 더 행복한 사용자, 그리고 더 적은 스트레스를 받을 거예요! 하지만 어떤 도구든 그것의 한계가 있죠. 비동기 로깅은 복잡성을 추가하며 신중한 구성이 필요하다는 것을 기억하세요.
