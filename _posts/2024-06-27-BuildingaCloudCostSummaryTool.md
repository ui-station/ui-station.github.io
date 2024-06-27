---
title: "클라우드 비용 요약 도구 만들기 가이드"
description: ""
coverImage: "/assets/img/2024-06-27-BuildingaCloudCostSummaryTool_0.png"
date: 2024-06-27 19:29
ogImage: 
  url: /assets/img/2024-06-27-BuildingaCloudCostSummaryTool_0.png
tag: Tech
originalTitle: "Building a Cloud Cost Summary Tool"
link: "https://medium.com/dev-genius/building-a-cloud-cost-summary-tool-94c2491c654c"
---


우리 회사에서는 클라우드 제공 업체로 Azure와 AWS를 사용하고 있어요. 두 플랫폼 모두 상당히 포괄적인 비용 분석 UI를 제공하지만, 재무 및 운영 팀 구성원들이 각 프로젝트의 이번 달 실제 비용과 예측을 독립적으로 확인하고 이전 달과 비교하기가 다소 어려웠어요.

그래서 양 제공자로부터 이 데이터를 가져와 프로젝트별로 집계하고 노션 페이지에 요약하는 도구를 만들기로 결정했어요.

# Azure 비용 가져오기

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

작은 Go 애플리케이션을 시작하여 테넌트 내 모든 Azure 구독 및 비용을 가져 오고자했습니다. 또한 각 구독에 액세스 권한을 가진 사용자 목록을 가져 오고 싶었습니다.

이를 위해 azure-sdk-for-go를 사용했는데, 구체적으로 azidentity, armsubscription 및 armauthorization 패키지를 사용했습니다. AzureAuthenticationService 및 AzureSubscriptionService를 구축하여 모든 Azure 구독과 해당 사용자 및 역할을 나열할 수 있도록했습니다.

이 두 서비스의 사용 예제로 다음 코드는 모든 구독 및 해당 사용자 및 역할을 나열하는 방법을 보여줍니다:

```js
func main() {
 ctx := context.Background()

 cred, err := azidentity.NewDefaultAzureCredential(nil)
 if err != nil {
  fmt.Println("[ERROR] Failed to authenticate with azure with error: " + err.Error())
  return
 }

 subscriptionService, err := AzureApi.NewAzureSubscriptionService(cred)
 if err != nil {
  fmt.Println("[ERROR] Failed to instatiate subscription client with error: " + err.Error())
  return
 }

 subscriptions, err := subscriptionService.ListSubscriptions(ctx)
 if err != nil {
  fmt.Println("[ERROR] Failed to get subscription with error: " + err.Error())
  return
 }

 for _, sub := range subscriptions {
  authService, err := AzureApi.NewAzureAuthenticationService(cred, sub.SubscriptionID)
  if err != nil {
   fmt.Println("[ERROR] Failed to instatiate subscription client with error: " + err.Error())
   return
  }
  roles, err := authService.ListUserRoleAssignments(ctx)
  if err != nil {
   fmt.Println("[ERROR] Failed to instatiate subscription client with error: " + err.Error())
   return
  }
  fmt.Println("------------------------------------------------")
  fmt.Printf("Users for Subscription %s\n", sub.DisplayName)
  for _, role := range roles {
   fmt.Printf("%s %s\n", role.UserDisplayName, role.RoleName)
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

비용 관리 패키지인 armcostmanagement을 사용하여 필요한 정보를 검색할 수 있습니다.

한편, 필요한 것을 모두 처리하는 오픈 소스 .NET 도구인 azure-cost-cli를 찾았습니다. 이 도구는 매우 완전한 JSON 보고서를 출력하는 것뿐만 아니라, 꽤 멋진 마크다운 출력도 가능했습니다. 그래서 완전히 처음부터 개발하는 대신에 이것을 사용하기로 결정했습니다.

# Notion 보고서

Notion은 Notion 워크스페이스에서 기존 콘텐츠를 생성하고 변경할 수 있게 해주는 좋은 REST API를 제공합니다. API 및 해당 콘텐츠에 접근하려면 API 키가 필요합니다. 이를 위해 통합을 만들고 필요한 권한을 부여해야 합니다. (여기에서 자세히 설명되어 있습니다).

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

다음은 노션에서 만든 내용입니다:

- 각 공급업체에 대한 합계 집계가 포함된 주요 요약 페이지
- 각 공급업체별 페이지는 각 계정/구독에 대한 상세 정보가 포함되어 있습니다.
- 계정/구독별 상세한 일별 정보가 포함된 페이지

![이미지](/assets/img/2024-06-27-BuildingaCloudCostSummaryTool_1.png)

![이미지](/assets/img/2024-06-27-BuildingaCloudCostSummaryTool_2.png)

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

<img src="/assets/img/2024-06-27-BuildingaCloudCostSummaryTool_3.png" />

Azure와 AWS 페이지 모두 드릴 다운 세부 정보가 Notion 데이터베이스 구성요소에 저장되어 있었습니다. 그런 후 DB ID가 내 Go 어플리케이션으로 전달되어 구독 레벨이 업데이트된 정보를 매일 행마다 추가하거나 업데이트할 수 있도록 했습니다:

- 이번 달 초부터 현재 날짜까지의 실제 비용
- 현재 날짜부터 이번 달의 마지막 날까지의 예측 비용
- 지난 달 총 비용
- 소유권 데이터

또한 각 행의 일별 세부 정보를 포함하는 일일 세부 정보 패널도 있습니다:

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

- 하루당 비용
- 향후 일일 비용 예측
- 서비스 유형별 실제 비용
- 위치별 실제 비용
- 자원 그룹별 실제 비용
- 이상 징후 감지 설명

API를 사용하기 위해 go-notion이라는 오픈 소스 라이브러리를 사용했어요. 그런데 그 주위에 notion-client.go 래퍼를 구현해야 했어요. 이것은 AppendBlockChild 메서드에 필요한 누락된 After 매개변수 때문이었어요. 페이지의 끝이 아니라 항상 상단에 내용을 추가하기 위해 필요한 것이었죠 (소스 저장소로의 보류 중인 풀 리퀘스트).

또한, notion-service.go도 구현되어 이 페이지에서 모든 콘텐츠 생성을 처리했어요.

스크린샷에서도 알 수 있듯이 데이터베이스 뷰는 프로젝트 소유자와 내부 프로젝트 이름에 대한 정보도 포함하고 있어요. 이 정보는 다른 노션 데이터베이스에 보관되어 있으며, 응용 프로그램은 자동으로 두 데이터베이스 간의 관계 열을 업데이트하여 이 필드가 항상 최신 상태로 유지됩니다.

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

최종적으로 Go 어플리케이션은 현재 및 과거 비용 및 이상 징후 JSON 보고서를 읽는 명령줄 도구로 변형되어, 필요한 집계를 수행하고 해당 노션 페이지를 생성/업데이트합니다.

Go 어플리케이션을 호출하려면 다음과 같은 정보를 전달해야 합니다:

- Azure 구독 ID 및 이름
- 노션 API 비밀 키
- 노션 구독 및 소유자 데이터베이스 ID
- 현재 및 지난 달의 JSON 비용 보고서
- 이상 징후 JSON 보고서

예를 들어, 호출은 다음과 같이 보일 겁니다:

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

```bash
go run ./cmd/cost-analyser/main.go -id "$SUBSCRIPTION_ID" -name "$SUBSCRIPTION_NAME" -secret "$NOTION_TOKEN" -subscriptionsCostDatabaseId "$NOTION_DATABASE_ID" -subscriptionsOwnerDatabaseId "$NOTION_OWNER_DATABASE_ID" -inputActualReportJson "$CURR_COST_DATA" -inputPastReportJson "$PAST_COST_DATA" -anomaliesJson "$ANOMALIES_COST_DATA"
```

이는 Azure의 각 구독에 대해 호출해야 하지만, 이에 대해 자세히는 아래의 Github Actions 섹션을 참조해주세요.

# AWS 비용 가져오기

AWS 비용을 가져오기 위해 aws-sdk-go-v2 라이브러리를 사용한 또 다른 작은 Go 앱이 만들어졌습니다.


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

다음은 AWS 계정과 해당 비용을 가져오는 데 책임이 있는 aws_cost_service.go를 사용했습니다.

이후 비용 보고서를 우리 노션 서비스 이전에 사용한 동일한 구조로 변환하여 응용 프로그램은 Azure에 표시된 것과 유사한 AWS를 위한 노션 페이지를 작성합니다. aws-cost-retrieval 코드는 여기에서 찾을 수 있습니다.

이 경우 하루에 한 번만 애플리케이션을 실행하면 모든 AWS 계정을 검토하고 한 번에 적절한 데이터 업데이트를 생성합니다. 다시 한 번 이 내용은 아래의 Github Actions 섹션에서 자세히 설명하겠습니다.

# 요약 페이지 및 Slack

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

위의 스크린샷에서 확인할 수 있듯이 매일 업데이트해야 하는 노션 요약 페이지가 있습니다. 또한 매주 제공자별로 요약된 집계 비용 정보를 포함하는 슬랙 채널 알림이 필요했습니다.

이를 달성하기 위해 또 다른 작은 Go 애플리케이션인 slack-cost-summary를 개발했습니다.

코드 자체가 매우 명확합니다. 이 애플리케이션은 Azure의 구독 비용을 아티팩트 폴더 내의 로컬 파일에서 읽은 다음 환경 변수를 통해 AWS의 비용을 읽어 공급자별로 비용을 집계하고 현재 요일이 구성된 요일과 일치하는 경우 슬랙 알림을 보냅니다.

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

# Github 작업

우리가 모든 필요한 요소를 갖추고 Notion에 보고서를 자동으로 추가하는 작업을 자동화하기만 하면 되었습니다. 이를 GitHub 워크플로에 구성된 일일 작업으로 설정하는 것이 필요했습니다.

![이미지](/assets/img/2024-06-27-BuildingaCloudCostSummaryTool_5.png)

Azure의 경우 비용 보고서를 가져오기 위해 기존 도구를 사용했습니다. 이 도구는 구독 수준에서 작동했기 때문에 먼저 모든 구독을 가져와야 했고, 그런 다음 각각의 구독을 위해 도구를 실행해야 했습니다 (현재 월용으로 3회, 이전 달용으로 1회, 그리고 이상 징후 보고서용으로 1회). 따라서 구독 목록을 가져오는 과정은 다음과 같습니다:

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
get-list-of-subscriptions:
    runs-on: ubuntu-latest
    steps:
      - name: Azure 로그인
        uses: azure/login@v2
        with:
          client-id: ${ secrets.AZURE_CLIENT_ID }
          tenant-id: ${ secrets.AZURE_TENANT_ID }
          allow-no-subscriptions: true

      - name: 구독 목록 가져오기
        id: setMatrix
        run: echo "subscriptions={\"subscription\":$(az account list --query '[].{Id:id,Name:name}' --output json | jq -c)}" >> $GITHUB_OUTPUT
    outputs:
      matrix: ${ steps.setMatrix.outputs.subscriptions }
```

