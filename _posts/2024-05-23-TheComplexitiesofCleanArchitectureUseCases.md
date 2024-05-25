---
title: "깔끔한 아키텍처 사용 사례의 복잡성"
description: ""
coverImage: "/assets/img/2024-05-23-TheComplexitiesofCleanArchitectureUseCases_0.png"
date: 2024-05-23 14:42
ogImage: 
  url: /assets/img/2024-05-23-TheComplexitiesofCleanArchitectureUseCases_0.png
tag: Tech
originalTitle: "The Complexities of Clean Architecture Use Cases"
link: "https://medium.com/@VolodymyrSch/the-complexities-of-clean-architecture-use-cases-71ac89ea8b40"
---


Clean Architecture는 따라야 할 규칙이 있는데, 엄격히 준수한다고 해서 문제가 발생할 수 있습니다. 이 글에서는 정확히 이러한 규칙을 엄격히 준수할 때 발생할 수 있는 몇 가지 문제점, 특히 유스케이스와 단일 책임 원칙 (SRP)에 초점을 맞춰 구체적으로 논의하겠습니다.
이 글은 Clean Architecture와 해당 용어에 이미 익숙한 사람들을 대상으로 합니다.

Clean Architecture의 주요 기반 중 하나는 레이어링입니다. 핵심 아이디어는 소프트웨어를 특정 책임을 갖는 구분된 레이어로 분리하여, 의존성이 하나의 방향으로 흐르도록 하는 것입니다: 외부 레이어에서 내부 레이어로.

![이미지](/assets/img/2024-05-23-TheComplexitiesofCleanArchitectureUseCases_0.png)

Clean Architecture의 규칙 중 하나는 의존성 원칙인데, 이것은 소스 코드 의존성이 내부로만 향한다는 것을 말합니다. 즉, 데이터 레이어에서 뭔가를 얻거나 수행하려면 항상 중간에 프록시 역할을 하는 것을 생성해야 하며, 일반적으로 그것이 유스케이스입니다. 유스케이스는 시스템이 수행해야 하는 단일 재사용 가능한 작업에 대한 비즈니스 로직을 캡슐화합니다.

<div class="content-ad"></div>

한 줄로 된 UseCase

비즈니스 규칙이 'UI에 데이터를 표시해야 한다'는 규칙 외에는 어떤 비즈니스 규칙도 없더라도, 종속성 규칙을 따르면 데이터 레이어에서 데이터를 가져오는 UseCase를 만들어야 합니다.

```kotlin
class GetUserUseCase( 
   private val userRepository: UserRepository 
) { 
 
 operator fun invoke(userConfig: UserConfig): Result<User> { 
   return userRepository.getUser() 
 } 
} 
```

GetUserUseCase에는 규칙이 없고 다른 레이어로의 프록시 역할만 수행합니다. 어떻게 하면 추가적인 로직 없이 모든 사용자에 대한 CRUD를 갖을 수 있을까요?

<div class="content-ad"></div>

```kotlin
class GetUserUseCase(
   private val userRepository: UserRepository
) {

 operator fun invoke(userConfig: UserConfig): Result<User> {
   return userRepository.getUser()
 }

}

class AddUserUseCase(
   private val userRepository: UserRepository
) {

 operator fun invoke(user: User) {
   return userRepository.addUser(user)
 }

}

class DeleteUserUseCase(
   private val userRepository: UserRepository
) {

 operator fun invoke(user: User) {
   return userRepository.deleteUser(user)
 }

}

class UpdateUserUseCase(
   private val userRepository: UserRepository
) {

 operator fun invoke(user: User) {
    return userRepository.updateUser(user)
 }

}
```

한 줄짜리 use case 수는 기하급수적으로 늘어날 수 있습니다. 하나의 데이터 유형에 대해 여러 개의 use case를 생성할 수 있습니다. 실제 세계에서의 상황은 더 나빠질 수도 있습니다. GetAllUsersUseCase, GetOldUserCase 등과 같은 UseCases를 가질 수 있습니다.

