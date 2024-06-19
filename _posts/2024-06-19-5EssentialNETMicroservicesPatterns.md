---
title: "5가지 필수 NET 마이크로서비스 패턴"
description: ""
coverImage: "/assets/img/2024-06-19-5EssentialNETMicroservicesPatterns_0.png"
date: 2024-06-19 11:29
ogImage: 
  url: /assets/img/2024-06-19-5EssentialNETMicroservicesPatterns_0.png
tag: Tech
originalTitle: "5 Essential .NET Microservices Patterns"
link: "https://medium.com/@kmorpex/5-essential-net-microservices-patterns-c7d38ab888a2"
---


```markdown
![2024-06-19-5EssentialNETMicroservicesPatterns_0](/assets/img/2024-06-19-5EssentialNETMicroservicesPatterns_0.png)

소프트웨어 개발의 끊임없는 세계에서, 마이크로서비스 아키텍처는 특히 .NET의 광범위한 영역에서 인기 있는 선택지가 되었습니다. .NET은 확장 및 적응이 가능한 능력으로 대규모의 데이터 및 트래픽을 처리할 수 있는 이상적인 플랫폼입니다. 

이 기사에서는 마이크로서비스 아키텍처를 숙달하고자 하는 .NET 개발자들을 위한 다섯 가지 필수 패턴을 탐구할 것입니다. 우리는 실용적인 예제와 실행 가능한 조언을 제공하여 마이크로서비스의 우수성을 달성하는 여정에서 당신을 돕겠습니다.

## 1. 게이트웨이 집계 패턴
```

<div class="content-ad"></div>

```markdown
![image](/assets/img/2024-06-19-5EssentialNETMicroservicesPatterns_1.png)

마이크로서비스 아키텍처에서는 클라이언트 응용 프로그램이 상호 작용해야 할 여러 서비스가 있는 것이 일반적입니다. 각 개별 서비스와 직접 상호 작용하는 대신 API 게이트웨이를 사용하는 것이 더 좋습니다. 이 방법은 클라이언트 코드를 간소화하고 백엔드 서비스에 액세스하기 위한 단일 진입점을 생성합니다.

```js
// Ocelot API Gateway 사용 예제
public async Task<AggregatedResult> AggregateData()
{
    var userTask = _userService.GetUser();
    var ordersTask = _orderService.GetOrdersForUser();
    
    await Task.WhenAll(userTask, ordersTask);
    
    var user = await userTask;
    var orders = await ordersTask;

    return new AggregatedResult
    {
        User = user,
        Orders = orders
    };
}
```

## 2. 회로 차단기 패턴
```

<div class="content-ad"></div>

```md
![Circuit Breaker](/assets/img/2024-06-19-5EssentialNETMicroservicesPatterns_2.png)

서킷 브레이커를 구현하면 네트워크 또는 서비스 장애가 다른 서비스로 전파되는 것을 방지할 수 있습니다. 서비스 장애가 발생하면 서킷 브레이커가 이를 감지하고 그 서비스로의 후속 호출을 일정 기간 동안 자동으로 차단합니다.

```js
// Polly 라이브러리 사용 예제
var circuitBreakerPolicy = Policy
    .Handle<Exception>()
    .CircuitBreaker(2, TimeSpan.FromMinutes(1));

await circuitBreakerPolicy.ExecuteAsync(async () =>
{
    await PerformServiceCall();
});
```

## 3. 서비스 디스커버리 패턴
```

<div class="content-ad"></div>

```markdown
![Image](/assets/img/2024-06-19-5EssentialNETMicroservicesPatterns_3.png)

마이크로서비스 아키텍처를 확장할수록 다양한 서비스 엔드포인트를 추적하기가 더 어려워집니다. 이 문제를 해결하기 위해 서비스 디스커버리를 구현할 수 있습니다. 이를 통해 서비스가 동적으로 서로를 찾을 수 있습니다.

```js
using System;
using System.Threading.Tasks;
using Consul;
using Microsoft.Extensions.DependencyInjection;

class Program
{
    static void Main(string[] args)
    {
        var serviceProvider = new ServiceCollection()
            .AddSingleton<IConsulClient, ConsulClient>(p => new ConsulClient(consulConfig =>
            {
                // Assuming Consul is running on localhost
                consulConfig.Address = new Uri("http://localhost:8500");
            }))
            .AddSingleton<IConsulService, ConsulService>()
            .BuildServiceProvider();

        var consulService = serviceProvider.GetService<IConsulService>();
        consulService.RegisterService().Wait();
        consulService.DiscoverServices().Wait();
    }
}

public interface IConsulService
{
    Task RegisterService();
    Task DiscoverServices();
}

