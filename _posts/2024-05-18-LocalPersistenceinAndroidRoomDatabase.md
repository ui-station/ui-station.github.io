---
title: "로컬 영속성Android Room 데이터베이스"
description: ""
coverImage: "/assets/img/2024-05-18-LocalPersistenceinAndroidRoomDatabase_0.png"
date: 2024-05-18 15:21
ogImage: 
  url: /assets/img/2024-05-18-LocalPersistenceinAndroidRoomDatabase_0.png
tag: Tech
originalTitle: "Local Persistence in Android: Room Database"
link: "https://medium.com/@pedroalvarez-29395/local-persistence-in-android-room-database-349e74f84ef5"
---


![Local Persistence in Android Room Database](/assets/img/2024-05-18-LocalPersistenceinAndroidRoomDatabase_0.png)

우리의 PokeAPI 시리즈를 이어서, 오늘은 사용자에게 즐겁게 포켓몬을 즐겨찾기할 수 있는 로컬 퍼시스턴스를 실험해 보겠습니다. 이를 위해 Room Database에 의존하며, Room Database는 데이터 모델을 저장하기 위한 SQLite 저장소입니다. Room에 액세스하는 패턴들은 Retrofit으로 API 요청을 생성하는 방식과 매우 유사합니다. 둘 다 Repository 패턴과 빌더를 사용하지만, 이를 한 단계씩 다룰 것입니다.

# SQL이란?

SQL 언어의 개념에 대해 대부분의 분들이 친숙할 것으로 기대합니다. 그러나 이 기사는 Android와 Kotlin에 더 초점을 맞추고 있기 때문에 간단히 소개하겠습니다. SQL은 관계 데이터베이스 테이블 작업에 사용되는 일반적인 언어로, 특정 데이터를 쿼리하거나 객체를 추가, 업데이트, 삭제하는 데 사용됩니다. 예를 들어, 만약 Person 테이블이 있고 18세 이상인 모든 행을 가져와 이름만 표시하려면 다음과 같이 합니다:

<div class="content-ad"></div>

```js
SELECT name
FROM person
WHERE age >= 18
```

1 . The SELECT 명령은 출력에 이름 열 만 투사합니다.

2. 우리는 person 테이블에서 데이터를 검색하고 있습니다.

3. 18세 이상인 행만 필터링하고 있습니다(age).

<div class="content-ad"></div>

위에서 언급한 대로, Android 개발에서 Room이 어떻게 작동하는지 살펴보겠습니다.

# 로컬 유지 보수

Room은 Android에서 로컬 데이터를 저장하는 가장 일반적인 솔루션이며, 이는 백엔드에서 원격 데이터를 처리하는 것과는 달라요. 모든 데이터는 내부 기기 데이터베이스에 저장되며, SQLite로 작동합니다. iOS에서는 이에 해당하는 솔루션으로 Core Data가 있습니다.

장치에 데이터 모델을 영구적으로 저장하기 위해서는 프로젝트에 구현해야할 중요한 구성 요소가 있어요. 먼저, Gradle 파일에 필요한 종속성을 동기화해보죠.

<div class="content-ad"></div>

```js
annotationProcessor("androidx.room:room-compiler:2.6.1")
implementation("androidx.room:room-runtime:2.6.1")
kapt("androidx.room:room-compiler:2.6.1")
implementation("androidx.room:room-ktx:2.6.1")
```

이제 Room 데이터베이스를 관리하는 가장 중요한 구성 요소를 구현할 수 있습니다. 아래는 우리 구조가 어떻게 보여야 하는지 입니다:

![Room Database 구조](/assets/img/2024-05-18-LocalPersistenceinAndroidRoomDatabase_1.png)

새로운 DAO와 같은 익숙하지 않은 층이 있는 것을 알 수 있습니다. 이에 대해 더 자세히 설명하겠습니다.
 

<div class="content-ad"></div>

# 포켓몬 DAO

저희 프로젝트에서는 Room이라는 SQLite 데이터베이스와 상호 작용하며, 모든 SQL DB처럼 SQL 쿼리를 사용하여 데이터를 조작하고 삽입합니다. DAO는 기본적으로 데이터베이스에서 상호 작용이 어떻게 이뤄지는 지를 정의하는 인터페이스입니다. Kotlin의 어노테이션의 놀라운 기능 덕분에 Kotlin 코드를 작성하지 않아도 데이터베이스를 관리하는 코드를 자동으로 생성할 수 있습니다. 어노테이션 자체가 메타데이터로 작동하여 데이터베이스를 관리하는 코드를 자동으로 생성합니다.

