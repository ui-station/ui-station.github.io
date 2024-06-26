---
title: "쿠버네티스와 커널 패닉"
description: ""
coverImage: "/assets/img/2024-05-18-KubernetesAndKernelPanics_0.png"
date: 2024-05-18 17:35
ogImage:
  url: /assets/img/2024-05-18-KubernetesAndKernelPanics_0.png
tag: Tech
originalTitle: "Kubernetes And Kernel Panics"
link: "https://medium.com/netflix-techblog/kubernetes-and-kernel-panics-ed620b9c6225"
---

넷플릭스의 컨테이너 플랫폼이 리눅스 커널 패닉을 쿠버네티스 파드로 연결하는 방법

카일 앤더슨

최근에는 컨테이너 플랫폼 Titus에서 엔지니어(최종 사용자가 아닌)의 고통을 줄이기 위한 노력이 있었습니다. 나는 "고아" 파드를 조사하기 시작했습니다. 완료되지 못한 파드들로, 만족스럽지 못한 최종 상태로 가비지 컬렉션되어야 했습니다. 우리의 서비스 작업(ReplicatSet같이 생각하십시오) 소유자들은 그리 심하게 신경쓰지 않지만, 우리의 배치 사용자들은 신경을 많이 씁니다. 실제로 반환 코드가 없다면, 다시 시도해도 안전한지 아닌지 어떻게 알 수 있을까요?

이러한 고아 파드들은 시스템 전체 파드 중 소수지만 사용자들에게 진짜 고통을 미칩니다. 정확히 어디로 가는 것인가요? 왜 사라졌나요?

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

이 블로그 포스트는 최악의 시나리오인 커널 패닉부터 Kubernetes(k8s)까지 연결하여 마침내 우리 운영자들이 우리의 k8s 노드가 왜 어떻게 사라지는지 추적할 수 있도록 안내하는 방법을 보여줍니다.

# 유랑된 Pod들은 어디에서 왔을까요?

유랑된 Pod들은 기본 k8s 노드 객체가 사라지기 때문에 분실됩니다. 그렇게 되면 GC 프로세스가 Pod를 삭제합니다. Titus에서는 Pod 및 Node 객체의 이력을 저장해 사용자들에게 설명을 제공할 수 있도록하는 사용자 지정 컨트롤러를 실행합니다. 우리의 UI에서 이러한 실패 모드는 다음과 같이 보입니다:

![이미지](/assets/img/2024-05-18-KubernetesAndKernelPanics_0.png)

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

이 설명은 설득력이 부족했나봐요. 에이전트가 어디로 사라졌는지 궁금하죠?

# 잃어버린 노드는 어디서 왔을까요?

노드는 어떤 이유로든 사라질 수 있어요, 특히 "클라우드"에서 말이죠. 그럴 때는 일반적으로 클라우드 벤더에 의해 제공되는 k8s 클라우드 컨트롤러가 실제 서버, 이 경우에는 EC2 인스턴스,가 실제로 없어졌다는 것을 감지하고, 이에 따라 k8s 노드 객체를 삭제할 거에요. 그래도 "왜"라는 질문에 정말로 대답하지 못하겠죠.

