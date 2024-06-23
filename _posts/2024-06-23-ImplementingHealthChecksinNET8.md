---
title: "NET 8에서 헬스 체크 구현하는 방법"
description: ""
coverImage: "/assets/img/2024-06-23-ImplementingHealthChecksinNET8_0.png"
date: 2024-06-23 22:01
ogImage: 
  url: /assets/img/2024-06-23-ImplementingHealthChecksinNET8_0.png
tag: Tech
originalTitle: "Implementing Health Checks in .NET 8"
link: "https://medium.com/@jeslurrahman/implementing-health-checks-in-net-8-c3ba10af83c3"
---


<img src="/assets/img/2024-06-23-ImplementingHealthChecksinNET8_0.png" />

Health checks are critical for monitoring the status of your applications and services. They offer a fast and automated method to ensure that your application's dependencies and components are functioning correctly.

This article will delve into how to implement health checks in a .NET 8 web application.

## Why Health Checks in ASP.NET Applications?

<div class="content-ad"></div>

헬스 체크는 애플리케이션 내에서 문제점을 사전에 식별할 수 있게 도와주어 사용자에 영향을 미치기 전에 그 문제를 해결할 수 있는 기회를 주는 중요한 도구입니다. 정기적으로 애플리케이션 구성 요소들의 건강 상태를 확인하는 것은 더욱 신뢰성이 높고 견고한 시스템을 유지할 수 있도록 도와줍니다.

ASP.NET 애플리케이션을 개발할 때, 이는 종종 데이터베이스, 파일 시스템, API 등과 같은 다양한 하위 시스템에 의존합니다. 가장 흔한 시나리오 중 하나는 데이터베이스에 대한 의존성입니다. 사실 거의 모든 애플리케이션은 데이터베이스와의 원활한 상호 작용을 필요로 하며 이는 핵심 시스템 구성 요소로 작용합니다. 그러나 기존의 애플리케이션 개발은 종종 이 부분을 간과하고 데이터베이스와의 연결이 끊어지는 경우 잠재적인 고장을 야기할 수 있습니다.

예를 들어 애플리케이션이 데이터베이스에 의존하는 상황을 생각해봅시다. 다양한 이유로 데이터베이스에 대한 연결이 끊겨버리면 애플리케이션이 작동하지 않을 가능성이 높습니다. 이 상황은 헬스 체크가 왜 유용한지의 기본적인 예시이지만, 그들의 개발의 전체적인 범위를 충분히 이해하기에는 부족합니다. 따라서,

## 데이터베이스 예제를 깊게 들어가 ASP.NET 헬스 체크가 어떻게 더 큰 규모에서 중요한지 살펴보겠습니다.

<div class="content-ad"></div>

- 데이터베이스 연결을 설정하기 전에 데이터베이스의 가용성을 확인할 수 있다면 어떨까요?
— — 애플리케이션의 중요한 하위 시스템의 상태와 가용성을 사전에 확인할 수 있는 기능을 갖고 있다면 어떨까요? 데이터베이스 연결이 끊겨 갑작스럽게 애플리케이션 오류가 발생하는 대신, 가용성을 선행적으로 확인할 수 있다면 좋지 않을까요?
- 데이터베이스 가용성 시나리오를 우아하게 처리할 수 있는 애플리케이션이 있다면 어떨까요?
— — 데이터베이스에 접속할 수 없는 상황에서 사용자 친화적인 메시지를 표시하는 기능을 애플리케이션이 갖고 있다면 어떨까요? 사용자들을 혼란스럽게 만드는 암호화된 오류 메시지가 아닌 중요 구성 요소의 가용성을 원활하게 전달할 수 있다면 사용자 경험이 향상되지 않을까요?
- 가용하지 않을 때 대비해 대체 데이터베이스로 원활하게 전환할 수 있다면 어떨까요?
— — 주 데이터베이스가 가용하지 않을 경우 대비하여 대체 데이터베이스로 순조롭게 전환할 수 있는 유연성을 고려해보세요. 이는 애플리케이션의 연속성을 유지할 뿐만 아니라 사용자들이 최소한의 중단이 발생하도록 보장합니다.
- 상태 확인에 따라 대체 환경으로 스위치할 로드 밸런서를 지시할 수 있다면 어떨까요?
— — 애플리케이션의 상태를 로드 밸런서에게 전달할 수 있는 능력을 상상해보세요. 데이터베이스가 없거나 다른 심각한 문제로 인해 애플리케이션이 건강하지 않다고 판단되면 로드 밸런서는 지능적으로 트래픽을 대체 환경으로 리디렉션하여 지속적인 서비스 가용성을 보장합니다.

