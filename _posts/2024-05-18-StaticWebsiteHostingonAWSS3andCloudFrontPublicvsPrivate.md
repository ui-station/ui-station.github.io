---
title: "AWS S3와 CloudFront를 이용한 정적 웹사이트 호스팅 공개 vs 비공개"
description: ""
coverImage: "/assets/img/2024-05-18-StaticWebsiteHostingonAWSS3andCloudFrontPublicvsPrivate_0.png"
date: 2024-05-18 16:19
ogImage: 
  url: /assets/img/2024-05-18-StaticWebsiteHostingonAWSS3andCloudFrontPublicvsPrivate_0.png
tag: Tech
originalTitle: "Static Website Hosting on AWS S3 and CloudFront: Public vs Private"
link: "https://medium.com/@ikpemosi.braimoh/static-website-hosting-on-aws-s3-and-cloudfront-public-vs-private-0b67b1a91578"
---



![Static Website Hosting on AWS S3 and CloudFront](/assets/img/2024-05-18-StaticWebsiteHostingonAWSS3andCloudFrontPublicvsPrivate_0.png)

정적 웹 사이트에는 HTML 페이지, 이미지, CSS 파일, 때로는 JavaScript 및 웹 사이트를 호스팅하는 데 필요한 모든 필수 파일이 포함됩니다. 그들은 변하지 않기 때문에 정적입니다. 웹 사이트를 방문할 때마다 항상 동일한 정보를 동일한 순서와 위치에 보게 됩니다. 이는 로그인한 사용자나 검색 내용에 따라 콘텐츠가 변경되는 동적 웹 사이트와는 달라요.

AWS에서 정적 웹 사이트는 '무언가'라고 불리는 S3 버킷에 저장됩니다. 버킷은 웹 사이트와 관련된 모든 파일을 보관하는 호스트 또는 컨테이너로 비유될 수 있습니다. 서버 측 스크립팅이나 데이터베이스가 필요하지 않기 때문에 그들은 만들기 쉽고 호스팅하기 쉽습니다.

다른 한편 AWS CloudFront는 컨텐츠 전달 네트워크(CDN) 서비스입니다. 주요 기능은 웹 페이지, 비디오, 이미지 및 기타 파일과 같은 콘텐츠를 고성능, 낮은 지연 시간 및 빠른 전송 속도로 사용자에게 전송하는 것입니다. 따라서 정적 웹 사이트를 호스팅하고 CDN으로 CloudFront를 사용하면 사용자에게 웹 사이트 콘텐츠를 전 세계적으로 제공하는 확장 가능하고 신뢰할 수 있으며 고성능 솔루션을 얻을 수 있습니다.


<div class="content-ad"></div>

이 기사에서는 AWS에서 정적 웹 사이트를 호스팅하는 두 가지 방법을 탐색할 것입니다. 이를 위해 공개 S3 버킷 또는 비공개 버킷을 사용할 수 있습니다.

## 목차

- Part A: 공개 S3 버킷에 정적 웹 사이트 호스팅 및 CloudFront에 연결하기
- Part B: 비공개 S3 버킷에 정적 웹 사이트 호스팅 및 CloudFront로 액세스하기
- 리소스 종료 및 정리

## Part A: 공개 S3 버킷에 정적 웹 사이트 호스팅 및 CloudFront에 연결하기

<div class="content-ad"></div>

1. AWS Management Console에서 S3를 검색하세요.

![Step 1](/assets/img/2024-05-18-StaticWebsiteHostingonAWSS3andCloudFrontPublicvsPrivate_1.png)

2. N. Virginia 또는 us-east-1 지역에 있는지 확인하고 버킷 생성을 클릭하세요.

![Step 2](/assets/img/2024-05-18-StaticWebsiteHostingonAWSS3andCloudFrontPublicvsPrivate_2.png)

<div class="content-ad"></div>

Step 3: 일반 목적 버킷 유형, 고유한 버킷 이름을 선택하고 권장대로 ACL 비활성화합니다.

![Step 3 Screenshot](/assets/img/2024-05-18-StaticWebsiteHostingonAWSS3andCloudFrontPublicvsPrivate_3.png)

Step 4: 웹 사이트에 대중 접근 가능하도록 공개 버킷에 호스팅하고 있으므로 'public access 차단'을 선택 해제하고 확인합니다.

![Step 4 Screenshot](/assets/img/2024-05-18-StaticWebsiteHostingonAWSS3andCloudFrontPublicvsPrivate_4.png)

<div class="content-ad"></div>