귀하의 프로젝트 어딘가에 이 인터페이스를 생성해주십시오:

```js
@Dao
interface PokemonDao {
    // 즐겨찾기 테이블의 모든 객체를 반환합니다
    @Query("SELECT * FROM favorites")
    fun getFavorites(): Flow<List<Favorite>>

    // 주어진 이름으로 포켓몬을 검색하여 해당 포켓몬을 반환합니다 (존재하면)
    @Query("SELECT * FROM favorites WHERE name = :name")
    suspend fun getFavoriteByName(name: String): Favorite?

    // 새로운 즐겨찾기 포켓몬을 삽입합니다
    @Insert(onConflict = OnConflictStrategy.REPLACE)
    suspend fun insertFavorite(favorite: Favorite)

    // 기존 포켓몬을 업데이트합니다
    @Update(onConflict = OnConflictStrategy.REPLACE)
    suspend fun updateFavorite(favorite: Favorite)

    // 즐겨찾기 테이블의 모든 행을 삭제합니다
    @Query("DELETE FROM favorites")
    suspend fun deleteAllFavorites()

    // 주어진 항목을 삭제합니다
    @Delete
    suspend fun deleteFavorite(favorite: Favorite)
}
```

<div class="content-ad"></div>

- Dao 어노테이션은 이 인터페이스가 새로운 클래스를 생성하여 Room DB에 직접 액세스할 것임을 나타냅니다.
- Query 어노테이션은 DB에서 트리거될 SQL 쿼리를 정의하며, 이는 테이블의 행과 열을 나타내는 객체를 반환하게 됩니다. 이 경우에는 Favorite 객체(또는 DB에서 비동기적으로 가져오는 객체의 Flow)입니다.

3. Insert 어노테이션은 입력된 객체가 다른 객체와 충돌할 경우(기본 키가 이미 존재하는 경우) 기존 객체를 새 객체로 대체하는 삽입 작업을 설명합니다.

4. Update 어노테이션은 주어진 행의 데이터를 새로운 데이터로 대체합니다.

5. Delete 어노테이션은 입력값으로 주어진 Favorite 행을 삭제함을 명시합니다.

<div class="content-ad"></div>

위의 모든 작업은 데이터베이스를 조작하는 SQL 작업을 나타냅니다. 주석은 Kapt가 새 코드를 생성하여 데이터베이스에 액세스하는 구체적인 구현을 수행할 것입니다.

# 엔티티

이제 엔티티를 구현해 봅시다. 어떤 데이터베이스 모델에서의 Entity는 테이블에 해당하며, 행과 열의 집합으로, 객체 목록을 지속한다는 것을 나타냅니다. Kotlin의 엔티티는 SQL 테이블 엔티티와 매우 비슷한 컨셉으로 작동합니다.

```js
@Entity(tableName = "favorites")
data class Favorite(
    @PrimaryKey(autoGenerate = false)
    @ColumnInfo(name = "id")
    val id: String,
    @ColumnInfo(name = "name")
    val name: String
)
```

<div class="content-ad"></div>

이 데이터 클래스는 Entity로 주석이 달려 있으며 "favorites"라는 이름의 SQL 테이블을 나타냅니다. 각 열(속성)은 해당 테이블에서 나타내는 내용에 대한 메타데이터가 달린 변수에 해당합니다.

- Entity는 데이터 클래스가 SQL 테이블에 매핑되었음을 나타냅니다.
- PrimaryKey는 속성이 테이블에서 행을 식별하는 것을 나타냅니다. 다른 속성이 무엇이든 해당 기본 키로 해당 행이 단수식별됩니다.
- ColumnInfo는 속성을 DB의 열 이름에 매핑합니다. 예를 들어, 이름 변수는 이름 열에 해당합니다.

# Room Database

이제 DB 자체를 나타내는 개체도 필요합니다. 이 객체는 우리에게 DAO라는 자체 인터페이스를 제공하는 책임이 있습니다. Kotlin 주석 처리 도구가 자동 생성하므로 다음과 같이 주석이 달린 추상 클래스를 만듭니다:

<div class="content-ad"></div>

```kotlin
@Database(entities = [Favorite::class], version = 1, exportSchema = false)
abstract class PokemonDatabase: RoomDatabase() {
    abstract fun pokemonDao(): PokemonDao
}
```

우리에게 의미하는 바:

- Database는 Favorite 모델로 표현된 단일 테이블로 구성된 Room DB 표현으로 우리의 클래스를 주석 처리합니다.
- PokemonDao를 반환하는 단일 추상 함수를 제공하여 DB에 액세스하려고 시도할 때 호출될 것입니다.

모든 것이 SQLite 로컬 데이터베이스에서 작동하는 코드를 생성할 것입니다.


<div class="content-ad"></div>

# 저장소

이전에 설명했듯이, 저장소 패턴은 UI 바깥의 원격 데이터 소스에 대한 패싸드 역할을 하는 레이어입니다. 일반적으로 MVVM 아키텍처를 채택할 때는 ViewModel이 액세스하며, VIPER/Clean과 함께 작업할 때는 인터랙터가 액세스합니다. 중요한 점은 저장소가 DAO 내의 모든 작업을 캡슐화하고 각 함수가 데이터 소스에 매핑되어야 한다는 것입니다:

```js
interface FavoritesRepositoryInterface {
    fun getFavorites(): Flow<List<Favorite>>
    suspend fun getFavoriteByName(name: String): Favorite?
    suspend fun insertFavorite(favorite: Favorite)
    suspend fun updateFavorite(favorite: Favorite)
    suspend fun deleteFavorite(favorite: Favorite)
    suspend fun deleteAllFavorites()
}

class FavoritesRepository(
    private val dao: PokemonDao
): FavoritesRepositoryInterface {
    override fun getFavorites(): Flow<List<Favorite>> = dao.getFavorites()
    override suspend fun getFavoriteByName(name: String): Favorite? = dao.getFavoriteByName(name)
    override suspend fun insertFavorite(favorite: Favorite) = dao.insertFavorite(favorite)
    override suspend fun updateFavorite(favorite: Favorite) = dao.updateFavorite(favorite)
    override suspend fun deleteFavorite(favorite: Favorite) = dao.deleteFavorite(favorite)
    override suspend fun deleteAllFavorites() = dao.deleteAllFavorites()
}
```

PokemonDao를 저장소에 주입하고 각 함수가 Dao에서 다른 함수를 호출하는 것을 주목하세요. 또한 저장소는 ViewModel에 인터페이스로서 액세스되어야 합니다.

<div class="content-ad"></div>

# Flow 출력 x Suspend 함수

첫 번째 방법이 다른 것들처럼 suspendable하지 않은 이유와 왜 모델 자체가 아닌 데이터의 Flow를 반환하는지 궁금할 수 있습니다. 실제로 이 작업은 비동기적일 것이지만 Flow 자체는 비동기적으로 도착하는 데이터의 흐름입니다. 이는 iOS에서의 Publisher와 동등하며, 데이터 소스가 보내는 많은 객체를 수신하기 위해 구독할 수 있지만, 우리는 데이터 수집 중이라고 말했다는 점에서 구독과는 다릅니다. 상태 Flow와 다르게, 이는 cold flow입니다.

![image](/assets/img/2024-05-18-LocalPersistenceinAndroidRoomDatabase_2.png)

두 가지의 차이는 다음과 같습니다:

<div class="content-ad"></div>

- 다른 곳에서 반환된 Flow는 비동기가 아니지만 수집하는 것은 비동기이므로 메인 스레드 바깥에서 수행해야 합니다.
- 일시 중단 함수를 호출할 때는 항상 코루틴에서 수행해야 합니다. 출력에 관계없이 항상 비동기 작업입니다.
- Flow를 관찰할 때는 Flow가 반환하는 객체를 수신하는 연속적인 링크이므로 관찰을 중지할 때까지 많은 객체를 수신합니다.

# Pokemon 세부 정보에서 즐겨찾기 추가

이제 Pokemon 세부 화면에서 Pokemon을 즐겨찾기 기능을 추가하는 데 필요한 UI 및 기능을 추가해 보겠습니다. 먼저 앱 바에 새로운 위젯을 추가해야 합니다:

```js
@OptIn(ExperimentalMaterial3Api::class)
@Composable
fun AppTopBar(
    title: String,
    shouldDisplayBackButton: Boolean,
    trailingIcon: @Composable (() -> Unit) = { },
    iconTapAction: (() -> Unit)
) {
    Surface(
        shadowElevation = 4.dp,
        color = Color.White
    ) {
        CenterAlignedTopAppBar(title = {
            Text(
                title, fontSize = 18.sp,
                fontWeight = FontWeight.Bold
            )
        }, navigationIcon = {
            if(shouldDisplayBackButton)
                IconButton(
                    onClick = { iconTapAction() }
                ) {
                    Icon(
                        imageVector = Icons.Default.ArrowBack,
                        contentDescription = null
                    )
                }
        }, actions = {
            trailingIcon()
        })
    }
}
```

<div class="content-ad"></div>

디자인 설명에 대해서는 신경 쓰지 말고, 우리의 상세 화면에 즐겨찾기 버튼을 좋아하는 아이콘으로 적용해보자. 아이콘 틴트 색상은 포켓몬이 즐겨찾기 상태인지 아닌지에 따라 달라질 것이다. 아래 내용을 PokemonDetailsViewModel에 추가해 주세요:

```kotlin
class PokemonDetailsViewModel(
    private val repository: PokemonDetailsRepositoryInterface,
    // New repository
    private val favoritesRepository: FavoritesRepositoryInterface
): ViewModel() {
    private val _pokemonDetails = MutableStateFlow<PokemonDetailsModel?>(null)
    private val _isLoading = MutableStateFlow<Boolean>(true)
    private val _gotError = MutableStateFlow<Boolean>(false)
    // Favorite state
    private val _isFavorite = MutableStateFlow<Boolean>(false)
    private var favoriteModel: Favorite? = null
    val pokemonDetails: StateFlow<PokemonDetailsModel?> get() = _pokemonDetails.asStateFlow()
    val isLoading: StateFlow<Boolean> get() = _isLoading.asStateFlow()
    val gotError: StateFlow<Boolean> get() = _gotError.asStateFlow()
    // Favorite state
    val isFavorite: StateFlow<Boolean> get() = _isFavorite.asStateFlow()

    fun fetchDetails(name: String) {
        viewModelScope.launch {
            _isLoading.value = true  
            // Checks favorite status before fetching data
            getIsFavorite(name)
            val result = repository.getPokemonDetails(name)
            val error = result.errorBody()
            val data = result.body()
            if (error != null || !result.isSuccessful) {
                Log.d("Got an error", "Got an error")
                _isLoading.value = false
                _gotError.value = true
                return@launch
            }
            if (data != null) {
                Log.d("Got data", "Got data")
                _isLoading.value = false
                _pokemonDetails.value = data
            } else {
                Log.d("Got nothing", "Got data")
                _isLoading.value = false
            }
        }
    }

    // Checks if the pokemon is favorite or not and delegates the 
    // corresponding operation to the Repository
    fun didClickFavorite() {
        viewModelScope.launch {
            if (_isFavorite.value) {
                favoriteModel?.let {
                    favoritesRepository.deleteFavorite(it)
                }
            } else {
                pokemonDetails.value?.let {
                    favoritesRepository.insertFavorite(Favorite("${it.id}", it.name))
                }
            }
        }
        _isFavorite.value = !_isFavorite.value
    }

    // Fetches in our FavoritesRepository if the pokemon is favorite by
    // checking the output object
    private suspend fun getIsFavorite(name: String) {
        favoriteModel = favoritesRepository.getFavoriteByName(name)
        _isFavorite.value = favoriteModel != null
    }
}
```

- 포켓몬 이름으로 Favorite 객체를 가져와서 해당 포켓몬이 즐겨찾기 상태인지 아닌지 확인합니다. 만약 null이면 즐겨찾기가 아닙니다. Repository는 DB에서 정보를 가져오기 위해 DAO와 통신할 것입니다.
- 포켓몬이 즐겨찾기 상태인지 여부에 관계없이 이를 isFavorite 상태 변수에 저장할 것입니다.
- 즐겨찾기 버튼을 클릭하면 즐겨찾기 상태가 확인되고, 이에 따라 포켓몬이 태그가 달릴 것인지 여부가 결정됩니다.

