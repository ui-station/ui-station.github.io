---
title: "구글 드라이브를 무료 클라우드 저장 공간 솔루션으로 활용하여 머신 러닝 데이터 동기화를 자동화하는 방법"
description: ""
coverImage: "/assets/img/2024-06-19-HowdidIuseGoogleDriveasafreecloudstoragesolutiontoautomatesyncingmyMachineLearningdata_0.png"
date: 2024-06-19 11:59
ogImage:
  url: /assets/img/2024-06-19-HowdidIuseGoogleDriveasafreecloudstoragesolutiontoautomatesyncingmyMachineLearningdata_0.png
tag: Tech
originalTitle: "How did I use Google Drive as a free cloud storage solution to automate syncing my Machine Learning data?"
link: "https://medium.com/@shuchawl/how-did-i-use-google-drive-as-a-free-cloud-storage-solution-to-automate-syncing-my-machine-learning-e1f288ebeab9"
---

Google Drive은 문서와 미디어를 저장하는 데 탁월한 도구이지만, 상상해보세요: 어떻게 하면 앱의 데이터를 저장하는 데 사용할 수 있을까요? 네, 저도 구름에 멀티플레이어 게임 데이터를 안전하고 무료로 저장하는 방법을 고민하고 있었습니다. 이 기사에서는 기계 학습을 위해 게임 데이터를 Google Drive에 저장한 방법에 대해 설명하겠습니다.

![이미지가 여기에 표시됩니다.](/assets/img/2024-06-19-HowdidIuseGoogleDriveasafreecloudstoragesolutiontoautomatesyncingmyMachineLearningdata_0.png)

📢 안녕하세요, 이 기사에서 언급된 서비스 중 어느 것도 스폰서로서 제작되지 않았습니다. 제가 애플리케이션에서 직접 사용해보며 개발자로서의 경험을 공유하고자 합니다.

📢 이 기사는 구글 서비스와의 인증을 위해 서비스 계정을 사용하는 데 어느 정도 익숙한 것으로 가정합니다. 서비스 계정 및 해당 링크를 통해 어떻게 생성하는지에 대해 더 알아볼 수 있습니다.

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

# 🤔 왜 구글 드라이브를 사용하게 된 걸까요?

"The Strategists"의 승자를 예측하기 위한 머신 러닝 모델을 개발하는 도중, 모든 게임 데이터를 로컬 머신에 저장했습니다. 그 당시에는 그 방식이 합리적으로 느껴졌지만, 도커를 사용하여 게임을 컨테이너화하고 클라우드에 배포하기 시작하면서 몇 가지 의문이 생겼습니다.

훈련된 모델을 도커 이미지에 포함해야 할까요? 게임을 한 번씩 진행할 때마다 모델을 다시 훈련하기 때문에 게임 데이터를 도커 이미지에 포함해야 할까요? 게임 데이터를 도커 이미지에 저장하는 것이 안전한 일일까요? 이러한 질문들을 고민하면서, 보안 위험이 있기 때문에 모델과 게임 데이터를 함께 도커 이미지에 포함시키지 말아야겠다는 결론에 도달했습니다.

그래도 "The Strategists"를 배포할 때, 모든 게임 데이터와 예측 모델을 백엔드 도커 이미지에 포장해서 배포했습니다. 배포된 컨테이너에서 주기적으로 새로운 게임 데이터를 추출하는 아이디어도 고안했었죠. 이미 알 수 있겠지만, 이는 확장 가능한 해결책이 아니었습니다. 배포 단계에서 모델을 포장하지 않고 프로그래밍적으로 게임 데이터를 다운로드하고 업로드하며 훈련된 모델을 내보내야 한다는 것을 알았습니다. 그렇다면 어떻게 해야 할까요?

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

이번 사용 사례에서는 Amazon S3 및 Google Cloud Storage와 같은 서비스를 탐색해 보았어요. 이전 글을 읽은 분들은 저의 의도를 알 거예요. 요금을 청구하지 않는 서비스를 찾았고, 호기심 많은 개발자로서 Google Drive를 사용하기로 결정했어요. Google Drive API를 사용해보고 싶었는데, 이는 Google Drive를 애플리케이션의 데이터 저장소로 사용하기 위한 기능을 프로토타입화하는 완벽한 기회였어요.

Google Drive를 사용하여 프로덕션에 준비된 애플리케이션을 개발하는 것은 표준 산업 관행이 아니라는 점을 강조해야 해요. Amazon S3와 같은 서비스는 오브젝트 잠금 및 ID 및 액세스 관리를 포함한 더 넓은 범위의 기능을 제공해요. 게다가, Google Drive의 무료 계층은 15GB의 저장 공간으로 제한되어 있어요.

# 🛠️ 게임 데이터 동기화 구현은 어떻게 이루어졌을까요?

우선, 제가 어떻게 게임 데이터를 동기화하는지에 대해 이야기해볼게요. 제 로컬 설정에서는 The Strategists의 머신 러닝 워크플로를 설명해서 서버 시작 시 모델을 훈련시킨 다음, SpringBoot 기반의 백엔드 서비스는 각 게임 세션 후에 다시 훈련시켰어요. 이 훈련은 플레이어 투자 패턴을 CSV 파일로 내보낸 다음, 예측 모델 디렉토리에 게임 상태를 내보낸 후에 발생했어요.

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