다음 단계: 아래로 스크롤하여 버킷을 만드세요.

![이미지](/assets/img/2024-05-18-StaticWebsiteHostingonAWSS3andCloudFrontPublicvsPrivate_5.png)

다음 단계: 성공 알림을 받게 될 겁니다. 버킷 이름을 클릭하세요. 이제 웹사이트의 인덱스 파일과 이미지를 업로드할 시간입니다.

![이미지](/assets/img/2024-05-18-StaticWebsiteHostingonAWSS3andCloudFrontPublicvsPrivate_6.png)

<div class="content-ad"></div>

Step 7: 업로드를 클릭하세요.

![Upload](/assets/img/2024-05-18-StaticWebsiteHostingonAWSS3andCloudFrontPublicvsPrivate_7.png)

Step 8: 파일을 추가하세요. 이에는 index.html 파일, css (있을 경우), 자바스크립트 및 웹사이트에 있는 모든 이미지가 포함됩니다. 이 템플릿 웹사이트에서 또는 GitHub 저장소에서 웹사이트 파일을 다운로드하고 추출할 수 있습니다.

![Add Files](/assets/img/2024-05-18-StaticWebsiteHostingonAWSS3andCloudFrontPublicvsPrivate_8.png)

<div class="content-ad"></div>

9단계: 버킷 속성을 클릭하세요.

![이미지](/assets/img/2024-05-18-StaticWebsiteHostingonAWSS3andCloudFrontPublicvsPrivate_9.png)

10단계: 정적 웹사이트 호스팅으로 스크롤하여 편집하세요. 현재 비활성화 상태입니다.

![이미지](/assets/img/2024-05-18-StaticWebsiteHostingonAWSS3andCloudFrontPublicvsPrivate_10.png)

<div class="content-ad"></div>

11단계: 정적 웹사이트 호스팅을 활성화하고 index.html을 인덱스 문서로 지정합니다. 변경 사항을 저장하세요.

![이미지](/assets/img/2024-05-18-StaticWebsiteHostingonAWSS3andCloudFrontPublicvsPrivate_11.png)

12단계: 다시 정적 웹사이트 호스팅으로 돌아가서 버킷 URL을 복사합니다. 브라우저의 새 탭에 붙여넣기하거나 직접 링크를 클릭하여 확인할 수 있습니다.

![이미지](/assets/img/2024-05-18-StaticWebsiteHostingonAWSS3andCloudFrontPublicvsPrivate_12.png)

<div class="content-ad"></div>

13. 결과: 403 금지됨. 버킷 정책을 첨부해야 해서 액세스가 거부되었습니다.

![이미지](/assets/img/2024-05-18-StaticWebsiteHostingonAWSS3andCloudFrontPublicvsPrivate_13.png)

14. 버킷으로 돌아가서 권한 탭을 클릭하세요.

![이미지](/assets/img/2024-05-18-StaticWebsiteHostingonAWSS3andCloudFrontPublicvsPrivate_14.png)

<div class="content-ad"></div>

15단계: 버킷 정책으로 이동하여 편집하세요.

![이미지](/assets/img/2024-05-18-StaticWebsiteHostingonAWSS3andCloudFrontPublicvsPrivate_15.png)

16단계: 아래 정책을 복사하여 붙여넣으세요. 'resource'에 있는 ARN을 버킷 이름으로 바꾸세요.

```js
{
  "Version": "2012-10-17",
  "Statement": [
    {
      "Sid": "PublicReadGetObject",
      "Effect": "Allow",
      "Principal": "*",
      "Action": "s3:GetObject",
      "Resource": "arn:aws:s3:::YOUR_BUCKET_NAME/*"
    }
  ]
}
```

<div class="content-ad"></div>

아래의 'ARN'을 복사하고 붙여넣기하세요. 버킷 ARN은 강조된 영역에 표시됩니다.

![이미지](/assets/img/2024-05-18-StaticWebsiteHostingonAWSS3andCloudFrontPublicvsPrivate_16.png)

단계 17: 변경 사항 저장하기.

![이미지](/assets/img/2024-05-18-StaticWebsiteHostingonAWSS3andCloudFrontPublicvsPrivate_17.png)

<div class="content-ad"></div>

단계 18: 브라우저를 새로고침하세요. 웹사이트가 성공적으로 호스팅되었습니다.

![Image 1](/assets/img/2024-05-18-StaticWebsiteHostingonAWSS3andCloudFrontPublicvsPrivate_18.png)

단계 1: 관리 콘솔에서 CloudFront를 검색하세요.

