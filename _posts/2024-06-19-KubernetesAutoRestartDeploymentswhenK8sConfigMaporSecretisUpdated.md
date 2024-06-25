---
title: "Kubernetes K8s ConfigMap 또는 Secret가 업데이트될 때 배포 자동 재시작하기"
description: ""
coverImage: "/assets/img/2024-06-19-KubernetesAutoRestartDeploymentswhenK8sConfigMaporSecretisUpdated_0.png"
date: 2024-06-19 13:11
ogImage:
  url: /assets/img/2024-06-19-KubernetesAutoRestartDeploymentswhenK8sConfigMaporSecretisUpdated_0.png
tag: Tech
originalTitle: "Kubernetes: Auto Restart Deployments when K8s ConfigMap or Secret is Updated"
link: "https://medium.com/aws-in-plain-english/kubernetes-auto-restart-deployments-when-k8s-configmap-or-secret-is-updated-ad2c042a745f"
---

## 자동 롤아웃 재시작: K8s ConfigMap 또는 Secret가 업데이트될 때 배포 다시 시작하기

쿠버네티스 배포는 모든 쿠버네티스 클러스터에서 가장 일반적인 리소스 중 하나입니다. 우리는 모두 pod를 K8s 배포를 사용하여 실행하여 높은 가용성을 보장하고, pod가 삭제되면 자동으로 생성되도록합니다.

애플리케이션이 항상 여러 환경에서 원활하게 실행되도록하기 위해 구성이 필요한 것은 매우 흔합니다. 데이터베이스 사용자 이름, 비밀번호 등과 같은 중요한 정보가 필요할 수도 있습니다. 쿠버네티스에서는 구성 맵과 시크릿을 사용하여 응용 프로그램별 데이터를 저장하고 pod로 주입하여 응용 프로그램에서 사용할 수 있도록 할 수 있습니다.

그렇다면 구성 맵이나 시크릿의 값을 업데이트했을 때는 어떨까요? 최신 값을 반영하려면 pod를 다시 시작해야합니다, 맞죠? 또는 롤아웃을 다시 시작하여 새로운 pod를 생성하게 할 수도 있습니다. 이제 상상해보세요. 공통 configmap 또는 secret을 사용하는 수백 개의 배포가 있고 그 값을 업데이트하고 사용하는 것이 최신 값이라는 것을 확실하게 해야한다고 가정해보세요.

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

저희는 AWS Secrets Manager에 비밀을 저장하고, Kubernetes Secrets Store CSI Driver를 위해 AWS Secrets 및 구성 제공자(ASCP)로부터 Kubernetes Secrets를 생성합니다.

이 블로그 게시물에서는 Secret 또는 ConfigMap이 업데이트될 때 Kubernetes 배포를 자동으로 롤아웃 및 다시 시작하는 방법에 대해 설명하겠습니다.

# 아키텍처 다이어그램:

![Architecture Diagram](/assets/img/2024-06-19-KubernetesAutoRestartDeploymentswhenK8sConfigMaporSecretisUpdated_0.png)

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

## 시작해 봅시다!

### 준비물:

- EKS 클러스터
- EKS를 위한 OIDC 제공자 구성 필요
- Kubectl
- AWS CLI

### 단계 1: ASCP를 위한 IAM 역할 및 정책 생성

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

ASCP(Amazon EKS Security Token Service)는 Amazon EKS 파드 ID를 검색하여 IAM 역할로 교환합니다. 해당 IAM 역할에 대한 IAM 정책에서 권한을 설정합니다. ASCP가 IAM 역할을 가정하면 권한을 부여받은 시크릿에 액세스할 수 있습니다. 다른 컨테이너는 IAM 역할과 연결되지 않는 한 시크릿에 액세스할 수 없습니다.

- IAM 정책 문서 작성
  "secrets_policy"라는 이름의 파일을 만들고 다음 내용을 추가하십시오.

```js
{
    "Version": "2012-10-17",
    "Statement": [
        {
            "Action": [
                "secretsmanager:DescribeSecret",
                "secretsmanager:GetSecretValue"
            ],
            "Effect": "Allow",
            "Resource": "*"
        }
}
```

2. 다음 명령을 실행하여 IAM 정책을 만듭니다.
   IAM 정책을 IAM 역할에 연결할 때 policy ARN을 메모하십시오.

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
aws iam create-policy \
    --policy-name my-secret-manager-policy \
    --policy-document file://secrets_policy
