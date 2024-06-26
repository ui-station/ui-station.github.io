---
title: "신호, 셸 및 도커 발을 쏘는 축적체"
description: ""
coverImage: "/assets/img/2024-05-23-Signalsshellsanddockeranonionoffootguns_0.png"
date: 2024-05-23 15:06
ogImage:
  url: /assets/img/2024-05-23-Signalsshellsanddockeranonionoffootguns_0.png
tag: Tech
originalTitle: "Signals, shells, and docker: an onion of footguns"
link: "https://medium.com/benchling-engineering/signals-shells-and-docker-an-onion-of-footguns-ee592e2b587b"
---

가끔은 POSIX 시그널(SIGINT, SIGTERM 등)을 디버깅해야 할 때가 있었습니다. 불가피하게 쉘도 포함되어 있죠. 어느 날, 시그널, 쉘 및 컨테이너 간 이상한 상호 작용을 디버깅하던 중에 일부 행동에 혼란스러워졌습니다. Linux에 대해 자신 있다고 생각하는 사람들조차도 우리 조사 결과의 일부 내용이 놀라울 정도로 신기할 것이라 생각됩니다. 이런 종류의 내용이 여러분이 노트북을 창문 밖으로 던지고 알파카 농부 은둔자가 되고 싶지 않는다면 계속 읽어보세요.

![이미지](/assets/img/2024-05-23-Signalsshellsanddockeranonionoffootguns_0.png)

# 범행 현장

저희 Benchling에서는 꽤 표준적인 테스트/지속적 통합(CI) 설정이 있습니다: 코드를 푸시하여 풀 리퀘스트 브랜치에 올리면 테스트를 실행합니다. 몇 년 전, 푸시를 다시 하고 이전 커밋에서 여전히 테스트가 실행 중이면 이전 테스트 실행을 취소하는 최적화를 추가했습니다. 아마 여러분은 그 실행에 대해 신경 쓸 필요가 없을 것이고 약간의 비용을 절약할 것입니다... 아니면 그렇게 될까요?

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

저희 테스트를 실행하는 코드는 기본적으로 이렇습니다

```js
def test_pipeline() -> int:
    test_result = subprocess.run(["pytest", …])
    report_test_metrics()
    upload_artifacts()
    return test_result.returncode
```

그래서 우리의 프로세스 트리는 다음과 같습니다

```js
test_pipeline
└──pytest
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

subprocess.run은 자식 프로세스가 종료될 때까지 블로킹되므로 대부분의 시간이 걸릴 것입니다. CI 로그에서 테스트가 중간에 중단되고 더 이상 로그가 표시되지 않는 것을 보았는데, 확실히 작동 중인 것처럼 보입니다. 하지만 우리는 취소된 실행에 대한 메트릭스와 아티팩트를 얻을 수 있습니다. 이것은 전혀 이치에 맞지 않습니다. 나중에 우리는 실행이 취소되었고 로그 전달이 중지되었음을 보고했지만 pytest가 계속 실행되고 있음을 나준 낼 것입니다.

# 기초로 돌아가기

test_pipeline에서 pytest로 신호를 전달하지 않는 문제일 수도 있다고 생각하여 우리는 먼저 기본 신호 처리에 대해 고려했습니다. zsh를 실행 중인 터미널에서 zsh의 pid를 다음과 같이 얻을 수 있습니다.

```bash
$ echo $$
20147
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

그러면 zsh 안에서 bash를 실행하고 (테스트와 같이 매우 느린 명령어) bash 안에서 infinity로 sleep할 수 있어요.

```js
$ bash
$ sleep infinity
```

다른 쉘에서는 프로세스 트리를 확인할 수 있어요.

```js
$ pstree -p 20147
zsh(20147)───bash(65453)───sleep(65904)
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

(pstree은 Debian/Ubuntu에서는 psmisc 패키지에 포함되어 있고, brew에는 pstree 공식이 있습니다.) 여기서는 기대한 대로 zsh가 bash를 실행하고 bash가 sleep를 실행하는 것이 나타납니다. 이제 우리가 ctrl+c로 SIGINT를 보내면 sleep가 멈춥니다.

이게 왜 그럴까요? 터미널은 ctrl+c를 "SIGINT를 전송"으로 해석합니다. zsh가 SIGINT를 받아서 기본 셸 프로세스인 bash에 전달하고, bash가 이 신호를 sleep에 전달합니다. sleep는 SIGINT에 대한 자체 시그널 핸들러를 설정하지 않았고, 기본 시그널 핸들러가 종료합니다 (SIGINT에는 "term" 처리 방식이 있습니다).

조사 시작 시에는 이것이 셸 신호 처리에 대한 우리의 개념 모델이었습니다.

# 대화형이 아닌 셸

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

실제 문제가 발생한 것은 셸 스크립트가 bash로 실행될 때 나타났습니다 (위의 파이썬 코드를 bash 스크립트에서 실행합니다).

```js
bash
  └─test_pipeline
      └─pytest
