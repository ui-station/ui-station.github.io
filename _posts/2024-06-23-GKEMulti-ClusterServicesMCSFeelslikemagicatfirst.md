---
title: "GKE 멀티 클러스터 서비스MCS 처음에는 마법 같은 느낌이 드는 이유"
description: ""
coverImage: "/assets/img/2024-06-23-GKEMulti-ClusterServicesMCSFeelslikemagicatfirst_0.png"
date: 2024-06-23 22:58
ogImage:
  url: /assets/img/2024-06-23-GKEMulti-ClusterServicesMCSFeelslikemagicatfirst_0.png
tag: Tech
originalTitle: "GKE Multi-Cluster Services (MCS): Feels like magic — at first"
link: "https://medium.com/google-cloud/gke-multi-cluster-services-mcs-feels-like-magic-at-first-de39847554c2"
---

Multi-Cluster Services (MCS)은 한 GKE 클러스터에서 워크로드 간 통신을 허용하는 일반적인 문제에 대한 해결책을 제공합니다. 이 서비스는 이 글에서 MCS가 어떻게 구성되는지, 어떻게 구축되는지, 그리고 클러스터와 주변 인프라 수준에서 어떤 구성 요소가 관련되어 있는지 살펴볼 것입니다. 이를 통해 서비스에 대한 심층적인 이해를 얻고 문제 해결에 대한 신뢰를 높일 수 있을 것입니다.

만약 플릿 및 MCS에 대한 일반 소개를 원한다면 Kishore Jagannath의 훌륭한 두 부분 블로그 포스트를 추천합니다.

이 블로그 포스트에서는 조금 다른 방식으로 MCS의 구성 요소를 분석할 것입니다. 운이 좋다면 이런 과정이 기술이 마법처럼 느껴질 때의 어린 시절 추억을 떠올리게 할지도 모릅니다. 그러나 다행히 이번에는 가족용 계산기를 조금 복잡하게 살펴보고 고쳤다가 다시 조립했을 때 처럼 부모님께서 화를 내지 않을 것입니다.

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

# 우선 순위를 정하자 — MCS를 사용해야 할 때

MCS의 내부 작동 방식을 더 깊이 들어가기 전에, 교차 클러스터 통신 영역을 탐색하기 전에 먼저 한 걸음 물러나보겠습니다. 아래 다이어그램에서 볼 수 있듯이, MCS는 쿠버네티스 또는 구체적으로 GKE 클러스터에서 실행되는 서비스를 사용하는 것을 허용하기 위한 여러 가능한 솔루션 중 하나에 불과합니다.

MCS는 같은 플리트 내의 다른 클러스터에서 실행되는 팟에 백업된 서비스와 통신할 수 있도록 하는 작업에 집중하는 비교적 간단한 해결책입니다. 동일한 교차 클러스터 통신은 GKE의 Service Mesh나 Istio와 같은 서비스 메시의 다중 클러스터 기능이나 Cilium과 같은 네트워크 수준의 도구를 사용하여 구현할 수도 있습니다. 이미 이러한 방식 중 하나를 사용하고 있거나 교차 클러스터 통신 상단에 트래픽 관리, 투명 인증 또는 텔레미트리와 같은 기능을 사용할 계획이라면, 아마도 MCS는 너무 단순해서 당신의 사용 사례에 적합하지 않을 것이며, 대신 서비스 메시를 사용하는 것이 좋을 것입니다.

<img src="/assets/img/2024-06-23-GKEMulti-ClusterServicesMCSFeelslikemagicatfirst_1.png" />

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

# MCS 데모 준비 중

이 블로그 포스트에서 실제 탐구를 따라하고 싶다면, MCS와 놀 수 있도록 필요한 API를 활성화하고 GKE autopilot 클러스터 두 개를 생성하는 단계를 수행할 수 있습니다. 대신 표준 GKE 클러스터를 사용하고 싶다면, 여기 제공된 예시도 잘 작동할 것입니다.