```

3. IAM 역할에 신뢰 정책 생성하기
   "trust_policy"라는 이름의 파일을 생성하고 다음 내용을 추가하세요. 올바른 값으로 대체해야 합니다. `SERVICE_ACCOUNT_NAME`은 임의로 지정할 수 있지만 Kubernetes에서 실제 서비스 계정을 생성할 때 동일한 이름을 사용해야 합니다.

```js
{
  "Version": "2012-10-17",
  "Statement": [
    {
      "Effect": "Allow",
      "Action": "sts:AssumeRoleWithWebIdentity",
      "Principal": {
        "Federated": "arn:aws:iam::<AWS_ACCOUNT_ID>:oidc-provider/oidc.eks.<AWS_REGION>.amazonaws.com/id/<OIDC_ID>"
      },
      "Condition": {
        "StringEquals": {
          "oidc.eks.<AWS_REGION>.amazonaws.com/id/<OIDC_ID>:aud": "sts.amazonaws.com",
          "oidc.eks.<AWS_REGION>.amazonaws.com/id/<OIDC_ID>:sub": "system:serviceaccount:<K8S_NAMESPACE>:<SERVICE_ACCOUNT_NAME>"
        }
      }
    }
  ]
}
```

4. 다음 명령어를 실행하여 IAM 역할을 만드세요.

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
aws iam create-role --role-name my-secret-manager-role --assume-role-policy-document file://trust_policy
```

5. Attach IAM policy to IAM Role

```js
aws iam attach-role-policy --policy-arn <your_policy_arn> --role-name my-secret-manager-role
```

우리는 필요한 모든 IAM 역할과 정책을 생성했습니다.

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

# 단계 2: ASCP 설치 및 구성

이제 2개의 Helm 차트를 설치해야 합니다.

- AWS Secrets and Configuration Provider (ASCP) 차트 설치

```js
# ASCP Helm 차트 리포지토리 추가
helm repo add aws-secrets-manager https://aws.github.io/secrets-store-csi-driver-provider-aws

# ASCP Helm 차트 설치
helm install -n kube-system secrets-provider-aws aws-secrets-manager/secrets-store-csi-driver-provider-aws
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

2. Secrets Store CSI Driver 차트 설치

- Secrets Store CSI Driver 차트를 위한 helm 레포지토리 추가

```js
# Secrets Store CSI Driver 차트 레포지토리 추가
helm repo add secrets-store-csi-driver https://kubernetes-sigs.github.io/secrets-store-csi-driver/charts
```

- 기본값 확인

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

# 기본 값 가져오기

helm show values secrets-store-csi-driver/secrets-store-csi-driver > secrets-store-csi-driver.yaml

- secrets-store-csi-driver.yaml 파일에서 다음 값을 업데이트하세요.

## K8S Secrets 동기화에 필요한 RBAC 역할 및 바인딩 설치 여부

syncSecret:
enabled: true

## 시크릿 로테이션 기능 활성화 [알파]

enableSecretRotation: true

위의 구성은 "secrets-store-csi-driver"가 AWS Secret Manager에서 최신 값을 가져와 해당 값을 Kubernetes Secrets 객체에 업데이트할 수 있게 합니다.

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

회전-투표-간격은 기본적으로 2분으로 설정되어 있지만, 속성 rotationPollInterval을 설정함으로써 변경할 수 있습니다.

- Helm 차트 설치

```js
# Helm 차트 설치
helm install -n kube-system csi-secrets-store secrets-store-csi-driver/secrets-store-csi-driver -f secrets-store-csi-driver.yaml
```

# 단계 3: AWS Secret Manager에 테스트 시크릿 생성

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

AWS Secret Manager에서 테스트 시크릿을 생성할 것입니다.

```js
aws secretsmanager create-secret \
    --name my-test-secret \
    --description "CLI로 생성한 내 테스트 시크릿." \
    --secret-string "{\"user\":\"my-user\",\"password\":\"예시-비밀번호\"}"
```

# 단계 4: Kubernetes ServiceAccount 생성

이제 IAM 역할을 가정할 수 있도록 파드에 허용하는 ServiceAccount를 생성할 수 있습니다. 이 ServiceAccount는 K8s 배포에서 사용될 것입니다.

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

serviceaccount.yaml이라는 이름의 파일을 생성해주세요.

```yaml
apiVersion: v1
kind: ServiceAccount
metadata:
  name: <your_service_account_name> # 이 이름은 IAM 신뢰 정책을 만들 때 지정한 이름과 일치해야 합니다.
  annotations:
    eks.amazonaws.com/role-arn: <IAM_ROLE_ARN>
```

다음 명령을 실행하여 K8s에서 서비스 계정을 생성합니다.

```bash
kubectl apply -f serviceaccount.yaml
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

# 단계 5: 테스트 객체 생성하기

이제 필요한 모든 리소스를 배포했습니다. 이제 테스트 객체를 만들어 봅시다.

- 이름이 “my-test-secret-manifest.yaml”인 파일 생성