# UI 업데이트하기

<div class="content-ad"></div>

이제 우리의 PokemonDetailsScreen을 업데이트하여 사용자 상호 작용에 따라 새 이벤트를 ViewModel로 위임하겠습니다. 맨 위에 있는 새로운 앱 바를 추가하기 위해 Scaffold에 넣어주세요:

```js
@Composable
fun PokemonDetailsScreen(
    navController: NavController,
    name: String,
    viewModel: PokemonDetailsViewModel = get()
) {
    // MARK: - State
    val pokemonDetails = viewModel.pokemonDetails.collectAsState()
    val isLoading = viewModel.isLoading.collectAsState()
    val gotError = viewModel.gotError.collectAsState()
    // Favorite State
    val isFavorite = viewModel.isFavorite.collectAsState()

    LaunchedEffect(pokemonDetails) {
        viewModel.fetchDetails(name)
    }

    // Scaffold
    Scaffold(topBar = {
        // New app bar
        AppTopBar(
            title = "Pokemon Details",
            shouldDisplayBackButton = true,
            trailingIcon = {
                // Manipulates and represents favorite state
                IconButton(onClick = {
                    viewModel.didClickFavorite()
                }) {
                    Icon(
                        imageVector = Icons.Default.Favorite,
                        contentDescription = null,
                        tint = if(isFavorite.value) Color.Red else Color.LightGray
                    )
                }
            },
            iconTapAction = { navController.popBackStack() }
        )
    }) {
        Content(
            modifier = Modifier.padding(it),
            isLoading = isLoading.value,
            gotError = gotError.value,
            pokemonDetails = pokemonDetails.value
        )
    }
}
```

AppTopBar composable에 주목하고 trailing icon이 즐겨찾기 상태를 나타내는지 확인해주세요. 버튼을 클릭하면 해당 이벤트가 ViewModel로 위임됩니다.

이것이 우리의 상세 화면이 보이는 방식입니다:

<div class="content-ad"></div>

<img src="https://miro.medium.com/v2/resize:fit:1400/1*V6RtRHdYOsE2nyptLC7PFQ.gif" />

이제 우리는 모든 즐겨찾기 아이템을 제공하는 새로운 화면을 만들겠습니다.

# 즐겨찾기 화면

이제 우리는 즐겨찾기 포켓몬을 표시할 새로운 화면을 만들 것입니다. 즐겨찾기 목록을 모두 가져오고 데이터를 적절히 조작할 ViewModel을 만들어주세요. 이 ViewModel은 Repository에 접근할 수 있을 것입니다:

<div class="content-ad"></div>

``` kotlin
class FavoriteListViewModel(
    private val repository: FavoritesRepositoryInterface
): ViewModel() {
    private val _favoriteList = MutableStateFlow<List<Favorite>>(listOf())

    val favoriteList: StateFlow<List<Favorite>> get() = _favoriteList

    init {
        getFavoritePokemon()
    }

    private fun getFavoritePokemon() {
        viewModelScope.launch {
            repository
                .getFavorites()
                .distinctUntilChanged()
                .map { it.sortedBy { it.id } }
                .collect { _favoriteList.value = it }
        }
    }

    fun didClickDelete(favorite: Favorite) {
        viewModelScope.launch {
            repository.deleteFavorite(favorite)
        }
    }
}
```

알 수 있듯이, 해당 코드는 즐겨찾는 포켓몬 목록을 제공하며, 시작할 때 가져오며, 특정 포켓몬을 지우기 위해 트리거되는 삭제 기능을 제공합니다. 사용자가 특정 포켓몬을 삭제하려고 탭할 때 작동합니다.

이제 새로운 화면을 만들고 내비게이션 그래프에 통합해 보겠습니다. 좀 더 정교한 디자인으로 즐겨찾는 셀을 만들겠지만, UI 부분은 이 글의 초점이 아닙니다:

``` kotlin
@Composable
fun FavoriteListScreen(
    navController: NavController,
    viewModel: FavoriteListViewModel = get()) {
    val favoriteList = viewModel.favoriteList.collectAsState()

    Scaffold(topBar = {
        AppTopBar(
            title = "Favorite Pokemon",
            shouldDisplayBackButton = true) {
            navController.popBackStack()
        }
    }) {
        Surface(
            modifier = Modifier
                .fillMaxSize()
                .padding(it),
            color = Color.White
        ) {
            // Favorite pokemon lazy list
            LazyColumn(
                modifier = Modifier.padding(20.dp),
                horizontalAlignment = Alignment.CenterHorizontally,
                verticalArrangement = Arrangement.spacedBy(8.dp)
            ) {
                items(favoriteList.value) { item ->
                    // Favorite Row
                    FavoriteRow(favorite = item, clickAction = {
                        // 셀을 클릭하면 상세 화면으로 이동합니다.
                        navController.navigate("details/${item.name}")
                    }) {
                        // 삭제 버튼을 클릭하면 DB에서 포켓몬을 삭제합니다.
                        viewModel.didClickDelete(item)
                    }
                }
            }
        }
    }
}

@Composable
private fun FavoriteRow(
    modifier: Modifier = Modifier,
    favorite: Favorite,
    clickAction: (() -> Unit),
    deleteAction: (() -> Unit)
) {
    Surface(
        modifier = modifier
            .fillMaxWidth()
            .height(64.dp)
            .clickable { clickAction() },
        shape = RoundedCornerShape(
            topStart = CornerSize(32.dp),
            topEnd = CornerSize(4.dp),
            bottomStart = CornerSize(32.dp),
            bottomEnd = CornerSize(32.dp)
        ),
        color = Color(0xFFADD8E6)
    ) {
        Row(
            modifier = Modifier
                .fillMaxWidth()
                .padding(horizontal = 56.dp),
            verticalAlignment = Alignment.CenterVertically,
            horizontalArrangement = Arrangement.SpaceBetween,
        ) {
            // 포켓몬 이름
            Text(
                favorite.name,
                fontSize = 20.sp
            )

            // 삭제 버튼
            IconButton(onClick = deleteAction) {
                Icon(
                    imageVector = Icons.Default.Delete,
                    contentDescription = null,
                    tint = Color(0x89FF1F0F)
                )
            }
        }
    }
}
```

<div class="content-ad"></div>

저희 화면은 이렇게 나와야 합니다:

![이미지](/assets/img/2024-05-18-LocalPersistenceinAndroidRoomDatabase_3.png)

이제 앱 내비게이션 레이어에 새 경로를 추가해 보겠습니다:

```javascript
@Composable
fun PokeAPIApp(navController: NavHostController = rememberNavController()) {
    NavHost(navController = navController, startDestination = "list") {
        composable("list") {
            PokemonListScreen(navController = navController, viewModel = get())
        }

        composable(
            "details/{id}",
            arguments = listOf(navArgument("id") { type = NavType.StringType }),
            enterTransition = {
                slideIntoContainer(
                    towards = AnimatedContentTransitionScope.SlideDirection.Companion.Left,
                    animationSpec = tween(700)
                )
            },
            exitTransition = {
                slideOutOfContainer(
                    towards = AnimatedContentTransitionScope.SlideDirection.Companion.Right,
                    animationSpec = tween(700)
                )
            }
        ) { backStackEntry ->
            backStackEntry.arguments?.getString("id")
                ?.let { PokemonDetailsScreen(navController = navController, name = it) }
        }

        composable("favorites") {
            FavoriteListScreen(navController = navController)
        }
    }
}
```

<div class="content-ad"></div>

# 메인 화면에 링크 걸기

즐겨찾기 화면에 접근하려면 주요 허브를 약간 변경하여 새로운 플로팅 버튼을 지원하도록 해야 합니다. PokemonListScreen 콘텐츠를 Scaffold에 포함시키고 새로운 FavoriteButton composable을 새 화면으로 연결하는 플로팅 버튼으로 배치해주세요:

```js
@Composable
fun FavoriteButton(
    modifier: Modifier = Modifier,
    onClickAction: (() -> Unit)
) {
    IconButton(
        modifier = modifier,
        onClick = onClickAction
    ) {
        Surface(
            shape = CircleShape,
            color = Color.Blue
        ) {
            Icon(
                modifier = Modifier.padding(8.dp),
                imageVector = Icons.Default.Favorite,
                contentDescription = null,
                tint = Color.White
            )
        }
    }
}
```

