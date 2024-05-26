---
title: "자바에서 엔티티를 DTO로 매핑하고 그 반대로하기"
description: ""
coverImage: "/assets/img/2024-05-23-MappingEntitiestoDTOsandviceversainJava_0.png"
date: 2024-05-23 12:41
ogImage:
  url: /assets/img/2024-05-23-MappingEntitiestoDTOsandviceversainJava_0.png
tag: Tech
originalTitle: "Mapping Entities to DTOs and vice versa in Java"
link: "https://medium.com/thefreshwrites/mapping-entities-to-dtos-and-vice-versa-in-java-fe126f6bb6b2"
---


![Mapping entities to DTOs in Java](/assets/img/2024-05-23-MappingEntitiestoDTOsandviceversainJava_0.png)

In Java, entities and DTOs are two different types of classes often used together. Entities are used to represent data in the database, while DTOs are used to represent data that is sent to or received from a client.

To map data from an entity to a DTO, you need to define a mapping between the attributes of the two classes. This can be done manually or automatically.

## Manual mapping


<div class="content-ad"></div>

수동 매핑은 엔티티를 DTO로 매핑하는 가장 간단한 방법입니다. 이 접근 방식에서는 엔티티의 속성에서 DTO의 속성으로 값을 복사하는 코드를 작성합니다.

예를 들어, 다음 코드는 Customer 엔티티의 속성을 CustomerDTO의 속성에 매핑합니다:

```js
public CustomerDto toDto(Customer customer) {
  CustomerDto dto = new CustomerDto();
  dto.setName(customer.getName());
  dto.setCpf(customer.getCpf());
  dto.setDateOfBirth(customer.getDateOfBirth());
  return dto;
}
```

이 방법은 간단하고 유연하지만, 특히 복잡한 엔티티나 많은 매핑이 있는 경우에는 반복적이고 오류가 발생할 수 있습니다.

<div class="content-ad"></div>

# 자동 매핑

자동 매핑은 엔티티를 DTO로 매핑하는 더 효율적이고 신뢰할 수 있는 방법입니다. 이 방법을 사용하면 프레임워크나 라이브러리를 사용하여 매핑 코드를 자동으로 생성합니다.