```js
---
apiVersion: secrets-store.csi.x-k8s.io/v1
kind: SecretProviderClass
metadata:
  name: aws-secrets-providerclass
spec:
  provider: aws
  secretObjects:
    - secretName: my-test-k8s-secret
      type: Opaque
      data:
        - objectName: user
          key: user
        - objectName: password
          key: password
  parameters:
    objects: |
      - objectName: arn:aws:secretsmanager:<AWS_REGION>:<AWS_ACCOUNT_ID>:secret:my-test-secret
        jmesPath:
          - path: user
            objectAlias: user
          - path: password
            objectAlias: password
---
apiVersion: apps/v1
kind: Deployment
metadata:
  name: secret-rotation-test-ubuntu-deployment
  labels:
    app: ubuntu
spec:
  replicas: 1
  selector:
    matchLabels:
      app: ubuntu
  template:
    metadata:
      labels:
        app: ubuntu
    spec:
      serviceAccountName: <your_service_account_name> # 이 이름은 단계 4에서 만든 서비스 계정 이름과 일치해야 합니다
      volumes:
      - name: mount-secrets-access
        csi:
          driver: secrets-store.csi.k8s.io
          readOnly: true
          volumeAttributes:
            secretProviderClass: "aws-secrets-providerclass"
      containers:
      - name: ubuntu
        image: ubuntu
        command: ["sleep", "123456"]
        env:
        - name: USER
          valueFrom:
            secretKeyRef:
              name: my-test-k8s-secret
              key: user
        - name: PASSWORD
          valueFrom:
            secretKeyRef:
              name: my-test-k8s-secret
              key: password
        volumeMounts:
        - name: mount-secrets-access
          mountPath: "/mnt/aws-secrets"
          readOnly: true
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

2. manifest를 적용하십시오

```js
kubectl apply -f my-test-secret-manifest.yaml
```

3. 다음 리소스가 생성됩니다.

- SecretProviderClass 리소스 - AWS Secret Manager에서 데이터를 가져와 K8s Secret를 생성합니다
- 볼륨 마운트가 있는 배포 - SecretProviderClass를 볼륨으로 마운트해야 합니다
- Kubernetes Secret - 파드 내에서 환경 변수로 주입됩니다

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

아래 다이어그램은 YAML을 적용할 때 뒷단에서 무슨 일이 벌어지는지 잘 시각화한 것입니다.

![다이어그램](/assets/img/2024-06-19-KubernetesAutoRestartDeploymentswhenK8sConfigMaporSecretisUpdated_1.png)

4. 모든 것이 배포되었는지 확인해보세요.

```js
# SecretProviderClass 확인
kubectl get SecretProviderClass aws-secrets-providerclass -o yaml

# 배포 확인
kubectl get deploy secret-rotation-test-ubuntu-deployment -o yaml

# Pod 확인
kubectl get po <pod_name> -o yaml

# Secret 가져오기
kubectl get secret my-test-k8s-secret -o yaml
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

# 단계 6: Reloader 설치하기

Reloader는 ConfigMap과 Secret의 변경 사항을 감지하고 관련된 DeploymentConfig, Deployment, DaemonSet, StatefulSet 및 Rollout과 함께 Pod의 롤링 업그레이드를 수행할 수 있습니다.

- Reloader Helm Repo 추가

```js
# Helm Repo 추가
helm repo add stakater https://stakater.github.io/stakater-charts
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

2. 기본값 가져오기

```js
# 기본값 가져오기
helm show values stakater/reloader > reloader.yaml
```

3. reloader.yaml 파일 업데이트하기

```js
reloader:
  # 리더십 선출을 활성화하려면 true로 설정하여 여러 레플리카를 실행할 수 있습니다.
  enableHA: true
  deployment:
    # 여러 레플리카를 실행하려면 reloader.enableHA = true로 설정합니다.
    replicas: 2
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

4. Helm 차트 설치

```js
# Helm 차트 설치
helm install reloader -f reloader.yaml stakater/reloader -n kube-system
```

# 단계 7: Secret 업데이트로 테스트하기

- Reloader는 주석에 영향을 받습니다.
  기본 주석 reloader.stakater.com/auto는 주요 메타데이터에 있어야 합니다. 아래 명령을 사용하여 배포에 주석을 추가하세요.

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
# 배포 주석 추가
kubectl annotate deployment secret-rotation-test-ubuntu-deployment "reloader.stakater.com/auto=true"
```

또는 다음 블록으로 배포 파일을 편집하고 적용할 수도 있습니다.

```js
metadata:
  annotations:
    reloader.stakater.com/auto: "true"
```

2. AWS Secret Manager에서 시크릿 업데이트하기

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
aws secretsmanager put-secret-value \
      --secret-id my-test-secret \
      --secret-string "{\"user\":\"diegor\",\"password\":\"SAMPLE-PASSWORD\"}"
```

