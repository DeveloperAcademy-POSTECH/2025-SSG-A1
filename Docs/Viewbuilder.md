## GQ

- return 없이 화면이 그려질 수 있는 이유가 뭘까?
	- ![[Pasted image 20250707195729.png]]
- → viewbuilder가 있기 때문이다.

SwiftUI에서는 body 블록이 자동으로 @ViewBuilder로 처리되기 때문에, 여러 개의 뷰를 작성해도 return 없이 하나의 뷰로 묶여서 반환된다.

## 정의

### 클로저에서 뷰를 구성하는 사용자 정의 매개변수 속성

```swift
@resultBuilder struct ViewBuilder
```

→ @resultbuilder는 뭐지?

## resultbuilder
## 정의

- 선택한 여러 요소들을 순서대로 전달하면서 **하나의 새로운 값을 단계별로 만들어낼 수 있게 해주는 기능**
    
- 예를 들어 VStack 안에 여러 개의 뷰를 넣었을 때, Swift는 그 뷰들을 **자동으로 TupleView라는 내부 타입으로 묶어서** VStack의 자식으로 **하나의 뷰처럼 저장**할 수 있게 한다. 즉, 여러 개의 뷰를 **하나의 뷰로 변환해주는 역할.**
    
- 예시
    

```swift
@resultBuilder
struct SimpleStringBuilder {
static func buildBlock(_ parts: String...) -> String {
        parts.joined(separator: "\\n")
    }
}
```

모든 result builder는 **반드시 buildBlock()이라는 정적 메서드를 하나 이상 제공해야 한다.** 이 메서드는 어떤 데이터를 받아서 가공해야 한다.

위 예제에서는 **가변인자(String)**을 받아서 **줄바꿈(\n)으로 합쳐 하나의 문자열로 반환한다.**

SimpleStringBuilder 구조체는 **result builder가 된 것이고**, 이 기능이 필요한 곳에서 @SimpleStringBuilder를 붙여서 **여러 문자열을 줄바꿈으로 자동 결합**할 수 있다.

- 사용예제
    
    ```swift
    @SimpleStringBuilder
    func makeList() -> String {
        "스위프트 스터디"
        "열심히 할게요"
        "화이팅.."
    }
    ```

## 개요

viewbuilder는 하위뷰를 생성하는 클로저 매개변수에 붙이는 속성으로 사용된다. 해당 클로저 안에서 여러 개의 하위뷰를 반환할 수 있게 된다.

예를 들어, 아래에 나오는 contextMenu 함수는 뷰를 하나 또는 그 이상 반환하는 클로저를 매개변수로 받으며, 이 클로저는 viewbuilder를 통해 처리된다.

```swift
func contextMenu<MenuItems: View>(
    @ViewBuilder menuItems: () -> MenuItems
) -> some View
```

함수를 사용하는 클라이언트는 여러 문장으로 이루어진 클로저들을 사용해서 여러개의 하위뷰를 전달할 수 있다.

```swift
myView.contextMenu {
    Text("Cut")
    Text("Copy")
    Text("Paste")
    if isSymbol {
        Text("Jump to Definition")
    }
}
```

### 참조

[https://developer.apple.com/documentation/swiftui/viewbuilder](https://developer.apple.com/documentation/swiftui/viewbuilder)

[https://minsson.medium.com/swiftui-viewbuilder-1fa084ed290b](https://minsson.medium.com/swiftui-viewbuilder-1fa084ed290b)