![Image 2](/assets/img/2024-05-18-StaticWebsiteHostingonAWSS3andCloudFrontPublicvsPrivate_19.png)

<div class="content-ad"></div>

Step 2: 배포를 만듭니다.

![이미지](/assets/img/2024-05-18-StaticWebsiteHostingonAWSS3andCloudFrontPublicvsPrivate_20.png)

Step 3: 오리진으로 버킷 이름을 선택합니다.

![이미지](/assets/img/2024-05-18-StaticWebsiteHostingonAWSS3andCloudFrontPublicvsPrivate_21.png)

<div class="content-ad"></div>

Step 4: 방화벽을 활성화하면 청구를 막기 위해 이 데모의 끝에 삭제해야 합니다.

![image](/assets/img/2024-05-18-StaticWebsiteHostingonAWSS3andCloudFrontPublicvsPrivate_22.png)

Step 5: 아래로 스크롤하여 기본 루트로 이동하고 index.html을 입력하여 배포를 생성하세요.

![image](/assets/img/2024-05-18-StaticWebsiteHostingonAWSS3andCloudFrontPublicvsPrivate_23.png)

<div class="content-ad"></div>

6단계: 배포가 완료되었습니다. 일반적으로 배포 과정이 완료되기까지 시간이 소요됩니다. 과정이 완료될 때까지 기다리신 후, 분배 도메인 이름을 복사하여 브라우저에 붙여넣어 주세요.

![이미지](/assets/img/2024-05-18-StaticWebsiteHostingonAWSS3andCloudFrontPublicvsPrivate_24.png)

7단계: 완료되었습니다!

![이미지](/assets/img/2024-05-18-StaticWebsiteHostingonAWSS3andCloudFrontPublicvsPrivate_25.png)

<div class="content-ad"></div>

# 파트 B: 프라이빗 S3 버킷에 정적 웹사이트 호스팅 및 CloudFront로 액세스하기

단계 1: S3로 돌아가서 다른 고유한 버킷 이름을 선택하세요. ACL을 추천대로 비활성화하세요.

![이미지](/assets/img/2024-05-18-StaticWebsiteHostingonAWSS3andCloudFrontPublicvsPrivate_26.png)

단계 2: 모든 공개 액세스 차단하기.

<div class="content-ad"></div>

마크다운 형식으로 테이블 태그를 변경하세요.

<div class="content-ad"></div>

Step 5: 아직 버킷 정책을 편집하지 마세요. CloudFront로 이동하여 새 유통을 만듭니다.

Step 6: 원본 액세스 아래에서 원본 액세스 제어 설정을 선택하고 새로운 OAC를 생성하세요.

<div class="content-ad"></div>


![이미지](/assets/img/2024-05-18-StaticWebsiteHostingonAWSS3andCloudFrontPublicvsPrivate_31.png)

단계 7: Bucket 이름을 선택하고 생성하세요.

![이미지](/assets/img/2024-05-18-StaticWebsiteHostingonAWSS3andCloudFrontPublicvsPrivate_32.png)

단계 8: 배포를 생성한 후 CloudFront는 웹 사이트에 액세스할 수 있는 버킷 정책을 제공할 것입니다.


<div class="content-ad"></div>


![Step 9](/assets/img/2024-05-18-StaticWebsiteHostingonAWSS3andCloudFrontPublicvsPrivate_33.png)

Step 9: You can choose either A or B.

![Step 10](/assets/img/2024-05-18-StaticWebsiteHostingonAWSS3andCloudFrontPublicvsPrivate_34.png)

Step 10: If you enable a firewall, you will have to delete it at the end of this demo to avoid billing.


<div class="content-ad"></div>


![Image](/assets/img/2024-05-18-StaticWebsiteHostingonAWSS3andCloudFrontPublicvsPrivate_35.png)

Step 11: 기본 루트 객체에 index.html을 추가하고 변경 사항 저장.

![Image](/assets/img/2024-05-18-StaticWebsiteHostingonAWSS3andCloudFrontPublicvsPrivate_36.png)

Step 12: 생성된 정책 문을 복사하고 안전한 곳에 보관하세요. 배포 상태를 현재 날짜로 변경할 수 있도록 허용하고, 분산 도메인 이름을 복사하여 브라우저에 붙여넣으세요.


<div class="content-ad"></div>

<img src="/assets/img/2024-05-18-StaticWebsiteHostingonAWSS3andCloudFrontPublicvsPrivate_37.png" />

13 단계: 당신의 정책은 이와 유사해야 합니다.

