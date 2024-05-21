---
title: "아마존 S3 프리사인드 URL 활용 방법"
description: ""
coverImage: "/assets/img/2024-05-20-HowtoutilizeAmazonS3presignedURLs_0.png"
date: 2024-05-20 16:44
ogImage: 
  url: /assets/img/2024-05-20-HowtoutilizeAmazonS3presignedURLs_0.png
tag: Tech
originalTitle: "How to utilize Amazon S3 presigned URLs"
link: "https://medium.com/gitconnected/how-to-exploit-amazon-s3-presigned-urls-adffc32fb26a"
---


블롭 객체를 안전하게 저장하는 것은 중요합니다. AWS S3는 이를 위한 우수한 해결책을 제공합니다. 여러 언어로 제공되는 사용자 친화적인 SDK를 통해 파일을 업로드하고 다운로드하는 과정이 간단해집니다. 그러나 민감한 계정 자격 증명에 접속해야 하는 경우가 발생할 수 있습니다. 이런 상황에서 사전 서명된 URL은 귀중한 해결책으로 나타납니다. 이 URL은 AWS S3 버킷 리소스에 일시적으로 접속할 수 있는 수단을 제공하여 비밀 키를 직접 삽입할 필요가 없어집니다. 이 URL은 데이터 무결성을 보호하면서 보안을 보장하는 안전하고 일시적인 접근을 가능케 합니다.

본 글에서는 파일을 업로드하고 다운로드하는 용도로 사전 서명된 URL을 사용하는 방법을 설명하여 AWS S3를 효과적으로 활용하면서 데이터 보안의 실천을 유지할 수 있도록 돕고 있습니다.

# 사용 사례

다음은 사전 서명된 URL을 활용할 수 있는 여러 사용 사례입니다.

<div class="content-ad"></div>

- 일시적 액세스를 통한 다운로드 링크: 사전 서명된 GET URL을 생성하여 S3 버킷에서 특정 파일을 다운로드하는 데 일시적인 액세스를 제공할 수 있습니다. 이를 통해 AWS 자격 증명을 노출시키지 않고 다른 사람들과 파일을 안전하게 공유할 수 있습니다.
- 일시적 슬롯을 통한 업로드 링크: 비슷하게, 사전 서명된 PUT URL은 S3 버킷으로 파일을 업로드하는 데 일시적인 액세스를 제공하는 데 사용할 수 있습니다. 이를 통해 사용자들이 직접 AWS 자격 증명에 접근할 필요없이 파일을 안전하게 업로드할 수 있습니다.
- 가상 머신 보안: 부팅할 때 가상 머신에 사전 서명된 POST 정책을 삽입하여 지정된 조건하에 여러 파일을 S3 버킷에 안전하게 업로드할 수 있습니다. 이 접근 방식은 민감한 AWS 자격 증명이 가상 머신에 저장되지 않도록 보안을 강화합니다.
- HTML 폼으로 직접 파일 업로드: HTML 폼에 사전 서명된 POST 정책을 삽입하여 백엔드 서버를 프록시로 사용하지 않고 S3 버킷으로의 직접 파일 업로드를 허용할 수 있습니다. 이를 통해 업로드 프로세스를 간소화하고 서버 측 처리를 줄일 수 있습니다.

AWS S3 사전 서명된 URL을 사용하면 PUT 또는 POST 정책을 사용하여 단일 또는 여러 파일을 업로드할 수 있습니다. 다수의 파일을 다운로드할 수 있는 정책을 생성하는 것은 불가능합니다. 이 경우에는 여러 개의 사전 서명된 GET URL을 생성해야 합니다.

[2024-05-20-HowtoutilizeAmazonS3presignedURLs_0.png](/assets/img/2024-05-20-HowtoutilizeAmazonS3presignedURLs_0.png)

단일 파일을 다운로드하거나 업로드하기 위해, 파일 업로드가 필요한 구성 요소가 필요할 때 외부 서비스로부터 사전 서명된 URL을 동적으로 요청할 수 있는 풀 기반 아키텍처가 탁월합니다.

<div class="content-ad"></div>


