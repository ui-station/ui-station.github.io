---
title: "Jetpack Compose 내비게이션 초보부터 고급까지 전체 가이드"
description: ""
coverImage: "/assets/img/2024-05-18-NavigationinJetpackcomposeFullguideBeginnertoAdvanced_0.png"
date: 2024-05-18 17:13
ogImage: 
  url: /assets/img/2024-05-18-NavigationinJetpackcomposeFullguideBeginnertoAdvanced_0.png
tag: Tech
originalTitle: "Navigation in Jetpack compose. Full guide Beginner to Advanced."
link: "https://medium.com/@KaushalVasava/navigation-in-jetpack-compose-full-guide-beginner-to-advanced-950c1133740"
---


아래는 Markdown 형식으로 변환되었습니다.

![이미지](/assets/img/2024-05-18-NavigationinJetpackcomposeFullguideBeginnertoAdvanced_0.png)

# 안드로이드에서의 네비게이션은 무엇인가요?

네비게이션은 애플리케이션의 다른 구성 요소 간에 이동하는 방법을 이해하는 데 도움이 됩니다.

Android JetPack 네비게이션은 고수준의 네비게이션을 간편하게 구현하는 데 도움을 줍니다.

<div class="content-ad"></div>

네비게이션 컴포넌트는 세 가지 주요 부분으로 구성되어 있어요:

- 네비게이션 그래프: 이는 모든 네비게이션 관련 데이터를 한 곳에 모아 둔 리소스입니다. 이에는 앱 내의 모든 위치인 목적지들과 사용자가 앱을 통해 이동할 수 있는 가능한 경로들이 포함됩니다. 앱에서 갈 수 있는 모든 장소와 그 있는 방법을 담은 큰 책처럼 생각하시면 좋아요. 이것은 지도와 안내서가 결합된 것으로 이해할 수 있어요.
- NavHost: 이는 레이아웃에 포함할 수 있는 독특한 컴포저블(composable)입니다. 네비게이션 그래프에서 다양한 목적지를 표시해줘요. NavHost는 NavController를 네비게이션 그래프에 연결하여 네비게이션 사이를 이동할 수 있는 컴포저블 목적지를 지정하는 링크 역할을 합니다. 컴포저블 간을 이동하는 동안 NavHost의 내용은 자동으로 recompose됩니다. 네비게이션 그래프의 각 컴포저블 목적지는 경로에 연결돼 있어요.
- NavController: NavController는 네비게이션 컴포넌트의 중심 API입니다. 이는 상태를 가지고 있으며, 앱의 화면을 구성하는 컴포저블들의 백 스택과 각 화면의 상태를 추적합니다.

# Jetpack Compose에서의 네비게이션

네비게이션 컴포넌트는 Jetpack Compose 애플리케이션을 지원해줍니다. 네비게이션 컴포넌트의 인프라와 기능을 활용하면서 컴포저블들 간을 이동할 수 있어요.

<div class="content-ad"></div>

Jetpack Compose에서 탐색을 시작하려면 프로젝트의 build.gradle 파일에 필수 종속성을 포함해야 합니다:

```js
implementation "androidx.navigation:navigation-compose:2.7.1"
```

Jetpack Compose에서 탐색에 대한 기본 개념.

## NavController:

<div class="content-ad"></div>

NavController은 네비게이션 컴포넌트의 중심 API입니다. 상태를 유지하며, 앱의 화면을 구성하는 컴포저블의 백 스택 및 각 화면의 상태를 추적합니다.

이러한 NavController는 다음과 같이 rememberNavController() 메서드를 사용하여 만들 수 있습니다:

```js
val navController = rememberNavController()
```

NavController를 만들 때는 모든 컴포저블이 해당 NavController에 액세스할 수 있는 컴포저블 계층구조의 적절한 위치에서 만들어야 합니다. 이는 상태 끌어올리기(state hoisting)의 원리를 따르며, 현재 currentBackStackEntryAsState()를 통해 제공되는 NavController 및 상태를 통해 화면 외부의 컴포저블을 업데이트하는 데 참고할 수 있도록 합니다. 이러한 기능의 예시는 바텀 네비게이션바와의 통합을 참고하세요.

<div class="content-ad"></div>

## NavHost:

각 NavController는 단일 NavHost composable과 연결되어야 합니다. NavHost는 NavController를 네비게이션 그래프와 연결하여 이동할 수 있는 composable 목적지를 지정합니다. composable 사이를 이동하면 NavHost의 내용이 자동으로 recomposed됩니다. 네비게이션 그래프의 각 composable 목적지는 route와 연결됩니다.

NavHost를 생성하려면 이전에 rememberNavController()를 통해 생성한 NavController와 그래프의 시작 목적지인 route가 필요합니다. NavHost 생성은 네비게이션 Kotlin DSL에서 lambda 구문을 사용하여 네비게이션 그래프를 구성합니다. composable() 메서드를 사용하여 네비게이션 구조를 추가할 수 있습니다. 이 메서드는 route 및 목적지에 연결할 composable를 제공해야 합니다:

```js
NavHost(navController = navController, startDestination = "profile") {
    composable("profile") { Profile(/*...*/) }
    composable("friendslist") { FriendsList(/*...*/) }
    /*...*/
}
```

<div class="content-ad"></div>

예시: 네비게이션 그래프, 네브호스트 및 네비게이션 아이템 설정하는 방법

단계 1: 네비게이션을 위한 화면 이름 및 라우트를 하나의 파일에 정의합니다. 예시. AppNavigation.kt

```kotlin
enum class Screen {
    HOME,    
    LOGIN,
}
sealed class NavigationItem(val route: String) {
    object Home : NavigationItem(Screen.HOME.name)
    object Login : NavigationItem(Screen.LOGIN.name)
}
```

단계 2: NavHost 및 화면을 정의합니다. 예시. AppNavHost.kt

<div class="content-ad"></div>

```kotlin
@Composable
fun AppNavHost(
    modifier: Modifier = Modifier,
    navController: NavHostController,
    startDestination: String = NavigationItem.Splash.route,
    ... // 다른 매개변수
) {
    NavHost(
        modifier = modifier,
        navController = navController,
        startDestination = startDestination
    ) {
        composable(NavigationItem.Splash.route) {
            SplashScreen(navController)
        }
        composable(NavigationItem.Login.route) {
            LoginScreen(navController)
        }
    }
}
```

단계 3: MainActivity.kt 파일에서 AppNavHost를 호출합니다.

```kotlin
class MainActivity : ComponentActivity() {
    override fun onCreate(savedInstanceState: Bundle?) {
        super.onCreate(savedInstanceState)
        setContent {
            AutoPartsAppTheme {
               Surface(
                    modifier = Modifier.fillMaxSize(),
                    color = MaterialTheme.colorScheme.background
                ) {
                    AppNavHost(navController = rememberNavController())
                }
            }
        }
    }
}
```

