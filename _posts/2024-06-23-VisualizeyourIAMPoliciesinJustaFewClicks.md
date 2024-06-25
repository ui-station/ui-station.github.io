---
title: "몇 번의 클릭으로 IAM 정책 시각화 하는 방법"
description: ""
coverImage: "/assets/img/2024-06-23-VisualizeyourIAMPoliciesinJustaFewClicks_0.png"
date: 2024-06-23 00:16
ogImage:
  url: /assets/img/2024-06-23-VisualizeyourIAMPoliciesinJustaFewClicks_0.png
tag: Tech
originalTitle: "Visualize your IAM Policies in Just a Few Clicks 👆"
link: "https://medium.com/towards-aws/visualize-your-iam-policies-in-just-a-few-clicks-b35cf890f74d"
---

우리 모두가 IAM 정책이 AWS 리소스를 보호하는 데 매우 중요하다는 것을 알고 있습니다. 그러나 IAM 정책을 시각화해 본 적이 있나요? 아니라면, 이 게시물이 도움이 될 것입니다. 이 게시물에서는 IAM 정책을 몇 번의 클릭만으로 시각화하는 방법을 살펴보겠습니다.

# IAM 정책을 시각화하는 이유

간단한 IAM 정책이 다중 명령문을 가진 시나리오를 고려해 봅시다. 각 명령문에는 여러 작업, 리소스 및 조건이 있습니다.

예시:

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
      "Version": "2012-10-17",
      "Statement": [
          {
              "Effect": "Allow",
              "Action": [
                  "s3:GetObject",
                  "s3:PutObject"
              ],
              "Resource": "arn:aws:s3:::my-bucket/*",
              "Condition": {
                  "StringEquals": {
                      "s3:x-amz-acl": "public-read"
                  }
              }
          },
          {
              "Effect": "Deny",
              "Action": [
                  "s3:*"
              ],
              "Resource": "arn:aws:s3:::my-bucket/*",
              "Condition": {
                  "StringNotEquals": {
                      "s3:x-amz-acl": "public-read"
                  }
              }
          }
      ]
  }
```

이제 정책을 이해하려면 정책을 읽고 이해해야 합니다. 하지만, 여러 문과 조건이 포함된 복잡한 정책이 있는 경우는 어떨까요?

예시:

```js
{
      "Version": "2012-10-17",
      "Statement": [
          {
              "Effect": "Allow",
              "Action": [
                  "s3:GetObject",
                  "s3:PutObject"
              ],
              "Resource": "arn:aws:s3:::my-bucket/*",
              "Condition": {
                  "StringEquals": {
                      "s3:x-amz-acl": "public-read"
                  }
              }
          },
          {
              "Effect": "Deny",
              "Action": [
                  "s3:*"
              ],
              "Resource": "arn:aws:s3:::my-bucket/*",
              "Condition": {
                  "StringNotEquals": {
                      "s3:x-amz-acl": "public-read"
                  }
              }
          },
          {
              "Effect": "Allow",
              "Action": [
                  "s3:GetObject",
                  "s3:PutObject"
              ],
              "Resource": "arn:aws:s3:::my-bucket/*",
              "Condition": {
                  "StringEquals": {
                      "s3:x-amz-acl": "public-read"
                  }
              }
          },
          {
              "Effect": "Deny",
              "Action": [
                  "s3:*"
              ],
              "Resource": "arn:aws:s3:::my-bucket/*",
              "Condition": {
                  "StringNotEquals": {
                      "s3:x-amz-acl": "public-read"
                  }
              }
          }
      ]
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

정책을 읽는 것만으로는 정책을 이해하기가 매우 어려울 것입니다. 여기에 IAM 정책 시각화가 등장합니다. IAM 정책을 시각화하여 쉽게 이해하고 필요한 경우 변경할 수 있습니다.

# IAM 정책 시각화 방법

- 이 사이트로 이동하십시오. 이 사이트는 Amazon의 보안 엔지니어인 BOUR Abdelhadi가 만들었습니다.

![IAM Policies Visualization](/assets/img/2024-06-23-VisualizeyourIAMPoliciesinJustaFewClicks_0.png)

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

2. IAM 정책을 텍스트 영역에 붙여넣으세요. 이제 IAM 정책의 시각적 표현을 "정책 시각화" 섹션에서 볼 수 있습니다. 정책을 쉽게 이해하고 필요한 경우 변경할 수 있습니다.

![IAM Policy Visualization 1](/assets/img/2024-06-23-VisualizeyourIAMPoliciesinJustaFewClicks_1.png)

![IAM Policy Visualization 2](/assets/img/2024-06-23-VisualizeyourIAMPoliciesinJustaFewClicks_2.png)

3. 조금 아래로 스크롤하면 "이 정책이 하는 일은?"을 평문으로 볼 수 있습니다. 이를 통해 정책을 간단하게 이해할 수 있습니다.

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

<img src="/assets/img/2024-06-23-VisualizeyourIAMPoliciesinJustaFewClicks_3.png" />

여기까지입니다. 몇 번의 클릭으로 IAM 정책을 성공적으로 시각화했습니다.

IAM 정책을 시각화해야 하는 이유에 대해 더 알아보세요: [여기를 클릭하세요](링크)

사이트 제작자: BOUR Abdelhadi

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

LinkedIn에서 연결해요: LinkedIn 프로필

실전 프로젝트 살펴보기 (제 저장소가 도움이 된다면 GitHub에서 저를 팔로우하시는 것을 잊지 마세요): 내 GitHub 계정
