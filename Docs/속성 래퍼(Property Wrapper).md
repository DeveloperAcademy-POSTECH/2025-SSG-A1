# 기본 개념
- 속성(변수)에 특별한 기능을 추가하는 Swift 문법
- 속성에 `@` 기호를 붙여서 그 값을 자동으로 감싸고(read/write를 제어), 추가 로직을 실행하거나, 상태를 관리하는 데 사용
- SwiftUI에서는 상태(`@State`), 바인딩(`@Binding`), 객체 관찰(`@ObservedObject`) 등을 구현할 때 핵심적으로 사용
- 사용 시 장점
	- 반복되는 코드를 줄임
	- 속성의 동작을 캡슐화함

# 주요 기능
- 변수의 읽기/쓰기 동작을 감시하거나 제어
- 기본값 설정, 입력 값 제한, 로깅, UserDefaults 저장 등 다양한 용도로 활용 가능
- 커스텀 속성 래퍼도 직접 만들 수 있음
- SwiftUI에서는 View 상태 관리의 핵심으로 쓰임

# 코드 예시

## SwiftUI의 @State 예제

```swift
struct CounterView: View {
    @State private var count = 0

    var body: some View {
        VStack {
            Text("Count: \(count)")
            Button("증가") {
                count += 1
            }
        }
    }
}
```
- `count`라는 숫자 상태를 저장하고, 버튼을 누르면 숫자가 올라가는 코드
- SwiftUI에서 가장 기본적인 상태 관리 예제
	- `@State`도 실제로는 SwiftUI 내부에서 정의된 속성 래퍼

## 직접 만든 속성 래퍼

```swift
@propertyWrapper
struct Clamped<Value: Comparable> {
    var value: Value
    let range: ClosedRange<Value>

    var wrappedValue: Value {
        get { value }
        set { value = min(max(newValue, range.lowerBound), range.upperBound) }
    }

    init(wrappedValue: Value, _ range: ClosedRange<Value>) {
        self.range = range
        self.value = min(max(wrappedValue, range.lowerBound), range.upperBound)
    }
}

// 사용 예
struct Player {
    @Clamped(0...100) var health: Int = 120  // => 실제로는 100으로 제한됨
}
```
- 값을 범위 내로 강제하는 속성 래퍼
- 주요 포인트
	- `@propertyWrapper`: Swift에서 속성 래퍼를 만들 때 붙이는 키워드
	- `wrappedValue`: 속성에 실제로 저장되는 값을 제어하는 변수
	- `range`: 이 속성 래퍼에 지정할 수 있는 값의 범위 (예: 0...100)
	- `min(max(...))`: 값이 범위보다 작거나 크면 자동으로 제한해 줌