3. 한 번 시크릿이 AWS 시크릿 스토어 csi 드라이버에 업데이트되면 K8s 시크릿이 즉시 업데이트됩니다. K8s 시크릿이 업데이트되면 Reloader가 롤아웃을 다시 시작하도록 트리거합니다.

```js
# K8s 시크릿을 확인하세요. 새로운 값이 있어야 합니다.
kubectl get secret my-test-k8s-secret -o yaml

# Pod를 확인하세요. 몇 초 전에 시작되었어야 합니다.
kubectl get po

# Reloader 팟의 로그를 확인하세요.
kubectl logs <reloader-pod-name> -n kube-system

# Pod로 실행 후 새로운 값을 확인하세요.
kubectl exec -it <pod_name> -- bash

# Pod에 들어간 후 `env` 명령을 실행하세요. Pod에서 사용 가능한 모든 환경 변수가 출력됩니다.
```

# 단계 8: ConfigMap 업데이트를 테스트하세요.

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

- "my-test-cm-manifest.yaml" 파일을 생성해주세요.

```yaml
---
apiVersion: v1
kind: ConfigMap
metadata:
  name: my-test-k8s-cm
data:
  myvalue: "Hello World"
  drink: coffee
---
apiVersion: apps/v1
kind: Deployment
metadata:
  name: reloader-poc-ubuntu-deployment
  labels:
    app: ubuntu
  annotations:
    reloader.stakater.com/auto: "true"
spec:
  replicas: 1
  selector:
    matchLabels:
      app: ubuntu
  template:
    metadata:
      labels:
        app: ubuntu
    spec:
      containers:
        - name: ubuntu
          image: ubuntu
          command: ["sleep", "123456"]
          env:
            - name: DRINK
              valueFrom:
                configMapKeyRef:
                  name: my-test-k8s-cm
                  key: drink
            - name: MYVALUE
              valueFrom:
                configMapKeyRef:
                  name: my-test-k8s-cm
                  key: myvalue
```

2. 매니페스트 적용

```bash
kubectl apply -f my-test-cm-manifest.yaml
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

3. my-test-cm-manifest.yaml 파일에서 configmap을 업데이트하세요.

```yaml
---
apiVersion: v1
kind: ConfigMap
metadata:
  name: my-test-k8s-cm
data:
  myvalue: "안녕하세요"
  drink: 차
```

4. 파일을 다시 적용하세요.

```yaml
kubectl apply -f my-test-cm-manifest.yaml
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

5. 확인

```js
# ConfigMap 확인
kubectl get cm my-test-k8s-cm -o yaml

# Pod 확인
kubectl get po

# Pod에 접속하여 새 값 확인
kubectl exec -it <pod_name> -- bash

# Pod에 들어간 후 `env` 명령어를 실행하면 Pod 내에서 사용 가능한 모든 환경 변수가 출력됩니다
```

축하합니다!!! secret-store-csi-driver와 reloader를 성공적으로 구성했습니다.

감사합니다!!!

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

## 참고 자료:

- [AWS 공식 문서 - CSI 드라이버 통합](https://docs.aws.amazon.com/secretsmanager/latest/userguide/integrating_csi_driver.html)
- [Bootlabs 기술 블로그 - AWS Secrets Manager in Kubernetes 시크릿 회전과 리로더](https://blog.bootlabstech.com/aws-secrets-manager-in-kubernetes-secret-rotation-and-reloader)
- [Secrets Store CSI 드라이버 공식 홈페이지 - 시크릿 자동 회전](https://secrets-store-csi-driver.sigs.k8s.io/topics/secret-auto-rotation)
- [Secrets Store CSI 드라이버 차트 값 설정 파일](https://github.com/kubernetes-sigs/secrets-store-csi-driver/blob/main/charts/secrets-store-csi-driver/values.yaml)
- [Reloader GitHub 저장소](https://github.com/stakater/Reloader/tree/master)
- [Reloader 작동 확인 문서](https://github.com/stakater/Reloader/blob/master/docs/Verify-Reloader-Working.md)
- [Reloader 작동 방식 문서](https://github.com/stakater/Reloader/blob/master/docs/How-it-works.md)

# 간단히 말하자면 🚀

In Plain English 커뮤니티의 일원이 되어 주셔서 감사합니다! 계속 참여해 주세요:

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

- 작가에게 박수를 보내고 팔로우를 눌러주세요 ️👏️️
- 팔로우하기: X | LinkedIn | YouTube | Discord | Newsletter
- 다른 플랫폼에서 저희를 만나보세요: Stackademic | CoFeed | Venture | Cubed
- 알고리즘 콘텐츠를 다뤄야 하는 블로그 플랫폼에 지쳤나요? Differ를 시도해보세요
- PlainEnglish.io에서 더 많은 콘텐츠를 만나보세요
