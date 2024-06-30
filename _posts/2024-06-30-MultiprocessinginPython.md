---
title: "파이썬에서 멀티프로세싱 사용하는 방법"
description: ""
coverImage: "/assets/img/2024-06-30-MultiprocessinginPython_0.png"
date: 2024-06-30 22:13
ogImage: 
  url: /assets/img/2024-06-30-MultiprocessinginPython_0.png
tag: Tech
originalTitle: "Multiprocessing in Python"
link: "https://medium.com/gitconnected/multiprocessing-in-python-dabc766a1bf0"
---


<img src="/assets/img/2024-06-30-MultiprocessinginPython_0.png" />

# 소개

이 게시물은 multiprocessing 모듈을 사용하여 Python에서 다중 프로세싱을 소개하며 내용을 더 잘 이해하기 위한 몇 가지 예제와 시각화가 포함되어 있습니다.

마지막 부분의 자료 섹션에는 해당 주제를 더 깊게 이해할 수 있는 멋진 자료 링크가 몇 개 있습니다 🤓

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

관련 게시물들

- 동시성과 병렬 처리 소개
- Python에서의 Threading
- Python에서의 ProcessPoolExecutor

## 프로세스란 무엇인가요?

프로세스는 실행 중인 프로그램으로, 실행 중인 컴퓨터 프로그램의 인스턴스입니다. Python에서는 프로세스가 Python 코드를 실행하는 Python 인터프리터의 인스턴스입니다.

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

프로그램은 처리 명령을 포함한 실행 가능한 파일입니다. 응용 프로그램 또는 시스템 프로그램으로 나뉩니다:

- 응용 프로그램: 워드 프로세서, 게임, Google Chrome 등
- 시스템 프로그램: 컴파일러, 파일 관리 프로그램 등

이 파일들은 디스크에 저장되며 컴파일되어 메모리(RAM)로 로드되어 프로세서(CPU)에서 실행됩니다.

동시에 여러 프로세스가 실행될 수 있고, 하나의 프로그램은 여러 프로세스와 관련이 있을 수 있습니다(예: Chrome). 기계에서 실행 중인 각 프로세스는 자체 메모리 공간이 할당됩니다.

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

각 프로세스에는 적어도 하나의 스레드, 즉 주 스레드가 있습니다. 스레드는 프로세스 내에서 실행의 기본 단위입니다. 다른 독립적인 실행 흐름과 동일한 주소 공간을 공유하는 독립적인 실행 흐름입니다. 하나 이상의 스레드를 가질 수 있습니다.

![Multiprocessing in Python - Image 1](/assets/img/2024-06-30-MultiprocessinginPython_1.png)

아래에서 내 컴퓨터에서 실행 중인 일부 프로세스와 그 안에 있는 스레드 수를 확인할 수 있습니다. 크롬 프로그램에 속하는 여러 프로세스가 보이며, 각각의 프로세스 ID(PID)와 함께 표시됩니다.

![Multiprocessing in Python - Image 2](/assets/img/2024-06-30-MultiprocessinginPython_2.png)

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

파이썬 스크립트를 실행하면 운영 체제가 파이썬 인터프리터를 실행하는 프로세스를 생성하고, 이 프로세스가 다시 파이썬 코드를 실행합니다.

- 운영 체제는 새 프로세스를 시작합니다.
- 이 프로세스가 파이썬 인터프리터를 실행합니다.
- 파이썬 인터프리터가 사용자의 파이썬 스크립트를 읽고 실행합니다.

# Multiprocessing 사용 사례

Multiprocessing이 가장 적합한 작업 유형은 CPU 바운드 작업입니다. CPU 바운드 작업은 처리기 속도에 주로 좌우되는 작업으로, 수학 계산, 데이터 압축, 코드 컴파일, 머신 러닝 등과 같은 작업이 포함됩니다.

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

파이썬에서 multiprocessing을 사용할 때는 실제로 병렬성을 구현하는 것이며, 파이썬의 Global Interpreter Lock (GIL) 때문에 병렬 실행이 불가능한 멀티스레딩과는 달라요.

최소한 두 개의 CPU 코어를 갖춘 컴퓨터를 사용하여 multiprocessing을 통해 병렬로 여러 작업을 실행할 수 있어요. 하지만 오늘날에는 Simultaneous Multithreading SMT 또는 Hyper-Threading과 같은 기술 덕분에 하나의 코어 만으로도 여러 작업을 병렬로 실행할 수 있게 되었어요. 이 기술을 사용하면 컴퓨터가 하나의 물리 CPU 코어 안에 두 개 이상의 논리 코어를 가지게 됩니다.

CPU 바운드 작업을 수행하는 두 가지 작업을 고려해보세요. 이러한 작업은 완료하는 데 더 많은 시간이 소요돼요. 아래에는 이를 나타내기 위해 유연한 구석이 차 있는 원형으로 표현되어 있습니다. 이러한 작업을 병렬로 실행하면 많은 시간을 절약할 수 있어요.

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

대신, 동기적으로 실행하면 두 작업을 완료하는 데 필요한 시간이 더 오래 걸립니다.

![](/assets/img/2024-06-30-MultiprocessinginPython_4.png)

진정한 병렬성 없이 동시에 실행해도 이득이 없습니다.

![](/assets/img/2024-06-30-MultiprocessinginPython_5.png)

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

# 자식과 부모 프로세스

부모 프로세스는 하나 이상의 자식 프로세스를 생성한 프로세스입니다. 시스템 호출을 통해 자식 프로세스의 생성을 시작합니다.