우리는 어떻게 해야 할까요? 모든 사라진 인스턴스에 이유를 부여하고, 그 이유를 감안하며, 이것을 포드까지 전달할 수 있을까요? 이 모든 것은 주석에서 시작돼요:

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
{
     "apiVersion": "v1",
     "kind": "Pod",
     "metadata": {
          "annotations": {
               "pod.titus.netflix.com/pod-termination-reason": "Something really bad happened!",
...
```

이 데이터를 넣을 수 있는 장소를 만드는 것만으로도 좋은 시작이에요. 이제 우리의 GC 컨트롤러들이 이 주석을 인식하도록 만들고, 예기치 못하게 파드나 노드를 종료시킬 수 있는 어떠한 프로세스에도 이를 뿌리는 것만 하면 돼요. 상태를 수선하는 대신 주석을 추가함으로써 기록 목적을 위해 파드의 나머지 부분을 그대로 유지할 수 있어요. (종료 이유를 나타내기 위해 종료한 것에 대한 주석을 추가합니다)

pod-termination-reason 주석은 다음과 같은 인간이 읽을 수 있는 메시지를 작성하는 데 유용해요:

- “이 파드는 더 높은 우선 순위 작업($id)에 의해 예약 해제되었습니다”
- “이 파드는 하부 하드웨어의 장애($failuretype)로 종료되어야 했습니다”
- “$user가 노드에서 sudo halt 명령을 실행했기 때문에 이 파드를 종료해야 했습니다”
- “하부 노드 커널이 패닉 상태에 빠져 이 파드가 예기치 않게 종료되었습니다!”

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

하지만 기다려봐, 커널 패닉에 빠진 노드에 대한 팟을 어떻게 주석 처리할 수 있을까요?

# 커널 패닉 캡처

리눅스 커널이 패닉에 빠지면 할 일이 별로 없어요. 하지만 만약 "마지막 숨통으로 커버네티스에 저주를 내립니다!"라는 UDP 패킷을 보낼 수 있다면 어떨까요?

Google Spanner 논문에서 영감을 받아 Spanner 노드가 리스 및 락을 해제하기 위해 "마지막 끼통" UDP 패킷을 보내는 것처럼, 당신도 주식 리눅스 모듈인 netconsole을 사용하여 커널 패닉 시 서버를 설정할 수 있어요.

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

# 넷콘솔 설정

리눅스 커널이 심각한 상황에서 UDP 패킷을 '커널 패닉' 문자열과 함께 전송할 수 있다는 사실은 정말 놀랍죠. 이는 netconsole이 사전에 거의 모든 IP 헤더가 채워진 상태로 구성되어 있어야 한다는 것 때문에 가능합니다. 맞아요, Linux에게 소스 MAC, IP, UDP 포트 및 대상 MAC, IP, UDP 포트를 정확히 알려주어야 합니다. 특히, 커널을 위한 UDP 패킷을 거의 만들어 주는 것과 같죠. 그러나 그 전 작업을 해두면 시간이 지나면서 커널이 쉽게 패킷을 구성하고 (미리 구성된) 네트워크 인터페이스를 통해 전송할 수 있습니다. 다행히 netconsole-setup 명령어를 통해 설정을 간단히 할 수 있습니다. 모든 구성 옵션은 동적으로 설정할 수 있어서, 대상이 변경되면 새 IP로 바로 가리킬 수 있습니다.

이 설정을 하면 모듈 로딩 후 커널 메시지가 즉시 흐르기 시작합니다. 마치 dmesg | netcat -u $대상 6666와 같이 전체 프로세스가 커널 공간에서 작동하는 것을 상상해보세요.

# 넷콘솔 "마지막 발악" 패킷

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

netconsole을 설정하면, 충돌하는 커널로 인한 마지막 노후는 예상대로 UDP 패킷 세트처럼 보입니다. UDP 패킷의 데이터는 간단히 커널 메시지의 텍스트입니다. 커널 패닉인 경우, 다음과 같이 보일 것입니다 (한 줄에 하나의 UDP 패킷):

```js
Kernel panic - not syncing: buffer overrun at 0x4ba4c73e73acce54
[ 8374.456345] CPU: 1 PID: 139616 Comm: insmod Kdump: loaded Tainted: G OE
[ 8374.458506] Hardware name: Amazon EC2 r5.2xlarge/, BIOS 1.0 10/16/2017
[ 8374.555629] Call Trace:
[ 8374.556147] <TASK>
[ 8374.556601] dump_stack_lvl+0x45/0x5b
[ 8374.557361] panic+0x103/0x2db
[ 8374.558166] ? __cond_resched+0x15/0x20
[ 8374.559019] ? do_init_module+0x22/0x20a
[ 8374.655123] ? 0xffffffffc0f56000
[ 8374.655810] init_module+0x11/0x1000 [kpanic]
[ 8374.656939] do_one_initcall+0x41/0x1e0
[ 8374.657724] ? __cond_resched+0x15/0x20
[ 8374.658505] ? kmem_cache_alloc_trace+0x3d/0x3c0
[ 8374.754906] do_init_module+0x4b/0x20a
[ 8374.755703] load_module+0x2a7a/0x3030
[ 8374.756557] ? __do_sys_finit_module+0xaa/0x110
[ 8374.757480] __do_sys_finit_module+0xaa/0x110
[ 8374.758537] do_syscall_64+0x3a/0xc0
[ 8374.759331] entry_SYSCALL_64_after_hwframe+0x62/0xcc
[ 8374.855671] RIP: 0033:0x7f2869e8ee69
...
```

# 쿠버네티스에 접속

마지막 단계는 쿠버네티스(k8s)에 연결하는 것입니다. 다음을 수행하는 k8s 컨트롤러가 필요합니다:

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

- 포트 6666에서 netconsole UDP 패킷을 수신하여 노드에서 발생하는 커널 패닉처럼 보이는 것을 감시합니다.
- 커널 패닉 발생 시, 들어오는 netconsole 패킷의 IP 주소와 관련된 k8s 노드 객체를 찾습니다.
- 해당 k8s 노드에서 바인딩된 모든 팟을 찾아서 주석을 추가한 후 해당 팟을 삭제합니다 (팟이 다 녹아버리겠군요!).
- 해당 k8s 노드에 노드 주석을 추가한 다음 노드도 삭제합니다 (노드도 다 녹아버리겠군요!).

1부 및 2부는 다음과 같이 보일 수 있습니다:

```js
for {
    n, addr, err := serverConn.ReadFromUDP(buf)
    if err != nil {
        klog.Errorf("Error ReadFromUDP: %s", err)
    } else {
        line := santizeNetConsoleBuffer(buf[0:n])
        if isKernelPanic(line) {
            panicCounter = 20
            go handleKernelPanicOnNode(ctx, addr, nodeInformer, podInformer, kubeClient, line)
        }
    }
    if panicCounter > 0 {
        klog.Infof("KernelPanic context from %s: %s", addr.IP, line)
        panicCounter++
    }
}
```

그리고 3부와 4부는 다음과 같이 보일 수 있습니다:

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
func handleKernelPanicOnNode(ctx context.Context, addr *net.UDPAddr, nodeInformer cache.SharedIndexInformer, podInformer cache.SharedIndexInformer, kubeClient kubernetes.Interface, line string) {
    node := getNodeFromAddr(addr.IP.String(), nodeInformer)
    if node == nil {
        klog.Errorf("Got a kernel panic from %s, but couldn't find a k8s node object for it?", addr.IP.String())
    } else {
        pods := getPodsFromNode(node, podInformer)
        klog.Infof("Got a kernel panic from node %s, annotating and deleting all %d pods and that node.", node.Name, len(pods))
        annotateAndDeletePodsWithReason(ctx, kubeClient, pods, line)
        err := deleteNode(ctx, kubeClient, node.Name)
        if err != nil {
            klog.Errorf("Error deleting node %s: %s", node.Name, err)
        } else {
            klog.Infof("Deleted panicked node %s", node.Name)
        }
    }
}
```

위 코드를 넣으면 커널 패닉이 감지되자마자 pod와 노드가 즉시 사라집니다. 어떤 GC 프로세스를 기다릴 필요가 없습니다. 주석은 노드 및 pod에 무슨 일이 일어났는지 문서화하는 데 도움이 됩니다:

<img src="/assets/img/2024-05-18-KubernetesAndKernelPanics_1.png" />

# 결론

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

작업이 커널 패닉 때문에 실패했다는 것을 고객님에게 알리는 것은 만족스럽지 않을 수도 있습니다. 그러나 커널 패닉을 해결하기 위해 필요한 관측 도구를 갖추게 된 사실에 기쁨을 느끼실 수 있을 거예요!

시스템에서 왜 실패하는 지 정말로 깊이 파악하는 것이 즐거우셨나요? 혹은 커널 패닉이 멋지게 느껴지시나요? 저희 Compute 팀에 참여해 주세요. 저희는 엔지니어들을 위한 세계적 수준의 컨테이너 플랫폼을 구축 중이에요.