ASP.NET Health Checks를 통해 다음을 수행할 수 있습니다:

- 시스템의 건강 상태와 가용성을 평가합니다.
- 다른 시스템에 애플리케이션의 건강 상태를 알리기 위한 엔드포인트를 생성합니다.
- 다른 시스템의 건강 확인 엔드포인트를 사용합니다.

이러한 건강 확인 기능은 느슨하게 연결된 애플리케이션이 종속되어 있는 시스템의 건강을 알아야 하는 마이크로서비스 환경을 위해 특별히 디자인되었습니다. 그러나 다양한 하위 시스템과 인프라에 의존하는 단일 애플리케이션에서도 유용합니다.

<div class="content-ad"></div>

# .Net 8에서 건강 점검을 구현하는 방법

.NET 8에서 건강 점검 구성 방법을 두 가지로 보여드리겠습니다.

# 섹션 1: 기본 건강 점검 설정

이 섹션은 응용 프로그램의 건강 점검을 위한 기초를 구축하는 데 목적이 있습니다.

<div class="content-ad"></div>

NuGet 패키지 설치 필요
다음 NuGet 패키지가 설치되어 있는지 확인하세요:
1. Microsoft.Extensions.Diagnostics.HealthChecks

Health Checks 서비스 추가
Program.cs에 필요한 헬스 체크 서비스를 DI 컨테이너에 추가하세요:
Program.cs

```js
builder.Services.AddHealthChecks();

//HealthCheck Middleware
app.MapHealthChecks("/api/health");
```

이것들을 .NET 8 웹 API에 추가한 후, 애플리케이션을 성공적으로 실행하세요. 브라우저를 사용해 다음 엔드포인트로 이동하세요:
https://localhost:44333/swagger/feedbackservice/index.html
웹 API의 상태를 확인하려면 다음으로 이동하세요:
https://localhost:44333/api/health

<div class="content-ad"></div>

엔드포인트를 호출한 뒤에 웹 API가 "Healthy"임을 확인할 수 있습니다.

![이미지](/assets/img/2024-06-23-ImplementingHealthChecksinNET8_1.png)

이 엔드포인트를 호출하면 웹 API가 "Healthy"로 표시되는 것을 확인해야 합니다. 현재 단계에서는 애플리케이션의 전체적인 건강을 보장하기 위해 기본 건강 점검이 설정되어 있지만 부속 시스템에 대한 구체적인 건강 점검은 아직 구현되지 않았음을 알립니다.

# 섹션 2: 건강 점검으로 구현

<div class="content-ad"></div>

고객님을 위한 개인화된 건강 체크를 통한 첨단 모니터링
이 섹션에서는 .NET 8 웹 API의 중요 구성 요소를 모니터링하는 데 맞춤형 건강 체크를 구현하는 구체적인 예시를 살펴보겠습니다. 이러한 체크는 기본 설정을 뛰어넘어 응용 프로그램의 건강 상태에 대한 보다 상세하고 통찰력 있는 관점을 제공합니다.

필요한 NuGet 패키지
다음 NuGet 패키지가 설치되어 있는지 확인하세요:
1. Microsoft.Extensions.Diagnostics.HealthChecks
2. AspNetCore.HealthChecks.SqlServer
3. AspNetCore.HealthChecks.UI
4. AspNetCore.HealthChecks.UI.Client
5. AspNetCore.HealthChecks.UI.InMemory.Storage
6. AspNetCore.HealthChecks.Uris

참고: 별도의 HealthCheck.cs 파일을 만들고 모든 건강 체크 구성을 구현했습니다.

## a. 데이터베이스 건강 체크

<div class="content-ad"></div>

데이터베이스 헬스 체크는 애플리케이션의 웰빙을 모니터링하는 중요한 측면입니다. 특히 데이터 저장 및 검색을 위해 데이터베이스를 사용하는 경우에는 더욱 중요합니다. 이 헬스 체크는 데이터베이스가 연결 가능하고 쿼리에 응답할 수 있는지 확인합니다.

HealthCheck.cs

