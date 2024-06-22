---
title: "2024 최신 Flutter에서 Bloc 사용법 효율적인 상태 관리 가이드"
description: ""
coverImage: "/assets/img/2024-06-22-Flutter-Bloc_0.png"
date: 2024-06-22 23:16
ogImage: 
  url: /assets/img/2024-06-22-Flutter-Bloc_0.png
tag: Tech
originalTitle: "Flutter - Bloc"
link: "https://medium.com/@semihaltin99/flutter-bloc-c47dd9014ab0"
---


![Bloc](/assets/img/2024-06-22-Flutter-Bloc_0.png)

## Bloc이란?

Bloc은 '비즈니스 로직 구성 요소'의 약자로, Google에서 권장하는 패턴 중 하나입니다. 이 패턴은 비즈니스 로직을 UI로부터 분리할 수 있도록 도와줍니다. 또한 응용 프로그램에서 책임을 분리하여 쉽게 테스트 가능한 코드를 작성할 수 있는 패턴입니다. Google에서 권장하는 다른 패턴은 여기에서 확인할 수 있습니다.

Bloc은 세 가지 구조로 구성됩니다. 이들은 이벤트 클래스, 상태 클래스 및 Bloc 클래스입니다. 이제 이들을 좀 더 자세히 살펴보겠습니다.

<div class="content-ad"></div>

![img](/assets/img/2024-06-22-Flutter-Bloc_1.png)

1. 이벤트:
이벤트는 응용 프로그램에서 수행하려는 작업을 나타냅니다. 연락처 응용 프로그램을 고려해보세요. 이 경우, 이벤트는 GetUsers, UpdateUser, DeleteUser 등이 될 것입니다. 이 이벤트에 대한 추상 클래스가 있으며, 다른 이벤트들은 이 추상 클래스를 상속받아 사용합니다.

응용 프로그램에서 수행하려는 작업에 따라 직접 이벤트를 만들어야 합니다.

2. 상태:
상태는 응용 프로그램에서 발생할 수 있는 상황을 나타냅니다. 어떤 상황에서 무엇을 해야 할지를 결정하는 데 사용됩니다. 연락처 앱의 경우 UsersInitial, UsersLoading, UsersLoaded 및 UsersError 등이 있을 수 있습니다.

<div class="content-ad"></div>

3. 블록:
Bloc 클래스를 중간 클래스로 생각할 수 있습니다. 이 클래스는 이벤트와 상태를 연결합니다. 이벤트가 트리거되면, 어떤 함수가 호출될지와 어떤 상태로 애플리케이션이 전환될지는 블록 클래스에서 결정됩니다.

## 블록 사용

저는 Bloc을 사용하여 프로젝트를 개발했습니다. 이 프로젝트에는 화면이 있습니다. 이 화면에서는 The Last Airbender에서 캐릭터를 무작위로 가져올 수 있습니다. 저는 Clean Architecture로 프로젝트를 개발했지만, 이 이야기에서는 블록의 사용법만 설명합니다. Clean Architecture에 대한 자세한 내용은 여기에서 읽을 수 있습니다. 프로젝트 개발 중에는 The Last Airbender API를 사용했습니다.

## 예제 채우기

<div class="content-ad"></div>

먼저 코어, 구성 및 기능 폴더를 생성했습니다. 그런 다음 fetch characters의 기능을 위해 features 폴더 내에 characters 폴더를 만들었습니다. 마지막으로 characters 폴더 내에 데이터, 도메인 및 프리젠테이션 폴더를 생성했습니다.
기능 폴더에 bloc 폴더를 만들었습니다. 이는 Bloc이 애플리케이션의 비즈니스 로직에 사용되기 때문입니다.

Bloc의 세 부분인 이벤트, 상태 및 bloc 파일을 추가했습니다.

![2024-06-22-Flutter-Bloc_2.png](/assets/img/2024-06-22-Flutter-Bloc_2.png)

## 코드 예시

<div class="content-ad"></div>

먼저, 제가 CharactersInitial, CharactersLoading, CharactersError 및 CharactersLoaded 상태를 결정했고 CharactersState를 추상 클래스로 만들었습니다. 그런 다음 다른 상태에 CharactersState에서 상속을 제공했습니다.

```js
part of 'characters_bloc.dart';

abstract class CharactersState {}

class CharactersInitial extends CharactersState {}

class CharactersLoading extends CharactersState {}

class CharactersLoaded extends CharactersState {
  CharacterEntity character;
  CharactersLoaded({required this.character});
}

class CharactersError extends CharactersState {
  String? errorMessage;
  CharactersError(this.errorMessage);
}
```

둘째로, 이벤트를 위한 추상 클래스를 만들었습니다. 이 어플리케이션에서는 캐릭터만 가져올 것이기 때문에 GetCharacter 이벤트만 생성했습니다. 그리고 CharactersEvent 클래스에서 GetCharacters 클래스로 상속을 제공했습니다.

```js
part of 'characters_bloc.dart';

abstract class CharactersEvent {}

class GetCharacters extends CharactersEvent {}
```

<div class="content-ad"></div>

Bloc으로부터 CharactersBloc으로 상속을 받았고, 클래스의 생성자 메서드에 이벤트를 추가했습니다. 그런 다음 데이터를 가져오기 위해 getCharacters 함수를 만들었습니다. 이 함수에서 API 결과에 따라 상황을 트리거했습니다.

```js
part 'characters_event.dart';
part 'characters_state.dart';

class CharactersBloc extends Bloc<CharactersEvent,CharactersState> {
   final GetCharacterUseCase _getCharactersUseCase;
   CharactersBloc(this._getCharactersUseCase) : super(CharactersInitial()){
     on<GetCharacters>(getCharacters);
   }


   Future<void> getCharacters(GetCharacters event, Emitter<CharactersState> emit) async {
     emit(CharactersLoading());
     final dataState = await _getCharactersUseCase.call(null);
     if(dataState is DataSuccess){
       emit(CharactersLoaded(character: dataState.data!));
     } else if(dataState is DataFailed){
       emit(CharactersError(dataState.exception!.message));
     }
   }
}
```

마지막으로 CharactersPage를 작성했습니다. 상태를 감시하기 위해 BlocBuilder를 사용했습니다. 이것은 상태가 변경되었을 때 관련 함수를 반환할 수 있기 때문입니다.

```js
_buildBloc() {
    return BlocBuilder(
      bloc: _bloc,
      builder: (BuildContext context, state) {
        if(state is CharactersLoading){
          return _showLoadingAnimation();
        } else if(state is CharactersLoaded){
          return _buildCharactersList(state);
        } else if(state is CharactersError){
          return _buildErrorView(state);
        } else {
          return _buildInitialView();
        }
      },
    );
  }
```

<div class="content-ad"></div>

화면이 로드되면 GetCharacters 이벤트를 트리거합니다.

```dart
@override
void initState() {
  super.initState();
  _bloc.add(GetCharacters());
}
```

자세히 살펴보고 싶다면 GitHub 링크를 여기에서 찾을 수 있어요.

😊 읽어 주셔서 감사합니다 😊