각 구독에 대해 작업은 구독 ID 및 구독명이 포함된 구독 개체를 출력합니다.

이 작업과 병렬로 run-aws-costs-cli 작업에서는 AWS 데이터 수집기 Go 응용프로그램이 실행되어 모든 AWS 데이터를 업데이트합니다:

```js
run-aws-costs-cli:
    runs-on: ubuntu-latest
    outputs:
      subscriptions: ${ steps.setAwsSubscriptions.outputs.awsSubscriptions }
    steps:
      - name: 저장소 복제
        uses: actions/checkout@v4
      - name: AWS 자격 증명 구성
        uses: aws-actions/configure-aws-credentials@v4
        with:
          role-to-assume: ${ env.AWS_ARN_BILLING_COLECTOR }
          role-session-name: GitHub_to_AWS_via_FederatedOIDC
          aws-region: ${ env.AWS_REGION }

      - name: Go 1.21.x 설정
        uses: actions/setup-go@v5
        with:
          go-version: 1.21.x

      - name: Go 종속성 설치
        run: go get ./...

      - name: AWS 비용 실행
        id: setAwsSubscriptions
        env:
          NOTION_TOKEN: ${ secrets.NOTION_TOKEN }
          NOTION_DATABASE_ID: ${ secrets.NOTION_AWS_DATABASE_ID }
          NOTION_OWNER_DATABASE_ID: ${ secrets.NOTION_OWNER_DATABASE_ID }
        run: |
          echo "awsSubscriptions=$(go run ./cmd/aws-cost-retrieval/main.go -secret "$NOTION_TOKEN" -subscriptionsCostDatabaseId "$NOTION_DATABASE_ID" -subscriptionsOwnerDatabaseId "$NOTION_OWNER_DATABASE_ID" | jq -c)" >> $GITHUB_OUTPUT
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

마지막에는 각 AWS 계정의 비용 보고서 목록이 awsSubscription 배열에 저장될 것입니다.

get-list-of-subscriptions에 의해 출력된 각 Azure 구독마다, 다음 단계 구성으로 run-azure-cost-cli 작업이 생성됩니다:

```js
run-azure-cost-cli:
    name: Azure 구독용 비용 가져오기
    runs-on: ubuntu-latest
    needs: [get-list-of-subscriptions]
    strategy:
      max-parallel: 1
      matrix: ${fromJson(needs.get-list-of-subscriptions.outputs.matrix)}
    steps:
      - name: Azure 로그인
        uses: azure/login@v2
        with:
          client-id: ${secrets.AZURE_CLIENT_ID}
          tenant-id: ${secrets.AZURE_TENANT_ID}
          allow-no-subscriptions: true
      - name: Azure 비용 CLI 설치
        run: dotnet tool install -g azure-cost-cli --no-cache
      - name: JSON 비용 DATA 가져오기
        id: setCostData
        run: |
          day=$(date +%d)
          if [ "$day" = "01" ]; then
            from_two_months=$(date -d "2 month ago" "+%Y/%m/01")
            to_two_months=$(date -d "$(date -d "month ago" "+%Y/%m/01") - 1 day" "+%Y/%m/%d")
            from_last_month=$(date -d "1 month ago" "+%Y/%m/01")
            to_last_month=$(date -d "$(date "+%Y/%m/01") - 1 day" "+%Y/%m/%d")
            
            PREV_DATA=$(azure-cost -o json --subscription ${matrix.subscription.Id} -t Custom --from ${from_two_months} --to ${to_two_months} | jq -c) 
            DATA=$(azure-cost -o json --subscription ${matrix.subscription.Id} -t Custom --from ${from_last_month} --to ${to_last_month} | jq -c)
            
            echo "prevDataJson=$PREV_DATA" >> $GITHUB_OUTPUT
            echo "dataJson=$DATA" >> $GITHUB_OUTPUT
          else
            PREV_DATA=$(azure-cost -o json --subscription ${matrix.subscription.Id} -t TheLastBillingMonth | jq -c)
            DATA=$(azure-cost -o json --subscription ${matrix.subscription.Id} | jq -c)
              
            echo "prevDataJson=$PREV_DATA" >> $GITHUB_OUTPUT
            echo "dataJson=$DATA" >> $GITHUB_OUTPUT
          fi
          ANOMALIES=$(azure-cost detectAnomalies -o json --subscription ${matrix.subscription.Id} | jq -c)
          echo "anomaliesDataJson=$ANOMALIES" >> $GITHUB_OUTPUT
      - name: Markdown 형식으로 포맷팅
        run: |
          if echo '${steps.setCostData.outputs.dataJson}' | jq -c -r '.cost'| jq -e '. == []'; then
            echo "> 이 시기에는 ${matrix.subscription.Name} 구독에 대한 비용이 없습니다." >> $GITHUB_STEP_SUMMARY
          else
            day=$(date +%d)
            if [ "$day" = "01" ]; then
              from_last_month=$(date -d "1 month ago" "+%Y/%m/01")
              to_last_month=$(date -d "$(date "+%Y/%m/01") - 1 day" "+%Y/%m/%d")
              azure-cost -o markdown --subscription ${matrix.subscription.Id} -t Custom --from ${from_last_month} --to ${to_last_month} >> $GITHUB_STEP_SUMMARY
            else
              azure-cost -o markdown --subscription ${matrix.subscription.Id} >> $GITHUB_STEP_SUMMARY
            fi
          fi
      - uses: actions/checkout@v4
      - name: Go 1.21.x 설정
        uses: actions/setup-go@v5
        with:
          go-version: 1.21.x
      - name: Go 종속성 설치
        run: go get ./...
      - name: Notion으로 배포
        env:
          NOTION_TOKEN: ${secrets.NOTION_TOKEN}
          NOTION_DATABASE_ID: ${secrets.NOTION_DATABASE_ID}
          SUBSCRIPTION_ID: ${matrix.subscription.Id}
          SUBSCRIPTION_NAME: ${matrix.subscription.Name}
          NOTION_OWNER_DATABASE_ID: ${secrets.NOTION_OWNER_DATABASE_ID}
        run: |
          CURR_COST_DATA="$(echo '${steps.setCostData.outputs.dataJson}' | base64)"
          PAST_COST_DATA="$(echo '${steps.setCostData.outputs.prevDataJson}' | base64)"
          ANOMALIES_COST_DATA="$(echo '${steps.setCostData.outputs.anomaliesDataJson}' | base64)"
          go run ./cmd/cost-analyser/main.go -id "$SUBSCRIPTION_ID" -name "$SUBSCRIPTION_NAME" -secret "$NOTION_TOKEN" -subscriptionsCostDatabaseId "$NOTION_DATABASE_ID" -subscriptionsOwnerDatabaseId "$NOTION_OWNER_DATABASE_ID" -inputActualReportJson "$CURR_COST_DATA" -inputPastReportJson "$PAST_COST_DATA" -anomaliesJson "$ANOMALIES_COST_DATA" >> ${matrix.subscription.Id}.json
      - name: Output 업로드
        uses: actions/upload-artifact@v4
        with:
          name: artifact-${matrix.subscription.Id}
          path: ${matrix.subscription.Id}.json
          if-no-files-found: warn