이 use cases는 데이터 계층에 대한 프록시 역할을 하는 것 외에 아무것도 하지 않습니다. Clean Architecture의 일부 구현에서는 모델 간의 변환을 수행하는 매퍼도 존재할 수 있으며, 이는 상황을 더 악화시킵니다. 비즈니스 규칙을 행동으로 전환하지 않습니다. 어떤 문제도 해결하지 않고, 단지 Clean Architecture의 규칙을 만족시키기 위해 추가 코드를 작성합니다.

조금 생각해 봅시다:
```  

<div class="content-ad"></div>

시간이 흘러감에 따라 '한 줄짜리 사용 사례'의 기여로 인해 사용 사례의 수가 너무 많아질 수 있습니다. 프로젝트에는 50, 100, 200, 500개 이상의 사용 사례가 쌓일 수 있습니다. 이 많은 수의 사용 사례는 문제를 야기할 수 있습니다.

예를 들어, 새로운 화면을 작업 중이고 이미 애플리케이션에서 사용되는 데이터를 표시해야 한다고 상상해보세요. 이미 작성된 사용 사례, 저장소 등이 해당 데이터 유형과 작업하는데 사용됩니다. 그런데 이 수백 개의 사용 사례 중에서 재사용할 수 있는 것을 찾아야 합니다. 이 작업은 프로젝트 내의 다양한 요소에 따라 쉬울 수도 있고 어려울 수도 있습니다.

명명 규칙이 엄격합니까? 그렇다면 사용 사례의 이름은 "GetMyDataType"와 같이 시작할 수 있고 이를 통해 이름으로 검색을 시작할 수 있습니다. 그러나 명명은 어렵고 엄격한 규칙이 있더라도 의도를 정확하게 나타내지 못할 수 있습니다.

다중 저장소 아키텍처를 사용합니까? 이는 각 팀이 자체 저장소를 사용하여 다른 별도 프로젝트에서 작업하고, 이러한 모든 프로젝트가 라이브러리 형식으로 주 애플리케이션에 포함되는 구조를 말합니다. 이러한 경우 필요한 사용 사례가 다른 저장소에 있을 수 있으며 이를 찾고 재사용하는 것이 더 어려울 수 있습니다. 기본적으로 사용 사례를 찾기 위해 다양한 프로젝트를 검색해야 하며, 아마도 사용할 수 없을 것입니다. 사용 사례가 캡슐화되어 있을 수 있기 때문입니다. 만약 캡슐화되어 있지 않더라도 다른 팀이 변경을 가할 경우 코드가 깨질 수 있으므로 캡슐화돼 있어야 합니다.

<div class="content-ad"></div>

[기능 모듈화](https://developer.android.com/topic/modularization)와 관련된 내용입니다. 기능의 코드 변경이 다른 기능 또는 애플리케이션에 영향을 미치지 않아야 합니다. 기능 모듈의 고유성(cohesion)을 높이고 싶다면, 내부(modifier)로 접근 가능하도록 하여 재사용성을 의도적으로 줄일 수 있습니다.

의존성 규칙을 엄격히 준수하며 use case를 작성하는 주요 이유는 다음과 같습니다: "언제나 use case를 사용하여 코드를 앞으로의 변경으로부터 보호하게 됩니다. 예를 들어, 지불 전송에 추가 단계가 필요한 경우 새로운 use case를 생성하고 해당 repository 기능을 사용하던 모든 ViewModel을 use case로 대체해야 합니다." 그러나 실제로는 어떤가요?

예제를 살펴보겠습니다. 은행 앱에서 신용 카드 목록을 제공하는 use case가 있다고 가정해봅시다.
```js
class GetCreditCartsUseCase( 
    private val creditCardRepository: CreditCardRepository 
) { 
 
 operator fun invoke(): Result<List<CreditCard>> { 
    return creditCardRepository.getCreditCards() 
 } 
}
```

<div class="content-ad"></div>

그리고 앱 전체에 이 데이터를 여러 곳에 표시해야 합니다.

![이미지](/assets/img/2024-05-23-TheComplexitiesofCleanArchitectureUseCases_1.png)

요구 사항이 변경되었을 때 어려움이 생길 수 있는 날이 왔습니다. 변경 사항이 다른 곳에 적용되지 않을까 걱정할 필요 없이 유스케이스를 변경할 수 있습니다. 따라서 한 곳에서만 변경하고, 한 클래스에 대한 새로운 유닛 테스트를 추가하면 됩니다.

하지만 문제가 있습니다: 요구 사항이 한 화면에 대해서만 변경되었습니다. 개요 화면에서는 가장 최근에 획득한 신용카드 하나만 보여주어야 합니다. 이제 기존 유스케이스를 재사용하는 대신 새로운 유스케이스를 만들고 영향을 받는 viewModel을 수정해야 합니다. 제 경험 상, 대부분의 경우 요구 사항과 유스케이스가 이렇게 되는 것입니다. (미래를 예측할 수 있는 경우를 제외하고는, 무엇을, 어디를 재사용할 수 있는지 알 수 없습니다)

<div class="content-ad"></div>

```markdown
<img src="/assets/img/2024-05-23-TheComplexitiesofCleanArchitectureUseCases_2.png" />

