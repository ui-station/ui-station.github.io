---
title: "MVVM, MVI 및 Kotlin Flows를 활용한 최신 안드로이드 개발  Part 1"
description: ""
coverImage: "/assets/img/2024-06-22-ModernAndroidDevelopmentwithMVVMMVIandKotlinFlowsPart1_0.png"
date: 2024-06-22 22:37
ogImage: 
  url: /assets/img/2024-06-22-ModernAndroidDevelopmentwithMVVMMVIandKotlinFlowsPart1_0.png
tag: Tech
originalTitle: "Modern Android Development with MVVM, MVI, and Kotlin Flows — Part 1"
link: "https://medium.com/@mortitech/combining-mvvm-and-mvi-with-kotlin-flows-part1-48f7dd426341"
---


## 안드로이드 MVI 아키텍처의 심층 탐구

<img src="/assets/img/2024-06-22-ModernAndroidDevelopmentwithMVVMMVIandKotlinFlowsPart1_0.png" />

앱 개발 프로세스를 개선하고 스트림라인하는 방법을 찾느라 고심 중인 안드로이드 개발자이신가요? Kotlin flows를 사용하여 MVVM 및 MVI 아키텍처 패턴을 결합하는 것을 들어보았지만 그것이 어떻게 도움이 될 수 있는지 또는 어디서 시작해야 하는지 확실하지 않으신가요? 이 기사는 여러분을 위해 특별히 작성되었습니다.

안드로이드 개발에 있어서 올바른 아키텍처 패턴을 선택하는 것이 얼마나 중요한지 우리 모두 알고 있습니다. 이는 관리 가능한 프로젝트와 완전한 엉망인 프로젝트 사이의 차이를 의미할 수 있습니다. 이미 MVVM (Model-View-ViewModel) 및 MVI (Model-View-Intent) 패턴에 대해 알고 계실 것으로 생각됩니다. 각각에는 강점과 약점이 있습니다. MVVM은 데이터 바인딩과 표시 상태 관리에서 빛을 발하며, MVI는 단방향 데이터 흐름과 강력한 상태 관리로 빛을 발합니다.

<div class="content-ad"></div>

하지만 이 두 강력한 패턴을 결합하여 안드로이드 개발에 더 강력한 접근 방식을 만들 수 있다고 말했다면 어떨까요? 바로 Kotlin flows를 사용하여 MVVM과 MVI 간의 원활한 통합을 제공하는 방법을 이 글에서 살펴보겠습니다.

# MVC 대 MVI

결합된 방식에 대해 자세히 살펴보기 전에 먼저 MVC (Model-View-Controller)와 MVI의 핵심 원칙을 이해해 보겠습니다.

## MVC란 무엇인가요?

<div class="content-ad"></div>

MVC 패턴에서 Model은 데이터와 비즈니스 로직을 나타내고, View는 UI를 렌더링하는 역할을 하며, Controller는 Model과 View 사이에서 중개자 역할을 하며, 사용자 입력을 처리하고 Model과 View를 업데이트합니다.

![Image](/assets/img/2024-06-22-ModernAndroidDevelopmentwithMVVMMVIandKotlinFlowsPart1_1.png)

다음은 안드로이드에서 Model-View-Controller (MVC) 아키텍처를 구현하는 예시입니다:

Model 파일 이름은 UserModel.kt:

<div class="content-ad"></div>

```kotlin
data class User(val id: String, val name: String, val email: String)

class UserModel {
    private val users: MutableList<User> = mutableListOf()

    fun addUser(user: User) {
        users.add(user)
    }

    fun getUsers(): List<User> {
        return users.toList()
    }
}
```

UserModel 클래스는 Model을 나타내며 사용자 목록을 관리합니다.

컨트롤러는 UserController.kt 입니다:

```kotlin
class UserController(private val userView: UserView) {

    private val userModel = UserModel()

    // 모델 조작
    fun addUser(name: String, email: String) {
        val user = User(UUID.randomUUID().toString(), name, email)
        userModel.addUser(user)

        val users = userModel.getUsers()
        updateView(users)
    }

    private fun updateView(users: List<User>) {
        // 뷰에서 UI를 업데이트하는 적절한 메서드 호출
        userView.displayUsers(users)
    }
}
```

<div class="content-ad"></div>

The UserController 클래스는 Controller 역할을 하며 사용자 상호작용을 처리하고 Model을 업데이트합니다. 또한 Model의 변경 사항에 따라 UI를 업데이트하기 위해 View와 통신합니다.

뷰는 MvcActivity.kt에서 제공됩니다:

```js
interface UserView {
    fun displayUsers(users: List<User>)
}

class MvcActivity : AppCompatActivity(), UserView {
    private lateinit var userAdapter: UserAdapter
    private lateinit var binding: ActivityMvcBinding
    private lateinit var controller: UserController

    override fun onCreate(savedInstanceState: Bundle?) {
        super.onCreate(savedInstanceState)
        binding = ActivityMvcBinding.inflate(layoutInflater)
        setContentView(binding.root)
        setupRecyclerView()

        // 컨트롤러 생성
        controller = UserController(this)

        binding.addUserButton.setOnClickListener {
            val name = binding.nameEditText.text.toString()
            val email = binding.emailEditText.text.toString()
            // 사용자 입력 전송
            controller.addUser(name, email)
        }
    }

    override fun displayUsers(users: List<User>) {
        // 사용자 목록을 표시하기 위해 UI 업데이트
        userAdapter.setUsers(users)
    }

    private fun setupRecyclerView() {
        // 코드가 간결하게 유지될 수 있도록 주석 처리됨
    }
}
```

MvcActivity 클래스는 View를 나타내며 UI를 표시하는 역할을 담당합니다.

<div class="content-ad"></div>

## MVI란 무엇인가요?

![image](/assets/img/2024-06-22-ModernAndroidDevelopmentwithMVVMMVIandKotlinFlowsPart1_2.png)

MVI 패턴은 Model-View-Controller (MVC) 패턴의 변형으로 생각될 수 있습니다. Model-View-Intent (MVI)의 주요 아이디어는 사용자 인터페이스를 구축하기 위한 반응적이고 기능적 접근 방식을 제공하는 것입니다. 이는 Model-View-Controller (MVC) 아키텍처와 유사합니다.

## MVI는 어떻게 작동하나요?

<div class="content-ad"></div>

MVI에서 기본 개념은 동일하지만 보다 반응적이고 기능적인 프로그래밍 스타일을 강조합니다. Model을 직접 변경하거나 View를 업데이트하는 대신, MVI는 사용자 의도를 불변의 데이터 구조 (Intents)로 캡처하고 단방향 데이터 흐름을 통해 이를 처리하는 데 초점을 맞춥니다. 이러한 흐름은 일반적으로 세 가지 주요 구성 요소로 구성됩니다:

- Model: 응용 프로그램의 현재 상태를 나타냅니다. Model은 변경할 수 없습니다. Model은 의도를 처리하고 Model의 새 버전을 생성함으로써 업데이트됩니다.
- View: 현재 Model 상태를 기반으로 UI를 렌더링하는 역할을 합니다. View는 수동적이며 Model이 변경될 때마다 업데이트를 수신합니다.
- Intent: 사용자 작업 또는 의도를 나타내며, View에서 Model을 업데이트하기 위해 전송됩니다. Intents는 사용자가 수행하려는 작업을 설명하는 일반적으로 간단한 데이터 구조입니다.

![이미지](/assets/img/2024-06-22-ModernAndroidDevelopmentwithMVVMMVIandKotlinFlowsPart1_3.png)

MVI 패턴은 엄격한 단방향 데이터 흐름을 강제합니다. 데이터는 의도에서 모델로, 그리고 모델에서 뷰로 흐릅니다. 이렇게 하면 앱의 동작 및 상태에 대한 추론이 더 쉬워지며 구성 요소 간 부작용이나 종속성이 없기 때문에 유지보수가 쉽습니다.

<div class="content-ad"></div>

## MVC에서 MVI로 변환하기:

위의 코드를 MVI 아키텍처로 어떻게 변경할 수 있는지 알아봅시다. 먼저 두 가지 추가 클래스를 추가해야 합니다. 뷰 내에서 사용자 관련 데이터의 현재 상태를 나타내는 UserViewState 클래스를 소개했습니다. UserIntent sealed 클래스는 사용자 의도를 나타내는 다양한 의도를 정의합니다. 예를 들어 사용자를 추가하는 것입니다.

```js
data class UserViewState(val users: List<User>)

sealed class UserIntent {
    data class AddUser(val name: String, val email: String) : UserIntent()
    // GetUsers와 같은 다른 사용자 의도도 추가할 수 있습니다.
}
```

UserController와 UserView 인터페이스를 제거할 수 있습니다. 또한 UserModel 클래스를 변경하여 현재 뷰 상태를 유지하고 사용자 의도를 처리하며 사용자 상태를 업데이트할 수 있습니다. UserModel은 Kotlin Coroutines Flow를 사용하여 반응적일 수 있습니다.

<div class="content-ad"></div>

