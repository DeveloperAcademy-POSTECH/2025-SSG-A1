NavigationLink

GQ: NavigationLink를 어떻게 사용했더라?
## Description

###### A view that controls a navigation presentation.
- View 프로토콜을 따르는 하나의 UI 요소
- 특정 UI 흐름이나 상태 변화를 제어하거나 유도함
- navigation presentation은 다른 화면으로의 전환을 말함. 
- NavigationStack 안에서 다른 뷰로 푸쉬됨. 
![Screenshot 2025-07-10 at 9.13.48 PM.png](app://530b49a24d43f9358e484f3c2e2ef0664958/Users/libby/Documents/GitHub/2025-SSG-A1/Docs/assets/Screenshot%202025-07-10%20at%209.13.48%20PM.png?1752489518725)
## 주요 기능
`struct NavigationLink<Label, Destination> where Label : View, Destination : View
- `struct NavigationLink<Label, Destination> 
	- NavigationLink는 SwiftUI에서 제공하는 네비게이션 전환을 위한 뷰 구조체이다
	- <Label, Destination> 은 제네릭 파라미터이다. NavigationLink를 만들 때 Label과 Destination을 어떤 View로 쓸지 개발작가 지정. 
	- **라벨과 목적지의 뷰 타입을 외부에서 주입받음**
- where Label : View, Destination : View
	- 라벨로 사용할 타입은 View 프로토콜을 따라야 함
	- 목적지 타입도 View 프로토콜을 따라야 함
![[Screenshot 2025-07-10 at 9.29.53 PM.png]]
- 구조체를 라벨로 쓰고
- 목적지를 하나의 SwiftUI View로 설정한 예

## 코드 예시

Link to a destination view - 예제를 따라해 보았다. 
![[Screenshot 2025-07-10 at 9.41.39 PM.png]]
- 목적지 View를 직접 정의
- 눌렀을 때 즉시 뷰로 전환됨
- 눌렀을 때 어떤 뷰가 열릴지 코드 흐름상에서 고정된다. 
- 상태 추적이 어렵다. (Pink가 선택되었는지 값으로 확인하기 어렵다. 

같은 결과 다른 코드 
![[Screenshot 2025-07-10 at 9.48.49 PM.png]]
 - NavigationLink는 뷰 대신 값만 전달함
- 실제 목적지 뷰는 navigationDestination(for:) 에서 값을 받아 생성
- 데이터 기반 네비게이션 
- 데이터만 넘김으로써 프로그래밍적으로 화면 전환이 더 쉽다. 
- 어떤 화면을 보여줬는지 @state로 추적하고 저장할 수 있다. 
## Keywords

#### **[[상태 기반 네비게이션]]**
- 사용자가 직접 NavigationLink를 누르지 않아도 상태 값이 바뀌면 자동으로 화면으로 이동하는 구조
- 화면 전환을 코드로 직접 제어하려면, NavigationStack의 경로를 나타내는 state 변수를 만들어서 그 배열에 값을 추가하거나 제거함으로써 화면 이동을 프로그래밍적으로 실행할 수 있다. 
![[Screenshot 2025-07-11 at 12.11.33 AM.png]]
@State private var path: [Color]
- 현재 navigationStack 상태
- path 배열은 NavigationStack 안의 화면 전환 스택을 직접 추적
- 초기값이 비어있으면 스택은 비어있음 --> 아무 화면도 push 되지 않음
- 배열을 쓰는 이유: NavigationStack은 스택 구조이기 때문에, 화면을 이동한다는 것은 stack에 push 한다는 뜻이고, 뒤로 간다는 것은 stack 에서 pop 한다는 뜻. 
- path는 화면 이동과 뒤로가기를 코드에서 직접 제어할 수 있게 해주는 연결고리이다. 프로그래밍적 조작을 가능하게 함
 NavigationStack(path: $path)
 - 경로를 직접 추적하고 조작하게 만듬
path.append(.pink)
- 코드로 상태를 바꿔 자동으로 **이동**: 사용자가 UI를 누르지 않아도 프로그래밍으로 이동 가능
.onAppear
- 리스트 뷰가 화면에 나타날 때 호출
- 조건인 shouldNavigateToPink = true 일 경우 .pink가 navigation path에 추가됨.
- 결과적으로 아무것도 누르지 않아도 ColorDetail(color: .pink)로 이동됨
상태기반 네비게이션의 사용 예시
- 사용자가 로그인 직후 로그인 결과가 .pink이면 바로 이동
- 알림을 클릭했을 때 특정 색상으로 바로 이동
- 앱 재시작 시 마지막으로 본 색상 복원, 저장된 색상을 path 추가

#### Coordinate with a List
- NavigationSplitView, List, NavigationLink, @State 간의 상태 동기화를 다룬다
![[Screenshot 2025-07-11 at 12.35.27 AM.png]]
![[Screenshot 2025-07-11 at 12.35.27 AM.png]![[Screenshot 2025-07-11 at 12.46.44 AM.png]]
//아이패드에서는 Detail view가 같이 보임![[Screenshot 2025-07-11 at 12.46.44 AM.png]]
- 사용자가 List에서 항목을 고르면 selection이 자동으로 업데이트된다. 
- NavigationSplitView
	- 좌측- List, 우측- Detail View 구성
- List(colors, id:  \.**self**, selection: $selection)
	- selection 값에 따라 선택 항목이 자동으로 선택됨
- NavigationLink(color.description, value: color)
	- 선택시 selection에 값을 저장
- @State var selection: Color?
	- 현재 어떤 색이 선택되었는지 추적하는 single source of truth
- 동기화 방향 
	- 사용자-->상태
		- 사용자가 List에서 mint를 누름
		- selection = .pink가 자동 설정
		- 우측 detail view(아이폰에서는 다음 화면)에서 .pink에 해당하는 내용이 표시됨
	- 상태 --> 사용자
		- 코드에서 selection = .teal로 설정
		- 리스트에서 .teal이 자동 선택됨
			- ColorDetail(color: .teal)로 자동 이동
- 장점
	- 양방향 바인딩: 사용자의 선택도 상태로 반영되고, 상태의 변경도 UI에 반영됨
	- 프로그래밍으로 제어 가능: 버튼/조건문 등을 통해 selection을 설정하면 화면 전환 가능
	- splitview에 최적: iPad/macOS에서 흔히 사용하는 마스터-디테일 구조에 적합
	- 
Button("Go to Mint") {

    selection = .mint
    - 를 누르면 좌측 리스트에서 .Mint가 선택되고 우측에 ColorDetail이 자동으로 나타남. 
    - 

## References
https://developer.apple.com/documentation/swiftui/navigationlink#Control-a-presentation-link-programmatically