이제 UseCase의 비즈니스 요구 사항에 대해 생각해 봅시다. 우리는 신용 카드 목록을 가져와 UI에 표시해야 합니다. 사용된 모든 곳에 영향을 미치는 추가 로직이 있을 가능성은 어떤지요? 저는 "잘 없을 것"이라고 생각합니다. 현재 작업 중인 앱에 대해 생각해보면 이러한 요구 사항의 본질 때문에 결코 변경될 수 없는 많은 유스케이스를 찾을 수 있다고 확신합니다.

큰 프로젝트에서 발생할 수 있는 또 다른 문제는 생성자 과다 주입입니다:

```js
class UserSettingsViewModel( 
   private val getUserUserCase: GetUserUserCase, 
   private val getAllUsersUseCase: GetAllUsersUseCase, 
   private val addUserUseCase: AddUserUseCase, 
   private val deleteUserUseCase: DeleteUserUseCase, 
   private val updateUserUseCase: UpdateUserUseCase, 
   private val getPremiumUsersUseCase: GetPremiumUsersUseCase, 
   private val getFiltersUseCase: GetFiltersUseCase, 
   private val getAppSettingsUseCase: GetAppSettingsUseCase, 
   private val selectUserUseCase: SelectUserUseCase, 
   private val selectFilterUseCase: SelectFilterUseCase, 
   private val updateAppSettingsUseCase: UpdateAppSettingsUseCase 
   //…이하 생략… 
)
```

<div class="content-ad"></div>

익숙한 것 같나요? 댓글 달 건 없어요. (하지만, 공정하게 말하자면, 생성자에 10-20-40개 이상의 인수가 허용되는 경우에는 문제가 되지 않아요).

그럼, 이에 대해 어떻게 처리할까요?

데이터 레이어를 직접 사용하세요.
Google에서 권장하는 방법입니다:
https://developer.android.com/topic/architecture/domain-layer#data-access-restriction

"하지만, 잠재적으로 상당한 단점은 데이터 레이어에 대한 간단한 함수 호출일 때에도 유즈 케이스를 추가해야 하므로, 작은 혜택을 위해 복잡성을 추가할 수 있다는 것입니다."

<div class="content-ad"></div>

좋은 방법은 필요한 경우에만 사용 사례를 추가하는 것입니다. UI 레이어가 대부분의 경우 사용 사례를 통해 데이터에 액세스하는 것을 발견하면 데이터에 이렇게만 액세스하는 것이 더 낫다고 할 수 있습니다.

일부 경우에는 의존성 규칙을 위반하고 데이터 레이어를 UI 레이어에서 직접 사용하는 것을 제안하며, 이는 고려할 만한 사항이기도 합니다.

