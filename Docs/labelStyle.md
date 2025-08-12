> [!question]  
> GQ1. SwiftUI에서 아이콘과 텍스트가 함께 있는 라벨의 스타일을 바꾸려면 어떻게 할까?  
> GQ2. `LabelStyle`은 어떤 경우에 커스텀해서 써야 할까?

---

# Description
- `Label` 뷰에서 아이콘(심볼)**과 **텍스트의 표시 방식을 정하는 뷰 스타일
- 기본적으로 `Label`은 SF Symbol과 텍스트를 함께 보여줌
	- `labelStyle(_:)`을 사용하면 레이아웃이나 표시 여부를 변경 가능

---

# 주요 기능
- 아이콘과 텍스트의 표시 방식 지정 (가로/세로 배치, 텍스트만, 아이콘만 등)
- Apple 기본 스타일: `.automatic`, `.titleAndIcon`, `.iconOnly`, `.titleOnly` 
	- `.automatic`
		- 기기나 상황에 따라 자동으로 아이콘과 텍스트를 조절(자동 설정)
		- 예: iOS에서는 `titleAndIcon`, watchOS에서는 `iconOnly`처럼 작동 
	- `.titleAndIcon`
		- 아이콘 + 텍스트
		- 가장 일반적인 형태 
	    - 예: `📷 사진`
	- `.iconOnly`
		- 아이콘만 표시하고 텍스트는 숨김 
		- 공간이 좁거나 버튼 느낌으로만 쓰고 싶을 때 유용
	    - 예: `📷`
	- `.titleOnly`
		- 텍스트만 표시하고 아이콘은 숨김
		- 디자인상 심플하게 만들고 싶을 때 사용
	    - 예: `사진`

---

# 코드 예시

## 기본 스타일 사용

```swift
Label("설정", systemImage: "gear")
    .labelStyle(.titleAndIcon)

Label("사진", systemImage: "photo")
    .labelStyle(.iconOnly)

Label("텍스트만", systemImage: "star")
    .labelStyle(.titleOnly)
```
## 커스텀 LabelStyle 만들기

```swift
struct VerticalLabelStyle: LabelStyle {
    func makeBody(configuration: Configuration) -> some View {
        VStack { // 아이콘과 텍스트를 세로로 배치
            configuration.icon
            configuration.title
        }
    }
}

struct ContentView: View {
    var body: some View {
        Label("다운로드", systemImage: "arrow.down.circle")
            .labelStyle(VerticalLabelStyle())
    }
}
```