```js
export PROJECT_ID=<여기에 프로젝트 ID 입력>

gcloud services enable \
    compute.googleapis.com \
    container.googleapis.com \
    multiclusterservicediscovery.googleapis.com \
    gkehub.googleapis.com \
    cloudresourcemanager.googleapis.com \
    trafficdirector.googleapis.com \
    dns.googleapis.com \
    --project=$PROJECT_ID

gcloud container clusters create-auto "test-us-cluster" \
  --region "us-central1" --enable-master-authorized-networks \
  --network "default" --subnetwork "default" \
  --services-ipv4-cidr 10.99.0.0/20 \
  --async --project "$PROJECT_ID"

gcloud container clusters create-auto "test-eu-cluster" \
  --region "europe-west1" --enable-master-authorized-networks \
  --network "default" --subnetwork "default" \
  --services-ipv4-cidr 10.99.16.0/20 \
  --async --project "$PROJECT_ID"
```

클러스터가 준비되면, 피트에서 다중 클러스터 기능을 활성화하고 새로 생성된 클러스터를 피트에 추가할 수 있습니다.

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
gcloud container fleet multi-cluster-services enable --project $PROJECT_ID

gcloud container fleet memberships register test-us-cluster \
   --gke-cluster us-central1/test-us-cluster \
   --enable-workload-identity \
   --project $PROJECT_ID

gcloud container fleet memberships register test-eu-cluster \
   --gke-cluster europe-west1/test-eu-cluster \
   --enable-workload-identity \
   --project $PROJECT_ID
```

우리 클러스터 상황을 확인해보죠. 이를 위해 europe-west1 지역의 클러스터에 연결해봅시다:

```js
gcloud container clusters get-credentials test-eu-cluster --region europe-west1 --project $PROJECT_ID
```

MCS의 흔적이 이미 존재하는지 확인하기 위해 우리의 네임스페이스 리소스를 나열해보겠습니다:

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
kubectl get ns
```

네임스페이스 목록에는 "gke-mcs"라는 새로 생성된 네임스페이스가 포함되어 있어야 합니다. 해당 네임스페이스 이름은 이미 플릿에서 활성화된 MCS 기능과 관련이 있을 가능성이 높으며, 또한 해당 네임스페이스가 만들어진 시간은 클러스터를 플릿에 등록했을 때와 일치합니다.

"gke-mcs" 네임스페이스를 좀 더 자세히 알아보고 이미 실행 중인 것이 있는지 확인해 봅시다:

```js
kubectl get all -n gke-mcs # 이 명령은 gke-mcs-importer에 대한 배포를 보여줍니다.

kubectl logs -n gke-mcs -l k8s-app=gke-mcs-importer --tail -1 # Importer의 로그를 얻기 위해
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

로그에서는 수입 업체가 트래픽 디렉터 API에 액세스할 권한이 아직 부여되지 않았기 때문에 권한 오류가 발생할 수 있습니다:

```js
핸들러 오류: 스트림을 통해 ADS 응답 수신 중: 권한이 거부되었습니다:
rpc 오류: 코드 = PermissionDenied desc = 권한
'trafficdirector.networks.getConfigs'이 자원에 대해 거부되었습니다
'//trafficdirector.googleapis.com/projects/...' (또는 존재하지 않을 수 있음).
```

수입 업체의 워크로드 ID를 사용하여 Cloud DNS에서 정보를 가져오는 데 사용되는 네트워크 뷰어 역할을 할당함으로써 이 문제를 해결할 수 있습니다:

```js
gcloud projects add-iam-policy-binding $PROJECT_ID \
    --member "serviceAccount:$PROJECT_ID.svc.id.goog[gke-mcs/gke-mcs-importer]" \
    --role "roles/compute.networkViewer"
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

