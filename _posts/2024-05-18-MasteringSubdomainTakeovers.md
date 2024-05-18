---
title: "하위 도메인 탈취 기술 마스터하기"
description: ""
coverImage: "/assets/img/2024-05-18-MasteringSubdomainTakeovers_0.png"
date: 2024-05-18 20:59
ogImage: 
  url: /assets/img/2024-05-18-MasteringSubdomainTakeovers_0.png
tag: Tech
originalTitle: "Mastering Subdomain Takeovers"
link: "https://medium.com/@tanishqshahsays/mastering-subdomain-takeovers-c9a531fe5d3b"
---


여기 저는 서브도메인 탈취를 통해 바운티를 받은 방법을 설명할게요 -

서브도메인 탈취는 의도된 소유자가 더 이상 사용하지 않는 서브도메인(예: mail.example.com과 같은 대형 웹사이트의 일부)에서 발생하는 취약점입니다. 이는 다양한 이유로 발생할 수 있습니다. 예를 들면:

- 포기된 서비스: 서브도메인이 더 이상 제공되지 않는 서비스에 사용되었을 수 있어 비활성 상태가 될 수 있습니다.
- 잘못된 구성: 도메인 또는 서브도메인 관리 중의 실수로 인해 청구되지 않은 서브도메인이 만들어질 수 있습니다.

바운티를 받는 방법 -

<div class="content-ad"></div>

**단계 1:** 먼저 대상 도메인을 선택하고 잠재적인 서브도메인을 식별하는 프로세스를 시작하세요. Subfinder와 같은 도구를 사용하여 ' -sc 404 ' 플래그와 결합하여 소유되지 않은 서브도메인을 정확하게 파악하세요. 잠재적인 인수 시 필수적인 것입니다. 실행할 초기 명령은 다음과 같습니다:

```js
subfinder -d example.com | httpx -sc 404 | tee list.txt
```

**단계 2:** 404 응답 코드로 표시된 각 서브도메인을 수동으로 검토하세요. 제공된 단서 또는 정보에 주의를 기울이며, 특히 소유되지 않은 S3 버킷이나 다른 관련 세부 정보의 표시에 유의하세요. 또한 'dig' 명령을 사용하여 CNAME (Canonical Name) 레코드를 조사하세요. 예를 들어, dig 명령을 활용하여 CNAME을 검색하여 원본 서브도메인이 어디로 이동되는지 확인할 수 있습니다. 이 단계는 성공적인 도메인 인수에 중요합니다.

```js
dig mail.example.com
```

<div class="content-ad"></div>

```markdown
![MasteringSubdomainTakeovers](/assets/img/2024-05-18-MasteringSubdomainTakeovers_0.png)

**단계 3:** 이제 이 GitHub 저장소를 확인하세요. 거기에 있는 테이블에는 취약한 cname 목록이 표시되어 있습니다. 확인할 수 있습니다. cname이 취약한지 확인할 수 있습니다.

더 간단한 방법은 Nuclie를 사용하는 것입니다. "takeover"라는 템플릿이 있어 도메인을 빼앗을 수 있는지 확인하는 데 도움이 됩니다. 그러나 자동화 도구는 실수를 할 수 있으므로 때로는 수동으로 두 번 확인하는 것이 좋습니다. Nuclie를 사용하려면 다음 명령어를 입력하세요:

```js
nuclei -l subdomain_results.txt -t <nuclei_template_path> -o results.txt
```

<div class="content-ad"></div>

Nuclei Template1, Nuclei Template2

만약 인수 가능성이 있다면, 그를 달성하기 위한 다양한 전략을 탐색해보세요. 고려할 몇 가지 방법을 설명해 드리겠습니다.

- unbouncepages.com — 이 문제는 많은 웹사이트에서 상당히 일반적이며 쉽게 보상을 받을 수 있습니다. 구독 가치 약 $100~$150이 필요하기 때문에 번금을 청구할 필요가 없습니다. 그냥 신고하고 고가 때문에 실제 인수가 어려울 수 있다는 사실을 컨셉 증명서에 언급하면 됩니다. 그것만으로 번금을 받을 수 있고, 아마도 첫 번째 번금일 수도 있습니다.

![이미지](/assets/img/2024-05-18-MasteringSubdomainTakeovers_1.png)

<div class="content-ad"></div>

보고서를 제출하는 것이 버그 바운티 프로그램에 중요하다는 것을 명심해주세요. 그래서 올바른 용어를 알아 사용하여 좋은 바운티를 받을 기회를 극대화하는 것이 중요합니다.

- 요청하지 않은 s3 버킷들 —
"NoSuchBuckets"와 같은 메시지가 표시되는 하위 도메인을 찾으면 큰 성과입니다. AWS에 로그인한 후 동일한 이름의 버킷을 생성하고 동일한 지역에 있는지 확인하세요. "공개 액세스 차단" 옵션을 선택 해제하는 것을 잊지 마세요. 그런 다음 버킷을 요청하고 HTML 파일을 업로드하여 표시할 수 있습니다.

<div class="content-ad"></div>

- Azure — CNAME이 “.cloudapp.net” 또는 “.azurewebsites.net”로 끝난다면 취약합니다. Microsoft Azure로 이동하여 로그인하세요.

Case I — .cloudapp.net

단계 1: Azure 포털로 이동합니다.

단계 2: 메뉴에서 “Cloud Services (classic)”를 선택합니다.

<div class="content-ad"></div>

**단계 3:** "추가" 버튼을 클릭하여 새 클라우드 서비스를 생성합니다.

**단계 4:** 서비스 이름, 구독, 리소스 그룹, 위치 등 필요한 세부 정보를 입력합니다.

**단계 5:** 적절한 구성 옵션과 배포 모델을 선택합니다.

![이미지](/assets/img/2024-05-18-MasteringSubdomainTakeovers_4.png)

<div class="content-ad"></div>

Case II — .azurewebsites.net

단계 1: Azure 포털에 액세스합니다.

단계 2: "앱 서비스"로 이동합니다.

단계 3: "새 웹 앱 만들기"를 클릭합니다.

<div class="content-ad"></div>

### 단계 4: 필요한 세부 정보를 제공하세요. 이름, 구독, 리소스 그룹 및 지역을 입력해주세요. 교체하려는 경우 기존 이름과 일치하도록 합니다.

### 단계 5: 웹 앱이 생성되면 대시보드로 이동하세요.

### 단계 6: 배포 옵션에 액세스하고 FTP, Git 또는 Azure 파이프라인과 같은 선호하는 배포 방법을 선택하세요.

### 단계 7: 배포 패키지를 업로드하거나 연결한 후 배포 프로세스를 시작하세요.

<div class="content-ad"></div>

![이미지](/assets/img/2024-05-18-MasteringSubdomainTakeovers_5.png)

![이미지](/assets/img/2024-05-18-MasteringSubdomainTakeovers_6.png)

서브도메인을 소유하고 관련 보상을 받을 수 있는 다양한 방법이 있습니다. 한 가지 제안은 Google이나 이전 보고서에서 관련 내용을 검색하여 대체 CNAME 레코드를 탐색하는 것입니다. 이를 통해 가치 있는 정보를 얻을 수 있을 수도 있습니다.