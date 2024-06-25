---
title: "Kubernetes에서 Jenkins, Docker, Harbor Private Repository 및 ArgoCD를 사용하여 CICD 파이프라인으로 애플리케이션을 빌드하고 배포하는 방법"
description: ""
coverImage: "/assets/img/2024-05-20-HowtoBuildandDeployApplicationonKuberneteswithCICDPipelineUsingJenkinsDockerHarborPrivateRepositoryandArgoCD_0.png"
date: 2024-05-20 17:22
ogImage:
  url: /assets/img/2024-05-20-HowtoBuildandDeployApplicationonKuberneteswithCICDPipelineUsingJenkinsDockerHarborPrivateRepositoryandArgoCD_0.png
tag: Tech
originalTitle: "How to Build and Deploy Application on Kubernetes with CI CD Pipeline Using Jenkins, Docker, Harbor Private Repository, and ArgoCD"
link: "https://medium.com/@tanmaybhandge/how-to-build-and-deploy-application-on-kubernetes-with-ci-cd-pipeline-using-jenkins-docker-harbor-45ade4fee59b"
---

오늘날의 소프트웨어 개발 환경에서 고품질 소프트웨어를 효과적으로 생산하기 위해서는 CI/CD 파이프라인이 필수적입니다. 이 글에서는 Jenkins, Docker 이미지, Harbour 프라이빗 저장소, Git 및 ArgoCD를 사용하여 지속적인 통합/배포 파이프라인(CI/CD)을 어떻게 설정하는지 설명합니다. 이를 통해 이미지를 원활하게 생성하고 harbor에 푸시한 다음 쿠버네티스 파드에 자동으로 배포할 수 있습니다.

# 시작하기

일반적인 작업 흐름은 다음과 같습니다.

1. Docker 파일 생성: 먼저 Docker 파일을 만들어야 합니다. 이 파일에서는 DevOps 엔지니어가 애플리케이션에 필요한 환경 및 종속성을 지정합니다.

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

2. 리포지토리에 코드를 커밋합니다: Git과 같은 리포지토리는 소스 코드와 Docker 파일을 저장합니다. 저는 GitHub에 이 리포지토리를 저장할 것입니다.

3. 젠킨스가 코드를 가져옵니다: 우리는 젠킨스 작업을 시작하여 소스 코드 리포지토리에서 가장 최근의 코드를 가져와 빌드 프로세스를 시작할 수 있습니다.

4. 도커 이미지 빌드 및 푸시: 젠킨스는 Docker 파일을 사용하여 Docker 이미지를 빌드합니다. 젠킨스는 완료된 이미지를 Harbour 개인 리포지토리에 푸시합니다. Harbour 개인 리포지토리를 빌드하는 방법은 이 곳에서 확인할 수 있습니다.

5. 배포 리포지토리 업데이트: 젠킨스는 애플리케이션에 필요한 쿠버네티스 배포 구성이 포함된 배포 파일 리포지토리에 변경 사항을 가합니다.

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

6. ArgoCD와 동기화: 모든 것이 최신 상태인지 확인하기 위해 ArgoCD라는 Kubernetes 연속 전달 도구는 배포 소스에서 가장 최근 업데이트를 검색합니다.

7. Kubernetes로 배포: 마지막으로, ArgoCD는 모든 기능이 구성된 것을 보장하고, 이러한 수정 사항을 Kubernetes 클러스터와 동기화하여 응용 프로그램을 지정된 pod에 배포합니다.

## 작업 흐름

# 구현

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

## 필요한 도구

- Kubernetes 클러스터
- Docker
- Jenkins
- Harbor
- ArgoCD
- GitHub

## 1. Docker 파일 생성하기

아래에 언급된 Docker 파일을 사용할 것입니다.

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

아래 index.html 파일을 사용하고 있습니다.

GitHub 저장소에서 모든 파일을 찾을 수 있습니다: https://github.com/tanmaybhandge/Harbor_CICD_Pipeline.git