위의 로그 문을 다시 실행하면 예상대로 폴링이 작동하지만 우리를 위해 생성된 존이 없다는 것을 확인할 것입니다. 이제 클러스터는 다중 클러스터 서비스를 배포할 준비가 되었습니다.

# 데모 응용 프로그램 배포

두 GKE 클러스터 간의 컨텍스트 전환을 쉽게하기 위해 먼저 클러스터 컨텍스트의 이름을 변경합니다. 또는 클라우드 셸에 미리 설치된 kubectx 단축키를 사용할 수도 있습니다.

```js
gcloud container clusters get-credentials test-us-cluster --region us-central1 --project $PROJECT_ID
kubectl config rename-context "$(kubectl config current-context)" mcs-us

gcloud container clusters get-credentials test-eu-cluster --region europe-west1 --project $PROJECT_ID
kubectl config rename-context "$(kubectl config current-context)" mcs-eu
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

이 예시에서는 지나치게 화려한 애플리케이션이 필요하지 않습니다. 결국, 다른 GKE 클러스터에서 실행 중인 특정 서비스에 도달할 수 있는지 여부를 보여주기만 원합니다. 데모 목적으로, 우리의 EU 클러스터에서 기존의 hello-web 예제를 배포하고 클러스터 내에서 전통적인 ClusterIP 서비스로 노출합니다.

```js
kubectl create ns shared-services --context mcs-eu

kubectl apply -f https://raw.githubusercontent.com/GoogleCloudPlatform/kubernetes-engine-samples/main/quickstarts/hello-app/manifests/helloweb-deployment.yaml -n shared-services  --context mcs-eu

kubectl expose deployment/helloweb --port 8080 -n shared-services --context mcs-eu
```

서비스가 생성되면 클러스터 내에서 기대대로 자동으로 생성된 k8s cluster.local DNS 이름을 사용하여 성공적으로 호출할 수 있습니다:

```js
kubectl run test-curl --image=curlimages/curl -it --rm --pod-running-timeout=4m --context mcs-eu -- curl -v http://helloweb.shared-services.svc.cluster.local:8080
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

흥미로운 사이드 노트와 나중에 중요한 세부 정보는, 우리의 GKE 클러스터가 클라우드 DNS를 사용한다는 것입니다. 따라서 Google Cloud 콘솔의 Cloud DNS UI에서 우리 서비스를 위해 자동으로 생성된 A 레코드도 볼 수 있습니다. DNS 존에는 명시적으로 이 Cloud DNS 존이 특정 GKE 클러스터에서만 사용 가능하다고 나와 있습니다. 이 DNS 존은 어떤 VPC에도 첨부되어 있지 않기 때문에 전형적인 사설 존은 아닙니다.

![그림](/assets/img/2024-06-23-GKEMulti-ClusterServicesMCSFeelslikemagicatfirst_2.png)

MCS의 목표는 미국 클러스터에서 이 서비스를 사용할 수 있도록 하는 것입니다. 서비스를 노출하기 전에 기본 서비스에서 어떤 일이 발생하는지 살펴봅시다. 아마도 예상하시겠지만, EU 클러스터에서 사용한 DNS 레코드는 미국 클러스터에서 사용할 수 없으며, 심지어 서비스 IP도 미국 클러스터에서 도달할 수 없습니다. 클러스터를 생성할 때 RFC 1918 범위를 사용했지만, 미국 클러스터에서는 이러한 클러스터를 호출할 수 없기 때문입니다.