```js
{
        "Version": "2008-10-17",
        "Id": "PolicyForCloudFrontPrivateContent",
        "Statement": [
            {
                "Sid": "AllowCloudFrontServicePrincipal",
                "Effect": "Allow",
                "Principal": {
                    "Service": "cloudfront.amazonaws.com"
                },
                "Action": "s3:GetObject",
                "Resource": "arn:aws:s3:::pemosi/*",
                "Condition": {
                    "StringEquals": {
                      "AWS:SourceArn": "arn:aws:cloudfront::296532954493:distribution/E92LWG7KIDQE3"
                    }
                }
            }
        ]
      }
```

14 단계: S3 버킷으로 돌아가서 클라우드프런트에서 복사한 정책으로 버킷 정책을 편집하십시오. 변경사항을 저장하세요.

<div class="content-ad"></div>

<img src="/assets/img/2024-05-18-StaticWebsiteHostingonAWSS3andCloudFrontPublicvsPrivate_38.png" />

단계 15: 여기 왔어요! 이제 클라우드프론트로 웹 사이트에 접근할 수 있습니다.

<img src="/assets/img/2024-05-18-StaticWebsiteHostingonAWSS3andCloudFrontPublicvsPrivate_39.png" />

더 흥미롭게 만들기 위해 AWS Route 53을 사용하여 도메인 이름을 사용자 정의할 수 있습니다. 그래서 문자와 숫자의 연속이 아닌 당신의 이름 또는 선호하는 별칭으로 정적 웹 사이트가 제작됩니다.

<div class="content-ad"></div>

# 리소스 종료 및 정리

청구를 피하기 위해 생성한 모든 리소스를 삭제하는 것이 좋습니다. 삭제되지 않은 리소스는 직불 카드에 요금이 부과됩니다.

시작하려면 S3 버킷으로 돌아가세요.

단계 1: 버킷 이름 옆의 원형 체크박스를 클릭하고 삭제를 클릭하세요.

<div class="content-ad"></div>


![image](/assets/img/2024-05-18-StaticWebsiteHostingonAWSS3andCloudFrontPublicvsPrivate_40.png)

Step 2: You’ll be prompted to empty your bucket first before deleting.

![image](/assets/img/2024-05-18-StaticWebsiteHostingonAWSS3andCloudFrontPublicvsPrivate_41.png)

Step 3: Type ‘permanently delete’ in the space provided and empty.


<div class="content-ad"></div>

![Image](/assets/img/2024-05-18-StaticWebsiteHostingonAWSS3andCloudFrontPublicvsPrivate_42.png)

단계 4: 이제 버킷을 삭제할 수 있습니다. 다시 확인란을 선택하고 이번에는 삭제를 클릭하세요. 버킷 이름을 입력하여 삭제 요청을 확인하세요. 성공적으로 삭제되었습니다!

![Image](/assets/img/2024-05-18-StaticWebsiteHostingonAWSS3andCloudFrontPublicvsPrivate_43.png)

단계 5: CloudFront로 이동하세요. 배포 확인란을 선택하고 비활성화하세요. 시간이 걸릴 수 있습니다.

<div class="content-ad"></div>

이미지 태그를 Markdown 형식으로 변경하십시오.

Step 6: 비활성화한 후에 삭제할 수 있습니다.

Step 7: 성공적으로 완료되었습니다! 앞으로 나가서 CloudFront를 위해 생성한 OAC를 분리하고 삭제하십시오!

<div class="content-ad"></div>


![이미지](/assets/img/2024-05-18-StaticWebsiteHostingonAWSS3andCloudFrontPublicvsPrivate_46.png)

파이어월을 활성화했다면 저와 같이, 파이어월도 삭제해야 합니다. 콘솔에서 WAF를 검색하여 WAF 및 Shield를 선택합니다. 웹 ACLs를 선택합니다. 지역을 us-east-1에서 Global CloudFront로 변경한 후, 파이어월을 클릭하여 삭제합니다.

본 문서는 Amazon S3 및 CloudFront에서 정적 웹사이트를 호스팅하는 과정을 안내했습니다. 이를 통해 다양한 객체를 S3 버킷에 저장하고 모든 종류의 웹사이트를 호스팅할 수 있습니다. 그러나 제작 목적 외의 모든 생성된 리소스는 항상 종료하여야 합니다.

구름에서 만나요!
 

<div class="content-ad"></div>

저와 연락을 주세요:
Twitter(X)
LinkedIn
협업 및 일자리? 이메일