![이미지](/assets/img/2024-05-23-TheComplexitiesofCleanArchitectureUseCases_3.png)

Facade

<div class="content-ad"></div>

하지만, 만약 관심사 분리를 유지하고 깔끔한 아키텍처 계층 원칙을 완전히 따르면서 지나치게 한 줄 논리 사용 사례의 함정에 빠지지 않으려면 Facade 패턴을 사용하는 것을 고려해보세요.

```js
class UserFacade(
   private val userRepository: UserRepository,
   private val userMapper: UserMapper
) {

 fun getUser(userConfig: UserConfig): Result<User> {
   return userRepository.getUser(userConfig)
 }

 fun getAllUsers(): Result<List<User>> {
   return userRepository.getUsers()
 }

 fun getAllPremiumUsers(): Result<List<User>> {
   return userRepository.getPremiumUsers()
 }

 fun addUser(user: User): Result<User> {
   return userRepository.addUser(user)
 }

 fun deleteUser(user: User): Result<User> {
    return userRepository.deleteUser(user)
 }

 fun updateUser(user: User): Result<User> {
    return userRepository.updateUser(user)
 }
}
```

Facade 패턴은 하위 시스템을 더 쉽게 사용할 수 있도록 단일하고 더 연관성 있는 인터페이스로 복잡성을 캡슐화합니다. 코드베이스 전반에 흩어져 있는 여러 한 줄 논리 사용 사례 대신에 Facade는 이러한 작업을 통합합니다. 이렇게 하면 본질적으로 동일한 작업을 수행하는 많은 사용 사례를 가지는 중복성이 줄어듭니다 — 데이터 계층과 상호 작용하는 것과 관련이 있는 사용 사례가 많이 감소합니다.

어떠한 다른 옵션들, 예를 들어 모든 데이터 형식과 재사용할 수 있는 일반 타입의 Facade, 같은 작업이 무엇을 가장 잘 수행하는지 찾아보기 위한 실험이 가능합니다.

<div class="content-ad"></div>

단일 책임 원칙

이제 SOLID 원칙 중 SRP에 대해 이야기해보겠습니다. UseCase가 단일 작업이어야 한다는 것을 기억하세요. 그리고 해당 정의에 따라 SRP를 준수해야 합니다.

새 사용자 등록을 담당하는 UseCase가 있습니다:

```js
class UserRegistrationUseCase(
    private val userRepository: UserRepository,
    private val appThemeRepository: AppThemeRepository,
    private val emailService: EmailService,
    private val securityService: SecurityService,
    private val promotionsService: PromotionsService,
) {

    operator fun invoke(userDetails: UserDetails): Result<User> {
        if (securityService.weak(password = userDetails.password)) {
            return Result.failure(Exception("비밀번호가 약합니다"))
        }

        val isPromotional = checkPromotionalEligibility(userDetails.email, userDetails.location)
        val userSettings = UserSettings("en-US", receiveNewsletters = isPromotional)
        val starterPack = if (isPromotional)
            promotionsService.getPromotionalStarterPack(userDetails.location)
        else promotionsService.getDefaultStarterPack(userDetails.location)

        val user = User.fromUserDetails(userDetails, securityService.encryptPassword(userDetails.password), isPromotional, userSettings, starterPack)

        userRepository.save(user)
        emailService.sendWelcomeEmail(userDetails.email, isPromotional)

        if (isPromotional)
            appThemeRepository.save(user.id, "dark")
        else appThemeRepository.save(user.id, "light")

        promotionsService.schedulePersonalizedFollowUps(user.id, user.email, user.isPromotional)
        return Result.success(user)
    }

    private fun checkPromotionalEligibility(email: String, location: String): Boolean {
        val isEmailEligible = email.endsWith("@example.com")
        val isLocationEligible = location == "USA" // 미국의 프로모션 자격을 가정합니다
        return isEmailEligible && isLocationEligible
    }
}
```  

<div class="content-ad"></div>