데이터 내보내기 디렉토리에는 내보낸 CSV 파일이 모두 저장되어 있었기 때문에 훈련 스크립트는 이 데이터를 모두 로드하고 해당 데이터를 사용하여 모델을 훈련했습니다. 첫 번째 과제는 서버가 시작될 때 이 디렉토리를 채우는 것이었습니다. 훈련 스크립트가 예측 모델을 내보내기 위한 작업이 가능하도록 하려면 구글 드라이브 폴더에 있는 기존 CSV 파일을 업로드했습니다. 이 "Downloads" 폴더는 이제 게임 서버에서 사용 가능한 게임 데이터를 찾기 위해 참조되는 진실의 원천으로 제공됩니다.

이 과제에 대처하기 위해 "Downloads" 폴더에서 이 CSV 파일들을 다운로드하는 Python 유틸리티를 아래 스니펫처럼 작성했습니다. 실제 코드는 여기에서 확인할 수 있습니다.

```js
# Google 서비스에 연결하기 위한 서버 자격 증명 생성
credentials = Credentials.from_service_account_file(filename="<SERVICE_ACCOUNT_FILE의_경로>")

# Google 드라이브 서비스 초기화
service = discovery.build("drive", "v3", credentials=credentials)

# "Downloads" 폴더에 있는 모든 CSV 파일 나열
all_csv_files, page_token = [], None
while True:

  # 현재 페이지 토큰에 대해 csv 파일 나열
  response = (
    service.files().list(
      q=f"(mimeType='text/csv') and ('<DOWNLOADS_폴더_ID>' in parents)",
      spaces="drive",
      fields="nextPageToken, files(id, name)",
      pageToken=page_token
    ).execute()
  )

  # 리스트에 csv 파일 추가
  csv_files, page_token = response.get("files", []), response.get("nextPageToken", None)
  all_csv_files.extend(csv_files)

  # 더 많은 csv 파일이 있는지 확인
  if page_token is None:
    break

# 나열된 모든 csv 파일 다운로드
for i, csv_file in enumerate(all_csv_files):

  # csv 파일 메타데이터 가져오기
  csv_file_id, csv_file_name = csv_file.get("id"), csv_file.get("name")

  # csv 파일 바이트 가져오기
  csv_bytes = io.BytesIO()
  downloader = MediaIoBaseDownload(file, request)
  downloaded = False
  while downloaded is False:
    status, downloaded = downloader.next_chunk()

  # 파일 내용 저장
  export_file_path = os.path.join("<데이터_디렉토리>", csv_file_name)
  with open(export_file_path, "wb") as csv:
    csv.write(csv_bytes.getvalue())
```

구글 서비스 계정의 이메일 주소가 최소한 "뷰어" 권한으로 이 CSV 파일에 액세스할 수 있도록 이 "Downloads" 폴더를 공유하도록 반드시 확인해주세요.

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

데이터 다운로드 문제를 해결했으니, 이제 새 CSV 파일을 Google 드라이브에 업로드하는 방법을 구현하기 시작했습니다. 이를 위해 "Uploads"라는 폴더를 만들었고, CSV 파일의 다운로드와 업로드를 위해 별도의 폴더를 유지했습니다. 새 CSV 파일이 다운로드 폴더로 이동하기 전에 먼저 제가 확인할 수 있도록 했습니다.

다음 코드 조각은 이 "Upload" 폴더로 CSV 파일을 업로드한 방법입니다. 실제 코드는 여기에서 확인하실 수 있습니다.

```js
# 모든 로컬 csv 파일 나열
local_csv_files = []
for file_name in os.listdir("<DATA_DIRECTORY>"):
  if file_name.endswith(".csv"):
    local_csv_files.append(file_name)

# 나열된 모든 로컬 csv 파일 다운로드
for i, local_csv_file in enumerate(local_csv_files):

  # 업로드할 CSV 파일을 업로드 폴더에 업로드
  local_csv_file_path = os.path.join("<DATA_DIRECTORY>", local_csv_file)
  mimeType = "text/csv"

  body = {
    "name": local_csv_file,
    "mimeType": mimeType,
    "parents": ["<UPLOADS_FOLDER_ID>"]
  }
  media = MediaFileUpload(local_csv_file_path, mimetype=mimeType)
  file = service.files().create(body=body, media_body=media, fields="id")
```

스크립트가 새 CSV 파일을 업로드할 수 있도록 Google 서비스 계정 이메일 주소에 적어도 "편집자" 권한으로 이 "Uploads" 폴더를 공유해야 합니다.

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

실제 코드 구현에서는 서버 데이터 디렉토리에 있는 CSV 파일만 다운로드했습니다. 업로드에 대해선 "다운로드" 또는 "업로드" 구글 드라이브 폴더에 이미 존재하지 않은 CSV 파일만 업로드했습니다.

이 기사가 여러분이 응용 프로그램의 무료 클라우드 저장소로 Google 드라이브를 어떻게 사용하는지 이해하는 데 도움이 되었으면 좋겠습니다. The Strategists의 개발을 계속 따르고 싶다면 제 블로그를 구독해보세요. 프로젝트에 기여하는 것을 고려해주시고, 다음 링크를 통해 GitHub의 프로젝트 저장소에 액세스할 수 있습니다.

제 포트폴리오를 확인해보세요. 시간 내어 이 기사를 읽어주셔서 감사합니다.
