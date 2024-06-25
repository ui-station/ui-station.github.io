---
title: "테스트 속도를 높이는 parallel_tests의 힘을 활용해보세요 로컬 설정 및 실행법"
description: ""
coverImage: "/assets/img/2024-05-20-HarnessingthePowerofparallel_testsforFasterTestingLocalSetupandExecution_0.png"
date: 2024-05-20 15:50
ogImage:
  url: /assets/img/2024-05-20-HarnessingthePowerofparallel_testsforFasterTestingLocalSetupandExecution_0.png
tag: Tech
originalTitle: "Harnessing the Power of parallel_tests for Faster Testing: Local Setup and Execution"
link: "https://medium.com/@amas701111367/harnessing-the-power-of-parallel-tests-for-faster-testing-local-setup-and-execution-2e1e1a3dcd01"
---

# parallel_tests란 무엇인가요?

이는 RSpec, Cucumber 및 기타 테스트 도구 그룹의 실행 속도를 높이기 위해 사용되는 Ruby gem입니다.

ParallelTests는 테스트를 균형 잡힌 그룹(라인 수 또는 실행 시간으로)으로 분할하고 각 그룹을 해당 데이터베이스와 함께 프로세스에서 실행합니다.

# 설정 및 실행

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

- .zshrc 파일에 export PARALLEL_TEST_PROCESSORS=n을 추가하여 세그먼트 수를 지정하세요.
- `test` 섹션 내에서 database.yml 파일에 병렬 테스트 구성을 추가하세요.

```js
test:
 database: yourproject_test<%= ENV['TEST_ENV_NUMBER'] %>
```

- .rspec 파일에 형식 구성을 추가하세요.

```js
--format progress
--format ParallelTests::RSpec::RuntimeLogger --out tmp/parallel_runtime_rspec.log
--format ParallelTests::RSpec::SummaryLogger --out tmp/spec_summary.log
--format ParallelTests::RSpec::FailuresLogger --out tmp/failing_specs.log
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

- 다음 명령을 실행하여 병렬 테스트 데이터베이스를 생성하세요: rake parallel:create.
- 마이그레이션에 업데이트가 있는 경우 rake parallel:migrate를 실행하세요.
- rake parallel:prepare 명령을 사용하여 개발 스키마를 복사하세요.
- 병렬로 스펙을 실행하려면 rake parallel:spec 명령을 사용하세요.
- 모든 병렬 테스트 데이터베이스를 삭제하려면 rake parallel:drop 명령을 실행하세요.

참고사항:

- 기본적으로 PARALLEL_TEST_PROCESSORS의 수는 기기의 프로세서 수로 설정됩니다.
- 모든 작업을 한 번에 처리하려면 🤩 다음 명령어를 사용하세요

```js
RAILS_ENV=test PARALLEL_TEST_PROCESSORS=6
rails db:drop db:create db:migrate parallel:prepare parallel:spec
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

# 장점

- 빠른 테스트 실행 달성
  일반적으로 병렬 테스트를 사용하지 않을 때 테스트 실행에 약 1.5시간이 소요된다고 가정해 보겠습니다. 그러나 6개 코어를 이용하여 병렬 테스트를 수행하면 이 시간을 15분에서 20분으로 줄일 수 있어 대략 85~90%의 속도 향상이 가능합니다.
- 최적화된 자원 활용
  6개 코어를 보유한 상황을 가정해 봅시다. 병렬 테스트를 사용하지 않는다면 모든 사양은 단일 코어에서만 실행됩니다. 그러나 병렬 테스트를 활용하면 여러 코어를 이용하여 사양을 실행할 수 있습니다.
- Rails와의 원활한 통합
  한 번만 병렬 테스트를 구성하면 여러 테스트 도구에서 사용할 수 있습니다. 예를 들어, RSpec을 사용하는 경우 rake parallel:spec을 실행할 수 있습니다. Minitest로 전환한다면 실행 명령을 rake parallel:test로 변경하기만 하면 됩니다.

# 결점

- 자원 부담
  병렬로 테스트를 실행하는 것은 CPU 및 메모리와 같은 추가 시스템 자원을 소비합니다.
- 불안정성 가능성
  캐싱이나 Redis와 같은 공유 자원을 활용하는 일부 사양은 설정되어 있지만 다른 세그먼트의 사양에서는 해당 자원을 읽을 수 있습니다. 이로 인해 오류나 일관성에 문제가 발생할 수 있습니다.

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

# 자원

- 문서

# 결론

요약하자면, 더 빠른 테스트를 위해 병렬 테스트를 활용하는 것은 상당한 이점을 제공합니다. 자원 활용을 최적화하고 Rails와 완벽하게 통합되어 테스트 실행 시간을 크게 줄입니다. 그러나 자원 과부하 및 안정성과 같은 잠재적인 단점에 대해 주의해야 합니다. 적절한 구성과 모니터링으로 병렬 테스트가 개발 프로세스에서 유용한 자산임을 입증했습니다. 더 많은 지침을 위해서 문서를 참조하세요.