이 UseCase를 살펴보면 SRP를 위반하는 것 같네요. 아마도 맞을 겁니다. 코드가 별도의 UseCase로 추출되고 재사용될 수 있는 부분이 있습니다. 함께 해결해보죠.

```js
class UserRegistrationFlowUseCase(  
    private val saveUserUseCase: SaveUserUseCase,  
    private val prepareNewUserUseCase: PrepareNewUserUseCase,  
    private val userFollowUpUseCase: UserFollowUpUseCase,  
    private val sendWelcomeEmailUseCase: SendWelcomeEmailUseCase,  
    private val setAppThemeUseCase: SetAppThemeUseCase,  
) {  
  
  operator fun invoke(userDetails: UserDetails): Result<User> {  
      val userResult = prepareNewUserUseCase.prepareUser(userDetails)
      if (userResult.isError()) {
         return userResult
      }  
      val user = userResult.get()
        
      saveUserUseCase(user)  
      sendWelcomeEmailUseCase(user)  
      userFollowUpUseCase(user)  
      setAppThemeUseCase(user)  
      return Result.success(user)    
  }  
}  
  
class PromotionEligibilityUseCase(  
    private val emailPromotionEligibilityUseCase: EmailPromotionEligibilityUseCase,  
) {  
  
  fun checkEligibility(userDetails: UserDetails): Boolean {  
      val isEmailEligible = emailPromotionEligibilityUseCase(userDetails.email)  
      val isLocationEligible = userDetails.location == "USA" // Assume promotional eligibility for USA  
      return isEmailEligible && isLocationEligible  
  }  
  
}  
  
class EmailPromotionEligibilityUseCase {  
  
  operator fun invoke(email: String): Boolean {  
      val emailRegex = "^[a-zA-Z0-9._%+-]+@[a-zA-Z0-9.-]+\\.[a-zA-Z]{2,6}$"  
      return email.endsWith("@example.com") && email.matches(emailRegex.toRegex())  
  }  
}  
  
class PrepareNewUserUseCase(  
    private val securityService: SecurityService,  
    private val getStarterPackUseCase: GetStarterPackUseCase,  
    private val promotionEligibilityUseCase: PromotionEligibilityUseCase,  
) {  
  
  fun prepareUser(userDetails: UserDetails): Result<User> { 
      if (securityService.weak(password = userDetails.password)) {  
         return Result.failure(Exception("Password is weak"))  
      }
      val isPromotional = promotionEligibilityUseCase.checkEligibility(userDetails)  
      val userSettings = UserSettings("en-US", receiveNewsletters = isPromotional)  
      val starterPack = getStarterPackUseCase(userDetails.location, isPromotional)  
      val encryptedPassword = securityService.encryptPassword(userDetails.password)  
      return Result.success(User(  
          id = 0,  
          name = userDetails.name,  
          email = userDetails.email,  
          password = encryptedPassword,  
          location = userDetails.location,  
          isPromotional = isPromotional,  
          settings = userSettings,  
          starterPack = starterPack  
        )
      )  
  }  
}  

(이하 계속)  
```

우리는 SRP를 만족하는 새로운 8개 UseCase를 생성했어요. 모두 작고 깔끔하며 쉽게 재사용할 수 있어요. 테스트에 대해서 언급하지는 않았는데, 이 두 경우 모두 모든 것을 쉽게 테스트할 수 있어요.

그러나 이 접근은 엄청난 **맥락적 오버헤드**를 초래합니다. 함수가 많아질수록 일어나게 됩니다. 이 예시에서는 전체 그림을 파악하려면 9개 파일 간을 이동해야 합니다. 한 책의 한 문장이 일어나는 것을 이해하기 위해 해당 문단을 읽은 다음 다음 페이지로 넘어가서 특정 문단을 읽은 다음 다시 원래 페이지로 돌아가서 다음 문장을 읽은 다음 책의 끝까지 가서 다른 문단을 읽는 것을 상상해보세요.

<div class="content-ad"></div>