```

대화식 셸(다른 차이 사항 중 stdin을 읽는)이 비대화식 셸이나 "스크립트"와 다르게 작동할 수 있다고 생각하여, 파일에 2줄을 작성했습니다.

```js
sleep infinity
echo done
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

돌렸다

```js
$ ./test.sh
```

다른 쉘에서는 똑같은 프로세스 트리를 볼 수 있었어요

```js
$ pstree -p 20147
zsh(20147)───bash(65910)───sleep(65911)
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

그런 다음, 우리는 직접 bash에게 신호를 보냈습니다.

```js
$ kill -s INT 65910
```

하지만 아무 일도 일어나지 않았습니다. bash 문서(man bash)에 숨겨진 "신호(signals)" 섹션에는

대화식 쉘에 대해서 기본적으로 작업 제어가 켜져 있고, 스크립트에 대해서는 꺼져 있다는 내용이 있습니다(“모니터 모드”에 대한 문서 참조). 그래서 아무 일도 일어나지 않았던 이유는 bash가 sleep(전경에 있는 명령)가 종료되기를 기다리고 있었기 때문입니다.

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

하지만 여기에 프로세스 그룹에 관한 힌트도 있습니다. `pstree` 명령을 사용하면 이를 확인할 수 있습니다 (macOS에서는 작동하지 않음):

```js
$ pstree -pg 20147
zsh(20147,20147)───bash(65910,65910)───sleep(65911,65910)
```

여기서 볼 수 있듯이, 대화식 zsh에서 실행한 bash가 자체 프로세스 그룹을 갖고 있다는 것을 알 수 있습니다. 반면, 비대화식 bash에서 실행한 sleep는 bash와 동일한 pgid를 공유합니다. 그룹 내의 두 프로세스에 대해 신호를 보내려면 pid를 부정화하면 됩니다:

```js
$ kill -s INT -65910
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

이렇게 하면 sleep이 SIGINT를 수신하여 종료됩니다. bash도 SIGINT를 받아서 문서에 나와 있는 대로 자체적으로 종료됩니다. 상호작용식 zsh로 돌아와서 다음을 실행할 수 있습니다.

```js
$ sleep infinity
```

그리고 sleep이 예상대로 자체 pgid를 받는 것을 확인할 수 있습니다.

```js
$ pstree -p 20147
zsh(20147,20147)───sleep(65916,65916)
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

# 비 대화형 셸에서의 마지막 명령어

지금은 종종 셸이 자식 프로세스로 신호를 전달하지 않을 수 있다는 것을 알게 되었습니다. 어떤 사람이 한 번 bash -c `sleep infinity`를 실행하여 이것을 재현하려 했습니다. 그들은 ctrl+c를 눌러 sleep를 중지시킬 수 있었습니다. 그러나 이는 비 대화형 셸이므로 bash가 SIGINT를 전달해서는 안 되는 것 아닌가요? 왜 그럴까요?

```js
$ bash -c ‘sleep infinity’
```

보통의 경우, 다른 쉘에서:

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
$ pstree -p 20147
zsh(20147)───sleep(65920)
```

어이쿠, bash가 어디로 갔지? 우리는 bash를 실행했는데요! 왜 pstree가 zsh가 sleep을 실행 중이라고 하는 걸까요?

프로그램을 "실행"할 때 일반적으로 하는 것은 fork한 후에 exec하는 것을 의미합니다. fork는 새로운 프로세스의 부모 pid를 설정하여 나중에 pstree와 같은 도구가 이쁜 트리를 그릴 수 있도록 합니다. exec는 새로운 프로세스의 명령을 설정하여 pstree와 같은 도구가 해당 pid가 무슨 일을 하는지 유의미한 정보를 보여줄 수 있도록 합니다.

하지만 이곳에서 일어난 일은 bash가 단순히 sleep을 exec해주기 전에 fork하는 것을 건너뛴 것입니다. 이 동작에 대한 문서를 찾을 수 없어서, 대신 해드릴 수 있는 것은 ash 소스 코드입니다:

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
/* 포크를 피할 수 있을까요? 예를 들어, 스크립트나 서브셸에서
 * 가장 마지막 명령은 포킹이 필요 없습니다,
 * 그냥 실행(exec)할 수 있습니다.
 */