## 2. Jenkins 작업 트리거

저희는 Jenkins에서 두 개의 작업을 구성했습니다.

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

첫 번째 작업(상위 작업)은 최신 소스 코드를 얻기 위해 GitHub 저장소를 복제하는 작업을 포함합니다. 이 코드에서 Docker 이미지가 생성됩니다. 그런 다음 이미지를 빌드하고 Harbor 프라이빗 레지스트리에 로그인한 후 이미지를 푸시하고 상위 작업의 빌드 번호와 태깅합니다.

이미지가 성공적으로 푸시된 후 상위 작업에 의해 하위 작업이 시작됩니다. 하위 작업은 빌드 번호를 매개변수로 받고 해당 빌드 번호가 될 것입니다. 그런 다음 하위 작업은 이미지 버전을 빌드 번호와 동일하게 수정한 다음 쿠버네티스 배포 파일에 변경 사항을 푸시할 것입니다.

상위 작업과 하위 작업이란 무엇을 의미합니까?

한 Jenkins 작업이 다른 작업을 시작할 때, 프로세스를 시작하는 작업을 상위 작업이라고하고, 결과로 시작되는 작업을 하위 작업이라고합니다. 아래 예시에서 Build_Docker_Image_Push_Harbor은 상위 작업이고 push_image_tag_git은 하위 작업입니다.

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

## 작업: 도커 이미지 빌드 및 Harbor로 푸시

파이프라인 작업 "Build_Docker_Image_Push_Harbor"을 생성하고 아래 스크립트를 붙여넣으십시오.

이 Jenkins 파이프라인 스크립트는 GitHub 저장소에서 Docker 이미지를 빌드하고, 해당 이미지를 Harbor 저장소로 푸시하며, 다른 Jenkins 작업 "push_image_tag_git"을 트리거하여 GitHub에서 배포 파일을 수정하고 변경 사항을 커밋하는 프로세스를 자동화합니다.

