> [!question]  
> GQ1. SwiftUI에서 버튼의 모양이나 행동을 바꾸려면 어떻게 할까?  
> GQ2. `buttonStyle`은 어떤 상황에서 쓰는 게 좋을까?

---

# Description
- SwiftUI에서 버튼의 **모양과 반응을 커스터마이징**할 수 있는 뷰 스타일 문법
- 기본 버튼 스타일 외에 자신만의 버튼 스타일을 만들 수 있고, **재사용이 가능**한 스타일을 정의하여 일관된 UI를 구성 가능
- SwiftUI에서 제공하는 기본 스타일: `DefaultButtonStyle`, `BorderedButtonStyle`, `PlainButtonStyle` 등
	- `DefaultButtonStyle`
		- 기본 버튼 스타일(플랫폼마다 기본 스타일이 다르게 적용됨)
	- `BorderedButtonStyle`
		- 테두리만 있는 버튼 스타일
	- `PlainButtonStyle`
		- 스타일이 거의 없이, 텍스트만 있는 버튼 스타일

---

# 주요 기능

- 버튼의 시각적 스타일(테두리, 배경, 눌렀을 때의 반응 등)을 변경
- `buttonStyle()`로 커스텀 스타일을 적용해 일관된 디자인 시스템 구성
- `ButtonStyle` 프로토콜을 채택하여 나만의 버튼 스타일 정의

---

# 코드 예시

## 기본 제공 스타일 사용
```swift
Button("기본 버튼") {
    print("눌림")
}
.buttonStyle(.bordered)
```
## 커스텀 버튼 스타일 정의 및 적용

```swift
struct CapsuleButtonStyle: ButtonStyle {
    func makeBody(configuration: Configuration) -> some View {
        configuration.label
            .padding()
            .background(configuration.isPressed ? Color.gray : Color.blue)
            .foregroundColor(.white)
            .clipShape(Capsule())
    }
}

struct ContentView: View {
    var body: some View {
        Button("커스텀 버튼") {
            print("눌림")
        }
        .buttonStyle(CapsuleButtonStyle())
    }
}
```
