---
title: "쿠버네티스 시크릿을 암호화하는 방법"
description: ""
coverImage: "/assets/img/2024-05-20-HowtoEncryptKubernetesSecrets_0.png"
date: 2024-05-20 16:42
ogImage: 
  url: /assets/img/2024-05-20-HowtoEncryptKubernetesSecrets_0.png
tag: Tech
originalTitle: "How to Encrypt Kubernetes Secrets?"
link: "https://medium.com/@nidhiashtikar/how-to-encrypt-kubernetes-secrets-4d1b5540b37f"
---


쿠버네티스 시크릿은 Kubernetes 클러스터에서 실행되는 애플리케이션에서 필요한 비밀 정보를 저장하고 관리하는 메커니즘입니다.

- 민감한 데이터를 응용 프로그램 코드와 분리하여 보관합니다.
- 시크릿을 생성, 업데이트 및 처리하기 위해 Kubernetes API를 통해 관리됩니다.
- 시크릿 액세스를 제한하는 구성 가능한 액세스 정책이 있습니다.
- 볼륨 내의 파일로 노출되거나 환경 변수로 포드에 노출됩니다.

# 시크릿 암호화의 중요성 :

- etcd에서 암호화되지 않은 시크릿은 데이터베이스가 침해당한 경우에 접근할 수 있습니다.
- 구성이 잘못된 리소스를 통해 실수로 노출될 수 있는 위험이 있습니다.
- 스토리지 액세스 권한이 있는 관리자 및 사용자가 액세스할 수 있습니다.

<div class="content-ad"></div>

# 암호화의 장점 :

- 복호화 키 없이 비밀을 읽을 수 없게 만듭니다.
- 규정 준수를 통해 데이터 보호 요구 사항 충족을 돕습니다.
- 암호화된 데이터는 키 없이는 쓸모 없어서 침해로부터의 피해를 줄입니다.
- 네트워크 전송 중 가로채기를 방지합니다.

# Kubernetes Secrets의 예시 :

- 비밀번호: 데이터베이스 자격 증명, 애플리케이션 로그인 비밀번호 또는 다른 형태의 사용자 인증 비밀번호입니다.

<div class="content-ad"></div>

- 예시: MySQL 데이터베이스 비밀번호.

2. API 키: 외부 서비스 및 API에 인증하고 액세스하기 위한 토큰.

- 예시: 구글 맵스 API 키, Stripe API 키.

3. SSH 키: 서버에 안전한 셸 액세스에 사용되는 키.

<div class="content-ad"></div>

- 예시: 원격 Git 저장소에 액세스하는 개인 SSH 키.

4. TLS 인증서: 안전한 HTTPS 연결 설정에 사용되는 인증서.

- 예시: 웹 서버용 SSL/TLS 인증서.

5. OAuth 토큰: OAuth 흐름에서 권한 부여에 사용되는 토큰.

<div class="content-ad"></div>

- 예시: GitHub 또는 Google과 같은 타사 API에 액세스 토큰입니다.

7. Docker 레지스트리 자격 증명: 개인 Docker 레지스트리에 액세스하기 위한 자격 증명입니다.

- 예시: Docker Hub 또는 기타 컨테이너 레지스트리의 사용자 이름과 비밀번호입니다.

8. 암호화 키: 데이터를 암호화하고 해독하는 데 사용되는 키입니다.

<div class="content-ad"></div>

- 예시: 데이터를 안전하게 보관하기 위해 사용되는 AES 암호화 키.

# 쿠버네티스에서 Secrets 사용법:

- 환경 변수: Secrets는 컨테이너 내에서 환경 변수로 노출될 수 있습니다.

```yaml
apiVersion: v1
kind: Pod
metadata:
  name: mypod
spec:
  containers:
  - name: mycontainer
    image: myimage
    env:
    - name: DB_PASSWORD
      valueFrom:
        secretKeyRef:
          name: mysecret
          key: db_password
```