public class ConsulService : IConsulService
{
    private readonly IConsulClient _consulClient;

    public ConsulService(IConsulClient consulClient)
    {
        _consulClient = consulClient;
    }

    public async Task RegisterService()
    {
        var registration = new AgentServiceRegistration()
        {
            ID = Guid.NewGuid().ToString(),
            Name = "MyService",
            Address = "localhost",
            Port = 5000,
        };

        await _consulClient.Agent.ServiceRegister(registration);
        Console.WriteLine("Consul에 서비스 등록됨");
    }

    public async Task DiscoverServices()
    {
        var services = await _consulClient.Agent.Services();
        foreach (var service in services.Response.Values)
        {
            Console.WriteLine($"발견된 서비스: {service.Service}");
        }
    }
}
```

## 4. 이벤트 소싱 패턴
```  

<div class="content-ad"></div>

<img src="/assets/img/2024-06-19-5EssentialNETMicroservicesPatterns_4.png" />

이벤트 소싱은 응용 프로그램 엔티티의 상태 변경을 이벤트 시리즈로 저장하는 패턴입니다. 이 접근 방식은 특히 마이크로서비스 아키텍처에서 유용하며 시스템에 대한 모든 변경 사항의 감사 가능한 레코드를 제공하여 디버깅 및 문제 해결에 도움이 됩니다. 게다가 이벤트 소싱은 응용 프로그램의 다른 구성 요소 간 데이터 일관성을 보장하는 데 도움이 될 수 있습니다.

```js
// EventStore를 사용한 이벤트 소싱 예시
public async Task SaveEvents(Guid entityId, IEnumerable<Event> events, long expectedVersion)
{
    var eventData = events.Select(e => new EventData(
        Guid.NewGuid(),
        e.GetType().Name,
        true,
        Serialize(e),
        null));

    var streamName = $"Entity-{entityId}";
    await _eventStoreConnection.AppendToStreamAsync(streamName, expectedVersion, eventData);
}
```

## 5. CQRS 패턴

<div class="content-ad"></div>

```markdown
![image](/assets/img/2024-06-19-5EssentialNETMicroservicesPatterns_5.png)

Command Query Responsibility Segregation (CQRS)는 시스템의 읽기 및 쓰기 작업을 분리하는 디자인 패턴입니다. 이 접근 방식은 마이크로서비스에 적합하며 복잡한 데이터 모델을 관리하고 시스템을 더 효과적으로 확장하는 데 도움이 됩니다.

새로운 사용자 추가를 위한 명령

```js
// Commands/AddUserCommand.cs
public class AddUserCommand
{
    public string Name { get; }

    public AddUserCommand(string name)
    {
        Name = name;
    }
}

// Handlers/AddUserCommandHandler.cs
public class AddUserCommandHandler : ICommandHandler<AddUserCommand>
{
    public void Handle(AddUserCommand command)
    {
        // Logic to add the user to the database
        Console.WriteLine($"User {command.Name} added");
    }
}
``` 
```

<div class="content-ad"></div>

ID로 사용자를 가져오는 쿼리

```js
// Queries/GetUserQuery.cs
public class GetUserQuery
{
    public int UserId { get; }

    public GetUserQuery(int userId)
    {
        UserId = userId;
    }
}

// Handlers/GetUserQueryHandler.cs
public class GetUserQueryHandler : IQueryHandler<GetUserQuery, User>
{
    public User Handle(GetUserQuery query)
    {
        // 데이터베이스에서 사용자를 가져오는 로직
        return new User { Id = query.UserId, Name = "John Doe" };
    }
}
```

핸들러를 위한 인터페이스

```js
// Commands/ICommandHandler.cs
public interface ICommandHandler<TCommand>
{
    void Handle(TCommand command);
}

// Queries/IQueryHandler.cs
public interface IQueryHandler<TQuery, TResult>
{
    TResult Handle(TQuery query);
}
```

<div class="content-ad"></div>

이 다섯 가지 방식을 따라가면 .NET 마이크로서비스를 숙달하기에 한 발짝 더 다가갈 것입니다. 이것들을 구성 요소로 활용하고 프로젝트에 맞게 사용자 정의하여 사용하세요. 당신의 마이크로서비스는 번성할 것입니다. 코딩을 즐기세요!

![이미지](https://miro.medium.com/v2/resize:fit:1400/0*UmnFETy8f6kvj1TV.gif)

👏 만약 이 콘텐츠가 도움이 된다면, 버튼을 누른 채로 여러 번 클랩(clap)할 수 있습니다. 또한, 여러분의 생각과 제안을 댓글로 남겨 주시면 더 많은 토론을 할 수 있습니다.

읽어 주셔서 감사합니다...