![image1](/assets/img/2024-05-20-HowtoutilizeAmazonS3presignedURLs_1.png)

Conversely, for scenarios such as uploading using HTML form or running specific jobs within a virtual machine, a push-based approach presents a straightforward solution. Here, the initiation of the presigned POST policy aligns with the document creation or VM startup process. By injecting a policy during these events, we empower the source to autonomously upload files and minimize external dependencies.

![image2](/assets/img/2024-05-20-HowtoutilizeAmazonS3presignedURLs_2.png)

# How-to guide


<div class="content-ad"></div>

파이썬 boto 패키지를 사용하여 미리 서명된 URL을 생성하고 사용하는 방법을 보여드립니다. 간단한 인터페이스를 제공하는 AWS S3 클라이언트입니다.

```js
import os

import boto3
from botocore.client import Config

ACCESS_KEY = os.getenv("ACCESS_KEY")
SECRET_KEY = os.getenv("SECRET_KEY")
region = "eu-central-1"

s3_client = boto3.client(
    's3',
    aws_access_key_id=ACCESS_KEY,
    aws_secret_access_key=SECRET_KEY,
    config=Config(signature_version='s3v4'),
    region_name=region,
)
```

물론 AWS S3 클라우드에도 액세스해야합니다. 이전 포스트 중 하나에서 IAM 계정 및 S3 버킷을 생성하는 방법을 설명했습니다. 이 포스트는 AWS S3 업로드에 대한 자동화 테스트에 관한 것입니다.

# Presigned POST policy

<div class="content-ad"></div>

미리 서명된 POST URL에서는 여러 파일을 업로드할 수 있는 정책을 구성합니다. 다음을 정의할 수 있습니다:

- S3 버킷 파일이 저장될 위치 — bucket_name
- 정책의 유효 기간 — ExpiresIn
- 추가로 허용할 폼 필드 — Fields
- 추가 조건 세트 — Conditions (예: 파일의 허용 확장자, 파일 이름의 허용 접두사)

우리의 예시에서는 정책이 생성 시간으로부터 1분(60초) 후에 만료될 것이라고 지정합니다. 또한 mytest/ 문자열로 시작하는 키를 가진 파일만 허용합니다. Amazon S3에서 키(객체 이름이라고도 함)는 버킷 내 객체의 고유 식별자로 사용됩니다. 이것은 본질적으로 버킷의 네임스페이스 내 객체의 전체 경로를 나타냅니다. 이 구성은 testjorzel 버킷 내 mytest 폴더로 파일을 업로드할 수 있도록 허용합니다.

```js
import requests 

bucket_name = "testjorzel"
prefix = "mytest/"
object_name = prefix + "${filename}"
presigned_post = s3_client.generate_presigned_post(
    bucket_name,
    object_name,
    Fields=None,
    Conditions=[["starts-with", "$key", prefix]],
    ExpiresIn=expiration,
)
```

<div class="content-ad"></div>

저희 사전 서명된 POST 정책은 다음과 같이 보입니다:

```js
{
    'url': 'https://testjorzel.s3.amazonaws.com/',
    'fields': {
        'key': 'mytest/${filename}',
        'x-amz-algorithm': 'AWS4-HMAC-SHA256',
        'x-amz-credential': 'AKIAU5USI2VZ3RIF3L5V/20240403/eu-central-1/s3/aws4_request',
        'x-amz-date': '20240403T201346Z',
        'policy': 'eyJleHBpcmF0aW9uIjogIjIwMjQtMDQtMDNUMjE6MTM6NDZaIiwgImNvbmRpdGlvbnMiOiBbWyJzdGFydHMtd2l0aCIsICIka2V5IiwgIm15dGVzdC8iXSwgeyJidWNrZXQiOiAidGVzdGpvcnplbCJ9LCBbInN0YXJ0cy13aXRoIiwgIiRrZXkiLCAibXl0ZXN0LyJdLCB7IngtYW16LWFsZ29yaXRobSI6ICJBV1M0LUhNQUMtU0hBMjU2In0sIHsieC1hbXotY3JlZGVudGlhbCI6ICJBS0lBVTVVU0kyVlozUklGM0w1Vi8yMDI0MDQwMy9ldS1jZW50cmFsLTEvczMvYXdzNF9yZXF1ZXN0In0sIHsieC1hbXotZGF0ZSI6ICIyMDI0MDQwM1QyMDEzNDZaIn1dfQ==',
        'x-amz-signature': '672ca05236f727190841b102a73e4d3298b5821b5afbc62cbfba2dad6128c74c'
    }
}
```