```markdown
![이미지](/assets/img/2024-05-23-TheComplexitiesofCleanArchitectureUseCases_4.png)

기본적으로는 각 use case에서 함수 트리와 그들이 하는 일을 머릿속에 유지해야 합니다. 각 use case에서 무슨 일이 일어나고 있는지 읽고 기억해야 합니다. 함수가 무슨 일을 하는지 읽고 다음으로 진행 — 다시 읽고, 그리고 다음으로 진행 — 다시 읽고, 그리고 다시 처음 use case로 돌아가고, 나머지 남은 모든 함수와 함께 계속합니다. 모든 함수가 서로 다른 파일에 있기 때문에 모두 기억해야 합니다.

![이미지](/assets/img/2024-05-23-TheComplexitiesofCleanArchitectureUseCases_5.png)

또한 디버깅할 때도 귀찮은 작업인데, 다른 파일의 중단점 사이를 이동해야하고, 디버거의 데이터는 일반적으로 현재 클래스에만 표시됩니다. 코드 리뷰에 있어서도 마찬가지로 도전적입니다. 파일과 함수 사이를 쉽게 이동하는 것이 항상 가능한 것은 아니기 때문입니다.
```

<div class="content-ad"></div>

단지 2-3-4개의 유스케이스만으로도 코드가 해독하기 어려워집니다. 예시의 기능은 간단하고 쉽지만, 실제 프로젝트에서는 보통 더 복잡하며, 이름이 항상 명확하거나 대표적이지는 않습니다 (이름 짓기가 어려워요).

첫 번째 방식에서 유스케이스는 작은 디스플레이에서도 맞게 코드가 20-25줄 정도의 함수로 있습니다. 이것을 책처럼 읽을 수 있고, 20초 전에 검토했던 세 함수가 무엇이었는지 과부하가될 필요가 없이 전체 작업을 볼 수 있습니다. 또한, 단순히 한 곳에서 사용되지 않는다면 이 "재사용성"은 필요한 것인가요? 재사용성이 명확한 필요가 있는 경우가 오기 전에 작은 더 구체적인 구성 요소로 유스케이스를 분리하는 것은 조기 최적화입니다. 이 접근 방식은 구조를 불필요하게 복잡하게 만들 수 있고, 관리 및 유지보수해야 하는 요소를 더 많이 도입하여 실질적인 이점 없이 만들어 낼 수 있습니다.

문제를 해결하려는 것인지, 아니면 코드를 작성하는 유일한 올바른 방법으로 생각해서 규칙을 엄격하게 따르려는 것인지 고민해봐야 합니다.

이 룰을 엄밀히 준수할 필요가 없는 경우에도 코드를 작업하고 유지하는 것이 훨씬 쉽다고 생각해요 (그런데 이 룰들은 많은 년이 지나도 수정되지 않았죠).

<div class="content-ad"></div>

요약

- 의존성 규칙: 이 규칙은 소스 코드 의존성이 내부로만 가리킬 수 있다는 것을 말합니다. 이로 인해 종종 데이터 레이어에 프록시 역할을 하는 사용 사례가 생성됩니다.

- 일줄 사용 사례: Clean Architecture 엄격히 준수하면 다른 레이어로의 프록시 역할만 하는 다수의 일줄 사용 사례가 생성되어 사용 사례 수가 기하급수적으로 증가할 수 있습니다.

- 기하급수적 성장: 사용 사례 수가 급속히 증가함에 따라 코드베이스를 유지하고 탐색하는 것이 어려움을 초래할 수 있습니다.

<div class="content-ad"></div>

- 복잡성과 오버헤드: SRP를 엄격히 준수하면 하나의 사용 사례를 많은 작은 사용 사례로 분해하여 시스템의 복잡성과 맥락 상의 오버헤드를 증가시킬 수 있습니다.

- 균형: UseCase를 설계할 때 이론적 순수성과 재사용성을 유지하는 Single Responsibility Principle (SRP)과 작동 오버헤드를 최소화하는 실용적이고 유지보수 가능한 시스템을 만드는 사이의 상충 관계를 고려해주세요.

독서해 주셔서 감사합니다!