자동 매핑을 위한 인기 있는 라이브러리 중 하나는 MapStruct입니다. MapStruct(https://mapstruct.org/)는 도메인별 언어(Domain-Specific Language, DSL)를 사용하여 매핑 규칙을 정의합니다.

예를 들어, 다음 코드는 Customer 엔티티의 속성을 CustomerDTO의 속성에 매핑하는 매핑 규칙을 정의합니다:

<div class="content-ad"></div>

```js
@Mapper
public interface CustomerMapper {

  CustomerDto toDto(Customer customer);

}
```

위 코드는 Customer 엔티티를 입력으로 받아 CustomerDTO를 출력으로 반환하는 toDto() 메서드를 정의합니다. MapStruct는 이 두 클래스의 속성 간 매핑을 결정하기 위해 메서드의 이름을 사용합니다.

MapStruct는 DTO에서 엔티티로 값들을 매핑하는 데에도 사용할 수 있습니다. 예를 들어, 다음 코드는 CustomerDTO의 속성들을 Customer의 속성들과 매핑하기 위한 매핑 규칙을 정의합니다:

```js
@Mapper
public interface CustomerMapper {

  Customer fromDto(CustomerDto dto);

}
```

<div class="content-ad"></div>

ModelMapper라고 하는 강력한 라이브러리도 있어요. 이 라이브러리는 객체 매핑 프로세스를 간단하게 해주며 매핑 동작을 사용자 정의하는 데 많은 유연성을 제공해요.

Java 프로젝트에서 ModelMapper를 사용하려면 프로젝트에 ModelMapper 라이브러리를 추가해야 해요. 만약 Maven을 사용 중이라면 다음 종속성을 프로젝트의 pom.xml 파일에 추가하세요:

```js
<dependency>
  <groupId>org.modelmapper</groupId>
  <artifactId>modelmapper</artifactId>
  <version>2.4.2</version>
</dependency>
```

만약 Gradle을 사용 중이라면 build.gradle 파일에 다음을 추가하세요:

<div class="content-ad"></div>


implementation 'org.modelmapper:modelmapper:2.4.2'


프로젝트에 ModelMapper 라이브러리를 추가한 후, 서로 다른 구조를 갖는 두 개의 객체 간에 매핑을 시작할 수 있습니다.

예를 들어, 서로 다른 구조를 갖는 두 개의 객체가 있고, 이들 사이의 데이터를 매핑하려고 한다고 가정해봅시다. 아래는 예시입니다:

```java
public class User {
    private String name;
    private int age;

    // 생성자, 게터, 세터 메서드
}

public class UserDTO {
    private String fullName;
    private int userAge;

    // 생성자, 게터, 세터 메서드
}

public class Main {
    public static void main(String[] args) {
        User user = new User("John", 30);
        ModelMapper modelMapper = new ModelMapper();
        UserDTO userDTO = modelMapper.map(user, UserDTO.class);
        System.out.println(userDTO.getFullName()); // 출력: John
        System.out.println(userDTO.getUserAge()); // 출력: 30
    }
}
```

<div class="content-ad"></div>

위의 예에서는 User 및 UserDTO라는 두 개의 다른 구조를 갖는 두 개의 객체가 있습니다. User 객체에는 이름(name)과 나이(age) 필드가 있고, UserDTO 객체에는 풀 네임(fullName)과 사용자 나이(userAge) 필드가 있습니다. User 객체를 UserDTO로 매핑하기 위해 ModelMapper 인스턴스를 사용합니다. map() 메서드는 소스 객체와 대상 객체 클래스 두 가지 인수를 사용합니다.

main 메서드를 실행하면 출력으로 John과 30이 나오는데, 이는 매핑이 성공적으로 수행되었음을 나타냅니다.

ModelMapper를 사용하고 사용자화하는 방법에 대한 자세한 안내를 원하시면 다음 기사를 참고하십시오: [링크](http://bit.ly/4b4b5sz)

# 따라서, 어떤 접근 방식을 선택해야 할까요?

<div class="content-ad"></div>

엔티티를 DTO로 매핑하는 가장 좋은 방법은 여러 요소에 따라 다릅니다. 일반적으로, 간단한 클래스나 매핑이 적은 경우 수동 매핑이 좋은 선택이 됩니다. 복잡한 클래스나 많은 매핑이 필요한 경우 자동 매핑이 좋은 선택입니다.

다음은 접근 방식을 선택할 때 고려해야 할 사항입니다:

- 관련된 클래스의 복잡성: 많은 속성을 가진 복잡한 클래스는 수동 매핑이 반복적이고 오류를 발생하기 쉽게 만들 수 있습니다. 이러한 클래스에 대해 자동 매핑이 좋은 선택일 수 있습니다.
- 필요한 매핑의 양: 많은 매핑이 필요한 클래스는 수동 매핑을 유지하기 어렵게 만들 수 있습니다. 이러한 클래스에 대해 자동 매핑이 좋은 선택일 수 있습니다.
- 사용자 정의 필요성: 수동 매핑을 통해 필요에 맞게 매핑을 사용자 정의할 수 있습니다. 자동 매핑은 더 제한적일 수 있습니다.

최종적으로, 엔티티를 DTO로 매핑하는 가장 좋은 방법은 애플리케이션의 특정 요구사항을 충족시키는 방법입니다.

<div class="content-ad"></div>

## 추가 고려 사항

위에서 언급된 요소들 외에도, 엔티티들을 DTO에 매핑할 때 고려해야 할 몇 가지 사항이 있습니다:

- 데이터 유형: 두 클래스의 속성들의 데이터 유형이 호환되는지 확인하세요.
- Null 값: Null 값을 어떻게 처리할지 고려해보세요.
- 컬렉션: 두 클래스 중 하나가 속성의 컬렉션을 포함하고 있다면, 컬렉션 내 각 요소에 대한 매핑을 정의해야 합니다.

이러한 사항들을 따르면, 매핑 코드가 정확하고 효율적임을 보장할 수 있습니다.

<div class="content-ad"></div>

# 결론

MapStruct와 ModelMapper는 Java 프로젝트에서 엔티티를 DTO로 매핑하거나 그 반대로 하는 데 편리하고 효율적인 솔루션을 제공합니다. 이러한 라이브러리를 도입함으로써 개발자는 보일러플레이트 코드를 크게 줄이고 코드 유지 보수성을 향상시키며 타입 안정성을 보장할 수 있습니다. MapStruct 또는 ModelMapper를 프로젝트에 통합하면 매핑 프로세스를 간소화하여 견고하고 확장 가능한 애플리케이션을 구축하는 데 집중할 수 있습니다.

즐거운 코딩 ;)

아래 기사들도 참고해보세요
