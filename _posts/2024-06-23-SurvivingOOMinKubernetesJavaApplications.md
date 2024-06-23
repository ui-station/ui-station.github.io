---
title: "Kubernetes에서 OOM 생존 가이드 Java 애플리케이션을 위한 팁"
description: ""
coverImage: "/assets/img/2024-06-23-SurvivingOOMinKubernetesJavaApplications_0.png"
date: 2024-06-23 20:35
ogImage: 
  url: /assets/img/2024-06-23-SurvivingOOMinKubernetesJavaApplications_0.png
tag: Tech
originalTitle: "Surviving OOM in Kubernetes: Java Applications"
link: "https://medium.com/@yonahdissen/surviving-oom-in-kubernetes-java-applications-fd1fb1a65f02"
---



![Surviving OOM in Kubernetes Java Applications](/assets/img/2024-06-23-SurvivingOOMinKubernetesJavaApplications_0.png)

자바 애플리케이션의 세계에서 OutOfMemoryError (OOM) 문제는 할당된 메모리를 소진할 때 발생하는 과제입니다. 기존의 표준 자바 설정에서 OOM을 다룰 때 일반적으로 힙 덤프를 트리거링함으로써 처리합니다. 이는 특정 시점의 응용 프로그램 메모리 스냅샷을 제공하여 개발자가 애플리케이션의 메모리 사용 상태에 대한 통찰력을 제공하는 진단 도구입니다.

Kubernetes에서 Java 앱이 OOM을 경험할 때 플랫폼은 원활한 작동을 유지하기 위해 다시 시작됩니다. OOM이 완전한 종료로 이어지는 기존 설정과는 달리 Kubernetes는 자동으로 다시 기동하여 지속적 가용성을 보장합니다. 이 자동 재시작 기능은 힙 덤프를 캡처하려고 할 때 일련의 복잡성을 추가하며, 힙 덤프를 포드가 삭제되기 전에 잡을 잡아야 합니다.

# 접근 방법 1:


<div class="content-ad"></div>

OOM이 발생하기 전에 힙 덤프를 트리거하세요.

Prometheus 메트릭을 사용하는 방법 중 하나입니다.

jvm 메모리의 90%와 같은 임계값을 선택하고 해당 임계값이 초과되면 웹훅이나 Robusta와 같은 도구를 사용하여 힙 덤프를 트리거할 수 있습니다.

단점:

<div class="content-ad"></div>

- 외부 웹훅을 개발하거나 클러스터에 Robusta를 설치해야 합니다.
- 애플리케이션에 Jmap이 있거나 일시적 컨테이너를 허용해야 합니다. 이 두 가지는 프로덕션에 일반적이지 않습니다.

## 접근 방식 2:

팟이 OOM을 받을 때 Heap 덤프를 생성합니다.

하지만 기다려주세요. 만약 JVM의 최대 메모리에 도달하면, 팟은 다시 시작되는 것이 아니었나요?

<div class="content-ad"></div>

그곳에 일부 JVM 매개변수가 유용하게 사용됩니다. 다음 JAVA_OPTS를 설정하여 킬 신호가 전송될 때 힙 덤프를 생성할 수 있습니다.

```js
JAVA_OPTS = "XX:+HeapDumpOnOutOfMemoryError"
```

좋아요! 이제 파드 내에 생성된 힙 덤프가 있습니다. 하지만 이것은 파드가 다시 시작될 때 삭제되기 때문에 우리에게 큰 도움이 되지 않습니다.

![이미지](/assets/img/2024-06-23-SurvivingOOMinKubernetesJavaApplications_1.png)

<div class="content-ad"></div>

나중에 분석할 힙 덤프를 영구 저장하기 위해 몇 가지 옵션을 살펴볼 거에요.

## 사이드카:

Java 어플리케이션을 포함하는 각각의 파드에는 해당 컨테이너들 사이에서 볼륨을 공유하는 사이드카가 있을 수 있어요. 사이드카의 역할은 힙 덤프를 가져와 우리가 힙 덤프를 영구로 저장하길 원하는 곳에 업로드하는 거에요.