```csharp
public static void ConfigureHealthChecks(this IServiceCollection services, IConfiguration configuration)
{
    services.AddHealthChecks()
        .AddSqlServer(configuration["ConnectionStrings:DefaultConnection"], healthQuery: "select 1", name: "SQL Server", failureStatus: HealthStatus.Unhealthy, tags: new[] { "Feedback", "Database" });

    //services.AddHealthChecksUI();
    services.AddHealthChecksUI(opt =>
    {
        opt.SetEvaluationTimeInSeconds(10); //체크 간 시간(초)    
        opt.MaximumHistoryEntriesPerEndpoint(60); //체크 히스토리 최대    
        opt.SetApiMaxActiveRequests(1); //API 요청 동시성    
        opt.AddHealthCheckEndpoint("피드백 API", "/api/health"); //헬스 체크 API 맵핑    

    })
        .AddInMemoryStorage();
}
```

`configuration["ConnectionStrings:DefaultConnection"]`은 구성에서 연결 문자열을 검색하여 데이터베이스 연결 구성을 유연하게 설정할 수 있습니다.
`failureStatus: HealthStatus.Unhealthy`는 헬스 체크 실패 시 전체 헬스 상태를 건강하지 않음으로 표시해야 함을 나타냅니다.

<div class="content-ad"></div>

프로그램.cs에서 ConfigureHealthChecks()를 구성하세요.

```js
// Health Check 구성
builder.Services.ConfigureHealthChecks(builder.Configuration);

// Health Check 미들웨어
app.MapHealthChecks("/api/health", new HealthCheckOptions()
{
    Predicate = _ => true,
    ResponseWriter = UIResponseWriter.WriteHealthCheckUIResponse
});
app.UseHealthChecksUI(delegate (Options options) 
{
    options.UIPath = "/healthcheck-ui";
    options.AddCustomStylesheet("./HealthCheck/Custom.css");
});
```

결과:
Endpoint: /api/health

<img src="/assets/img/2024-06-23-ImplementingHealthChecksinNET8_2.png" />

<div class="content-ad"></div>

엔드포인트: /healthcheck-ui

![health-check-screenshot](/assets/img/2024-06-23-ImplementingHealthChecksinNET8_3.png)

## b. 원격 엔드포인트의 건강 상태 확인

다음으로, 원격 엔드포인트 및 메모리의 건강 상태를 확인하는 작업을 구현할 것입니다.
RemoteHealthCheck.cs

<div class="content-ad"></div>

```csharp
using Microsoft.Extensions.Diagnostics.HealthChecks;
using System;
using System.Net.Http;
using System.Threading;
using System.Threading.Tasks;

namespace FeedbackService.Api
{
    public class RemoteHealthCheck : IHealthCheck
    {
        private readonly IHttpClientFactory _httpClientFactory;
        public RemoteHealthCheck(IHttpClientFactory httpClientFactory)
        {
            _httpClientFactory = httpClientFactory;
        }
        public async Task<HealthCheckResult> CheckHealthAsync(HealthCheckContext context, CancellationToken cancellationToken = new CancellationToken())
        {
            using (var httpClient = _httpClientFactory.CreateClient())
            {
                var response = await httpClient.GetAsync("https://api.ipify.org");
                if (response.IsSuccessStatusCode)
                {
                    return HealthCheckResult.Healthy($"Remote endpoint is healthy.");
                }

                return HealthCheckResult.Unhealthy("Remote endpoint is unhealthy");
            }
        }
    }
}
```

이 health check은 HTTP 요청을 통해 원격 엔드포인트(예: API)의 상태를 모니터링합니다.

## b. 메모리 헬스 체크

마지막으로 API 서비스의 메모리 상태를 모니터링하는 health check을 구현해 봅시다.


<div class="content-ad"></div>

MemoryHealthCheck.cs

```js
using System;
using System.Collections.Generic;
using System.Linq;
using System.Threading;
using System.Threading.Tasks;
using Microsoft.Extensions.Diagnostics.HealthChecks;
using Microsoft.Extensions.Options;

namespace FeedbackService.Api.HealthCheck
{
    public class MemoryHealthCheck : IHealthCheck
    {
        private readonly IOptionsMonitor<MemoryCheckOptions> _options;

        public MemoryHealthCheck(IOptionsMonitor<MemoryCheckOptions> options)
        {
            _options = options;
        }

        public string Name => "memory_check";

        public Task<HealthCheckResult> CheckHealthAsync(
            HealthCheckContext context,
            CancellationToken cancellationToken = default(CancellationToken))
        {
            var options = _options.Get(context.Registration.Name);

            // Include GC information in the reported diagnostics.
            var allocated = GC.GetTotalMemory(forceFullCollection: false);
            var data = new Dictionary<string, object>()
        {
            { "AllocatedBytes", allocated },
            { "Gen0Collections", GC.CollectionCount(0) },
            { "Gen1Collections", GC.CollectionCount(1) },
            { "Gen2Collections", GC.CollectionCount(2) },
        };
            var status = (allocated < options.Threshold) ? HealthStatus.Healthy : HealthStatus.Unhealthy;

            return Task.FromResult(new HealthCheckResult(
                status,
                description: "Reports degraded status if allocated bytes " +
                    $">= {options.Threshold} bytes.",
                exception: null,
                data: data));
        }
    }
    public class MemoryCheckOptions
    {
        public string Memorystatus { get; set; }
        //public int Threshold { get; set; }
        // Failure threshold (in bytes)
        public long Threshold { get; set; } = 1024L * 1024L * 1024L;
    }
}
```

