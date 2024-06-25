---
title: "단위 테스트보다 통합 테스트를 선호하여 정확성 확인하기"
description: ""
coverImage: "/assets/img/2024-05-27-FavoringIntegrationTestsOverUnitTeststoVerifyCorrectness_0.png"
date: 2024-05-27 16:17
ogImage:
  url: /assets/img/2024-05-27-FavoringIntegrationTestsOverUnitTeststoVerifyCorrectness_0.png
tag: Tech
originalTitle: "Favoring Integration Tests Over Unit Tests to Verify Correctness"
link: "https://medium.com/mjukvare/favoring-integration-tests-over-unit-tests-to-verify-correctness-187a3af55074"
---

![unit test image](/assets/img/2024-05-27-FavoringIntegrationTestsOverUnitTeststoVerifyCorrectness_0.png)

저는 전문적인 코딩을 시작한 이후로 단위 테스트를 작성해 왔어요. 그 속에는 특별한 "영감"이 있죠. 다른 개발자가 여러분의 코드를 사용해야 한다는 점을 생각하게 만들어요.

그 "다른 개발자"가 바로 여러분의 미래 자신이기도 해요.

단위 테스트를 작성할 때마다, 정확성을 검증할 뿐만 아니라 클래스의 작업 편의성도 평가하려고 해요. 좋은 코드를 작성하는 중요한 측면 중 하나는 함께 작업하기 즐거운 코드를 작성하는 것이라고 생각해요.

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

그러나 전반적인 시스템의 정확성을 검증하는 것은 단위 테스트가 갖고 있지 않은 특징입니다.

단위 테스트를 작성하는 데 드는 비용은 매우 적습니다. 기대되는 결과를 빠르게 작성, 실행 및 확인할 수 있습니다. 그러나 다른 측면에서도 저렴하다고 할 수 있습니다. 단위 테스트는 실제로 발생할 수 있는 프로덕션에서 발생할 수 있는 버그를 잡는 데는 거의 충분하지 않습니다.

🔔 이와 유사한 기사를 더 보고 싶으시면 여기에서 가입하세요.

매우 제어된 상황에서 고립된 상태로 작동하는 것을 본다는 것은 여러 협업 객체, 다양한 사용 사례 등이 포함된 더 큰 환경에서 작동하는 것을 보는 것과는 전혀 다릅니다.

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

의존성을 경멸하는 것은 재앙의 길이 될 수 있습니다. 그리고 내 경험상, 데이터베이스와의 상호 작용을 모의하는 경우에는 특히 그렇습니다.

이 유닛 테스트를 분석해 보세요. 우리는 "UserManager"를 통해 사용자를 저장하는 것을 확인하려고 합니다.

```js
[Fact]
public void SaveNewUser()
{
   // Arrange
   var repository = Substitute.For<IUserRepository>();


   var user = new User();
   var sut = new UserManager(repository);

   // Act
   sut.SaveUser(user);

   // Assert
   repository.Received(1).SaveUser(Arg.Any<User>());
}
```

이것은 투명하고 화이트박스 테스트입니다. UserManager는 실제 저장을 협력자 "IUserRepository"에 위임하고 있으며, "UserManager"가 "SaveUser" 메서드를 호출하는지 확인합니다.

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

이는 아무 것도 테스트하지 않는 종류의 단위 테스트입니다. 아무 것도 확인하지 않으며 확신을 줄 수도 없습니다.

우리는 모든 것이 잘되도록 하려는 중입니다.

이러한 테스트는 무언가를 확실히 해서 빌드 서버에서 코드 품질 단계를 통과시키는 임의의 코드 커버리지 목표를 충족하는 데로 인해 우리를 기분 좋게 만들어 줍니다. 하지만 실제로는 일이 잘 되고 있다는 빈 약속일 뿐입니다.

만약 당신의 코드베이스에 제가 방금 보여준 것과 유사한 테스트가 있다면, 그것은 당신에게 큰 문제가 있을 수 있다는 것을 의미합니다.

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