플랫폼에 따라, multiprocessing은 프로세스를 시작하는 세 가지 방식을 지원합니다:

- spawn
  - 부모 프로세스는 새로운 Python 인터프리터 프로세스를 시작합니다. 자식 프로세스는 프로세스 객체의 Process.run() 메서드를 실행하는 데 필요한 리소스만 상속받습니다.
  - Windows 및 macOS에서 기본값입니다.
- fork
  - 부모 프로세스는 os.fork()를 사용하여 Python 인터프리터를 복제합니다. 자식 프로세스는 시작할 때 부모와 동일합니다.
  - macOS를 제외한 POSIX(Linux)에서 기본값입니다.
- forkserver
  - 서버 프로세스가 생성되며 새 프로세스가 필요할 때마다 부모 프로세스는 서버에 연결하여 새 프로세스를 생성하도록 요청합니다.
  - Linux와 같은 Unix 파이프를 통해 파일 디스크립터를 전달하는 POSIX 플랫폼에서 사용할 수 있습니다.

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

프로그램이 실행될 때 시작하는 기본 프로세스를 주 프로세스라고 합니다. 

자식 프로세스는 부모 프로세스에 의해 생성된 프로세스입니다. 부모 프로세스의 사본이지만 고유한 프로세스 ID(PID)를 가지고 있습니다.

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

부모로부터 환경 변수, 열린 파일, 특정 리소스 제한 등 속성을 상속받을 수 있습니다.

프로세스를 다룰 때 운영체제는 프로세스에 필요한 자원(CPU 시간, 메모리 등)을 할당하고, 어떤 프로세스가 실행되는지 결정하여 CPU 시간을 공정하게 분배하고 효율적인 멀티태스킹을 보장합니다.

# 프로세스 상태

실행 중인 프로세스는 다음 상태들 사이를 변경할 수 있습니다. 프로세스가 실행 중일 때 높은 우선 순위의 프로세스에 의해 중단될 수 있어 다시 준비 상태로 돌아가게 됩니다. 또한 프로세스가 실행 중일 때 입력/출력 작업이나 이벤트 대기가 필요할 수 있습니다. 그런 경우 프로세스는 대기 상태로 변경되며, 대기 상태가 완료되면 준비 상태로 돌아갑니다.

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

<img src="/assets/img/2024-06-30-MultiprocessinginPython_6.png" />

만약 자식 프로세스가 실행을 완료했지만 부모가 아직 종료 상태를 읽지 않아 프로세스 테이블에 남아 있는 경우, 이를 좀비 프로세스 또는 망자 프로세스라고 합니다.

부모 프로세스가 이미 종료되어 자식 프로세스가 아직 남아 있는 경우, 해당 자식 프로세스는 고아 프로세스가 됩니다.

# 파이썬 multiprocessing 첫 걸음

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

멀티프로세싱 모듈은 스레딩 모듈과 유사한 API를 사용하여 프로세스를 생성하는 것을 지원합니다. multiprocessing 패키지는 로컬 및 원격 동시성을 모두 제공하여 스레드 대신 서브프로세스를 사용함으로써 전역 인터프리터 락을 우회합니다.

우리는 multiprocessing.Process 객체를 생성하여 메인 프로세스에서 새로운 프로세스를 생성할 수 있습니다. 메인 프로세스는 새로운 자식 프로세스의 부모입니다.

하지만 먼저, 파이썬의 Threading에서 사용한 것과 같은 CPU 바운드 작업을 정의해 봅시다.

```js
def cpu_bound_operation(n: int) -> tuple[float]:
    """CPU-bound task."""
    start = perf_counter()
    count = 0
    for i in range(n):
        count += i
    finish = perf_counter()

    return start, finish
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

이 코드는 n개의 숫자 범위를 반복하고 이를 모두 더합니다. 그런 다음 나중에 차트를 그릴 수 있도록 시작 및 종료 시간이 포함된 튜플을 반환합니다.

그런 다음 단순히 다른 함수에서 CPU 집약 작업을 실행하는 함수를 호출하고 반환 값을 기록합니다.

마지막으로 multiprocessing 모듈에서 Process 클래스를 사용하여 자식 프로세스를 생성합니다.

```python
def cpu_bound_task(counts: int) -> None:
    """Run a CPU-bound task and append the results to shared_list."""
    time = cpu_bound_operation(counts)
    logging.info(f"time - {time}")

def multiprocessing() -> None:

    # Run in a child process ; 150000000 is 3.25 secs aprox in my laptop
    p = Process(target=cpu_bound_task, args=(150000000,))
    p.start()  # Starts the process and calls the target function
    p.join()  # Blocks the thread


if __name__ == "__main__":
    logging.info("Init program")
    multiprocessing()
    logging.info("Finish program")
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

위의 multiprocessing() 함수에서:

- multiprocessing.Process 객체를 생성할 때 args 매개변수에 일부 인수를 전달하여 생성한 함수를 대상 함수로 전달할 수 있습니다.
- 대상 함수 cpu_bound_task는 multiprocessing.Process의 내부 run() 메서드에서 호출됩니다(부모 클래스 process.BaseProcess에서 상속되었지만 아직 호출되지 않았습니다).
- start() 메서드가 호출될 때까지 프로세스는 준비 상태로 대기합니다.
- start() 메서드를 호출하면 프로세스가 실행 중인 상태로 전환되어 새로운 자식 프로세스가 고유한 주 메인 스레드를 가지고 생성됩니다.
- 새로운 프로세스는 전달한 인수와 함께 대상 함수를 실행하는 run() 메서드를 실행합니다.
- 프로세스는 차단되지 않는 한 실행 중인 상태입니다.
- join() 메서드는 프로세스가 실행을 완료할 때까지 대기합니다. 호출하는 스레드(주 프로세스의 주 스레드)는 프로세스가 종료될 때까지 블록됩니다.

