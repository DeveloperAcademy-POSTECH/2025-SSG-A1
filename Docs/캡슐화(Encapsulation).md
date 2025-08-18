> [!question]
> GQ1. 캡슐화가 뭐지?
> GQ2. 캡슐화를 하는 방법은 어떤 게 있지?
> GQ3. SwiftUI에서 캡슐화를 쓰는 이유는?
> GQ4. SwiftUI만의 캡슐화 특성은?

# Description
- 객체지향 설계의 핵심
- 내부 구현을 숨기고 외부와의 상호작용을 잘 정의된 인터페이스만으로 수행하도록 하는 원칙
	 = 복잡한 코드를 깔끔하게 감추고, 필요한 부분만 드러내는 것
- Swift
	- `private`, `fileprivate`, `internal`, `public` 같은 접근 제어를 사용
- SwiftUI
	- `@State`, `@Binding` 같은 속성 래퍼로 데이터 보호

---
# 주요 기능
## 접근 제한
* 접근 제어(`private` 등)를 써서 뷰 내부 구현과 외부 인터페이스를 분리
	* 변수나 함수를 바깥에서 못 보게 막음
```swift
private var count = 0  // 외부에서는 count를 못 봄!
```
## 데이터 보호
* `@State`, `@Binding`, `@ObservedObject`, `@StateObject` 같은 프로퍼티 래퍼는 데이터 흐름의 출처를 명확히 하며, 외부 변경에 대한 캡슐화된 업데이트를 지원
	* 뷰 안에서만 데이터를 안전하게 관리
	* 여러 화면에서 데이터를 공유하되, 누가 수정할 수 있는지는 정할 수 있음

## 스타일 캡슐화
* SwiftUI 스타일 프로토콜(`ButtonStyle`, `LabelStyle`, `TextFieldStyle` 등)을 통해 뷰 구성 방식과 스타일을 분리
* 스타일 구현체를 만들면 `뷰 구조(body)`와 권한, 디자인을 모듈화하여 조합

## 커스텀 뷰

* 여러 modifier와 동작을 조합한 `struct CustomView: View`를 만들면, 복잡한 UI도 깔끔한 인터페이스로 추상화 가능
## 재사용성과 유지보수 향상
- 다른 뷰에서 재사용하기 편함
- 코드를 깔끔하고 이해하기 쉽게 만들 수 있어서 유지보수가 쉬움

---
# 코드 예시

## 접근 제어 + 상태 캡슐화

```swift
struct CounterView: View {
    @State private var count = 0  // 외부에서 접근 불가

    var body: some View {
        VStack {
            Text("횟수: \(count)")
            Button("올리기") {
                count += 1
            }
        }
    }
}

```

## 스타일 캡슐화

```swift
struct MyButtonStyle: ButtonStyle {
    func makeBody(configuration: Configuration) -> some View {
        configuration.label
           .padding()
           .background(configuration.isPressed ? Color.gray : Color.blue)
           .foregroundColor(.white)
           .cornerRadius(8)
    }
}

struct ContentView: View {
    var body: some View {
        Button("확인") { /* ... */ }
           .buttonStyle(MyButtonStyle())
    }
}
```

## 커스텀 뷰 캡슐화

```swift
struct ProfileCard: View {
    let name: String
    let image: Image
    
    var body: some View {
        HStack {
            image.resizable().frame(width: 50, height: 50).clipShape(Circle())
            Text(name).font(.headline)
        }
        .padding()
        .background(Color(.secondarySystemBackground))
        .cornerRadius(10)
    }
}
```
- 이 `ProfileCard`는 다른 화면에서 `ProfileCard(name: "파란", image: Image("me"))`처럼 편하게 쓸 수 있음
- 내부 디자인은 바깥에서 신경 안 써도 됨