```js
# 호스트 이름을 해결할 수 없다는 오류로 실패합니다
kubectl run test-curl --image=curlimages/curl -it --rm --pod-running-timeout=4m --context mcs-us -- curl -v http://helloweb.shared-services.svc.cluster.local:8080

# 타임아웃으로 실패합니다
EU_SERVICE_IP="$(kubectl get svc -l app=hello -n shared-services --context mcs-eu -ojsonpath='{.items[*].spec.clusterIP}')"
kubectl run test-curl --image=curlimages/curl -it --rm --pod-running-timeout=4m --context mcs-us -- curl -v "http://$EU_SERVICE_IP:8080"
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

저희가 겪고 있는 문제는 미국 클러스터의 작업량이 EU 클러스터의 작업량에 도달하지 못하는 것이 아니라, 아래에서 직접 Pod IP를 호출하여 증명할 수 있는 것입니다. 네트워크 수준에서는 VPC 네이티브 클러스터 네트워킹을 사용하여 VPC 내에서 Pod IP가 경로 지정될 수 있기 때문에 이 작업이 가능합니다. 아래 예시에서는 양 쪽 클러스터의 API 서버에 연결하여 이를 활용하여 서로간의 교차 클러스터 통신을 증명하는 데 사용합니다.

```js
EU_POD_IP="$(kubectl get po -l app=hello -n shared-services --context mcs-eu -ojsonpath='{.items[*].status.podIP}')"

kubectl run test-curl --image=curlimages/curl -it --rm --pod-running-timeout=4m --context mcs-us -- curl -v http://$EU_POD_IP:8080
```

물론 포드 IP를 명시적으로 Kubernetes API에 쿼리하는 것은 확장 가능한 해결책이 아닙니다. 이것이 클러스터 간에 서비스 발견을 자동화하기 위해 MCS로 전환해야 하는 이유입니다.

# MCS로 서비스 내보내기

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

MCS에서 실행 중인 서비스를 EU 클러스터에서 내보내려면 EU 클러스터에 ServiceExport 리소스를 만듭니다. 동시에 해당 서비스를 가져올 US 클러스터의 이름 공간을 만듭니다:

```js
kubectl create ns shared-services --context mcs-us

kubectl apply --context mcs-eu -f - <<EOF
apiVersion: net.gke.io/v1
kind: ServiceExport
metadata:
 namespace: shared-services
 name: helloweb
EOF
```

이제 방금 내보낸 서비스와 관련된 로그 항목이 포함된 MCS 가져오기자 로그를 US 클러스터에서 살펴볼 수 있습니다:

```js
kubectl logs -n gke-mcs -l k8s-app=gke-mcs-importer --tail=25 --context mcs-us
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

미국 클러스터가 EU에서 실행 중인 작업에 대한 정보를 수신했음을 나타내고 해당 정보를 자동으로 사용하는 엔드포인트가 만들어 졌음을 나타내고 있습니다:

```js
ADS 응답을 받음 (europe-west1-d), 유형: type.googleapis.com/envoy.config.endpoint.v3.ClusterLoadAssignment
europe-west1-d에서 1개의 negs로부터 업데이트
엔드포인트 "gke-mcs-..." 생성 중
```

MCS importer를 통해 미국 클러스터에서 자동으로 생성된 리소스를 살펴봅시다.

```js
kubectl get ServiceImport -n shared-services --context mcs-us

kubectl get Service -n shared-services --context mcs-us

kubectl get Endpoints -n shared-services --context mcs-us
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

위 명령어에서 나열된 엔드포인트들은 EU 클러스터에서 실행 중인 워크로드의 pod ip를 포함하고 있습니다. 이는 ServiceExport 리소스가 이전 섹션에서 수동으로 제공한 교차 클러스터 리소스 식별을 자동화했다는 것을 의미합니다. 이제 위에서 찾은 서비스와 엔드포인트를 테스트하고 미국 클러스터 내에서부터 호출해 봅시다:

```js
SVC_NAME=$(kubectl get service -o=jsonpath='{.items[?(@.metadata.annotations.net\.gke\.io/service-import=="helloweb")].metadata.name}' -n shared-services --context mcs-us)


kubectl run test-curl --image=curlimages/curl -it --rm \
  --pod-running-timeout=4m --context mcs-us -- \
  curl -v http://$SVC_NAME.shared-services.svc.cluster.local:8080