아래는 예제를 실행한 결과로, 자식 프로세스가 완료하는 데 걸린 시간을 보여줍니다.

```js
20:45:31: 프로그램 시작
20:45:35: 시간 - (45723.689522083, 45727.402436291)
20:45:35: 프로그램 완료
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

# 각 프로세스에는 별도의 메모리 주소 공간이 있습니다.

파이썬의 스레딩에 대한 포스트를 읽으셨다면, 같은 메모리 공간을 공유하는 쓰레드들이 완료하는 데 걸린 시간을 shared_data 변수에 저장했다는 것을 알 수 있을 것입니다. 같은 프로세스 내의 스레드는 동일한 메모리 공간을 공유하기 때문에 모두 데이터를 공유할 수 있습니다. 하지만 프로세스들은 다른 프로세스의 공유 데이터에 직접적으로 액세스할 수 없습니다.

다음 예시에서 간단한 변수를 통해 두 프로세스가 데이터를 직접적으로 공유할 수 없음을 확인할 수 있습니다. 왜냐하면 각 프로세스는 별도의 메모리 공간을 갖기 때문입니다.

우리는 주 프로세스에서 shared_list라는 변수를 생성하여 그 안에 시간 결과를 저장합니다. 그런 다음 두 가지 작업을 정의하는데, 각각은 자신의 결과를 shared_list 변수에 추가할 것입니다.

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
import logging
from concurrency.operations.cpu_bound import cpu_bound_operation
from multiprocessing import Process


format = "%(asctime)s: %(message)s"
logging.basicConfig(format=format, level=logging.INFO, datefmt="%H:%M:%S")


# 동일한 프로세스의 스레드는 메모리 공간을 공유합니다
# 프로세스는 다른 메모리 공간을 가집니다
shared_list = []


def cpu_bound_task_1(counts: int) -> None:
    """CPU 바운드 작업을 실행하고 결과를 shared_list에 추가합니다."""
    time = cpu_bound_operation(counts)
    logging.info("시간 1 - ", time)
    logging.info(f"공유 리스트 1 {shared_list}")
    shared_list.append([time])
    logging.info(f"공유 리스트 1 {shared_list}")


def cpu_bound_task_2(counts: int) -> None:
    """CPU 바운드 작업을 실행하고 결과를 shared_list에 추가합니다."""
    time = cpu_bound_operation(counts)
    logging.info("시간 2 - ", time)
    logging.info(f"공유 리스트 2 {shared_list}")
    shared_list.append([time])
    logging.info(f"공유 리스트 2 {shared_list}")
```

이제 multiprocessing() 함수에서 CPU 바운드 작업을 호출합니다.

cpu_bound_task_1()은 부모 프로세스인 메인 프로세스의 주 스레드에서 실행됩니다.

그런 다음 작업 1이 완료되면 자식 프로세스에서 작업 2를 실행합니다.

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

```python
def multiprocessing() -> None:

    start = perf_counter()
    # Main process에서 실행 - 1
    cpu_bound_task_1(150000000)

    # 자식 프로세스에서 실행 - 2
    p = Process(target=cpu_bound_task_2, args=(150000000,))
    p.start()  # 프로세스 시작 및 대상 함수 호출
    p.join()  # 스레드 차단

    logging.info(f"최종 shared_list {shared_list}")
    end = perf_counter()

    logging.info(f"총 소요 시간 :: {round(end - start, 4)} 초")


if __name__ == "__main__":
    logging.info("프로그램 시작")
    multiprocessing()
    logging.info("프로그램 종료")
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

만약 두 작업의 순서를 변경한다면 (아래 예시 참조) join() 메서드가 주 스레드를 차일드 프로세스가 완료될 때까지 차단하기 때문에 병렬로 실행되지 않게 됩니다.

```js
def multiprocessing() -> None:

    start = perf_counter()
    # 자식 프로세스에서 실행 - 2
    p = Process(target=cpu_bound_task_2, args=(150000000,))
    p.start()  # 프로세스를 시작하고 타겟 함수를 호출합니다
    p.join()  # 스레드를 차단합니다

    # 메인 프로세스에서 실행 - 1
    cpu_bound_task_1(150000000)

    logging.info(f"최종 공유 리스트 {shared_list}")
    end = perf_counter()

    logging.info(f"총 시간 :: {round(end - start, 4)} 초")
```

완료하는 데 걸리는 시간은 거의 동일합니다.

```js
17:55:18: 프로그램 시작
...
17:55:25: 총 시간 :: 6.6648 초
17:55:25: 프로그램 완료
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

실제로 두 작업을 병렬로 실행하려면 작업 1이 실행되기 전에 부모 프로세스의 주 스레드를 차단하지 않아야 합니다. 따라서 작업 1이 시작된 후 join() 메서드를 호출해야 합니다.

