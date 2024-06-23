---
title: "Turkcell 다중 프로젝트 환경에서 Gradle 원격 캐시 사용법"
description: ""
coverImage: "/assets/img/2024-06-23-GradleRemoteCacheforMulti-ProjectEnvironmentatTurkcell_0.png"
date: 2024-06-23 23:23
ogImage: 
  url: /assets/img/2024-06-23-GradleRemoteCacheforMulti-ProjectEnvironmentatTurkcell_0.png
tag: Tech
originalTitle: "Gradle Remote Cache for Multi-Project Environment at Turkcell"
link: "https://medium.com/@aydinmillioglu/gradle-remote-cache-for-multi-project-environment-on-turkcell-e5ffd0aeeb03"
---


소프트웨어 개발의 빠르게 변화하는 세계에서는 효율성과 속도가 프로젝트의 성공을 좌우합니다. 저희 터크셀의 데브옵스 팀은 백엔드, 프론트엔드 및 모바일 플랫폼을 아우르는 다양한 프로젝트의 조율을 맡고 있습니다. 각 프로젝트마다 기술적으로 복잡하고 도전적인 부분이 있지만, 이들을 모두 연결짓는 공통 요소는 신속한 빌드 프로세스의 필요성입니다.

![이미지](/assets/img/2024-06-23-GradleRemoteCacheforMulti-ProjectEnvironmentatTurkcell_0.png)

그러나 기존의 설정은 큰 어려움을 겪고 있었습니다. 빌드 시간이 길어지면서 배달 일정과 개발자 생산성에 영향을 미치고 있었습니다.

이 문제로 저희는 Gradle의 빌드 캐시 노드 기능을 탐구하고 최종적으로 구현하게 되었습니다. 이 게시물에서는 우리가 이 기능을 OpenShift에 구현한 여정, 직면한 어려움, 창조한 해결책 및 달성한 결과에 대해 나눌 것입니다.

<div class="content-ad"></div>

# 빌드 환경에 대한 배경

터키셀의 디지털 서비스에서는 Bip, TV+, Fizy, Lifebox 등과 같은 다양한 안드로이드 프로젝트 포트폴리오를 관리합니다. 각 프로젝트는 고유한 종속성과 빌드 프로세스를 갖고 있어 복잡성과 빌드 시간을 증가시키는 요소로 작용합니다.

저희 파이프라인은 기존에는 Jenkins를 사용하고 있으며 빌드 에이전트로 openshift 포드를 활용하고 있습니다. Jenkinsfile과 포드 템플릿 YAML로 프로세스를 관리합니다. 그러나 이 컨테이너화된 환경에서는 각 빌드가 청소된 상태에서 시작되므로 빌드 사이에 아티팩트가 유지되지 않습니다.

# 해결책 탐색

<div class="content-ad"></div>

## 지역 캐시

우선, Maven 프로젝트에서 사용하는 지역 캐싱 솔루션을 구현할 수 있다고 생각했습니다. OpenShift의 Persistent Volume Claims (PVCs)를 사용하여 빌드 아티팩트 및 종속성을 저장하여 다운로드 시간과 빌드 소요 시간을 줄이는 목표를 가졌습니다. 구조는 단순히 pvc를 로컬 디스크로 사용하고 해당 경로를 지역 캐시로 지정하는 것입니다.

```js
gradle ${GRADLE_OPTIONS} --build-cache --project-cache-dir=${PVC_PATH}/gradle/android/ ${GRADLE_TASKS}
```

Gradle과 함께 사용할 때 이 접근 방식에는 치명적인 결함이 있었습니다. 종속성과 빌드 스크립트를 효율적으로 처리하지만 캐시를 사용할 때 잠금 메커니즘이 발생합니다. 이 잠금 메커니즘은 동시 빌드가 캐시를 손상시키는 것을 방지하지만 동시에 한 번에 하나의 빌드만 캐시를 사용할 수 있음을 의미합니다. 이는 고도로 동시성 환경에서 효과를 크게 감소시킵니다.

