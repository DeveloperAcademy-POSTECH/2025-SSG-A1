
# The Composable Architecture

- 구성 테스트 및 인체 공학을 염두에 두고 일관되고 이해하기 쉬운 방식으로 애플리케이션을 구축하기 위한 라이브러리.

## 특징
- **상태 관리(State management)** 단순한 값 타입을 사용하여 개발하는 애플리케이션의 상태를 관리하거나 많은 화면 간의 상태를 공유하는 방법을 제공한다. 이는 특정 화면의 변화를 다른 화면에서 즉시 관찰할 수 있도록 도와준다.  
    
- **구성 (Composition)** 큰 기능들을 작은 컴포넌트로 분해할 수 있도록 해준다.  
    분해된 작은 컴포넌트들은 격리된 모듈로 추출될 수 있으며, 기능의 형태를 구성하기 위해 쉽게 붙을 수 있다. 모듈화라는 말 인듯하다.  
    
- **사이드 이펙트 (Side Effects)** 애플리케이션의 특정 부분이 최대한 테스트 가능하고, 이해가능한 방식으로 외부 세계와 소통할 수 있도록 도와준다.  
    
- **테스팅(Testing)** 아키텍쳐 상에 구축 된 기능들을 테스트할 뿐만 아니라, 많은 파트로 구성되어진 기능들의 통합 테스트를 작성할 수 있다.  
    또한 종단테스트 (end-to-end tests)를 통해, 사이드 이펙트가 애플리케이션에 어떤 영향을 주는지 이해할 수 있다. 이를 통해서 개발자가 예상한 방식대로 비즈니스 로직이 돌아가고 있는지를 강력하게 보장할 수 있도록 해준다.  
    
- **인체공학(Ergonomics)** 가능한 적은 수의 개념과 동작 파트만을 가진 단순한 API만으로 모든것을 수행할 수 있다.
---

## 1. 도메인 구성 타입 정의

- `State` : 비즈니스 로직을 수행하거나 UI를 그릴 때 필요한 데이터에 대한 설명을 나타내는 타입
- `Action` : 사용자가 하는 행동이나 노티피케이션 등 어플리케이션에 생길 수 있는 모든 행동을 나타내는 타입
- `Environment` : api client나 analytics client와 같이 어플리케이션이 필요로 하는 의존성을 가지는 타입
- `Reducer` : 어떤 Action이 주어졌을 때 지금 State를 다음 상태로 변화시키는 방법을 가지고 있는 함수. 리듀서는 실행할수 있는 effect(api request)를 반환해야 하며, 보통은 effect 값을 반환
- `Store` : 실제로 기능이 작동하는 공간. 사용자의 action을 보내서 store는 reducer와 effect를 실행할 수 있고, store에서 일어나는 state를 observe해서 UI를 업데이트 할 수 있음.

![[Pasted image 20250627173722.png]]
- View영역
    - store
        - 뷰가 state를 읽고, 액션을 보낼 수 있게 해주는 핵심 객체
        - view에 주입되고, viewstore에서 사용됨.
    - viewstore
        - store를 바탕으로 view에 바인딩된 읽기 전용 view
        - 상태를 관찰하고 액션을 전송하는데 사용
- Reducer 영역
    - state
        - 앱의 데이터를 표현하는 구조체
        - UI는 이 값을 기반으로 화면을 그림.
    - action
        - 사용자의 행위나 시스템 이벤트를 표현
        - reducer는 이 action을 기준으로 어떻게 state를 바꿀지 결정
    - environment
        - 외부 의존성을 모아놓는 구조체
        - reducer 내부에서 외부와 상호작용할 때 필요
        - effect를 통해 비동기 작업을 실행할 수 있음.

## 2. 동작 흐름
![[Pasted image 20250627173735.png]]