## 네비게이션 인자:
```

<div class="content-ad"></div>

네비게이션 Compose는 코틀린을 사용하여 콤포저블 목적지 간에 인수를 전달하는 것을 지원합니다. 이를 위해 기본 네비게이션 라이브러리를 사용할 때 딥 링크에 인수를 추가하는 방식과 유사한 방식으로 라우트에 인수 자리 표시자를 추가해야 합니다. 

## 사용 사례:

- 인수가 없을 때
- Int, String 등 미리 정의된 데이터 유형과 같은 간단한 인수를 사용할 때

3. 사용자가 정의한 데이터 유형 같은 복잡한 인수를 사용할 때

<div class="content-ad"></div>

4. 선택적 매개변수

5. 결과값으로 되돌아가기

## 매개변수 없이:

```js
NavHost(navController = navController, startDestination = "profile") {
    composable("profile") { Profile(/*...*/) }
    composable("friendslist") { FriendsList(/*...*/) }
    /*...*/
}
```

<div class="content-ad"></div>

## 간단한 인수로:

기본적으로 모든 인수는 문자열로 구문 분석됩니다. composable()의 인수 매개변수는 NamedNavArguments의 목록을 허용합니다. navArgument 메서드를 사용하여 NamedNavArgument를 빠르게 만들고 그 정확한 유형을 지정할 수 있습니다:

```js
NavHost(startDestination = "profile/{userId}") {
    ...
    composable("profile/{userId}") {...}
}
```

기본적으로 모든 인수는 문자열로 구문 분석됩니다. composable()의 인수 매개변수는 NamedNavArguments의 목록을 허용합니다. navArgument 메서드를 사용하여 NamedNavArgument를 빠르게 만들고 그 정확한 유형을 지정할 수 있습니다.

<div class="content-ad"></div>

```js
NavHost(startDestination = "profile/{userId}") {
    ...
    composable(
        "profile/{userId}",
        arguments = listOf(navArgument("userId"){
           type = NavType.StringType 
        })
    ) {...}
}
```

`composable("profile/{userId}") { backStackEntry ->
   val userId = backStackEntry.arguments?.getString("userId")
   // 여기서 사용자 데이터를 가져와야 합니다
   Profile(
      navController, 
      // 사용자 데이터를 가져와서 전달하세요. 예: UserInfo
   )
}
`

대상으로 전달하려면 navigate 호출 시 경로에 추가해야 합니다:

<div class="content-ad"></div>

```js
navController.navigate("profile/user1234")
```

지원되는 유형 목록을 보려면 전달 방법을 참조하세요.

## 복잡하거나 사용자 정의 인수로:

이동할 때 복잡한 데이터 객체를 전달하는 것은 권장되지 않지만 대신 고유 식별자 또는 기타 형식의 ID와 같이 최소한의 정보를 인수로 전달해야합니다. 이를 통해 이동 작업 수행 가능합니다.

<div class="content-ad"></div>

```js
// 새로운 대상으로 이동할 때 사용자 ID만 전달하실 때
navController.navigate("profile/user1234")
```

복잡한 객체는 데이터 레이어와 같은 단일 진실의 원천으로 저장해야 합니다. 이동 후 목적지에 도착하면 전달된 ID를 사용하여 단일 진실의 원천에서 필요한 정보를로드할 수 있습니다. 데이터 레이어에 액세스하는 ViewModel에서 인수를 검색하려면 SavedStateHandle를 사용할 수 있습니다.

```js
class UserViewModel(
    savedStateHandle: SavedStateHandle,
    private val userInfoRepository: UserInfoRepository
) : ViewModel() {

    private val userId: String = checkNotNull(savedStateHandle["userId"])

    // 전달된 userId 인수를 기반으로 데이터 레이어(예: userInfoRepository)에서 관련 사용자 정보 검색
    private val userInfo: Flow<UserInfo> = userInfoRepository.getUserInfo(userId)

   --------------- OR -----------------
 
    // 네트워크 또는 데이터베이스에서 데이터 가져오기    
    private val _dataFlow =
            MutableStateFlow<UserInfo>(userInfoRepository.getUserInfo(userId))
    val dataFlow get() = _dataFlow.asStateFlow()
}
```

적합한 기능```

<div class="content-ad"></div>

```kotlin
//Navhost
composable("profile/{userId}") { backStackEntry ->
   val userId = backStackEntry.arguments?.getString("userId")
   // 여기서 사용자 데이터를 가져와야 합니다
   val userInfo by taskViewModel.dataFlow.collectAsState()   
   Profile(
      navController, 
      userInfo
   )
}

// 프로필 화면
@Composable
fun Profile(navController: NavController, userInfo: UserInfo){
    // 여기서 작업을 수행합니다
}
```

이 접근 방식은 구성 변경 중에 데이터 유실을 방지하고 해당 객체가 업데이트되거나 변경될 때 불일치를 방지합니다.

복잡한 데이터를 인수로 전달하는 것을 피해야 하는 이유 및 지원되는 인수 유형 목록에 대한 보다 자세한 설명은 Best practice를 참조하세요.