<div class="content-ad"></div>

## 빌드 캐시 노드

철저한 조사와 고려 끝에, 중앙 캐시로 Gradle의 빌드 캐시 노드를 설정하기로 결정했습니다. Openshift에서 구축하는 것이 가장 좋다고 결정했습니다. 다음 단계는 배포를 설계하고 구현하여 우리의 OpenShift 환경과 호환되도록 견고하게 만드는 것이었습니다. PVC, 배포, 서비스 및 라우트를 적용한 뒤, 우리의 노드가 가동되었습니다.

# 기술적 구현

## 설치

<div class="content-ad"></div>

저희는 전용 PVC를 사용한 배포를 구현했어요.

PVC Yaml:

```js
kind: PersistentVolumeClaim
apiVersion: v1
metadata:
  name: gradle-remote-cache-pvc
spec:
  accessModes:
    - ReadWriteMany
  resources:
    requests:
      storage: 50Gi
  storageClassName: ocs-external-storagecluster-cephfs
  volumeMode: Filesystem
```

배포 Yaml:

<div class="content-ad"></div>

```yaml
kind: Deployment
apiVersion: apps/v1
metadata:
  name: gradle-remote-cache
spec:
  replicas: 1
  selector:
    matchLabels:
      app: gradle-remote-cache
  template:
    metadata:
      labels:
        app: gradle-remote-cache
    spec:
      volumes:
        - name: gradle-remote-cache-pvc
          persistentVolumeClaim:
            claimName: gradle-remote-cache-pvc
      containers:
        - name: build-cache-node
          args: [ "start" ]
          image: gradle/build-cache-node:18.1
          ports:
            - containerPort: 5071
              protocol: TCP
          resources: {}
          volumeMounts:
            - name: gradle-remote-cache-pvc
              mountPath: /data
          imagePullPolicy: IfNotPresent
      restartPolicy: Always
  strategy:
    type: RollingUpdate
    rollingUpdate:
      maxUnavailable: 25%
      maxSurge: 25%
```

회사 전체에서 사용할 수 있도록 하려면 서비스와 라우트를 통해 열어야 합니다.

서비스 Yaml:

```yaml
apiVersion: v1
kind: Service
metadata:
  name: gradle-remote-cache
spec:
  selector:
    app: gradle-remote-cache
  ports:
    - protocol: TCP
      port: 80
      targetPort: 5071
```

<div class="content-ad"></div>

루트 Yaml:

```yaml
kind: Route
apiVersion: route.openshift.io/v1
metadata:
  name: gradle-remote-cache
spec:
  host: <캐시 라우트 URL>
  to:
    kind: Service
    name: gradle-remote-cache
    weight: 100
  port:
    targetPort: 5071
  tls:
    termination: edge
  wildcardPolicy: None
```

설치 후, 콘솔 로그에 제공된 자격 증명을 사용하여 앱에 로그인합니다. 푸시 권한이 있는 사용자를 만들어 CI/CD 프로세스에서 사용합니다. 또한 개발자들이 로컬 빌드에서 리드 권한만으로 원격 캐시를 사용할 수 있도록 허용합니다.

<img src="/assets/img/2024-06-23-GradleRemoteCacheforMulti-ProjectEnvironmentatTurkcell_1.png" />


<div class="content-ad"></div>

# 사용법

먼저 settings.gradle에 원격 캐시 구성을 추가해야 합니다.

```js
boolean isJenkins = System.getenv().containsKey("IS_JENKINS")

buildCache {
    if (isJenkins){
        remote(HttpBuildCache) {
            url = '<캐시 경로 URL>/cache/'
            push = true
            credentials {
                username = System.getenv("GRADLE_REMOTE_CACHE_USERNAME")
                password = System.getenv("GRADLE_REMOTE_CACHE_PASSWORD")
            }
        }
    }
}
```