```js
pipeline {
    agent any

    stages {
        stage('Build') {
            steps {
                // GitHub 저장소에서 코드 가져오기
                git branch: 'main', url: 'https://github.com/tanmaybhandge/Harbor_CICD_Pipeline.git'
                sh 'docker build -t library/harbor_cicd_v2 .'
            }
        }
        stage('Harbor로 푸시') {
            environment {
                DOCKER_CREDENTIALS = credentials('Harbor')
            }
            steps {
                script {
                    // 인증 정보를 사용하여 Harbor에 로그인
                    sh "docker login -u ${DOCKER_CREDENTIALS_USR} -p ${DOCKER_CREDENTIALS_PSW} 10.1.1.1"

                    // 이미지에 태그 추가
                    sh 'docker tag library/harbor_cicd_v2 10.1.1.1/library/harbor_cicd:v${BUILD_NUMBER}'

                    // Harbor로 이미지 푸시
                    sh 'docker push 10.1.1.1/library/harbor_cicd:v${BUILD_NUMBER}'
                }
            }
        }
        stage('GitHub 푸시 트리거') {
            steps {
                build job: 'push_image_tag_git', wait: true, parameters: [string(name: 'Build_Number_Image', value: "${BUILD_NUMBER}")]
            }
        }
    }
}
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

작업 "Build_Docker_Image_Push_Harbor"에서는 선택된 체크박스가 없습니다. 위 스크립트를 그대로 Pipeline 스크립트에 붙여넣기만 하면 됩니다.

![이미지](/assets/img/2024-05-20-HowtoBuildandDeployApplicationonKuberneteswithCICDPipelineUsingJenkinsDockerHarborPrivateRepositoryandArgoCD_0.png)

## 스테이지

각 스테이지에 대한 설명입니다.

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

Build

- Git Checkout: 지정된 URL에서 저장소를 복제하고 'main' 브랜치를 체크아웃합니다.
- Docker Build: 저장소의 Dockerfile을 사용하여 Docker 이미지를 빌드하고 library/harbor_cicd_v2로 태깅합니다.

Harbor로 푸시

- 환경 변수: Jenkins에 저장된 'Harbor' ID를 사용하여 Docker 자격 증명을 가져옵니다. 이미 Jenkins에서 Harbor 자격 증명을 만들었습니다 (Harbor의 기본 자격 증명: 사용자 admin, 비밀번호 Harbor12345).
- Docker Login: 검색한 자격 증명을 사용하여 Harbor 레지스트리에 로그인합니다.
- Docker Tag: 빌드된 Docker 이미지에 Jenkins 빌드 번호를 포함한 새 태그를 추가합니다. 현재 파이프라인의 태그로 빌드 번호를 사용할 것입니다.
- Docker Push: 태그가 붙은 Docker 이미지를 지정된 Harbor 저장소로 푸시합니다.

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

GitHub Push를 트리거합니다.

- 또 다른 작업 트리거: push_image_tag_git 작업을 시작합니다.
- 매개변수 전달: 현재 빌드 번호를 트리거된 작업에 전송합니다.

## 작업: Push_Image_Tag_Git

이 작업은 GitHub에 호스팅된 Kubernetes 배포 YAML 파일을 수정하여 상위 작업에서 푸시된 새 이미지 태그를 포함시킵니다. 이를 위해 다음이 필요합니다:

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

- Parameterized Trigger Plugin
- GitHub Plugin
- push access 권한이 있는 GitHub 자격 증명

작업의 구성은 다음과 같습니다.

push_image_tag_git이라는 이름의 Freestyle 프로젝트를 생성합니다.

A. This project is parameterised 옵션이 선택되었는지 확인한 후, 문자열 매개변수를 적절하게 구성합니다.

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

![이미지1](/assets/img/2024-05-20-HowtoBuildandDeployApplicationonKuberneteswithCICDPipelineUsingJenkinsDockerHarborPrivateRepositoryandArgoCD_1.png)

B. 소스 코드 관리 섹션에서 Git을 선택하고 저장소 URL을 붙여넣은 다음 GitHub에 권한 및 액세스 권한이 있는 적절한 자격 증명을 선택합니다. 빌드할 브랜치 섹션에 브랜치를 지정하세요. 저는 기본 메인 브랜치를 사용합니다.

![이미지2](/assets/img/2024-05-20-HowtoBuildandDeployApplicationonKuberneteswithCICDPipelineUsingJenkinsDockerHarborPrivateRepositoryandArgoCD_2.png)

C. 빌드 단계에 실행 셸을 추가하고 다음을 붙여넣으세요.

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

이 스크립트는 Deployment.yaml 파일에서 이미지 태그를 찾아 해당 버전을 Jenkins 빌드의 ($ 'Build_Number_Image')로 바꿉니다. 또한 Git 커밋을 위해 이메일과 이름을 구성하고, 수정된 모든 파일을 Git 스테이징 영역에 추가하고, 설명적인 메시지로 변경 사항을 커밋합니다.

D. 'Post-Build Actions' 섹션에서 '빌드 성공 시에만 푸시'를 선택한 다음 'Branches' 필드 아래에서 브랜치 이름과 대상 원격 이름을 지정합니다. 저는 브랜치 이름을 'main', 대상 원격 이름을 'origin'으로 지정했습니다.

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

<img src="/assets/img/2024-05-20-HowtoBuildandDeployApplicationonKuberneteswithCICDPipelineUsingJenkinsDockerHarborPrivateRepositoryandArgoCD_4.png" />

## 3. 도커 빌드 작업을 트리거합니다.

수동으로 Build_Docker_Image_Push_Harbor를 트리거하면 아래 작업을 수행하고 Push_image_Tag_Git 작업을 트리거합니다. 두 작업은 다음을 수행합니다.

Build_Docker_Image_Push_Harbor

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

- 빌드 단계: GitHub 저장소를 복제하고 Docker 이미지를 빌드합니다.
- Harbor에 푸시하는 단계: Harbor 레지스트리에 로그인하여 이미지에 빌드 번호를 태깅하고 푸시합니다.
- GitHub 푸시 트리거 단계: 하향 작업인 Push_image_Tag_Git을 트리거하고 빌드 번호를 매개변수로 전달합니다.

Push_image_Tag_Git

- 쉘 실행: Deployment.yaml 파일에서 이미지 태그를 찾아 이전 젠킨스 빌드에서 지정된 버전으로 대체하고, Git 커밋을 위해 이메일 및 이름을 구성하고, 변경된 모든 파일을 Git 스테이징 영역에 추가하여 설명적인 메시지로 변경 사항을 커밋합니다.

여기 Status 출력 결과입니다.

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

<img src="/assets/img/2024-05-20-HowtoBuildandDeployApplicationonKuberneteswithCICDPipelineUsingJenkinsDockerHarborPrivateRepositoryandArgoCD_5.png" />

도커 이미지 빌드 및 쿠버네티스 배포 간의 동기화를 보장하기 위해 Deployment.yaml 파일에 지정된 버전이 Build_Docker_Image_Push_Harbor 중에 생성된 빌드 번호를 반영하도록 업데이트됩니다.

<img src="/assets/img/2024-05-20-HowtoBuildandDeployApplicationonKuberneteswithCICDPipelineUsingJenkinsDockerHarborPrivateRepositoryandArgoCD_6.png" />

## 4. 더 새로운 이미지 버전으로 Pods 다시 생성하기

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

ArgoCD 애플리케이션을 설정하여 deployment.yaml 파일을 주시하고 Kubernetes 클러스터에서 모든 것이 동기화되고 최신 상태를 유지하도록 하였습니다. 따라서 우리의 deployment.yaml에서 조정이 발생할 때마다 ArgoCD가 바로 개입하여 손을 거치지 않고도 Kubernetes 배포 환경이 동기화되도록 합니다.

다음은 ArgoCD 애플리케이션의 구성 세부 정보입니다:

- 클러스터: https://kubernetes.default.svc
- 네임스페이스: webapp
- 저장소 URL: https://github.com/tanmaybhandge/Harbor_CICD_Pipeline.git
- 경로: Deployment

쿠버네티스 클러스터에 webapp 네임스페이스를 생성해야 할 수도 있습니다.

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

![image](/assets/img/2024-05-20-HowtoBuildandDeployApplicationonKuberneteswithCICDPipelineUsingJenkinsDockerHarborPrivateRepositoryandArgoCD_7.png)

![image](/assets/img/2024-05-20-HowtoBuildandDeployApplicationonKuberneteswithCICDPipelineUsingJenkinsDockerHarborPrivateRepositoryandArgoCD_8.png)

## 5. 포드에 액세스하기

ArgoCD 배포가 모두 정상적으로 보인다면, 배포된 애플리케이션을 테스트할 수 있습니다.

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

노드 포트 서비스를 통해 배포를 노출했습니다. 테스트 목적으로 사용할 수 있습니다. 어플리케이션에 액세스하려면 워커 노드의 IP 주소와 서비스 포트를 사용하십시오. 이 설정을 통해 웹페이지에 쉽게 액세스하여 앱의 기능을 테스트하고 유효성을 검사할 수 있습니다.

Markdown 형식의 테이블입니다.

```yaml
apiVersion: v1
kind: Service
metadata:
  name: webapp-service
spec:
  selector:
    app: webapp
  ports:
    - protocol: TCP
      port: 80
      targetPort: 80
  type: LoadBalancer
```

![웹페이지](/assets/img/2024-05-20-HowtoBuildandDeployApplicationonKuberneteswithCICDPipelineUsingJenkinsDockerHarborPrivateRepositoryandArgoCD_9.png)

여기까지 오신 것을 환영합니다! 즐거운 독해가 되었으면 좋겠습니다. 궁금한 점이 있거나 연락하고 싶다면 언제든지 LinkedIn에서 연락해 주세요. 즐거운 하루 보내세요!