```js
def multiprocessing() -> None:

    start = perf_counter()
    # Run in a child process - 2
    p = Process(target=cpu_bound_task_2, args=(150000000,))
    p.start()  # 프로세스를 시작하고 대상 함수를 호출합니다.

    # Run in the main process - 1
    cpu_bound_task_1(150000000)
    
    p.join()  # 스레드를 차단합니다.

    logging.info(f"final shared_list {shared_list}")
    end = perf_counter()

    logging.info(f"Total time :: {round(end - start, 4)} secs")
```

아래에서 볼 수 있듯이, 두 작업을 완료하는 데 소요된 시간이 대략 절반으로 감소했습니다:

```js
18:08:48: 병렬 프로그램 초기화
...
18:08:51: 총 시간 :: 3.3857 초
18:08:51: 병렬 프로그램 완료
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

어이, 잠시만요! 방금 다른 프로세스가 같은 메모리 공간을 공유하지 않는지 확인했던 거 아니었나요? 🧐

병렬로 실행할 수 있는 우리 프로그램의 전체 출력을 조사해보면 몇 가지 흥미로운 점을 발견할 수 있어요.

```js
18:08:48: 병렬 프로그램 초기화

18:08:51: 시간 1 - (14708.567523791, 14711.932722375)
18:08:51: 공유 목록 1 []
18:08:51: 공유 목록 1 [[(14708.567523791, 14711.932722375)]]

18:08:51: 시간 2 -  (14708.587407041, 14711.944044333)
18:08:51: 공유 목록 2 []
18:08:51: 공유 목록 2 [[(14708.587407041, 14711.944044333)]]

18:08:51: 최종 공유 목록 [[(14708.567523791, 14711.932722375)]]

18:08:51: 총 실행 시간 :: 3.3857 초

18:08:51: 병렬 프로그램 완료
```

- 우리는 부모 프로세스에서 작업 1을 실행하기 전에 작업 2의 목표로 자식 프로세스를 생성하고 시작합니다. 그러나 작업 1이 작업 2보다 먼저 실행되기 시작합니다. 이는 자식 프로세스를 생성하는 과정이 충분한 시간이 소요되어 작업 1이 시작된 후에야 그 이후로 늦춰진다고 생각해요.
- 두 작업은 부모 프로세스에서 생성된 share_list 변수에 결과 시간을 추가합니다. 그러나 우리가 로그를 찍을 때 변수에 저장된 결과는 부모 프로세스에서 실행되었던 작업인 작업 1의 결과만 보관하는 것을 확인할 수 있어요. 이것은 자식 프로세스가 부모 프로세스와 데이터를 공유하지 못하도록 되어 있다는 것을 확인해줘요.

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

위의 결과를 주의 깊게 살펴보면, 두 작업 모두 메모리 공간을 공유하지 않는 상황에서도 데이터를 shared_list라는 변수에 저장한다는 점을 알 수 있어요. 🤯 여기서 무슨 일이 벌어지고 있는 걸까요? 😱

spawn 방법을 사용하여 multiprocessing 모듈에 의해 생성된 각 프로세스(새로운 Python 인터프리터 프로세스를 시작함)는 독립된 메모리 공간을 갖습니다. 즉, shared_list와 같은 전역 변수를 포함한 모든 변수는 각 프로세스에서 복제되며, 한 프로세스에서 이러한 변수를 변경해도 다른 프로세스에 영향을 주지 않아요.

이를 더 잘 이해하기 위해 프로세스 ID와 shared_list의 메모리 주소를 확인하는 코드를 추가해볼까요?

```js
...
shared_list = []
logging.info(
    f"--> id - main thread in {os.getpid()} process shared_list: {id(shared_list)}"
)


def cpu_bound_task_1(counts: int) -> None:
    ...
    shared_list.append([time])
    logging.info(
        f"--> id - main thread in {os.getpid()} process in task 1 shared_list: {id(shared_list)}"
    )
    logging.info(f"shared_list 1 {shared_list}")


def cpu_bound_task_2(counts: int) -> None:
    ...
    shared_list.append([time])
    logging.info(
        f"--> id - main thread in {os.getpid()} process in task 2 shared_list: {id(shared_list)}"
    )
    logging.info(f"shared_list 2 {shared_list}")


def multiprocessing() -> None:

    start = perf_counter()
    # Run in a child process - 2
    p = Process(target=cpu_bound_task_2, args=(150000000,))
    p.start()  # Starts the process and calls the target function

    # Run in the main process - 1
    cpu_bound_task_1(150000000)

    p.join()  # Blocks the thread

    logging.info(f"final shared_list {shared_list}")
    logging.info(
        f"--> id - main thread in {os.getpid()} process final shared_list: {id(shared_list)}"
    )
    end = perf_counter()

    logging.info(f"Total time :: {round(end - start, 4)} secs")
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

위의 코드 결과는 다음을 보여줍니다:

```js
19:58:05: --> FIRST id - 메인 스레드인 17723 프로세스에서 shared_list: 4340073984

19:58:05: 병렬 프로그램 초기화

19:58:05: --> FIRST id - 메인 스레드인 17725 프로세스에서 shared_list: 4313028032

19:58:08: 시간 2 - (21265.75012825, 21269.074238291)
19:58:08: 공유된 목록 2 []
19:58:08: --> 17725 프로세스의 작업 2에서 메인 스레드 ID가 shared_list: 4313028032인 것
19:58:08: 공유된 목록 2 [[(21265.75012825, 21269.074238291)]]

19:58:09: 시간 1 - (21265.729837416, 21269.162869458)
19:58:09: 공유된 목록 1 []
19:58:09: --> 17723 프로세스의 작업 1에서 메인 스레드 ID가 shared_list: 4340073984인 것
19:58:09: 공유된 목록 1 [[(21265.729837416, 21269.162869458)]]

19:58:09: 최종 공유된 목록 [[(21265.729837416, 21269.162869458)]]

19:58:09: --> 17723 프로세스의 최종 공유된 목록: 4340073984

19:58:09: 총 소요 시간: 3.4389초

19:58:09: 병렬 프로그램 완료
``` 