위의 "사용자" 테이블이 이렇게 생겼다고 가정해 봅시다.

```js
-- PostgreSQL
CREATE TABLE "Users" (
   "Id" uuid NOT NULL,
   "Name" text NOT NULL,

   -- 그리고 다른 많은 열들

   CONSTRAINT "PK_Users" PRIMARY KEY ("Id")
);
```

그럼 "Id"나 "Name"이 없이 만들어진 사용자를 저장하려고 하면 예외가 발생하게 되어, 모든 것이 정상적으로 작동되고 있다고 생각하는 것을 방지할 수 있습니다.

```js
var user = new User(); // 저장되지 않아야 함
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

이것은 분명히 클래스와 협력자 간의 상호 작용이 매우 적은 간단한 예제입니다. 그러나 단위 테스트에 무작정 의존하는 함정을 보여줍니다.

🔔 이와 유사한 기사를 더 보시려면 여기에서 가입하세요.

프로페셔널 소프트웨어 개발의 여러 해를 거친 후, 무언가를 모킹할 때 항상 그 테스트를 단위 테스트로 유지해야 하는지 아니면 통합 테스트로 승격해야 하는지 신중하게 생각하는 시간을 가집니다.

이제 이 전환된 테스트와 이 통합 테스트를 대조해 보세요.

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

```csharp
public class UserManagerShould(DatabaseFixture fixture) : IClassFixture<DatabaseFixture>
{
  [Fact]
  public async Task SaveNewUser()
  {
     // Arrange
     UserDbContext context = new TestDbContextFactory(fixture.ConnectionString)
         .CreateDbContext(null!);

     await context.Database.EnsureCreatedAsync();
     await context.Users.ExecuteDeleteAsync();

     var repository = new EfUserRepository(context);

     var user = new User();
     var sut = new UserManager(repository);


     // Act
     sut.SaveUser(user);

     // Assert
     List<User> result = context.Users
         .AsNoTracking()
         .ToList();

     result.Should()
           .HaveCount(1);
  }
}
```

이 테스트는 우리 시스템이 프로덕션에서 수행할 작업을 정확히 모방합니다. 이 테스트는 픽스처와 PostgreSQL 도커 컨테이너가 하나의 테스트를 위해 실행됩니다.

이제 "SaveUser(user)" 메서드를 호출하면 실제 데이터베이스에 삽입이 수행되며, 사용자에게 이름이 없기 때문에 오류가 발생합니다: Npgsql.PostgresException 23502: null value in column "Name" of relation "Users" violates not-null constraint.

실제로 잘못된 상황에서 오류가 발생하는 이러한 테스트는 사용자를 저장하기 전에 추가적인 확인 절차를 수행해야 함을 알려줍니다.

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

테스트 실패는 우리에게 대응을 촉발시킵니다. 이 경우 UserManager의 SaveUser(user) 메서드에 가드 절을 추가하여 사용자가 유효한 상태에 있는지 확인하십시오.

# 요약하자면...

테스트는 시스템이 작동한다는 확신을 주어야 하며, 단순히 통제된 실험실 조건에서 각 부분이 작동하는 것만으로는 충분하지 않습니다.

🔔 이와 같은 기사를 더 읽고 싶으신가요? 여기에서 등록하세요.

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

시스템 내 중요한 클래스 상호작용을 모의하는 것은 클래스 간 통신 및 데이터 교환에서 발생하는 통합 문제를 알리지 못하게 할 수 있습니다. 실제 데이터베이스를 사용하는 데 조금 노력을 기울이면 장기적으로 이득을 볼 수 있습니다.

빌드 시간에 통합 문제를 발견하는 것은 운영시간(프로덕션)에 발생하는 쉽게 피할 수 있는 문제를 발견하는 것보다 훨씬 저렴합니다.

# 계속 연락을 유지합시다!

뉴스레터에 등록하여 유사한 기사에 대한 알림을 받고 YouTube 채널인 @Nicklas Millard를 확인해보세요.

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

린크드인에 연결하지 않는 것을 잊지 마세요.