```

작동이 잘 되었네요. 이번에는 EU 클러스터의 API 서버와 통신하여 워크로드를 실행 중인 pod의 IP를 찾아낼 필요가 없었습니다. 왜냐하면 Service Import가 이미 해당 정보를 동기화했기 때문입니다. 유일하게 해결해야 할 문제는 "gke-mcs-`해시`" 형식으로 자동 생성된 MCS importer의 서비스 이름이 사전에 쉽게 알려지지 않는다는 것입니다. 위 예에서도 다시 API 서버를 사용하여 올바른 서비스 이름을 검색했습니다. 실제 사용 사례에서는 워크로드가 원격 서비스를 호출하기 전에 쿠버네티스 API 서버에 문의하는 것을 원치 않는 것은 당연합니다. 이는 추상화를 깨뜨리고 파드의 서비스 계정에 불필요한 권한이 필요하게 됩니다.

클러스터셋 로컬 호스트 이름의 호기심스러운 사례#

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

자동 생성되는 기억하기 어려운 서비스 이름 문제를 해결하기 위해 MCS가 제공하는 편리한 DNS 기반 솔루션을 사용할 수 있습니다. 각 가져온 서비스에 대해 "SERVICE_EXPORT_NAME.NAMESPACE.svc.clusterset.local" 형식의 DNS 항목을 만듭니다. 이 호스트 이름을 사용하여 생성된 서비스 이름을 식별하는 추가 단계 없이 서비스를 호출할 수 있는 결정론적인 방법이 생겼습니다. 가져온 서비스의 호스트 이름을 작성하고 클러스터 중 하나에서 팟 내부에서 호출할 수 있습니다:

```js
kubectl run test-curl --image=curlimages/curl -it --rm \
  --pod-running-timeout=4m --context mcs-eu -- \
  curl http://helloweb.shared-services.svc.clusterset.local:8080

kubectl run test-curl --image=curlimages/curl -it --rm \
  --pod-running-timeout=4m --context mcs-us -- \
  curl http://helloweb.shared-services.svc.clusterset.local:8080
```

참고: 위 요청 중 하나에 대해 오류가 발생하면 DNS 캐싱 때문일 수 있습니다. 관리 DNS 존이 클러스터셋 로컬 DNS A 레코드를 나열하면 결국 팟에서 인식할 수 있습니다.

처음에 예상하지 못한 흥미로운 점 중 하나는 clusterset.local DNS 존이 Cloud DNS UI에 노출되지 않는다는 것입니다. 하지만 우리 호스트 이름을 위해 레코드 세트가 있는 하부 관리 DNS 존이 있음을 다음 명령어를 실행하여 확인할 수 있습니다:

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
gcloud dns managed-zones list --location=us-central1-b

gcloud dns managed-zones describe <name of the zone from above> --location=us-central1-b

gcloud dns record-sets list --location=us-central1-b --zone <name of the zone from above
```

만약 클라우드 콘솔에서 clusterset.local 호스트명을 보고 싶다면 Traffic Director로 이동할 수 있어요. GCP 콘솔에서 Traffic Director UI를 열면 라우팅 규칙 맵 탭에서 우리의 플릿과 연결된 정방향 규칙 및 helloweb 서비스가 연결된 서비스로 나열된 라우팅 규칙을 볼 수 있어요. 또한 이에 연결된 정방향 규칙도 표시돼요.

<img src="/assets/img/2024-06-23-GKEMulti-ClusterServicesMCSFeelslikemagicatfirst_3.png" />

라우팅 규칙의 이름을 클릭하면 정방향 규칙과 라우팅 규칙에서 사용된 호스트명 목록을 볼 수 있어요.

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

<img src="/assets/img/2024-06-23-GKEMulti-ClusterServicesMCSFeelslikemagicatfirst_4.png" />