<div class="content-ad"></div>

2. 볼륨 마운트: 시크릿은 컨테이너 내에서 파일로 마운트될 수 있어요.

```js
apiVersion: v1
kind: Pod
metadata:
  name: mypod
spec:
  containers:
  - name: mycontainer
    image: myimage
    volumeMounts:
    - name: secret-volume
      mountPath: "/etc/secrets"
  volumes:
  - name: secret-volume
    secret:
      secretName: mysecret
```

# 쿠버네티스 시크릿의 암호화 유형:

- 암호화 철자: 무엇을 의미하며 왜 중요한지 설명합니다.
- 전송 중 암호화: 시크릿이 전송 중에 암호화되도록 보장하는 방법에 간단히 언급합니다.

<div class="content-ad"></div>

# 암호화 구성 파일 만들기:

이 파일은 암호화 공급자와 암호화에 사용되는 키를 지정합니다.

```js
apiVersion: apiserver.config.k8s.io/v1
kind: EncryptionConfiguration
resources:
  - resources:
    - secrets
    providers:
    - aescbc:
        keys:
        - name: key1
          secret: <base64-encoded-secret>
    - identity: {}
```

# Encryption Key 생성하기:

<div class="content-ad"></div>

256비트 암호화 키를 Base64로 인코딩하세요. 다양한 도구를 사용하여 이 작업을 수행할 수 있습니다. OpenSSL을 사용하여 다음과 같이 수행할 수 있습니다:

```js
head -c 32 /dev/urandom | base64
```

생성된 키로 구성 파일에서 `base64-encoded-secret`을(를) 교체하세요.

# 암호화 구성 적용하기:

<div class="content-ad"></div>

API 서버 Manifest 파일을 수정해야 합니다. 일반적으로 /etc/kubernetes/manifests/kube-apiserver.yaml 경로에 위치합니다. 다음과 같이 API 서버 Manifest 파일을 수정해주세요.

```yaml
apiVersion: v1
kind: Pod
metadata:
  name: kube-apiserver
  namespace: kube-system
spec:
  containers:
  - name: kube-apiserver
    command:
    - kube-apiserver
    # 다른 플래그들...
    - --encryption-provider-config=/path/to/encryption-config.yaml
```

암호화 구성 파일이 모든 제어 평면 노드의 지정된 경로에서 액세스 가능한지 확인해주세요.

<div class="content-ad"></div>

# API 서버 재시작:

API 서버는 새 구성을 적용하고 비밀을 안전하게 암호화하기 시작할 것입니다.

# 암호화 확인:

비밀이 정상적으로 암호화되고 있는지 확인하려면:

<div class="content-ad"></div>

- 테스트 비밀 정보 만들기:

```js
kubectl create secret generic test-secret --from-literal=key1=supersecret
```

- etcd 확인: etcd 데이터에 직접 액세스하시면서 (일반적으로 프로덕션에서 피해야 하는 직접적인 etcd 쿼리를 수행하므로 주의하세요). 데이터가 암호화되어 있는지 확인하기 위해 etcdctl 도구를 사용하세요.

```js
ETCDCTL_API=3 etcdctl get /registry/secrets/default/test-secret --prefix --key-file=<path-to-key-file> --cert-file=<path-to-cert-file> --cacert=<path-to-ca-cert>
```

<div class="content-ad"></div>

# 암호화 키 회전:

보안을 강화하기 위해 주기적으로 암호화 키를 회전하세요.

- 새 키 추가: 새 키를 목록 상단에 업데이트된 암호화 구성 파일에 추가하세요.
- 비밀 정보 재암호화: 새 키로 모든 비밀 정보를 재암호화하세요.

```js
kubectl get secrets --all-namespaces -o json | kubectl replace -f -
```

<div class="content-ad"></div>

- 이전 키 제거: 모든 비밀을 재암호화한 후, 구성에서 이전 키를 제거하십시오.