이는 일반적인 URL과 POST 요청과 함께 전달해야 하는 인증 필드 세트로 구성되어 있습니다(requests.post 함수).

```js
import requests 

def upload_file(filepath, object_name, policy):
    with open(filepath, 'rb') as f:
        files = {'file': (filepath, f)}
        fields = policy['fields']
        fields['key'] = object_name
        http_response = requests.post(policy['url'], data=fields, files=files)
    print(f'File: {object_name} uploaded, HTTP status code: {http_response.status_code}, text: {http_response.text}')

filepath = "test.txt"
upload_file(filepath, 'mytest/post_test1.txt', presigned_post)
upload_file(filepath, 'mytest/post_test2.txt', presigned_post)
```

<div class="content-ad"></div>

test.txt 파일은 하나의 줄로 구성된 간단한 텍스트 파일입니다: My test put/get/post. POST 요청이 성공하면 결과는 내용이없는 204 응답 코드여야 합니다. 이는 파일이 성공적으로 업로드되었음을 의미합니다.

```js
File: mytest/post_test1.txt uploaded, HTTP status code: 204,
text:

File: mytest/post_test2.txt uploaded, HTTP status code: 204,
text:
```

우리는 S3 클라우드의 testjorzel 버킷 내용을 확인할 수 있습니다.

![이미지](/assets/img/2024-05-20-HowtoutilizeAmazonS3presignedURLs_3.png)

<div class="content-ad"></div>

`Markdown` 형식으로 변경된 테이블:

| 파일 이름 | 결과 코드 | 메시지 | 요청 ID | 호스트 ID |
|-----------|-----------|--------|---------|-----------|
| post_test1.txt | 200 | - | - | - |
| post_test2.txt | 200 | - | - | - |
| post_test3.txt | 403 | AccessDenied | 3GZ7Q2XYK4R9190H | CAL0rRroo8PjDPvXEKhAa5XB/FKwf0k+dm00Kwr95ri2yaN+Hr/qZgzmnH4Fs/Jmnt45rMBidY8= |


<div class="content-ad"></div>

```js
파일 test/post_test4.txt가 업로드되었습니다. HTTP 상태 코드: 403,
텍스트: <?xml version="1.0" encoding="UTF-8"?>
<Error><Code>AccessDenied</Code><Message>정책에 따라 무효함: 정책 조건 실패: ["starts-with", "$key", "mytest/"]</Message><RequestId>4AKJZREYX4YD17VF</RequestId><HostId>WLz45xZYd7dSgSIi/6SWtWuzPPYF2aCI1oeX8P+RxTdFskeAM6c7g0VH0OwJRVDIEBSmUS8qDnw=</HostId></Error>
```

# GET을 위한 사전 서명 된 URL

S3 버킷에 파일이 있다고 가정하고(예: post_test1.txt), 사전 서명된 GET URL을 생성할 수 있습니다.

```js
bucket_name = "testjorzel"
prefix = "mytest/"
presigned_get = s3_client.generate_presigned_url(
    'get_object',
    Params={
        'Bucket': bucket_name,
        'Key': "mytest/post_test1.txt"
    },
    ExpiresIn=60,
)
```

<div class="content-ad"></div>

아래 URL은 쿼리 문자열에 추가 필드를 포함한 문자열이어야 합니다:

```js
https://testjorzel.s3.amazonaws.com/mytest/post_test1.txt?X-Amz-Algorithm=AWS4-HMAC-SHA256&X-Amz-Credential=AKIAU5USI2VZ3RIF3L5V%2F20240403%2Feu-central-1%2Fs3%2Faws4_request&X-Amz-Date=20240403T203856Z&X-Amz-Expires=60&X-Amz-SignedHeaders=host&X-Amz-Signature=25b531d9eb1ed01afcaaa1ce61fddf36720833e04a239d0ab0c6fa78ce308fa1
```

이를 사용하여 파일을 다운로드할 수 있습니다 (request.get을 실행함).

```js
import requests

def download_file(url):
    response_get = requests.get(url)
    print(f"다운로드, HTTP 코드: {response_get.status_code}, 내용: '{response_get.text}'")
    with open("presigned_get.txt", mode="wb") as file:
        file.write(response_get.content)

download_file(presigned_get)
```

<div class="content-ad"></div>

파일은 저희 디스크에 presigned_get.txt 이름으로 저장되어야 하며, 응답은 다음과 같아야 합니다:

```js
다운로드, HTTP 코드: 200, 내용: 'My test put/get/post'
```

# PUT을 위한 Presigned URL

마지막 케이스는 단일 파일을 업로드하기 위한 presigned PUT URL을 생성하는 것입니다 (키: mytest/presigned_put.txt 아래). GET과 비슷하게 동작할 것입니다.

<div class="content-ad"></div>


bucket_name = "testjorzel"
prefix = "mytest/"
presigned_put = s3_client.generate_presigned_url(
    'put_object',
    Params={
        'Bucket': bucket_name,
        'Key': prefix + "presigned_put.txt"
    },
    ExpiresIn=60,
)


The `presigned_put` will also be a string:


[https://testjorzel.s3.amazonaws.com/mytest/presigned_put.txt?X-Amz-Algorithm=AWS4-HMAC-SHA256&X-Amz-Credential=AKIAU5USI2VZ3RIF3L5V%2F20240403%2Feu-central-1%2Fs3%2Faws4_request&X-Amz-Date=20240403T204459Z&X-Amz-Expires=60&X-Amz-SignedHeaders=host&X-Amz-Signature=ed117f655fc007d1572618474b9fc96f1a1820ec705317a6954c6a78730fd769](https://testjorzel.s3.amazonaws.com/mytest/presigned_put.txt?X-Amz-Algorithm=AWS4-HMAC-SHA256&X-Amz-Credential=AKIAU5USI2VZ3RIF3L5V%2F20240403%2Feu-central-1%2Fs3%2Faws4_request&X-Amz-Date=20240403T204459Z&X-Amz-Expires=60&X-Amz-SignedHeaders=host&X-Amz-Signature=ed117f655fc007d1572618474b9fc96f1a1820ec705317a6954c6a78730fd769)


Now we can upload a file using `requests.put` function.


<div class="content-ad"></div>

```js
import requests

filepath = "test.txt"

def upload_file(filepath, presigned_put):
    with open(filepath, 'rb') as f:
        response_put = requests.put(presigned_put, data=f)
        print(f"파일 업로드, HTTP 상태 코드: {response_put.status_code}, 내용: '{response_put.text}'")

upload_file(filepath, presigned_put)
```

위 코드의 실행 결과는:

```js
파일 업로드, HTTP 상태 코드: 200, 내용: ''
```

이 파일은 'testjorzel' 버킷에서 찾을 수 있습니다.

<div class="content-ad"></div>

<img src="/assets/img/2024-05-20-HowtoutilizeAmazonS3presignedURLs_4.png" />

# 결론

이 짧은 가이드에서 우리는 Amazon S3의 사전 서명된 URL의 다양성을 탐구하고, 해당 사용법을 파이썬으로 실제 예제를 통해 보여주었습니다. 단일 파일 및 여러 파일에 대해 둘 다 수행할 수 있는 방법을 보여주었으며, 이는 AWS 자격 증명을 응용 프로그램에 포함시키지 않거나 사용자에게 민감한 AWS 자격 증명을 공유하지 않고 S3에서 파일 업로드 또는 다운로드를 활성화하고자 하는 시나리오에서 특히 유용합니다.

이 주제를 다룬 코드베이스는 여기에서 찾을 수 있습니다.