이 Health Check은 Feedback Service의 메모리 상태를 할당된 바이트를 기반으로 평가합니다.

이제 HealthCheck.cs 내부에서 RemoteHealthCheck.cs와 MemoryHealthCheck.cs를 구성합시다.

<div class="content-ad"></div>

HealthCheck.cs

```js
public static void ConfigureHealthChecks(this IServiceCollection services, IConfiguration configuration)
{
    services.AddHealthChecks()
        .AddSqlServer(configuration["ConnectionStrings:Feedback"], healthQuery: "select 1", name: "SQL 서버", failureStatus: HealthStatus.Unhealthy, tags: new[] { "Feedback", "Database" })
        .AddCheck<RemoteHealthCheck>("원격 엔드포인트 헬스체크", failureStatus: HealthStatus.Unhealthy)
        .AddCheck<MemoryHealthCheck>($"피드백 서비스 메모리 체크", failureStatus: HealthStatus.Unhealthy, tags: new[] { "피드백 서비스" })
        .AddUrlGroup(new Uri("https://localhost:44333/api/v1/heartbeats/ping"), name: "기본 URL", failureStatus: HealthStatus.Unhealthy);

    //services.AddHealthChecksUI();
    services.AddHealthChecksUI(opt =>
    {
        opt.SetEvaluationTimeInSeconds(10); // 각 체크 사이의 시간(초)    
        opt.MaximumHistoryEntriesPerEndpoint(60); // 체크 이력 최대 갯수    
        opt.SetApiMaxActiveRequests(1); // API 요청 동시성    
        opt.AddHealthCheckEndpoint("피드백 API", "/api/health"); // 헬스체크 API 매핑    

    })
        .AddInMemoryStorage();
}
```

**출력:**
Endpoint: /api/health

![ImplementingHealthChecksin.NET8.4](/assets/img/2024-06-23-ImplementingHealthChecksinNET8_4.png)

<div class="content-ad"></div>

Endpoint: /healthcheck-ui

![Health Check](https://assets/img/2024-06-23-ImplementingHealthChecksinNET8_5.png)

여기 있습니다! 웹 API 내에서 몇 가지 헬스 체크를 성공적으로 구현했습니다. 이러한 체크를 통해 응용 프로그램이 모니터링되고 웰빙이 보장되도록 할 수 있습니다. .NET 8의 헬스 체크의 힘으로 견고하고 견고한 응용 프로그램을 계속하여 만들어보세요! 😍

![Resilient Applications](https://miro.medium.com/v2/resize:fit:996/1*ECmfhzVd4avHlD_B1-gocg.gif)

<div class="content-ad"></div>

# 결론

.NET 8 애플리케이션에 건강 점검을 구현하는 것은 탄탄하고 안정적인 시스템을 구축하기 위한 중요한 단계입니다. 내장 및 사용자 정의 건강 점검을 통해 응용 프로그램과 해당 종속성의 상태를 모니터링하여 더 원할한 사용자 경험을 제공할 수 있습니다.

본 문서에서는 응용 프로그램에 건강 점검을 추가하는 기본 사항을 다루었으며, 고유한 요구에 맞춰 점검을 자세히 사용자 정의할 수 있습니다. 응용 프로그램을 계속 개발하는 과정에서 시스템 건강의 다양한 측면을 커버하기 위해 추가적인 점검 항목을 추가하는 것을 고려해 보세요. 정기적인 검토 및 업데이트를 통해 건강 점검을 유지하면 강력하고 반응 빠른 응용 프로그램을 유지할 수 있습니다.

더 자세한 정보와 고급 구성에 대해서는 AspNetCore.Diagnostics.HealthChecks 저장소를 참조하십시오.

<div class="content-ad"></div>

저자: Jeslur Rahman 😍