# Kubernetes Secrets을 암호화하는 것은 클러스터 내의 민감한 데이터를 안전하게 보호하는 데 중요합니다. Kubernetes Secrets를 암호화하는 다양한 방법은 다음과 같습니다:

## 1. 내장된 메커니즘을 사용하여 정지 상태의 Secrets 암호화

Kubernetes은 정지 상태의 Secrets를 암호화하는 내장 지원을 제공합니다. 이는 가장 간단한 방법이며 API 서버를 암호화 제공자로 구성하는 것이 포함됩니다. 이것이 수행하는 방법은 다음과 같습니다:

<div class="content-ad"></div>

## 단계:

- 암호화 구성 파일 만들기:

```js
apiVersion: apiserver.config.k8s.io/v1
kind: EncryptionConfiguration
resources:
- resources:
  - secrets
  providers:
  - aescbc:
      keys:
      - name: key1
        secret: <base64-encoded-key>
  - identity: {}
```

2. API 서버에서 암호화 구성 지정: kube-apiserver 매니페스트를 편집하십시오 (보통 /etc/kubernetes/manifests/kube-apiserver.yaml에 위치함).

<div class="content-ad"></div>

```plaintext
--encryption-provider-config=/path/to/encryption-config.yaml
```

3. API 서버 재시작: 매니페스트를 업데이트한 후에는 kube-apiserver가 자동으로 재시작되어 비밀을 암호화하기 시작합니다.

## 2. 외부 키 관리 서비스(KMS) 사용

보안을 강화하기 위해 Kubernetes는 AWS KMS, Google Cloud KMS 또는 HashiCorp Vault와 같은 외부 키 관리 서비스와 통합할 수 있습니다. 이 방법을 사용하면 Kubernetes가 외부 시스템을 사용하여 키 관리를 수행할 수 있습니다.


<div class="content-ad"></div>

## 단계:

- KMS 프로바이더 구성:

  - AWS KMS의 경우: AWS KMS 프로바이더 플러그인을 사용하고 암호화 구성 파일을 해당대로 구성합니다.
  - Google Cloud KMS의 경우: GCP KMS 프로바이더 플러그인을 사용하고 암호화 구성 파일을 구성합니다.
  - HashiCorp Vault의 경우: Vault를 구성하여 키를 관리하고 Vault 프로바이더를 설정합니다.

2. Encryption Configuration 파일 업데이트:

<div class="content-ad"></div>

```yaml
apiVersion: apiserver.config.k8s.io/v1
kind: EncryptionConfiguration
resources:
- resources:
  - secrets
  providers:
  - kms:
      name: <provider-name>
      endpoint: <kms-endpoint>
      cachesize: 1000
  - identity: {}
```

3. API 서버 구성 업데이트:

```bash
--encryption-provider-config=/path/to/encryption-config.yaml
```

4. API 서버 재시작: API 서버가 새 구성을 사용하도록 합니다.```

<div class="content-ad"></div>

## 3. 커스텀 암호화 제공자를 사용하여 시크릿 암호화

더 많은 제어를 필요로 하는 경우, 커스텀 암호화 제공자를 구현할 수 있습니다. 이 방법은 커스텀 암호화 플러그인을 작성하고 배포하는 과정을 포함합니다.

## 단계:

- 커스텀 프로바이더 개발: 요구 사항에 기반하여 암호화 및 복호화 로직을 구현합니다.
- 커스텀 프로바이더 배포: 커스텀 프로바이더가 API 서버에서 접근 가능하도록 합니다.
- 암호화 구성 설정:

<div class="content-ad"></div>

```yaml
apiVersion: apiserver.config.k8s.io/v1
kind: EncryptionConfiguration
resources:
- resources:
  - secrets
  providers:
  - custom:
      name: <custom-provider-name>
      endpoint: <custom-provider-endpoint>
  - identity: {}