JAVA_OPTS에 우리의 경우, 이런 식으로 힙 덤프를 공유된 볼륨에 넣는 설정을 추가할 거에요:

<div class="content-ad"></div>

```js
-XX:HeapDumpPath=/etc/shared-path/${pod_name}.hprof"
```

아키텍처는 이렇게 보일 것입니다:

<img src="/assets/img/2024-06-23-SurvivingOOMinKubernetesJavaApplications_2.png" />

단점:

<div class="content-ad"></div>

- 코드 유지보수 - 이제 모든 파드가 사이드카를 가져야 하며 2개의 컨테이너 사이에 마운트가 필요합니다(이를 Helm 라이브러리를 사용하여 수행할 수 있습니다).
- CPU 및 메모리 사용량 - 각각의 사이드카는 비록 휴면 상태일지라도 약간의 풋프린트를 갖고 있으며, 많은 파드가 실행 중일 때 이는 빠르게 누적될 수 있습니다.

이러한 2가지 단점으로 인해 다른 접근 방식을 취할 필요가 있습니다.

## 네트워크 볼륨(예: NFS):

단일 NFS 볼륨을 생성하고 모든 클러스터 노드에 마운트할 수 있습니다. 이를 통해 관련 파드를 호스트 경로에 마운트하고 힙 덤프를 이 경로로 직접 지정할 수 있습니다.

<div class="content-ad"></div>

```js
-XX:HeapDumpPath=/etc/efs-mount/${pod_name}.hprof"
```

이제 남은 것은 지속적으로 실행되고 NFS 볼륨을 모니터링하며 이를 대상지(예: S3 버킷)로 복사할 별도의 pod입니다.

이제 아키텍처는 다음과 같이 보일 것입니다:

![아키텍처](/assets/img/2024-06-23-SurvivingOOMinKubernetesJavaApplications_3.png)


<div class="content-ad"></div>

모니터링 팟은 다음과 같이 작은 파이썬 앱일 수 있습니다:

```python
import boto3
import os
import logging
import time

DUMP_FOLDER = '/etc/efs-mount'
BUCKET_PATH = 'heap-dumps'
BUCKET_NAME = 'my-app-heap-dumps'

def main():
    files_list = os.listdir(DUMP_FOLDER)
    s3 = boto3.resource('s3')

    if len(files_list) == 0:
        logging.info("업로드할 힙 덤프가 없습니다.")
    else:
        for file in files_list:
            full_file_path = '{}/{}'.format(DUMP_FOLDER, file)
            logging.info("{}을(를) {}에 업로드 중".format(file, BUCKET_NAME))
            s3.meta.client.upload_file(full_file_path, '{}-{}'.format(BUCKET_NAME), '{}/{}'.format(BUCKET_PATH, file))
            os.remove(full_file_path)

if __name__ == "__main__":
    logging.basicConfig(level=logging.INFO)
    while True:
        main()
        time.sleep(300)
```

이 해결책은 한 가지 유휴 리소스(모니터링 팟)만 사용하므로 더 저렴한 비용을 제공합니다. 그리고 마운트된 저장소는 사실상 무료입니다(예: EFS), 많은 양을 저장하지도 않고 몇 분 이상 저장하지 않기 때문입니다.

주의사항:

<div class="content-ad"></div>

- 클라우드 스토리지(예: S3 버킷)에 대한 인증을 하려면 kube2iam과 같은 것을 사용하는 것을 권장합니다.
- 예제와 같이 힙 덤프를 팟 이름으로 명명하십시오. 이 작업의 까다로운 부분은 매니페스트 아규먼트가 아니라 팟의 엔트리포인트에 넣어야 한다는 것입니다.
- 이는 포드 제한에 도달해서 발생하는 OOM에 대한 해결책이 아닙니다.

요약하면, JVM OOM을 처리하는 다양한 방법이 있습니다. 환경에 가장 적합한 방법을 선택하십시오.