## 선택적 인수 추가하기
```

<div class="content-ad"></div>

Navigation Compose는 선택적 네비게이션 인수도 지원합니다. 선택적 인수는 필수 인수와 두 가지 방법으로 다릅니다:

- 쿼리 매개변수 구문("?argName='argName'")을 사용하여 포함되어야 합니다.
- defaultValue가 설정되어 있어야 하거나 nullable = true이어야 합니다(이는 기본 값을 자동으로 null로 설정합니다).

이는 모든 선택적 인수가 콤포저블() 함수에 명시적으로 추가되어야 한다는 것을 의미합니다:

```js
composable(
    "profile?userId={userId}/{isMember}",
    arguments = listOf(
         navArgument("userId") {
            type = NavType.StringType
            defaultValue = "user1234"
           // 또는
            type = NavType.StringType
            nullable = true
         },
         navArgument("isNewTask") {
            type = NavType.BoolType
         }
     )
) { backStackEntry ->
    val userId = backStackEntry.arguments?.getString("userId")
    val isMember = backStackEntry.arguments?.getBoolean("isMember")?:false
    Profile(navController, userId, isMember)
}
```

<div class="content-ad"></div>

이제 대상에 인수가 전달되지 않더라도 defaultValue = "user1234"가 대신 사용됩니다.

경로를 통해 인수를 처리하는 구조는 Composable이 Navigation과 완전히 독립되도록 하며 이로 인해 테스트하기 훨씬 더 용이해집니다.

## 결과 값으로 되돌아가기

결과 값을 사용하여 되돌아가는 것이 가장 일반적인 작업입니다. 즉, 필터 대화상자를 열고 필터를 선택한 다음 해당 필터를 적용하기 위해 선택된 필터와 함께 되돌아가는 경우입니다.

<div class="content-ad"></div>

두 개의 화면이 있습니다. 1. 첫 번째 화면과 2. 두 번째 화면입니다. 우리는 두 번째 화면에서 첫 번째 화면으로 데이터를 필요로 합니다.

NavHost.kt : 내비게이션 그래프 설정.

```js
 val navController = rememberNavController()
 NavHost(
     navController = navController,
     startDestination = "firstscreen"
 ) {
    composable("firstscreen") {
        FirstScreen(navController)
    }
    composable("secondscreen") {
        SecondScreen(navController)
    }
}
```

FirstScreen.kt: NavController의 현재 백 스택 항목의 savedStateHandle를 사용하여 두 번째 화면에서 다시 이동한 후 데이터를 검색합니다.

<div class="content-ad"></div>

```kotlin
@Composable
fun FirstScreen(navController: NavController) {
    // 다음 화면에서 데이터를 가져옵니다
    val msg = 
        navController.currentBackStackEntry?.savedStateHandle?.get<String>("msg")
    Column(
        Modifier.fillMaxSize(),
        horizontalAlignment = Alignment.CenterHorizontally,
        verticalArrangement = Arrangement.Center
    ) {
        Button(onClick = { navController.navigate("secondscreen") }) {
            Text("다음 화면으로 이동")
        }
        Spacer(modifier = Modifier.height(8.dp))
        msg?.let {
            Text(it)
        }
    }
}
```

SecondScreen.kt: 이전 백 스택 항목의 savedStateHandle 내에 데이터를 넣습니다.

```kotlin
@Composable
fun SecondScreen(navController: NavController) {
    var text by remember {
        mutableStateOf("")
    }
    Column(
        Modifier.fillMaxSize(),
        horizontalAlignment = Alignment.CenterHorizontally,
        verticalArrangement = Arrangement.Center
    ) {
        TextField(
            value = text, onValueChange = { text = it },
            placeholder = {
                Text("텍스트를 입력하세요", color = Color.Gray)
            }
        )
        Spacer(Modifier.height(8.dp))
        Button(onClick = {

           // 데이터를 savedStateHandle에 넣어 이전 화면에서 데이터를 가져옵니다
   
            navController.previousBackStackEntry?.savedStateHandle?.set("msg", text)
            navController.popBackStack()
        }) {
            Text(text = "제출")
        }
    }
}
```

비디오: 
```

<div class="content-ad"></div>

https://github.com/KaushalVasava/JetPackCompose_Basic/assets/49050597/1d96d44f-66e1-4f3b-bba1-2844ab6553cc

GitHub 저장소: https://github.com/KaushalVasava/JetPackCompose_Basic/tree/navigate-back-with-result

# 딥 링크

Navigation Compose은 암시적 딥 링크를 지원하며 composable() 함수의 일부로 정의할 수 있습니다. 딥 링크 매개변수 deepLinks는 navDeepLink 메소드를 사용하여 빠르게 생성할 수 있는 NavDeepLinks 목록을 수용합니다:

<div class="content-ad"></div>

```js
val uri = "https://www.example.com"
composable(
    "profile?id={id}",
    deepLinks = listOf(navDeepLink { uriPattern = "$uri/{id}" })
) { backStackEntry ->
    Profile(navController, backStackEntry.arguments?.getString("id"))
}
```

이러한 딥 링크를 사용하면 특정 URL, 액션 또는 MIME 유형을 composable과 연결할 수 있습니다. 기본적으로 이러한 딥 링크는 외부 앱에 노출되지 않습니다. 이러한 딥 링크를 외부에서 사용 가능하게 하려면 앱의 manifest.xml 파일에 적절한 `intent-filter` 요소를 추가해야 합니다. 위의 딥 링크를 활성화하려면 manifest의 `activity` 요소 내에 다음을 추가해야 합니다:

```js
<activity …>
  <intent-filter>
    ...
    <data android:scheme="https" android:host="www.example.com" />
  </intent-filter>
</activity>
```

다른 앱에 의해 트리거된 경우 딥 링크가 활성화될 때 해당 composable로 자동으로 이동합니다.
```

<div class="content-ad"></div>

이러한 동일한 딥 링크는 콤포저블에서 적절한 딥 링크와 함께 PendingIntent를 작성하는 데 사용할 수도 있습니다:

```kotlin
val id = "exampleId"
val context = LocalContext.current
val deepLinkIntent = Intent(
    Intent.ACTION_VIEW,
    "https://www.example.com/$id".toUri(),
    context,
    MyActivity::class.java
)
val deepLinkPendingIntent: PendingIntent? = TaskStackBuilder.create(context).run {
    addNextIntentWithParentStack(deepLinkIntent)
    
    val flag = if(Build.VERSION.SDK_INT > Build.VERSION_CODES.S){
                    PendingIntent.FLAG_IMMUTABLE
                } 
                else 
                    PendingIntent.FLAG_UPDATE_CURRENT
    getPendingIntent(0, flag)
}
```

그런 다음 이 deepLinkPendingIntent를 다른 PendingIntent와 마찬가지로 사용하여 앱을 딥 링크 대상지에서 열 수 있습니다.

# 중첩된 내비게이션

<div class="content-ad"></div>

```markdown
![Image](/assets/img/2024-05-18-NavigationinJetpackcomposeFullguideBeginnertoAdvanced_1.png)

앱의 UI에서 특정 플로우를 모듈화하기 위해 대상을 중첩 그래프로 그룹화할 수 있습니다. 이러한 예로는 독립적인 로그인 플로우가 있을 수 있습니다.

중첩 그래프는 메인 그래프처럼 대상을 그룹화하며 해당 경로에 대한 지정된 시작 대상이 필요합니다. 이것은 중첩된 그래프의 경로에 액세스할 때 이동할 위치입니다.

NavHost에 중첩된 그래프를 추가하려면 네비게이션 익스텐션 함수를 사용할 수 있습니다.
```  

<div class="content-ad"></div>

```js
NavHost(navController, startDestination = "home") {
    ...
    // 그래프를 통해 경로('login')로 이동하면 자동으로
    // 그래프의 시작 대상인 'username'으로 이동합니다.
    // 이로써 그래프의 내부 라우팅 로직을 캡슐화합니다.
    navigation(startDestination = "username", route = "login") {
        composable("username") { ... }
        composable("password") { ... }
        composable("registration") { ... }
    }
    ...
}
```

그래프가 커질수록 여러 메소드로 나누는 것이 좋습니다. 이렇게 하면 여러 모듈이 각자의 네비게이션 그래프를 기여할 수 있습니다.

```js
fun NavGraphBuilder.loginGraph(navController: NavController) {
    navigation(startDestination = "username", route = "login") {
        composable("username") { ... }
        composable("password") { ... }
        composable("registration") { ... }
    }
}
```

NavGraphBuilder를 확장 메소드로 만들면 미리 작성된 navigation, composable, dialog 익스텐션 메소드와 함께 사용할 수 있습니다.```

<div class="content-ad"></div>

```kotlin
NavHost(navController, startDestination = "home") {
    ...
    loginGraph(navController)
    ...
}
```

예시:

```kotlin
val navController = rememberNavController()
NavHost(navController = navController, startDestination = "home") {
    composable("about") {}
    navigation(
        startDestination = "login",
        route = "auth"
    ) {
        composable("login") {
            val viewModel = it.sharedViewModel<SampleViewModel>(navController)

            Button(onClick = {
                navController.navigate("calendar") {
                    popUpTo("auth") {
                        inclusive = true
                    }
                }
            }) {
            }
        }
        composable("register") {
            val viewModel = it.sharedViewModel<SampleViewModel>(navController)
        } 
        composable("forgot_password") {
            val viewModel = it.sharedViewModel<SampleViewModel>(navController)
        }
    }
    navigation(
        startDestination = "calendar_overview",
        route = "calendar"
    ) {
        composable("calendar_overview") { }
        composable("calendar_entry") { }
    }
}
```

NavBackStack entry를 위한 확장 함수

<div class="content-ad"></div>

