> [!question] GQ1. GQ를 쓰세요 GQ2. GQ를 쓰세요

## Description

Viewcontroller를 viewlayer로 동작함

Model - View - ViewModel

## 주요 기능

- ViewController 는 ViewModel 을 들고 있음
- ViewModel 은 Model(Data) 을 들고 있음
- ViewModel 은 아래의 역할을 함

**코드를 짤 때 문제점들**

- 내가 짠 코드들이 너무 길어지는 것
- 읽기가 힘들고 이해하기가 힘들다
- 코드들의 기능을 구분하기가 힘들다
- UI 컴포넌트와 데이터의 바인딩을 분리해줌

**해결책**

- 기능 단위로 잘라주는 구조
- M / v / vm
    - [[model]]: 앱의 데이터 처리를 담당
    - [[view]]: 화면을 담당
    - [[viewModel]]: view와 Model 사이를 연결

View와 view model 사이에 binding을 사용하여 결합도를 낮춤.
### 1. Data → Output

- Model(Data) 를 가지고 있고,
    - View 에 적용할수 있도록 가공되어 있음
- (View model은 view가 아무것도 하지 않고 보여주게 하는 역할이기 때문에 데이터를 미리 가공해 놓음)
- 데이터가 업데이트 되면 가공해놓고 view에 보여줄 준비를 함
### 2. User Action → Input

- User Action 에 대한 처리를 담당함
    - 실제 액션에 대해서는 View 가 알려줌
    - 다만 액션으로 수행되어야 하는 [[비즈니스로직]]을 처리함
    - 예를 들어, 사용자가 버튼을 클릭하면 view model이 처리

[[MVVM]]을 고도화 해서 사용하는 사람들은 output type, input type을 나누어서 설계

객체가 하나의 책임을 질 수 있게 분산시킨 것

**단점**

- swift data 처리하기에 번거로움
- swiftUI는 선언적 코드이다
## 코드 예시

n/a

## Keywords

##### **바인딩 방식 (단방향/양방향)**

- SwiftUI는 **단방향 데이터 흐름**이 기본
- @State, @Binding, @ObservedObject, @Published 등을 활용

#### **[[Combine]] 활용**

- [[@Published]], [[@ObservableObject]]로 View와 ViewModel 연결
- [[비동기]] 처리 시 [[Combine]]의 [[Publisher]], [[sink]], [[map]], [[assign]] 등 사용

#### **의존성 주입**

- ViewModel이 내부에서 Model (또는 Repository 등)을 직접 만들지 않고 외부에서 주입받도록 구성

#### **State 관리 ([[Enum]])**

- 로딩 상태, 에러 상태 등을 enum으로 구분해서 View에서 처리하기 쉬움


## References

개발자리
https://youtu.be/wx0hpLEfe7g?si=wN8FOslCHGxZzMcm

까불이코더
https://youtu.be/W-cxtKRrpVg?si=cDQbpD46kN10icfS


