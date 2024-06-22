---
title: "Flutter DropdownButtonFormField에서 텍스트 넘침 방지하는 방법"
description: ""
coverImage: "/assets/img/2024-06-22-PreventingTextOverflowinFluttersDropdownButtonFormField_0.png"
date: 2024-06-22 23:30
ogImage: 
  url: /assets/img/2024-06-22-PreventingTextOverflowinFluttersDropdownButtonFormField_0.png
tag: Tech
originalTitle: "Preventing Text Overflow in Flutter’s DropdownButtonFormField"
link: "https://medium.com/@paramjeet.singh0199/preventing-text-overflow-in-flutters-dropdownbuttonformfield-337d679fdce3"
---


# 문제: RenderFlex 오버플로우

플러터에서 DropdownButtonFormField를 사용할 때 긴 텍스트 항목은 레이아웃 문제를 일으킬 수 있습니다. 텍스트가 너무 길면 다음 오류를 만날 수 있습니다:

```js
======== Exception caught by rendering library =====================================================
The following assertion was thrown during layout:
A RenderFlex overflowed by 14 pixels on the right.
```

이 문제는 드롭다운 항목의 텍스트가 사용 가능한 너비를 초과하여 오버플로우가 발생하기 때문에 발생합니다.

<div class="content-ad"></div>

# 해결책: isExpanded 사용하기

이 문제의 간단한 해결책은 DropdownButtonFormField의 isExpanded 속성을 true로 설정하는 것입니다. 이렇게 하면 드롭다운 버튼과 해당 아이템이 부모 컨테이너의 전체 너비를 차지하도록해 오버플로우를 방지합니다.

# 코드 예시

다음은 이 해결책을 구현하는 방법을 보여주는 간결한 예시 코드입니다:

<div class="content-ad"></div>

```dart
Widget _buildDropdownButtonFormField() {
  return DropdownButtonFormField<String>(
    isDense: true,
    isExpanded: true, // Key property to handle text overflow
    value: _selectedValue,
    style: const TextStyle(fontWeight: FontWeight.bold),
    dropdownColor: const Color(0xFF55565A),
    decoration: InputDecoration(
      labelText: 'Select Item',
      hintText: 'Please select an item',
      labelStyle: Theme.of(context).textTheme.labelMedium,
    ),
    items: ['Item 1', 'A very long item text that overflows', 'Item 3'].map((String item) {
      return DropdownMenuItem<String>(
        value: item,
        child: Text(
          item,
          softWrap: true,
          overflow: TextOverflow.ellipsis,
          style: Theme.of(context).textTheme.titleMedium?.copyWith(fontWeight: FontWeight.w600),
        ),
      );
    }).toList(),
    onChanged: (newValue) {
      setState(() {
        _selectedValue = newValue;
      });
    },
    validator: (value) => value == null ? 'Please select an item' : null,
  );
}
```

## 작동 방식

isExpanded: true로 설정하면 드롭다운 버튼과 항목이 사용 가능한 너비에 맞게 확장됩니다. TextOverflow.ellipsis 속성은 여전히 너무 긴 텍스트를 자르고, 더 많은 텍스트가 있다는 것을 나타내기 위해 생략 부호를 추가합니다.

## 결론


<div class="content-ad"></div>

DropdownButtonFormField에서 isExpanded 속성을 사용하면 텍스트 오버플로 문제를 쉽고 효과적으로 처리할 수 있어요. 이 작은 변경은 드롭다운 메뉴 레이아웃을 개선하여 깔끔하고 사용자 친화적인 인터페이스를 보장해 줘요.