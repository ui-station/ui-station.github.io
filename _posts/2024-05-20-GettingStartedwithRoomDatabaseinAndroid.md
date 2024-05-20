---
title: "안드로이드에서 Room 데이터베이스 시작하기"
description: ""
coverImage: "/assets/img/2024-05-20-GettingStartedwithRoomDatabaseinAndroid_0.png"
date: 2024-05-20 15:54
ogImage: 
  url: /assets/img/2024-05-20-GettingStartedwithRoomDatabaseinAndroid_0.png
tag: Tech
originalTitle: "Getting Started with Room Database in Android"
link: "https://medium.com/@amitraikwar/getting-started-with-room-database-in-android-fa1ca23ce21e"
---


## Room 데이터베이스 구현에 대한 포괄적인 안내

![Android Room Database](/assets/img/2024-05-20-GettingStartedwithRoomDatabaseinAndroid_0.png)

## 소개:

로컬 데이터 저장은 많은 안드로이드 애플리케이션에게 중요하며, 데이터를 효율적으로 저장하고 검색할 수 있게 합니다. 이 안내서에서는 안드로이드 앱에서 데이터베이스 관리를 간편하게 하는 강력한 라이브러리인 Room을 살펴보겠습니다. Room 설정부터 데이터베이스 작업 수행 및 마이그레이션 처리까지 모두 다룰 것입니다.

<div class="content-ad"></div>

<img src="/assets/img/2024-05-20-GettingStartedwithRoomDatabaseinAndroid_1.png" />

## 섹션 1: Room 데이터베이스 설정하기

단계 1: 종속성 추가
앱의 `build.gradle` 모듈 레벨 파일을 열어 Room 및 Kotlin Coroutines (비동기 작업을 위한)에 필요한 종속성을 추가해주세요:

```js
gradle
dependencies {
 def roomVersion = "2.4.0" // 최신 버전을 확인하세요
 implementation "androidx.room:room-runtime:$roomVersion"
 kapt "androidx.room:room-compiler:$roomVersion"
 implementation "androidx.room:room-ktx:$roomVersion"
 implementation "org.jetbrains.kotlinx:kotlinx-coroutines-android:1.5.2" // 코루틴 종속성 추가
}
```

<div class="content-ad"></div>

OR

최신 안드로이드 및 Jetpack Compose에서 특정 오류로 실패하는 경우 'Kotlin 심볼 처리' ksp()을 추가해야 할 수도 있습니다.

아래 종속성 및 플러그인을 build.gradle(모듈 레벨)에 추가해보세요.

```js
plugins {
 .
 .
 id "com.google.devtools.ksp"
}

.
.
.

dependencies{
  // Room 종속성
    val room_version = "2.5.2"

    implementation("androidx.room:room-ktx:$room_version")
    // Kotlin 주석 처리 도구 (kapt) 사용을 위해
    ksp("androidx.room:room-compiler:$room_version")
}
```

<div class="content-ad"></div>

아래 클래스 경로를 build.gradle(앱 레벨)에 KSP에 추가해주세요.

```js
plugins {
    id "com.google.devtools.ksp" version "1.8.10-1.0.9" apply false
}
```

단계 2: 엔티티 클래스 생성
데이터베이스에서 테이블을 나타내기 위해 어노테이션을 사용하여 엔티티 클래스를 정의하세요. 예를 들어, `User` 엔티티를 생성해보겠습니다(각 데이터 멤버가 열 이름인 테이블로 간주합니다):

```js
import androidx.room.Entity
import androidx.room.PrimaryKey

@Entity(tableName = "users")
data class User(
 @PrimaryKey(autoGenerate = true) val id: Long = 0,
 val username: String,
 val email: String
)
```

<div class="content-ad"></div>

### Step 3: DAO (Data Access Object) Interface 생성
데이터베이스 작업을 정의하기 위한 DAO 인터페이스를 생성하세요. 예를 들어, `UserDao`를 만들어보겠습니다:

```kotlin
import androidx.room.Dao
import androidx.room.Insert
import androidx.room.OnConflictStrategy
import androidx.room.Query

@Dao
interface UserDao {

 @Insert(onConflict = OnConflictStrategy.REPLACE)
 suspend fun insertUser(user: User)

 @Query("SELECT * FROM users")
 suspend fun getAllUsers(): List<User>
}
```

### Step 4: 데이터베이스 클래스 정의
`RoomDatabase`를 확장하는 추상 클래스를 생성하여 데이터베이스 인스턴스를 정의하고 엔티티 및 DAO를 포함시키세요:

```kotlin
import androidx.room.Database
import androidx.room.RoomDatabase

@Database(entities = [User::class], version = 1)
abstract class AppDatabase : RoomDatabase() {
    abstract fun userDao(): UserDao
}
```

<div class="content-ad"></div>

Step 5: 데이터베이스 인스턴스 초기화하기
`Application` 클래스나 관련 진입점에서 Room 데이터베이스 인스턴스를 초기화하세요:

```kotlin
import android.app.Application
import androidx.room.Room

class MyApp: Application() {

    companion object {
        lateinit var database: AppDatabase
    }

    override fun onCreate() {
        super.onCreate()
        database = Room.databaseBuilder(
            applicationContext,
            AppDatabase::class.java,
            "my_database"
        ).build()
    }
}
```

## 섹션 2: 데이터베이스 작업 수행하기

Step 1: 데이터 삽입
사용자를 데이터베이스에 삽입하려면 `UserDao`에 정의된 `insertUser` 메서드를 사용할 수 있습니다:

<div class="content-ad"></div>

```js
val newUser = User(username = "JohnDoe", email = "john@example.com")
MyApp.database.userDao().insertUser(newUser)
```

단계 2: 데이터 검색
데이터베이스에서 모든 사용자를 검색하려면 `UserDao`의 `getAllUsers` 메서드를 사용하십시오:

```js
val userList: List<User> = MyApp.database.userDao().getAllUsers()
```

인젝션 가능한 Room 데이터베이스 객체를 설정하는 데모 프로젝트를 확인해주세요.

<div class="content-ad"></div>

데모 프로젝트 링크: https://github.com/raikwaramit/RoomDatabaseModule/

## 결론:

Android 앱에서 Room 데이터베이스를 구현하면 데이터 저장을 간편하게 처리할 수 있습니다. 직관적인 설정과 강력한 기능으로 앱의 로컬 데이터를 효율적으로 관리할 수 있습니다. 이 가이드를 따라가면 Room 설정, 엔티티 및 DAO 정의, 데이터베이스 작업 수행, 마이그레이션 처리 방법을 배울 수 있습니다.

이 가이드에서는 Room의 기본 사항을 다루었습니다. 라이브러리에 익숙해지면 데이터베이스 관계, LiveData 통합, 복잡한 쿼리와 같은 고급 기능을 탐색할 수 있습니다.

<div class="content-ad"></div>

룸을 사용하면 Android 앱에서 로컬 데이터를 관리하는 것이 더 쉬워집니다. 코딩을 즐기세요!