빌드 명령에서 캐싱을 활성화해야 합니다.

<div class="content-ad"></div>

```js
gradle ${GRADLE_OPTIONS} --build-cache ${GRADLE_TASKS}
```

# 초기 결과 및 분석

## 결과

모든 프로젝트를 구현한 후 캐시 크기와 빌드 시간을 관찰했습니다. 초기에는 50GB 공간을 볼륨으로 설정했습니다. 마지막으로 50GB를 초과하여 크기를 200GB까지 늘렸습니다.

<div class="content-ad"></div>


![Gradle Remote Cache for Multi-Project Environment at Turkcell](/assets/img/2024-06-23-GradleRemoteCacheforMulti-ProjectEnvironmentatTurkcell_2.png)

성공 결과 시간이 각 프로젝트마다 다릅니다. 일부는 34분에서 11분으로 단축되었습니다.

![Gradle Remote Cache for Multi-Project Environment at Turkcell](/assets/img/2024-06-23-GradleRemoteCacheforMulti-ProjectEnvironmentatTurkcell_3.png)

![Gradle Remote Cache for Multi-Project Environment at Turkcell](/assets/img/2024-06-23-GradleRemoteCacheforMulti-ProjectEnvironmentatTurkcell_4.png)


<div class="content-ad"></div>

일부 프로젝트에서는 시간이 감소하지 않았습니다.

![Image 1](/assets/img/2024-06-23-GradleRemoteCacheforMulti-ProjectEnvironmentatTurkcell_5.png)

![Image 2](/assets/img/2024-06-23-GradleRemoteCacheforMulti-ProjectEnvironmentatTurkcell_6.png)

빌드 캐시 노드는 많은 프로젝트의 빌드 시간을 크게 개선했지만 일부 프로젝트에는 개선되지 않았습니다. 이 솔루션의 전체 잠재력은 계속된 개선과 각 프로젝트의 고유한 특성에 대한 더 깊은 이해를 통해 실현될 수 있습니다.

<div class="content-ad"></div>

# 다음 단계

Gradle 빌드 캐시 노드가 이제 구현되어 일부 프로젝트 빌드 시간을 상당히 개선하는 결과를 보여주고 있으므로, 다음 단계는 개발자들에 이를 인계하는 것입니다.

초기 결과와 원격 캐시의 효율성을 향상시키는 방법에 대한 제안을 개발 팀에 공유했습니다.

이 도구가 제공하는 장점을 완전히 활용하기 위해서는 시스템을 설정하는 데 그치지 말고, 개발자들이 시스템을 지속적으로 최적화하고 사용하는 것이 필요합니다. 캐싱 메커니즘의 효과적인 사용, 캐시 가능한 작업의 적절한 식별, 의존성 관리, 캐시 히트율을 높이기 위한 필요한 조정은 개발자들이 적극적으로 실시함으로써 이루어질 수 있습니다.

<div class="content-ad"></div>

# 결론

마무리하며, Gradle의 빌드 캐시 노드를 OpenShift에 구현하는 여정은 도전적이고 보람찼습니다. 우리는 많은 프로젝트의 빌드 시간을 크게 줄여 제공 일정의 효율성을 높이고 개발자 생산성을 향상시키는 데 성공했습니다.

하지만, 이로 끝이 아닙니다. 우리는 이 솔루션의 잠재력을 완전히 이용하기 위해 개발자들이 시스템의 사용법을 지속적으로 최적화해야 한다는 점을 인식합니다. 이는 캐시할 수 있는 작업 식별, 의존성 관리, 및 캐시 히트율을 높이기 위해 필요한 조치를 취하는 것을 포함합니다.

# 참고문헌

<div class="content-ad"></div>

https://docs.gradle.com/build-cache-node/

https://docs.gradle.org/current/userguide/build_cache.html

https://medium.com/@cesarmcferreira/using-gradle-build-cache-server-73d7680baf2a