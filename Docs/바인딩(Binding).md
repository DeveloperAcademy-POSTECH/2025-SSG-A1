# Description
-  값을 연결해서, 하나가 바뀌면 다른 쪽도 자동으로 바뀌게 하는 연결 고리

---
# 기본 문법
- 부모 뷰가 상태를 갖고 있다 -> `@State` 사용
- 자식 뷰도 그 값을 바꾸고 싶다 -> `@Binding`으로 넘기기

---
# 코드 예시
```swift
@State private var name = "Paran"

var body: some View {
    TextField("Enter name", text: $name)
}
```
- `TextField`가 `name`과 **바인딩**되어 있어서 입력창에서 문자를 바꾸면 `name` 값도 자동으로 바뀜
	- `$name`: `Binding<String>` 타입으로 꺼낸다는 뜻
	- `@State` 변수 앞에 `$`를 붙이면 SwiftUI가 **바인딩 객체**로 바꿔줌

---
# ❗️ 그럼 `@` 붙은 게 다 바인딩인가?
- `@`는 [[속성 래퍼(Property Wrapper)]] 라는 걸 나타내는 문법이고,  그중 일부만 바인딩 관련
	- 바인딩과 관련된 
		- 직접적: `@State`, `@Binding`
		- 간접적: `@ObservedObject`(값 변화 감지), `@EnvironmentObject`(전역 바인딩처럼 쓸 수 있음)
	- 바인딩이 아닌 `@`
		- `@main`, `@ViewBuilder`, `@FocusState`, `@AppStorage`