- 뷰 → 액션 → 리듀서 → 스테이트 → 뷰
- 외부 의존성을 처리해야한다면 environment에서 의존성 처리하고 사이드이펙트를 action에 전달.

---

## 예제 코드

### store

```swift
import SwiftUI
import ComposableArchitecture

@main
struct tcaexampleApp: App {
    var body: some Scene {
        WindowGroup {
            
            //store: state와 reducer를 기반으로 생성되는 객체.
            //view로 주입되어 viewStore에서 사용
            ContentView(
                store: Store(
                    initialState: Feature.State(), //초기 상태
                    reducer: { Feature() } // 리듀서 인스턴스를 클로저로 넘김
                )
            )
        }
    }
}
```

### viewstore

```swift

import SwiftUI
import ComposableArchitecture

// view 계층: state를 보여주고, action을 전달하는 역할
struct ContentView: View {
    
    let store: StoreOf<Feature>
    
    //viewstore: store로 부터 상태를 관찰하고 액션 전송.
    var body: some View {
        WithViewStore(store, observe: { $0 }) { viewStore in
            VStack {
                Text("\\(viewStore.count)")//상태 읽기
                    .font(.title)
                
                HStack {
                    Button("-") {
                        // - Action
                        viewStore.send(.minusButtonTap) //action 전송
                    }
                    Button("+") {
                        // + Action
                        viewStore.send(.plusButtonTap)
                    }
                }
                .buttonStyle(.borderedProminent)
            }
        }
    }
}
```

### reducer

```swift
import Foundation
import ComposableArchitecture

// reducer: state와 action을 기반으로 상태를 변경하며, 필요하면 비동기 작업(effect)도 발생시킬 수 있는 순수 함수
struct Feature: Reducer {
    
    //state: 뷰를 그릴 때 필요한 데이터
    struct State: Equatable {
        var count: Int = 0 //현재 카운트 값
    }
    
    //action: 사용자의 의도나 시스템 이벤트를 표현하는 열거형.
    enum Action: Equatable {
        case plusButtonTap
        case minusButtonTap
    }
    
    //action이 발생했을 때 상태를 변경하고, 필요시 effect 반환 함수.
    func reduce(into state: inout State, action: Action) -> Effect<Action> {
        switch action {
        case .plusButtonTap:
            state.count += 1
            return .none // 아무런 이펙트를 발생시키지 않음.
            
        case .minusButtonTap:
            state.count -= 1
            return .none
        }
    }
}

```

-----------
## TCA의 장점

- 상태흐름이 예측 가능하고 추적 가능
	- 모든 상태 변화가 Reducer 안에서만 일어남 → 디버깅, 리플레이, 로깅이 쉬움
- 기능 분리와 확장이 쉬움
	- Feature마다 State, Action, Reducer, View를 나눌 수 있어 **큰 앱에서도 구조가 깔끔**
- 의존성 관리가 깔끔함
	- Environment로 외부 API, 시간, 파일 등 의존성을 주입해서 **테스트 가능하고 느슨한 결합 유지**

----------

참조.
[https://gist.github.com/pilgwon/ea05e2207ab68bdd1f49dff97b293b17](https://gist.github.com/pilgwon/ea05e2207ab68bdd1f49dff97b293b17)

[](https://www.youtube.com/results?search_query=tca+%EC%95%84%ED%82%A4%ED%85%8D%EC%B2%98)[https://www.youtube.com/results?search_query=tca+아키텍처](https://www.youtube.com/results?search_query=tca+%EC%95%84%ED%82%A4%ED%85%8D%EC%B2%98)

https://medium.com/@dmitrylupich/the-composable-architecture-swift-guide-to-tca-c3bf9b2e86ef

https://velog.io/@soc06212/SwiftUI-%EB%A7%88%EC%9D%8C%EC%9C%BC%EB%A1%9C-%EC%9D%B4%ED%95%B4%ED%95%98%EB%8A%94-TCA

----
더알아보고 싶은 내용