```

Azure API의 시간 초과 또는 요율 제한 문제로 인해 병렬로 더 많은 작업을 실행할 경우에도 한 번에 1개의 작업을 실행합니다. 주요 단계는 다음과 같습니다:

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

- Get JSON 비용 데이터는 이전 및 현재 일일 비용 보고서를 얻을 것입니다.
- Markdown 형식에서 현재 월 보고서를 얻어 Markdown 형식으로 포맷팅하여 GITHUB_STEP_SUMMARY에 출력하여 GitHub 작업 요약 페이지에서 볼 수 있게 합니다.
- Notion에 배포는 특정 구독을 위한 보고서를 업데이트하기 위해 Notion Go 애플리케이션을 실행합니다.
- 출력 업로드는 이후 summarise-and-notify 단계에서 사용할 비용 보고서 출력을 아티팩트로 저장합니다.

summarise-and-notify 작업은 모든 run-azure-cost-cli 및 run-aws-costs-cli 작업이 완료된 후에만 실행되며, summary 및 slack 알림을 위해 작동합니다:

```js
summarise-and-notify:
    runs-on: ubuntu-latest
    needs: [run-azure-cost-cli, run-aws-costs-cli]
    env:
      SUBSCRIPTIONS: ${needs.run-aws-costs-cli.outputs.subscriptions}
      SLACK_CHANNEL_WEBHOOK: ${secrets.SLACK_CHANNEL_WEBHOOK}
      TRIGGER_MESSAGE_DAY: ${vars.TRIGGER_MESSAGE_DAY}
      NOTION_AWS_PAGE_URL: ${vars.NOTION_AWS_PAGE_URL}
      NOTION_AZURE_PAGE_URL: ${vars.NOTION_AZURE_PAGE_URL}
      NOTION_TOKEN: ${secrets.NOTION_TOKEN}
      NOTION_COST_SUMMARY_PAGE: ${vars.NOTION_COST_SUMMARY_PAGE}
    steps:
      - uses: actions/checkout@v4
      - name: Setup Go 1.21.x
        uses: actions/setup-go@v5
        with:
          go-version: 1.21.x
      - name: Install go dependencies
        run: go get ./...
      - name: "Download matrix outputs"
        uses: actions/download-artifact@v4
        with:
          path: artifact
          pattern: artifact-*
          merge-multiple: true
      - name: "Notify to slack"
        run: |
          go run ./cmd/slack-cost-summary
