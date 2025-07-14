

>[!question]
>GQ1. return 없이 화면이 그려질 수 있는 이유가 뭘까?

## Description

### 클로저에서 뷰를 구성하는 사용자 정의 매개변수 속성

```swift
@resultBuilder struct ViewBuilder
```

## 주요 기능

SwiftUI에서는 body 블록이 자동으로 @ViewBuilder로 처리되기 때문에, 여러 개의 뷰를 작성해도 return 없이 하나의 뷰로 묶여서 반환된다.

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


## 코드 예시
```swift
struct ContentView: View {
	var body: some View {
		VStack {
			Image(systemName: "globe")
				.imageScale(.large)
				.foregroundStyle(.tint)
			Text("Hello, world!")
		}
		.padding()
	}
}
```


## Keywords
- [[리저트빌더(resultBuilder)]]
- [[클로저 (Closure)]]

## References
[https://developer.apple.com/documentation/swiftui/viewbuilder](https://developer.apple.com/documentation/swiftui/viewbuilder)

[https://minsson.medium.com/swiftui-viewbuilder-1fa084ed290b](https://minsson.medium.com/swiftui-viewbuilder-1fa084ed290b)
