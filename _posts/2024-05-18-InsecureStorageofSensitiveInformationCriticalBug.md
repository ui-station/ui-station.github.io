---
title: "민감한 정보의 불안정한 저장  중요한 버그"
description: ""
coverImage: "/assets/img/2024-05-18-InsecureStorageofSensitiveInformationCriticalBug_0.png"
date: 2024-05-18 21:01
ogImage: 
  url: /assets/img/2024-05-18-InsecureStorageofSensitiveInformationCriticalBug_0.png
tag: Tech
originalTitle: "Insecure Storage of Sensitive Information >> Critical Bug"
link: "https://medium.com/@javroot/insecure-storage-of-sensitive-information-critical-bug-20f0d38e7f35"
---


# 개요

<img src="/assets/img/2024-05-18-InsecureStorageofSensitiveInformationCriticalBug_0.png" />

# 재현 단계:

먼저 대상에서 서브도메인 열거가 필요합니다. 예를 들어 subfinder 및 httpx를 사용하여 도메인 정보를 얻을 수 있습니다. 다음 명령을 사용하세요:

<div class="content-ad"></div>

```js
subfinder -d example.com|httpx -td --title --status-code 
```

그 다음으로 터미널에서 관리자 제목을 찾을 것이고, 이 도메인은 내 방법론의 많은 테스트 케이스에 적합한 좋은 도메인입니다. 이제 브라우저를 열어 URL을 입력하세요

<img src="/assets/img/2024-05-18-InsecureStorageofSensitiveInformationCriticalBug_1.png" />

이 도메인에는 많은 콘텐츠가 있습니다 👾
```

<div class="content-ad"></div>

식당을 찾아서 거기에서 점심을 먹어요. 그리고 그 사진을 찍어요.

<div class="content-ad"></div>

```js
cat commons-logging-1.1.1.jar|strings
```

이제 데이터가 있지만 충분하지 않아요 :

![Image](/assets/img/2024-05-18-InsecureStorageofSensitiveInformationCriticalBug_3.png)

이후에, jadex에서 jar 파일을 열었어요. 이제 무엇이 있는지 확인해볼게요:

<div class="content-ad"></div>

```markdown
![Screenshot 1](/assets/img/2024-05-18-InsecureStorageofSensitiveInformationCriticalBug_4.png)

![Screenshot 2](/assets/img/2024-05-18-InsecureStorageofSensitiveInformationCriticalBug_5.png)

and boom 💥I have default user and password server backend code on java and many more data on this program.

![Screenshot 3](/assets/img/2024-05-18-InsecureStorageofSensitiveInformationCriticalBug_6.png)
```

<div class="content-ad"></div>

행복한 사냥을 하세요 XD