```

마지막으로 실패한 작업을 자동으로 다시 시도할 수 있도록 re-run-azure-cost-cli 작업이 추가되었습니다. 병렬 작업을 실행하지 않더라도 때로는 azure API가 실패할 수 있습니다. 따라서 임시적인 azure API 실패를 제거하는 데 충분한 5번의 시도로 실패한 작업을 다시 시도하는 메커니즘을 추가했습니다:

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


재실행-azure-cost-cli:
    이름: 실패한 작업 다시 실행하는 워크플로우 호출
    needs: run-azure-cost-cli
    if: failure() && fromJSON(github.run_attempt) < 5
    runs-on: ubuntu-latest
    steps:
      - env:
          GH_REPO: ${ github.repository }
          GH_TOKEN: ${ github.token }
          GH_DEBUG: api
        run: gh workflow run rerun.yml -F run_id=${ github.run_id }



이름: 실패한 작업 다시 실행하는 워크플로우

permissions:
  actions: write

on:
  workflow_dispatch:
    inputs:
      run_id:
        required: true
jobs:
  rerun:
    runs-on: ubuntu-latest
    steps:
      - name: ${ inputs.run_id } 다시 실행
        env:
          GH_REPO: ${ github.repository }
          GH_TOKEN: ${ github.token }
          GH_DEBUG: api
        run: |
          gh run watch ${ inputs.run_id } > /dev/null 2>&1
          gh run rerun ${ inputs.run_id } --failed