각 프로세스가 자체 메모리 주소를 갖는 shared_list 글로벌 변수의 고유한 버전을 가지고 있음을 알 수 있습니다. 이들은 완전히 다릅니다.

- 줄 1
  - 프로세스 PID 17723 - 부모 프로세스
  - shared_list 메모리 주소 4340073984
- 줄 5
  - 프로세스 PID 17725 - 자식 프로세스
  - shared_list 메모리 주소 4313028032
- 줄 9
  - 프로세스 PID 17725 - 자식 프로세스
  - shared_list 메모리 주소 4313028032
- 줄 14
  - 프로세스 PID 17723 - 부모 프로세스
  - shared_list 메모리 주소 4340073984
- 줄 19
  - 프로세스 PID 17723 - 부모 프로세스
  - shared_list 메모리 주소 4340073984

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

저는 macOS를 사용 중이기 때문에 multiprocessing 모듈은 spawn 방법을 사용하여 새로운 자식 프로세스를 생성합니다. spawn 방법은 spawn()과 같이 내부적으로 호출되는 방법이 아닙니다. 이 방법을 사용할 때 multiprocessing 모듈이 새로운 프로세스를 생성하기 위해 신선한 Python 인터프리터를 실행하는 방식으로 내부적으로 처리합니다. 그래서 우리의 shared_list 전역 변수는 다른 메모리 주소를 갖게 됩니다.

이제 시간 결과에 집중해보면 두 프로세스가 모두 해당 결과를 shared_list 변수에 추가하고 있음을 알 수 있습니다. 그러나 각각이 변수의 자체 버전에 추가하고 있습니다.

최종적으로 부모 프로세스에서 shared_list 변수의 값을 확인하면 부모 프로세스의 task 1이 그것에 추가한 값만 표시됩니다. 자식 프로세스의 task 2가 자신의 변수 버전에 값을 추가했기 때문에 데이터를 공유하지 않습니다.

```js
19:58:05: --> FIRST id - main thread in 17723 process shared_list: 4340073984

19:58:05: 병렬 프로그램 시작

19:58:05: --> FIRST id - main thread in 17725 process shared_list: 4313028032

19:58:08: 시간 2 - (21265.75012825, 21269.074238291)
19:58:08: shared_list 2 []
19:58:08: --> id - main thread in 17725 process in task 2 shared_list: 4313028032
19:58:08: shared_list 2 [[(21265.75012825, 21269.074238291)]]

19:58:09: 시간 1 - (21265.729837416, 21269.162869458)
19:58:09: shared_list 1 []
19:58:09: --> id - main thread in 17723 process in task 1 shared_list: 4340073984
19:58:09: shared_list 1 [[(21265.729837416, 21269.162869458)]]

19:58:09: 최종 shared_list [[(21265.729837416, 21269.162869458)]]

19:58:09: --> id - main thread in 17723 process final shared_list: 4340073984

19:58:09: 총 소요 시간: 3.4389 초

19:58:09: 병렬 프로그램 종료
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

이제 아마 궁금해하고 있을 거예요. 리눅스를 사용한다면 맥북 프로처럼 비싼 돈을 들여서 사용하는 경우에는 어떻게 될까요? 그렇죠? 그래서 os.fork() 메서드를 사용하여 자식 프로세스를 생성하는 경우에는 어떻게 될까요?

자, 이제 알아보겠습니다! ✨

우리는 multiprocessing 모듈의 시작 방법을 set_start_method()를 통해 명시적으로 지정할 수 있어요:

```js
...
import multiprocessing as mp 

...

def multiprocessing() -> None:

    mp.set_start_method("fork")

    start = perf_counter()
    # 자식 프로세스에서 실행 - 2
    p = mp.Process(target=cpu_bound_task_2, args=(150000000,))
    p.start()  # 프로세스를 시작하고 대상 함수를 호출함

    # 주 프로세스에서 실행 - 1
    cpu_bound_task_1(150000000)

    p.join()  # 스레드를 블록함

    logging.info(f"최종 공유 리스트 {shared_list}")
    logging.info(
        f"--> id - {os.getpid()} 프로세스 내의 메인 스레드가 가진 최종 공유 리스트: {id(shared_list)}"
    )
    end = perf_counter()

    logging.info(f"전체 시간 :: {round(end - start, 4)} 초")
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

프로세스 및 메모리 주소 로그에 집중하면 이제 shared_list 변수가 서로 다른 프로세스에 있더라도 동일한 메모리 주소를 가지고 있다는 사실을 알 수 있어요! 🤯