```js
@OptIn(ExperimentalMaterial3Api::class)
@Composable
fun PokemonListScreen(
    navController: NavController,
    viewModel: PokemonListViewModel = get()
) {
    val pagingData = viewModel.pagingData.collectAsLazyPagingItems()

    // Scaffold
    Scaffold(topBar = {
        Surface(
            shadowElevation = 4.dp,
            color = Color.White
        ) {
            // App bar
            AppTopBar(title = "Pokemon List", shouldDisplayBackButton = false) { }
        }
    }, floatingActionButton = {
        // 즐겨찾기 목록으로 이동하는 링크
        FavoriteButton(
            modifier = Modifier
                .padding(bottom = 16.dp)
        ) {
            navController.navigate("favorites")
        }
    }) {
        // 기존 코드!!!
        Surface(
            modifier = Modifier
                .fillMaxSize()
                .padding(it),
            color = MaterialTheme.colorScheme.background
        ) {
            Column(
                modifier = Modifier.fillMaxSize(),
                horizontalAlignment = Alignment.CenterHorizontally,
                verticalArrangement = Arrangement.Top
            ) {
                LazyColumn {
                    items(pagingData.itemCount) {
                        val name = pagingData[it]?.name ?: ""
                        PokemonCell(
                            index = "${it+1}",
                            name = name
                        ) {
                            navController.navigate("details/$name")
                        }
                    }
                    pagingData.apply {
                        when {
                            loadState.refresh is LoadState.Loading -> {
                                item { CircularProgressIndicator() }
                            }
                            loadState.refresh is LoadState.Error -> {
                                item {
                                    ErrorState()
                                }
                            }
                            loadState.append is LoadState.Loading -> {
                                item { CircularProgressIndicator() }
                            }
                            loadState.append is LoadState.Error -> {
                                item {
                                    ErrorState()
                                }
                            }
                        }
                    }
                }
            }
        }
    }
}
```

<div class="content-ad"></div>

이제 인터페이스가 이렇게 보입니다:

![interface](https://miro.medium.com/v2/resize:fit:1400/1*H3CYZpUd982OPtcJ-vcKDw.gif)

우리의 UI는 거의 완료되었지만, 한 가지 매우 중요한 부분을 잊고 있습니다: 의존성 주입.

# 앱 모듈 업데이트

<div class="content-ad"></div>

저희는 아직 데이터베이스와 DAO를 어떻게 생성할지 정의하지 않았어요. 각각을 위한 함수가 필요해요:

```js
fun providePokemonDao(database: PokemonDatabase): PokemonDao {
    return database.pokemonDao()
}

fun providePokemonDatabase(context: Context): PokemonDatabase {
    return Room.databaseBuilder(
        context,
        PokemonDatabase::class.java,
        "weather_database"
    )
        .fallbackToDestructiveMigration()
        .build()
}
```

DB의 생성은 Retrofit API 인터페이스를 인스턴스화하는 패턴과 매우 유사하지만, 이번에는 RoomDatabase 서브 클래스, 앱 컨텍스트, 그리고 DB 이름을 전달해요. DAO는 PokemonDatabase 클래스의 자동 생성된 함수에서만 반환되네요.

이제 모듈에 다음 DSL 표현식을 포함해주세요:

<div class="content-ad"></div>


단일<PokemonDatabase> {
   providePokemonDatabase(androidContext())
 }

단일<PokemonDao> {
    providePokemonDao(get())
 }

viewModel { PokemonDetailsViewModel(get(), get()) }

단일<FavoritesRepositoryInterface> { FavoritesRepository(get()) }
viewModel { FavoriteListViewModel(get()) }


이제 우리 앱이 작동할 준비가 되었습니다!

# 결론

이 문서는 Android 앱에서 데이터를 로컬로 영속화하는 다양한 방법 중 하나를 소개했습니다. SQL을 사용하여 꽤 조직화된 패턴으로 Room 데이터베이스를 다루는 것은 관계형 데이터베이스의 SQL 테이블을 조작하는 것과 매우 유사하지만 네트워크 연결이 필요하지 않습니다. 삽입, 업데이트, 쿼리 및 삭제 작업을 조작할 수 있으며 모든 작업은 Repository 패턴을 통해 추상화될 수 있습니다. 즐겁게 읽으셨기를 바랍니다 ;)
