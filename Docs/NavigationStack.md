GQ: NavigationStack은 NavigationLink와 어떻게 달랐더라?

## Description

- NavigationStack

: 부모뷰를 보여주면서 부모뷰를 넘어서 추가적인 뷰까지 보여주는 것.

`@MainActor @preconcurrency 
`struct NavigationStack<Data, Root> where Root : View`

@MainActor

- Navigation Stack의 모든 코드(메서드, 속성 등)는 메인 스레드에서 실행되어야 한다는 의미
    - 스레드(Thread)
        - 컴퓨터가 작업을 처리하는 흐름
        - 앱은 여러가지 일을 동시에 처리할 수 있는데 각각을 스레드라고 함.
    - 메인 스레드
        - 앱 실행시 자동으로 생성되는 기본 스레드
        - UI를 그리는 역할을 담당
        - 메인 스레드에서만 UI 작업을 하는 이유?
            - 안정성: 동시에 여러 스레드가 UI를 바꾸면 충돌이나 버그 발생 우려![[Screenshot 2025-07-08 at 1.21.37 AM.png]]
			- 일관성: UI는 순서대로 그려져야 예측 가능한 동작을 보여줄 수 있음![[Screenshot 2025-07-08 at 1.21.37 AM.png]]
				Swift UI 관련 코드를 메인 스레드에서 실행해야 안전함. UI는 백그라운드 스레드에서 업데이트하면 문제 발생.
@preconcurrency
- 동시성 기능 도입 이전에 정의되었음
- async, await과의 호환성을 위한 마커
- Swift 5.5 이상에서 도입된 동시성 기능과 이전 API를 함께 사용할 때 생기는 경고나 오류를 방지하기 위해 사용

struct NavigationStack<Data, Root> where Root : View

- SwiftUI 에서 계층적인 화면 전환(네비게이션)을 구현할 수 있도록 도와주는 구조체
- Data와 Root라는 제네릭 타입을 사용
    - 제네릭 타입: Int, Bool, String등을 정하지 않고 어떤 데이터들 담을 수 있도록 함
- Data: 네비게이션을 구성하는 데이타 타입
- Root: 네비게이션 스택의 루트뷰의 타입. 반드시 View를 따름
- where Root: View: Root 제네릭 타입은 SwiftUI의 View 프로토콜을 따라야 한다.
## 주요 기능

[](https://github.com/DeveloperAcademy-POSTECH/2025-SSG-A1/blob/main/Templates/Template.md#%EC%A3%BC%EC%9A%94-%EA%B8%B0%EB%8A%A5)

- 실제 활용을 작성

## 코드 예시

NavigationStack을 사용하여 루트 뷰 위에 뷰 스택을 제공합니다.

사람들은 NavigationLink를 클릭하거나 탭하여 스택 상단에 뷰를 추가할 수 있으며, 뒤로 버튼이나 스와이프 제스처와 같은 플랫폼에 적합한 내장 컨트롤을 사용하여 뷰를 제거할 수 있습니다. 스택은 항상 제거되지 않은 가장 최근에 추가된 뷰를 표시하며 루트 뷰를 제거할 수 없습니다.

NavigationLink를 생성하려면 스택의 뷰 계층 구조 내에**navigationDestination(for:destination:)** 수정자를 추가하여 뷰를 데이터 유형과 연결하십시오. 그런 다음 동일한 종류의 데이터의 인스턴스를 제공하는 NavigationLink를 초기화합니다.
![[Screenshot 2025-07-08 at 1.04.49 AM.png]]

navigation의 상태 관리

NavigationStack은 화면 전환(뒤로가기, 앞으로 가기 등)으로 스스로 상태를 관리. 하지만 개발자가 만든 데이터와 연결해서 직접 상태 관리 가능.

화면이 바뀔 때마다

- 새로운 데이터 추가
- 화면을 닫으면 데이터 제거 가능

‘@State 를 사용해서 화면의 상태를 관리

**를 공부하려다가 내 코드에 자리만 차지하는 줄이 있다는 것을 알게됨.**
![[Screenshot 2025-07-08 at 1.30.51 AM.png]]
![[Screenshot 2025-07-08 at 1.38.45 AM.png]]
![[Screenshot 2025-07-08 at 1.39.02 AM.png]]
![[Screenshot 2025-07-08 at 1.42.20 AM.png]]
- 화면이 나타난 후 3초 되에 isActive=true로 바뀌고
- true일 때 CalView()로 넘어간다.
- false라면 스플래시이미지에 남아있음

## Keywords
NavigationPath를 이용해서 화면 이동을 배열처럼 관리해보자
- 다양한 타입(String, Int, Struct 등)을 함께 사용 가능
`let path = NavigationPath()`
`path.append("Seoul")     // String`
`path.append(123)         // Int`
`path.append(MyStruct())  // Struct`

### 여러 종류의 화면으로 이동하려면 navigationDestination(for:destination:)을 여러개 써서 데이터 타입마다 다른 화면을 보여준다.

- 만약 다른 코드로 화면을 전환하고 싶다면 NavigationPath라는 것을 써서 여러 타입의 데이터를 섞어서 경로를 만들 수 있다.
`.navigationDestination(for: String.self) { string in Text("문자: \(string)") } 
`.navigationDestination(for: Park.self) { park in ParkDetailView(park: park) }`

- String 타입으로 이동 요청하면 첫 번째 화면을 볼 수 있음
- Park 타입으로 요청하면 두 번째 화면을 봄
- real life example
    - 카톡 채팅 목록에서 여러 항목 클릭 가능

개인 대화방 (String 타입 이름 - 1:1 채팅 화면
오픈채팅방 (OpenChat 타입) - 오픈채팅 화면
친구 프로필 (Friend 타입)      -  친구 정보 상세 화면

`.navigationDestination(for: String.self) { name in ChatView(with: name) } 
`.navigationDestination(for: OpenChat.self) { room in OpenChatView(room: room) }` 
`.navigationDestination(for: Friend.self) { friend in FriendProfileView(friend: friend) }`

## References

https://developer.apple.com/documentation/swiftui/navigationstack