```js
20:54:27: --> id - 19098 프로세스 메인 스레드에서 shared_list: 4375988096

20:54:27: 병렬 프로그램 시작

20:54:31: 시간 1 - (24648.12229775, 24651.257626791)
20:54:31: shared_list 1 []
20:54:31: --> id - 19098 프로세스 메인 스레드에서 작업 1의 shared_list: 4375988096
20:54:31: shared_list 1 [[(24648.12229775, 24651.257626791)]]

20:54:31: 시간 2 - (24648.122759958, 24651.44781075)
20:54:31: shared_list 2 []
20:54:31: --> id - 19099 프로세스에서 작업 2의 shared_list: 4375988096
20:54:31: shared_list 2 [[(24648.122759958, 24651.44781075)]]

20:54:31: 최종 shared_list [[(24648.12229775, 24651.257626791)]]

20:54:31: --> id - 19098 프로세스 메인 스레드에서 최종 shared_list: 4375988096

20:54:31: 총 소요 시간 :: 3.3296 초

20:54:31: 병렬 프로그램 종료
```

부모 프로세스가 os.fork() 메서드를 사용하여 Python 인터프리터를 복제하는 경우, 자식 프로세스는 시작할 때 부모 프로세스와 완전히 동일합니다. 자식 프로세스는 부모의 모든 리소스를 상속받습니다.

따라서 os.fork()를 사용할 때 자식 프로세스는 부모 프로세스의 복사본으로 생성됩니다. 이는 부모 프로세스의 메모리 공간을 복사하며, 이로 인해 자식 프로세스가 초기에는 부모 프로세스와 동일한 메모리 레이아웃을 가지며 모든 객체의 주소를 포함합니다. 따라서 fork 직후에는 두 프로세스(부모 및 자식)가 변수인 shared_list와 같은 메모리 내용 및 주소를 공유합니다. 하지만 이는 두 프로세스가 동일한 메모리 공간을 공유한다는 의미는 아닙니다. 그들은 단지 fork 직후에 동일한 우연히 동일한 메모리 공간을 가지고 있을 뿐입니다.

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

이 작업은 메모리 사용량을 최적화하기 위해 "copy-on-write" 기술을 사용하여 수행됩니다. 이는 자식 프로세스가 초기에는 부모 프로세스와 동일한 메모리 페이지를 공유하지만 부모 또는 자식 프로세스 중 하나가 페이지를 수정하면 해당 페이지의 복사본이 수정 프로세스를 위해 생성됩니다. 따라서 메모리가 처음에는 공유되지만 어떤 변경이 발생하면 별도의 복사본이 생기면서 독립적인 메모리 공간으로 이어집니다.

# 프로세스 간 객체 교환

multiprocessing 모듈은 프로세스 간 통신 채널로 두 가지 유형을 지원합니다:

- Queue()
- Pipe()

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

multiprocessing.Queue은 내부적으로 multiprocessing.Pipe를 사용하며 일반적으로 선호됩니다. Queue는 Pipe에 비해 더 간단하고 직관적인 API를 가진 더 높은 수준의 추상화를 제공합니다.

Queue는 다음과 같은 Pipe에 비해 몇 가지 이점을 제공합니다:

- Queue는 여러 생성자 및 소비자를 기본적으로 지원하므로 여러 프로세스가 동시에 큐에 항목을 넣고 큐에서 항목을 동시에 가져올 수 있습니다. 반면에 Pipe는 두 지점 간의 일대일 통신에 가장 적합하며 여러 생성자 또는 소비자를 관리하는 것은 복잡하고 오류가 발생할 수 있습니다.
- Queue는 스레드 및 프로세스 안전합니다. Queue는 잠금 및 세마포어를 내부적으로 관리하여 작업이 안전하게 수행되고 데이터 손상이나 경합 조건이 발생하지 않도록 보장합니다.
- Queue에는 내부 버퍼링이 포함되어 있어 프로세스 간의 컨텍스트 전환 빈도를 줄이는 방식으로 성능을 향상시킬 수 있습니다. 버퍼는 데이터가 한 곳에서 다른 곳으로 전송되는 동안 사용되는 컴퓨터 메모리의 임시 저장 영역입니다.

일반적으로 큐는 프로세스 간에 단방향 또는 양방향 채널을 설정하기 위해 Pipe를 사용합니다.

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

하지만, Pipe()를 사용하여 예제를 시작해 봅시다!

## Pipe 함수

Pipe() 함수는 기본적으로 이중(duplex)인 파이프로 연결된 두 개의 연결 객체를 반환합니다. Pipe()에 의해 반환된 두 연결 객체는 파이프의 두 끝을 나타냅니다. 각각의 연결 객체는 send() 및 recv() 메서드(그 외에도 다른 메서드들이 있음)를 갖고 있습니다.

여기서는 send()를 사용하여 자식 프로세스에서 데이터를 보내고 recv()를 사용하여 부모 프로세스에서 데이터를 받습니다.

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
...
from multiprocessing.connection import Connection 
...
from multiprocessing import Pipe, Process 


shared_list = []

...

def cpu_bound_task_2(counts: int, conn: Connection) -> None:
    """CPU 바운드 작업을 실행하고 결과를 부모에게 보냅니다."""
    time = cpu_bound_operation(counts)
    logging.info(f"시간 2 -  {time}")
    conn.send([time])
    conn.close()


def multiprocessing() -> None:

    parent_conn, child_conn = Pipe()

    start = perf_counter()
    # 자식 프로세스에서 실행 - 2
    p = Process(target=cpu_bound_task_2, args=(150000000, child_conn))
    p.start()  # 프로세스를 시작하고 대상 함수를 호출합니다.

    # 주 프로세스에서 실행 - 1
    cpu_bound_task_1(150000000)

    child_time = parent_conn.recv()
    logging.info(f"자식 프로세스 시간: {child_time}")
    shared_list.append(child_time)

    p.join()  # 스레드를 차단합니다.
    ...