```js
class UserModel {
    // 애플리케이션의 현재 뷰 상태를 유지합니다
    private val _userViewState: MutableStateFlow<UserViewState> = MutableStateFlow(UserViewState(emptyList()))
    // 다른 뷰들과 뷰 상태를 공유하여 핫 플로우로 제공합니다
    val userViewState = _userViewState.asStateFlow()

    // processUserIntents에서 사용할 현재 뷰 상태를 얻습니다
    private fun currentViewState(): UserViewState {
        return _userViewState.value
    }

    // 뷰로부터 사용자 의도를 처리합니다
    fun processUserIntents(userIntent: UserIntent) {
        when (userIntent) {
            is UserIntent.AddUser -> {
                val user = User(UUID.randomUUID().toString(), userIntent.name, userIntent.email)
                val newViewState = currentViewState().copy(users = currentViewState().users + user)
                _userViewState.value = newViewState
            }
            /*
            * 다른 사용자 의도(예: 사용자 목록 가져오기)도 여기서 처리할 수 있습니다
            * */
        }
    }
}
```

userViewState는 사용자 뷰 상태를 핫 플로우로 공유합니다. 핫 플로우는 활성 구독자 여부와 관계없이 값을 방출합니다. 핫 플로우는 구독자 여부와 상관없이 계속 업데이트를 방출하는 공유 상태를 유지합니다. 새로운 구독자가 참여하면 최신 값 및 이후의 업데이트를 수신합니다. asStateFlow() 함수는 MutableSharedFlow를 StateFlow로 변환하며, 얻어진 StateFlow는 최신 값 유지 및 구독자 활동 여부에 관계없이 업데이트를 방출하는 핫 플로우로 간주될 수 있습니다.

그리고 View 부분에 대한 변경사항은 다음과 같습니다:

```js
class MviActivity : AppCompatActivity() {
    private lateinit var userAdapter: UserAdapter
    private lateinit var binding: ActivityMviBinding
    private val model = UserModel()

    override fun onCreate(savedInstanceState: Bundle?) {
        binding = ActivityMviBinding.inflate(layoutInflater)
        setContentView(binding.root)
        setupRecyclerView()

        binding.addUserButton.setOnClickListener {
            val name = binding.nameEditText.text.toString()
            val email = binding.emailEditText.text.toString()

            // 사용자 의도를 생성하고 모델에 전달합니다
            val userIntent = UserIntent.AddUser(name, email)
            model.processUserIntents(userIntent)
        }

        // UI가 뷰 상태 변경을 관찰합니다
        model.userViewState
            .onEach { userViewState ->
                renderUserViewState(userViewState)
            }
            .launchIn(lifecycleScope)
    }

    private fun renderUserViewState(userViewState: UserViewState) {
        // 사용자 목록을 표시하기 위해 UI를 업데이트합니다
        userAdapter.setUsers(userViewState.users)
    }
}
```

<div class="content-ad"></div>

MviActivity에서는 onEach를 사용하여 상태 플로우를 관찰하고 renderUserViewState()를 호출하여 UI를 업데이트합니다.

수정된 코드로 다시 다이어그램을 살펴봅시다. 코드를 다이어그램의 각 부분에 매핑하는 방법을 살펴봅시다:

![Diagram](/assets/img/2024-06-22-ModernAndroidDevelopmentwithMVVMMVIandKotlinFlowsPart1_4.png)

위 다이어그램에서 사용자는 OnClick 등의 액션을 트리거할 수 있습니다. 이러한 액션들은 인텐트로 파싱되어 모델에 전달될 수 있습니다.

<div class="content-ad"></div>

모델에서 processUserIntentsexecutes는 전달된 Intent에 기반한 로직을 실행하며, 이 경우 UserIntent.AddUser를 받아 새로운 상태를 생성합니다:

```js
val newViewState = currentViewState().copy(users = currentViewState().users + user)
_userViewState.value = newViewState
```

copy()는 기존 객체와 동일한 속성을 가진 새 객체를 생성하는 Kotlin 함수입니다. 이 경우 currentViewState().copy()는 현재 뷰 상태와 동일한 속성을 가진 새 UserViewState 객체를 생성합니다.

users = currentViewState().users + user 부분은 새로운 뷰 상태의 users 속성을 업데이트해야 함을 명시합니다. 요약하자면, 이 두 줄의 코드는 현재 뷰 상태의 복사본을 만들어 사용자 목록에 새로운 사용자가 추가된 새로운 뷰 상태 객체를 생성합니다.

<div class="content-ad"></div>

뷰 부분에서 새 상태가 생성될 때마다 MviActivity가 변경 사항을 observe합니다.

```js
model.userViewState
  .onEach { userViewState ->
      renderUserViewState(userViewState)
  }
  .launchIn(lifecycleScope)
```

그리고 renderUserViewState(userViewState)를 사용하여 사용자에게 상태를 표시합니다. 반응형 프로그래밍과 Kotlin Coroutine 플로우 덕분에 가능해졌습니다.

# 다음은 무엇인가요?

<div class="content-ad"></div>

다음 시리즈에서는 MVVM 아키텍처를 MVI와 비교하고 이러한 아키텍처를 더 깊이 파헤쳐 양쪽에서 최상의 결과를 얻을 수 있는 방법에 대해 살펴볼 것입니다. 기대해주세요!