```

바쉬가 자신을 sleep으로 교체하고 pstree에 보여지는 것은, 이제 sleep을 실행 중인 것의 부모가 zsh 임을 보여줍니다. 대신 `bash -c 'sleep infinity && done'`을 실행함으로써 이전 동작을 얻을 수 있습니다.

실제로 우리는 bash 스크립트를 sh -c로 실행하기 때문에, 우리의 정신적 모델은 다음과 같습니다.

```js
sh
└─bash
    └─test_pipeline
        └─pytest
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

잠깐만요! 나무 구조에서 'sh'가 자체 PID가 아니라는 것을 깨달을 때까지는...

# sh, bash, dash, 그리고 ash에 관한 간단한 쉬는 시간

아쉬가 뭐죠? 제가 보낸 것은 무관한 코드 링크인가요? (네, 어느 정도 맞아요. bash와 동일한 동작을 하지만 소스 코드는 조금 덜...추상화되어 있죠.)

sh는 Bourne 쉘(보통 "POSIX sh"로 불립니다.)이며, Bash는 Bourne 쉘의 확장 버전입니다. 역사적으로는 많은 시스템에서 sh를 bash에 연결한 적이 있었습니다. bash는 argv[0]을 확인하고 sh 호환 모드에서 실행했죠. 현대의 리눅스 시스템에서는 sh가 일반적으로 dash로 대체되었지만 macOS에서는 여전히 sh 모드에서 bash를 사용합니다.

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

1989년 NetBSD를 위해 작성된 Almquist 셸이 원래 ash였습니다. 이것은 Linux로 이식되어 dash(Debian Almquist 셸)로 이름이 변경되었습니다. 요즘에는 "ash"가 일반적으로 busybox ash를 가리키는데, 이는 dash의 파생물입니다. 네, 맞아요. 계보는 ash → dash → ash입니다. 쉘 프로그래머들은 명명하는 데에 뛰어난 능력이 없다고 할 수 있겠네요.

그런데 sh 호환 모드에서 bash와 ash는 모두 이전 섹션에서 설명한 exec-without-fork 동작을 구현하지만, dash는 그렇지 않습니다. 또한 Docker Hub의 공식 bash 이미지에서 sh를 실행하려고 하면 (docker run -it --rm bash sh), sh 호환 모드의 bash를 기대했겠지만 ash가 실행됩니다 (ash와 혼동해서는 안 되는 것입니다).

# 플로우차트

여기가 바로 쉘 신호 처리의 껍질을 벗기기 전에 있었으면 좋았을 플로우차트입니다.

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

![Image](/assets/img/2024-05-23-Signalsshellsanddockeranonionoffootguns_1.png)

# 범죄 현장으로 돌아가기

우리는 편리한 플로우차트를 가지고, ci-agent의 코드를 읽기 위해 이동했고, 빌드가 취소되면 실행 중인 작업에 SIGTERM을 보낸다는 것을 발견했습니다.

```js
ci-agent
    └─bash
        └─test_pipeline
            └─pytest
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

"배시가 비대화식으로 실행되었고, test_pipeline이 마지막 명령어가 아니어서 어 anyway 신호가 전달되지 않았습니다. 그게 무슨 일이 발생한 걸 설명해주나요?

우리는 bash를 exec test_pipeline.py로 만들어 트리에서 제거하려 했지만, 문제가 해결되지 않았습니다. 그것은 우리의 프로세스 트리가 여전히 잘못되어 있다는 것을 의미할 것입니다.

# 컨테이너

ci-agent는 사실상 도커에게 스크립트를 실행하라고 지시하는 것 뿐입니다."

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

ci-agent
└─docker
└─bash
└─test_pipeline
└─pytest

도커가 신호를 베쉬에게 전달하나요? 도커는 각 컨테이너마다 새로운 pid 네임스페이스를 생성하기 때문에 실행되는 명령이 pid 1이 됩니다. 1은 매우 특별한 pid입니다 (일반적으로 init 프로세스) 그리고 기본 신호 처리기를 가져오지 않습니다. 이 문제를 해결하기 위해 tini 또는 dumb-init을 pid 1로 사용하는 것이 일반적입니다.

이미지를 조사한 결과 이미 우리는 dumb-init을 사용하고 있었다는 것이 밝혀졌습니다. 그래서 다음과 같은 트리가 남았습니다.

ci-agent
└─docker
└─dumb-init
└─bash
└─test_pipeline
└─pytest

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

문제에 대한 설명이 없네요.

# 이것이 마지막 트리에요, 정말

사실, 우리는 도커 컨테이너를 직접 실행하지 않아요. 대신 도컴 포즈로 실행해요.

```js
ci-agent
    └─docker compose
        └─docker
            └─dumb-init
                └─bash
                    └─test_pipeline
                        └─pytest
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