```

4. API 서버 업데이트:

```bash
--encryption-provider-config=/path/to/encryption-config.yaml
```

5. API 서버 재시작: 구성 변경을 적용하세요.```

<div class="content-ad"></div>

## 4. 응용 프로그램 수준에서 비밀을 암호화하는 방법

데이터를 안전하게 보관하기 위해 데이터를 안전하게 저장하는 대신 또는 그 외에 데이터를 Kubernetes Secrets에 저장하기 전에 응용 프로그램 수준에서 데이터를 암호화할 수 있습니다. 이 방법은 응용 프로그램이 암호화 및 복호화를 처리해야 합니다.

## 단계:

- 응용 프로그램에서 암호화 구현: Kubernetes Secret을 만들기 전에 민감한 데이터를 암호화하는 라이브러리나 도구를 사용합니다.
- Kubernetes Secret으로 암호화된 데이터 저장: Secret에 저장된 데이터는 이미 암호화되어 있습니다.
- 응용 프로그램에서 데이터 복호화: 응용 프로그램이 Secret을 검색할 때 데이터를 사용하기 전에 데이터를 복호화해야 합니다.

<div class="content-ad"></div>

## 5. Sealed Secrets 사용하기

Sealed Secrets는 비트나미에서 개발한 프로젝트로, Git 저장소에 암호화된 비밀을 저장할 수 있게 해줍니다.

## 단계:

- kubeseal 설치: kubeseal CLI 도구를 설치합니다.
- Secret 암호화: kubeseal을 사용하여 쿠버네티스 Secret에서 SealedSecret을 생성합니다.

<div class="content-ad"></div>

```js
kubectl create secret generic mysecret --from-literal=username=myuser --from-literal=password=mypass -o yaml --dry-run=client > mysecret.yaml
kubeseal < mysecret.yaml > mysealedsecret.yaml
```

3. SealedSecret 적용: SealedSecret 매니페스트를 클러스터에 적용합니다.

```js
kubectl apply -f mysealedsecret.yaml
```

4. 런타임에서 Controller 복호화: 클러스터의 Sealed Secrets 컨트롤러가 시크릿을 복호화하고 실제 시크릿 리소스를 생성합니다.

<div class="content-ad"></div>

## 6. SOPS(비밀 작업) 사용하기

SOPS는 Kubernetes 시크릿 매니페스트를 암호화하는 데 사용할 수 있는 도구입니다.

## 단계:

- SOPS 설치: SOPS CLI 도구를 설치합니다.
- 시크릿 매니페스트 암호화: Kubernetes 시크릿 매니페스트를 작성하고 SOPS를 사용하여 암호화합니다.

<div class="content-ad"></div>

```js
sops --encrypt --kms arn:aws:kms:region:account-id:key/key-id secret.yaml > encrypted-secret.yaml
```

3. Apply the Encrypted Secret: 클러스터에 암호화된 매니페스트를 적용하세요.

```js
kubectl apply -f encrypted-secret.yaml
```

4. Decrypt at Runtime: CI/CD 파이프라인이나 애플리케이션 로직 내에서 런타임에 시크릿을 복호화하는 데 SOPS를 사용하세요.

<div class="content-ad"></div>

이러한 방법들은 귀하의 인프라 및 보안 요구사항에 따라 다양한 수준의 보안과 유연성을 제공합니다. Kubernetes Secrets에 대한 암호화를 구현하면, 클러스터 내에서 민감한 데이터가 수명 주기 전체에 걸쳐 보호되도록 할 수 있습니다.

# 이 안내서가 도움이 되었다면 👏 버튼을 클릭해주세요.

더 많은 학습을 위해 팔로우 해주세요 😊

특정 주제에 궁금한 점이 있으시면, 개인적인 메모나 댓글을 남겨주세요. 궁금해하는 내용을 탐험하는 데 도움을 드리겠습니다!

<div class="content-ad"></div>

# 소중한 시간을 내어 지식을 향상시키기 위해 노력하셔서 감사합니다!