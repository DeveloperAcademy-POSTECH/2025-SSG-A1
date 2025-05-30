# Description

- SwiftUI에서 뷰(View)가 소유하는 관찰 가능한 객체(ObservableObject)를 생성하고 유지할 때 사용하는 속성 래퍼
	- View가 직접 ViewModel을 소유할 때 사용
- 뷰가 처음 생성될 때 한 번만 객체를 만들고, 뷰가 다시 그려질 때도 객체가 다시 생성되지 않도록 보장
	- 참고: [[뷰가 생성된다 vs 다시 그려진다(리렌더링)]]
- 객체의 변경 사항을 자동으로 감지하고 뷰를 업데이트
## 객체가 다시 생성되지 않으면 좋은 이유?
- 상태 유지
	- 데이터가 초기화되지 않음
- 성능 향상 불필요한 리소스 낭비 방지
	- 객체 생성은 비용이 들기 때문에, 계속 객체를 새로 만들면 **메모리 낭비**나 **네트워크 요청 중복**, **타이머 중복 실행** 같은 문제가 생길 수 있음
- 상태(state)를 안정적으로 유지하기 위해

---
# 주요 기능
- View가 **처음 만들어질 때만** 객체를 생성 (초기화는 한 번만)
- 객체가 바뀌면 View를 다시 그려줌
- 뷰 외부에서 객체를 주입받는 경우에는 사용 불가
	- 이때는 `@ObservedObject` 또는 `@EnvironmentObject` 사용
- 흔한 사용 예:
	- [[MVVM]] 패턴에서 View가 ViewModel을 직접 생성할 때
	- 독립적인 상태를 가진 뷰를 만들 때

---
# 코드 예시
```swift
import SwiftUI
import Combine

// 1. 관찰 가능한 ViewModel 정의
class CounterViewModel: ObservableObject {
    @Published var count = 0

    func increase() {
        count += 1
    }
}

// 2. View에서 @StateObject로 ViewModel을 소유
struct CounterView: View {
    @StateObject private var viewModel = CounterViewModel()

    var body: some View {
        VStack {
            Text("Count: \(viewModel.count)")
                .font(.largeTitle)
            Button("Increase") {
                viewModel.increase()
            }
        }
        .padding()
    }
}

```

- 이 예시에서는 `CounterViewModel` 객체가 뷰가 처음 만들어질 때 한 번만 생성
- 뷰가 다시 그려질 때도 ViewModel은 유지
```Swift
@ObservedObject var viewModel = CounterViewModel()
```
- 만약 `ObservedObject`를 이용해 위처럼 그렸다면 뷰가 다시 만들어질 때마다 `viewModel`도 새로 생성돼서 count가 매번 0으로 초기화됨

---
# Keywords
- `@ObservedObject` 와 차이
- [[MVVM]]
- 상태 관리
- 뷰모델(ViewModel)
- 생명 주기(Lifecycle)
- 바인딩(Binding)
- Combine
- 의존성 주입(Dependency Injection)
---

# References

- ChatGPT
### 다시 제대로 봐야 함

- [Apple 공식 문서: @StateObject](https://developer.apple.com/documentation/swiftui/stateobject
- [Apple 공식 문서: Managing model data in your app](https://developer.apple.com/documentation/swiftui/managing-model-data-in-your-app)
- SwiftUI Essentials 튜토리얼