이 트리를 마친 후 우리는 문제를 재현할 수 있었습니다. 문제는 docker-compose v2.0.0에서 v2.19.0 사이의 버전에서 발생합니다. 여기서 docker-compose run이 시그널을 전달하지 못하는 문제가 발생합니다. 우리가 이 문제를 보고한 후에 이 문제가 해결되었습니다.

이 버그는 docker-compose(v1)에서 docker compose(v2)로 업그레이드할 때 발생했습니다. 이 문제를 이해하는 데는 하이픈(hyphen)이 빠져있는 것을 발견하는 것이 필요했습니다. 이 누락된 하이픈을 발견하는 것은 중요했지만, 두 버전은 거의 동일한 매개변수를 가지고 있고, 거의 동일한 동작을 하기 때문에 발견하기 어려웠습니다. 이 이야기를 읽으면서 얻는 교훈 중 하나는, 이름을 짓는 것은 어렵지만 중요하다는 것입니다. 만약 "하이픈(-)을 공백으로 바꿔서 Compose V2를 사용하도록 스크립트를 업데이트하세요"와 같은 문서를 작성하게 되면, 아마도 중요한 명명 오류를 범한 것입니다.

이 문제를 디버깅하는 데 어려웠던 점 중에 하나는 전체 소유권 체인을 이해해야 했기 때문입니다. 시그널은 각 프로세스가 자식에게 전달해야 합니다. pytest가 시그널을 받지 못한 이유를 이해하려면 전파 체인이 깨진 지점까지 트리를 구성해야 했는데, 이 경우에는 상당히 멀리 있었습니다.

우리는 docker compose v1로 다시 다운그레이드하는 것을 고려했지만, 대신 CI 단계에서 실행된 컨테이너를 추적하고 끝에 도달하면 docker kill하는 방법을 선택했습니다. 나중에 상류에서 이 문제를 해결한 후, 우리의 완화책이 실제로 활성화되지 않았습니다. 문제가 해결된 후에는 이제 CI 실행이 다시 원하는 대로 중단됩니다. PR 브랜치로 여러 번 빠르게 푸시되면 이전 커밋을 실행하여 사이클을 낭비하지 않게 되어 전체 실행이 더 빨라졌습니다! (취소된 실행에 대한 측정 값을 더 이상 보고하지 않기 때문에 불안한 테스트를 식별하는 데 큰 도움이 됩니다.)

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

# 프론트그라운드 프로세스 보너스

"비대화된 쉘" 섹션에서 우리는 다음과 같은 프로세스 트리를 가졌습니다.

```js
zsh(20147)───bash(65910)───sleep(65911)
```

직접적으로 bash에 시그널을 보내는 방법을 다루었습니다.

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

```sh
$ kill -s INT -65910
```

zsh에 직접 신호를 보내지 않았던 이유가 뭔가요? zsh가 상호작용으로 실행 중이기 때문에 SIGINT를 bash로 전달해야 되지 않나요? 다음을 시도해 볼 수 있습니다.

```sh
$ kill -s INT -20147
```

하지만 아무 일도 일어나지 않아요.

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

현 상황에서 ctrl+c를 누르면 터미널이 zsh가 아니라 bash에 SIGINT를 보낸다는 걸 알게 되었어요. 이것은 zsh가 더 이상 전경 프로세스 그룹에 속해 있지 않기 때문입니다. 이것을 확인하기 위해 다음과 같이 실행해 볼 수 있어요.

```js
$ ps -xO stat
   PID STAT S TTY          TIME COMMAND
 20147 Ss   S pts/0    00:00:00 zsh
 65910 S+   S pts/0    00:00:00 bash
 65911 S+   S pts/0    00:00:00 sleep
```

man ps의 "프로세스 상태 코드" 섹션에 의하면

bash와 sleep은 보이는데 zsh는 보이지 않네요. 그들이 동시에 전경 프로세스 그룹에 있을 수 없기 때문에, zsh가 상호작용적으로 실행 중이기 때문에 zsh는 bash에 자체 프로세스 그룹을 설정했어요. 그래서 "zsh가 SIGINT를 받아서 bash로 전달한다"고 말했지만, 실제로는 그게 사실이 아니었던 거예요.

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

하지만 bash의 프로세스 그룹이 왜 전경 그룹인가요? tcsetpgrp. 이것은 ltrace로 확인할 수 있습니다:

```js
$ ltrace -e tcsetpgrp bash
bash->tcsetpgrp(255, 0xa9850, 0, 0x7f290bdb2fe4) = 0
```

그리고 bash가 종료되면 부모 쉘(zsh에서 확인됨)이 동일한 호출로 전경 상태를 다시 획득합니다.