# [옵션 부분] 계산기를 해체하고 다시 조립하는

만약 그게 맞다면, 우리의 호스트 이름이 실제로 전달 규칙에서 처리되고 내부 로드 밸런서의 경우처럼 연결된 NEG로 보내진다고 가정하는 유혹을 느낄 수 있습니다. 이것이 맞다면, 우리는 미국 클러스터의 쿠버네티스 리소스가 EU 서비스와 통신하기 위해서는 서비스와 엔드포인트에 대한 Kubernetes 리소스가 필요하지 않을 것입니다. 여기서 우리는 다시 시작점으로 돌아와 계산기를 해체해 우리의 이해를 확인하는 유사성을 다시 살펴보는 지점에 도달합니다.

이 가정을 확인하기 위해 미국 클러스터의 shared-services 네임스페이스를 삭제해볼 수 있습니다. 이렇게 하면 우리가 이전에 살펴본 서비스와 엔드포인트를 포함한 모든 네임스페이스 리소스를 삭제합니다. 마지막으로 미국 클러스터의 파드에서 curl을 다시 실행하고 싶을 것입니다.

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
kubectl delete ns shared-services --context mcs-us

kubectl run test-curl --image=curlimages/curl -it --rm \
  --pod-running-timeout=4m --context mcs-us -- \
  curl http://helloweb.shared-services.svc.clusterset.local:8080
```

위의 curl 명령은 실패할 것이며, MCS Importer가 shared-services 네임스페이스에 생성한 리소스가 실제로 필요했음을 확인합니다. 이것은 클러스터 외부의 라우트 규칙에서 호스트 이름이 구성되어 있더라도 해당된다는 것을 의미합니다. 계산기를 되돌려 놓고 MCS Importer가 리소스를 다시 생성할 수 있도록 shared-services 네임스페이스를 재생성합시다.

```js
kubectl create ns shared-services --context mcs-us
```

Importer가 서비스 및 엔드포인트 리소스를 다시 생성한 후, 우리는 버보즈 출력으로 curl 명령을 다시 실행합니다. 이렇게 하면 서비스를 삭제했을 때 위와 같은 이유로 실패했던 것을 확인할 수 있습니다.

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
kubectl run test-curl --image=curlimages/curl -it --rm \
  --pod-running-timeout=4m --context mcs-us -- \
  curl http://helloweb.shared-services.svc.clusterset.local:8080
```

여기서 볼 수 있듯이 호스트 이름이 우리 서비스의 클러스터 IP로 해석되어 MCS에 접근하기 위해 서비스 리소스가 여전히 필요합니다.

# 결론

이 모든 실험과 탐구를 통해 MCS에 대한 우리의 이해를 완성할 수 있습니다. 우리는 지금 관련된 구성 요소를 이해하고, 처음에는 다소 마술적으로 보였던 기능을 가능하게 하는 것에 대해 더 나은 그림을 그릴 수 있습니다.

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

![GKE Multi-Cluster Services](/assets/img/2024-06-23-GKEMulti-ClusterServicesMCSFeelslikemagicatfirst_5.png)

이 게시물에서는 MCS를 실제로 경험하면서 뿐만 아니라 여러 가지를 부수고 다시 조립하는 과정으로 탐구했습니다. 우리는 한 클러스터에 서비스를 생성하고, 그런 다음 MCS를 단계별로 구축하고 워크로드에서 서비스를 소비하는 방식으로 다른 클러스터에서 실행되는 워크로드 사이의 통신을 가능하게 하는 리소스를 살펴봄으로써 MCS를 살펴보았습니다.

자신만의 멀티 클러스터 서비스 여정을 계속해 보고 싶다면 문서에서의 MCS 예제를 살펴보고, 멀티 클러스터 통신 위에 추가 기능을 제공하는 서비스 메시와 같은 대체 구현도 고려해보세요.