```

parent_conn.recv() 메서드는 cpu_bound_task_1 이후에 위치해야 합니다. 그렇지 않으면 부모 프로세스의 주 스레드가 데이터를 받을 때까지 블록됩니다.

```js
18:10:59: 병렬 프로그램 초기화

18:11:02: 시간 1 - (44691.260117416, 44694.601532625)
18:11:02: 공유 리스트 1 []
18:11:02: 공유 리스트 1 [[(44691.260117416, 44694.601532625)]]

18:11:02: 시간 2 -  (44691.283312583, 44694.670279125)

18:11:02: 자식 프로세스 시간: [(44691.283312583, 44694.670279125)]

18:11:02: 최종 공유 리스트 [[(44691.260117416, 44694.601532625)], [(44691.283312583, 44694.670279125)]]

18:11:02: 총 경과 시간 :: 3.4163 초

18:11:02: 병렬 프로그램 완료
```

이제 다시 shared_list 변수에 시간이 포함되어 있기 때문에 막대 차트를 생성하는 플로팅 함수를 사용할 수 있습니다! 💃🏻

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

<img src="/assets/img/2024-06-30-MultiprocessinginPython_7.png" />

## Queue 클래스

multiprocessing.Queue 클래스는 프로세스간에 객체를 교환하는 우선적인 방법입니다.

앞의 프로그램을 구현하기 위해 약간의 변경만 필요합니다.

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

```python
...
from multiprocessing import Process, Queue 

...

shared_list = []

...

def cpu_bound_task_2(counts: int, q: Queue) -> None:
    """Runs a CPU-bound task and sends the results to parent."""
    time = cpu_bound_operation(counts)
    logging.info(f"time 2 -  {time}")
    q.put([time]) # Add items to the queue


def multiprocessing() -> None:

    q = Queue()

    start = perf_counter()
    # Run in a child process - 2
    p = Process(target=cpu_bound_task_2, args=(150000000, q))
    p.start()  # Starts the process and calls the target function

    # Run in the main process - 1
    cpu_bound_task_1(150000000)

    child_time = q.get() # Remove and return an item from the queue
    logging.info(f"child process time: {child_time}")
    shared_list.append(child_time)

    p.join()  # Blocks the thread
    ...
```

put() 메서드는 항목을 큐에 추가하는 데 사용됩니다. 이 메서드는 객체를 직렬화하고 내부 버퍼로 이를 전송합니다.

get() 메서드는 큐에서 항목을 제거하고 반환하는 데 사용됩니다. 이 메서드는 파이프로부터 직렬화된 객체를 읽고 역직렬화합니다.

그리고 결과는 꽤 유사합니다.

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


![Multiprocessing in Python](/assets/img/2024-06-30-MultiprocessinginPython_8.png)

# Pool of worker processes with Pool class

The Pool class provides a convenient way to parallelize the execution of a function across multiple input values, distributing the input data across available processes.

When a Pool object is created, it initializes several worker processes. The number of worker processes can be specified with the processes parameter, or it defaults to the number of logical CPU cores returned by os.cpu_count() (or by os.process_cpu_count() since Python 3.13).


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

map, map_async, apply, apply_async, imap, imap_unordered, starmap 및 starmap_async과 같은 메소드들을 사용하여 작업을 풀(pool)에 제출합니다.

풀(pool) 객체는 내부적으로 큐를 사용하여 작업을 메인 프로세스에서 작업 프로세스로 보내고, 작업 프로세스에서 결과를 다시 메인 프로세스로 전송합니다.

일부 예제를 살펴보겠습니다!

## map

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

map() 메소드에는 함수, 반복 가능한 객체 및 청크 크기를 지정하는 정수를 제공할 수 있습니다.

이 메소드는 반복 가능한 객체를 여러 청크로 나누고 이를 워커 프로세스 풀에서 분배합니다.

모든 항목이 처리될 때까지 map은 블록킹됩니다.

첫 번째 예시에서는 4개의 프로세스와 4개의 정수로 된 반복 가능한 객체가 있습니다. 우리의 함수는 각 프로세스에서 실행되며, 반복 가능한 객체의 값들이 프로세스 사이에 분배되고 함수에 인수로 전달됩니다.

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

```python
...

def cpu_bound_task(counts: int) -> None:
    """CPU 바운드 작업을 실행합니다."""
    time = cpu_bound_operation(counts)
    logging.info(f"-------- 프로세스: {os.getpid()} --------")
    logging.info(f"time - {time}\n")
    return [time]


def multiprocessing() -> None:

    args = [50000000, 50000000, 50000000, 50000000]

    # Worker 프로세스에서 실행
    with Pool(processes=4) as pool:
        res = pool.map(cpu_bound_task, args)  # 결과가 준비될 때까지 블록됩니다

        logging.info(res)
    ...
```

각 프로세스는 주어진 함수를 반복 가능한 값 중 하나로 실행합니다.

```python
22:17:24: 병렬 프로그램 시작

22:17:25: -------- 프로세스: 44665 --------
22:17:25: time - (56633.60939425, 56634.651306916)

22:17:25: -------- 프로세스: 44663 --------
22:17:25: time - (56633.612802333, 56634.656112583)

22:17:25: -------- 프로세스: 44666 --------
22:17:25: time - (56633.64181075, 56634.682685541)

22:17:25: -------- 프로세스: 44664 --------
22:17:25: time - (56633.641066125, 56634.707866125)