```kotlin
@Composable
inline fun <reified T : ViewModel> NavBackStackEntry.sharedViewModel(navController: NavController): T {
    val navGraphRoute = destination.parent?.route ?: return viewModel()
    val parentEntry = remember(this) {
        navController.getBackStackEntry(navGraphRoute)
    }
    return viewModel(parentEntry)
}
```

# 하단 탐색 막대와 통합

조합 가능한 구조의 위쪽 수준에서 NavController를 정의함으로써, 네비게이션을 하단 탐색 막대와 같은 다른 구성 요소와 연결할 수 있습니다. 이를 통해 하단 막대에서 아이콘을 선택하여 탐색할 수 있습니다.

BottomNavigation 및 BottomNavigationItem 구성 요소를 사용하려면 Android 애플리케이션에 androidx.compose.material 종속성을 추가하십시오.
```

<div class="content-ad"></div>

```js
좌측 테이블을 Markdown 형식으로 변환했습니다.
```

하단 네비게이션바의 항목을 네비게이션 그래프의 루트에 링크하려면 Screen과 같은 sealed class를 정의하는 것이 좋습니다. 이 클래스는 목적지의 루트와 문자열 리소스 ID를 포함합니다.

```js
sealed class Screen(val route: String, @StringRes val resourceId: Int) {
    object Profile : Screen("profile", R.string.profile)
    object FriendsList : Screen("friendslist", R.string.friends_list)
}
```

이후 BottomNavigationItem에서 사용할 수 있는 리스트에 해당 항목을 넣으십시오.

<div class="content-ad"></div>

```kotlin
val items = listOf(
    Screen.Profile,
    Screen.FriendsList
)
```

BottomNavigation 컴포저에서 currentBackStackEntryAsState() 함수를 사용하여 현재 NavBackStackEntry를 가져옵니다. 이 엔트리를 통해 현재 NavDestination에 액세스할 수 있습니다. 각 BottomNavigationItem의 선택 상태는 아이템의 경로를 현재 목적지 및 부모 목적지의 경로와 비교하여 결정할 수 있습니다 (중첩된 내비게이션을 사용하는 경우 처리하기 위해 NavDestination 계층구조를 통해).

아이템의 경로는 또한 onClick 람다를 navigate 호출과 연결하는 데 사용되어 해당 아이템을 탭하면 해당 아이템으로 이동합니다. saveState 및 restoreState 플래그를 사용하여 해당 아이템의 상태와 백 스택이 올바르게 저장되고 전환될 때 해당 아이템의 상태가 올바르게 복원됩니다.

```kotlin
val navController = rememberNavController()
Scaffold(
    bottomBar = {
        BottomNavigation {
            val navBackStackEntry by navController.currentBackStackEntryAsState()
            val currentDestination = navBackStackEntry?.destination
            items.forEach { screen ->
                BottomNavigationItem(
                    icon = { Icon(Icons.Filled.Favorite, contentDescription = null) },
                    label = { Text(stringResource(screen.resourceId)) },
                    selected = currentDestination?.hierarchy?.any { it.route == screen.route } == true,
                    onClick = {
                        navController.navigate(screen.route) {
                            popUpTo(navController.graph.findStartDestination().id) {
                                saveState = true
                            }
                            launchSingleTop = true
                            restoreState = true
                        }
                    }
                )
            }
        }
    }
) { innerPadding ->
    NavHost(navController, startDestination = Screen.Profile.route, Modifier.padding(innerPadding)) {
        composable(Screen.Profile.route) { Profile(navController) }
        composable(Screen.FriendsList.route) { FriendsList(navController) }
    }
}
```  
```

<div class="content-ad"></div>

NavController.currentBackStackEntryAsState() 메소드를 활용하여 네비게이션 컨트롤러 상태를 NavHost 함수 밖으로 빼내어 BottomNavigation 컴포넌트와 공유합니다. 이렇게 하면 BottomNavigation이 항상 최신 상태를 가지게 됩니다.

읽어 주셔서 감사합니다. 🙌🙏✌

더 많은 안드로이드 개발, 코틀린 및 KMP에 관한 유용한 기사를 보시려면 박수를 날려주세요 👏 그리고 팔로우해 주세요.

안드로이드, 코틀린 및 KMP 관련 도움이 필요하시다면 언제든지 도와드리겠습니다.

<div class="content-ad"></div>

Medium, LinkedIn, Twitter, GitHub, Instagram에서 나를 팔로우하고 DM으로 앱 개발 프리랜싱 업무를 문의해주세요.