22:17:25: [[(56633.60939425, 56634.651306916)], [(56633.612802333, 56634.656112583)], [(56633.641066125, 56634.707866125)], [(56633.64181075, 56634.682685541)]]

22:17:26: 병렬 프로그램 종료
```

네 가지 작업이 효과적으로 병렬로 실행됩니다.

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


![Image](/assets/img/2024-06-30-MultiprocessinginPython_9.png)

이제 iterable에 두 가지 값을 더 추가하고 네 개의 워커를 유지하면 두 개의 새 값이 워커 프로세스가 사용 가능해지면 처리되는 것을 볼 수 있습니다.

```python
...

def multiprocessing() -> None:

    args = [50000000, 50000000, 50000000, 50000000, 50000000, 50000000]

    # Worker 프로세스에서 실행
    with Pool(processes=4) as pool:
        res = pool.map(cpu_bound_task, args)  # 결과가 준비될 때까지 블록됨

        logging.info(res)

    ...
```

![Image](/assets/img/2024-06-30-MultiprocessinginPython_10.png)


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

이제 chunksize 매개변수로 놀아봅시다.

24개의 값을 처리할 이터러블을 제출하고 chunksize를 1로 전달하면 위의 것과 동일한 동작을 볼 수 있습니다.

```js
...

def multiprocessing() -> None:

    n_tasks = 24
    args = [50000000] * n_tasks
    chunksize = 1

    # Worker 프로세스에서 실행
    with Pool(processes=4) as pool:
        res = pool.map(
            cpu_bound_task, args, chunksize
        )  # 결과가 준비될 때까지 블록됨

        logging.info(res)

    ...
```

<img src="/assets/img/2024-06-30-MultiprocessinginPython_11.png" />

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

대신에, chunksize 매개변수를 설정하여 처리해야 할 작업의 수를 줄일 수 있습니다. map() 메서드는 이터러블을 여러 청크로 나누어 풀에 별도의 작업으로 제출합니다. 이러한 청크의 (대략적인) 크기는 chunksize를 양수 정수로 설정하여 지정할 수 있습니다.

아래 예시에서는 24개의 요소가 있는 args 이터러블과 chunksize가 2로 설정되어 있습니다. 이는 24개의 작업이 24개가 아닌 12개처럼 전송되며, 각 작업은 이터러블에서 2개의 요소를 가지게 됩니다.

```python
...

def multiprocessing() -> None:

    n_tasks = 24
    args = [50000000] * n_tasks
    chunksize = 1
    chunksize = 2

    # 워커 프로세스에서 실행
    ...
```

![그림](/assets/img/2024-06-30-MultiprocessinginPython_12.png)

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

그리고 chunksize가 3으로 설정하면 다음과 같습니다:

```js
...

def multiprocessing() -> None:

    n_tasks = 24
    args = [50000000] * n_tasks
    chunksize = 2
    chunksize = 3

    # 작업 프로세스에서 실행
    ...
```

![이미지](/assets/img/2024-06-30-MultiprocessinginPython_13.png)

## map_async

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

map_async()은 AsyncResult 객체를 반환하며 스레드를 차단하지 않습니다. 결과를 기다리거나 준비되었는지 확인하려면 get(), wait() 또는 ready()와 같은 AsyncResult 메소드를 사용할 수 있습니다. get() 및 wait()는 시간 초과 인자를 받습니다.

```python
...

def multiprocessing() -> None:

    n_tasks = 24
    args = [50000000] * n_tasks

    # worker 프로세스에서 실행
    with Pool(processes=4) as pool:
        res = pool.map_async(cpu_bound_task, args, 1)  # 차단되지 않음

        logging.info(f"res.ready(): {res.ready()}")
        logging.info("waiting...")
        res.wait()  # 결과가 준비될 때까지 차단
        logging.info(f"res.ready(): {res.ready()}")
        res = res.get()

    ...
```

```python
20:05:53: 병렬 프로그램 시작

20:05:53: res.ready(): False
20:05:53: 대기 중...

20:05:54: -------- 프로세스: 35723 --------
20:05:54: 시간 - (151823.566433833, 151824.614282083)

...

20:06:00: res.ready(): True

20:06:00: 병렬 프로그램 완료
```

![이미지](/assets/img/2024-06-30-MultiprocessinginPython_14.png)

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

multiprocessing.Pool 및 multiprocessing.AsyncResult 문서에서 apply_async와 기타 유용한 메서드를 찾을 수 있어요.

apply() 메서드는 제공한 인수로 함수를 호출하고 해당 함수가 완료될 때까지 블록되어 결과를 반환해요. 직접 함수 호출과 유사하지만 풀 내 작업자 프로세스에서 실행됩니다. apply_async()는 AsyncResult 객체를 반환하며 스레드를 블록하지 않아요.

아래에는 이 주제를 더 잘 이해하는 데 도움이 된 몇 가지 리소스가 있어요.

어떤 소셜 네트워크에서든 연락해도 괜찮아요. 피드백을 주시면 감사하겠어요!

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

읽어 주셔서 감사합니다 💛

javideveloper.com

# 기타 참고 자료

- multiprocessing — 프로세스 기반 병렬 처리
- multiprocessing 모듈 소스 코드
- concurrency repo | javiicc
- 좀비 프로세스
- 고아 프로세스
- Python Multiprocessing: 완전 가이드 | SuperFastPython
- Copy on Write | GeeksforGeeks
- 병행성과 병렬성 소개
- Python에서의 스레딩