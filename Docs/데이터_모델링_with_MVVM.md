>[!question]
>1. 데이터 모델링이란 뭐지?
>2. 데이터 모델링을 왜 하지?
>3. 데이터 모델링은 어떤 상황에서 하지?
>4. 데이터 모델링을 어떻게 하지?


## Description
#### 1. 데이터 모델링이란?
- (데이터) 모델 : 앱에서 처리하는 데이터
- (데이터) 모델링 : 앱에서 처리하는 (데이터) 모델을 만드는 과정

#### 2. 데이터 모델링은 왜 하지?
**✅ 1.**  **현실 세계의 개념을 코드로 표현하기 위해**
- 예: 사람을 표현할 때 UserInfo(name: "John", nickname: "johnny")처럼.
- 앱이 다루는 대상(사용자, 상품, 게시글 등)을 **하나의 타입으로 모델링**해서, 코드가 더 직관적이고 읽기 쉬워집니다.

**✅ 2.**  **데이터의 일관성과 안정성을 확보하기 위해**
- let name: String처럼 타입을 지정하면, **잘못된 데이터가 들어오는 것을 방지**할 수 있습니다.
- 컴파일러가 타입을 체크하므로, **런타임 오류를 줄이는 데**도 도움이 됩니다.

 **✅ 3.** **UI와 데이터를 분리하여 관리하기 위해 (MVVM, Clean Architecture 등)**
- SwiftUI에서는 View가 Model 데이터를 바탕으로 UI를 구성합니다.
- 예: UserInfo 모델이 있으면, UserView는 해당 모델을 기반으로 화면을 렌더링할 수 있음.
- MVVM : Model - Veiw - ViewModel 을 의미
	- 내가 이해한 것으로는 MVVM을 지키기 위해서 데이터 모델링을 한다고 이해가 됨.

#### 3. 데이터 모델링은 어떤 상황에서 하지?
**1.** **반복해서 사용되는 정보가 있을 때**
- 예: 사용자 정보, 게시물, 메시지, 제품, 주문 등
```swift
struct User {
	let id: UUID
	let name: String
	let nickname: String
}
```
**2.**  **서버에서 데이터를 받아와야 할 때**
- 서버에서 JSON으로 사용자 목록, 상품 정보 등을 받는 경우 → Codable 모델을 만들어서 파싱
```swift
struct Product: Codable {
	let id: Int
	let title: String
	let price: Double
}
```
**3.** **UI를 동적으로 구성할 때**
- 리스트로 사용자, 상품, 게시글 등을 반복 출력할 때 → 데이터를 배열로 관리하고 ForEach에 전달
```swift
struct ContentView: View {
	let users: [User]
	
	var body: some View {
		List(users) { user in
			Text(user.name)
		}
	}
}
```
**4.**  **앱 상태나 입력값을 저장하고 추적할 때** 
- 유저가 입력한 설문, 설정, 필터 옵션 등을 기억해야 하는 경우
```swift
struct SurveyResponse {
	var age: Int
	var selectedOptions: [String]
}
```
 **5.**  **ViewModel, 상태 관리와 연결될 때**
 - ViewModel에서 @Published로 모델 데이터를 다루기 위해 필요
```swift
class UserViewModel: ObservableObject {
	@Published var user: User
}
```
#### 4. 데이터 모델링을 어떻게 하지?
1. 추상화를 한다.
	추상화를 한다는 것은 **앱을 만들 때 필요한 정보를 뽑아내는 작업**을 의미한다.
	1. 예를 들어, 사람에 대한 정보를 뽑아본다면 다음의 feature들이 있을 것이다. (ex : 이름, 별명, 생일, 성별, 사는 곳, 키, 몸무게, 성격, 휴대폰 번호)
	2. 앱을 만들 때 필요한 정보를 뽑아야 한다. -> ex : 별명이 필요가 없다면 굳이 데이터 모델링에 넣을 필요가 없다.
2. 구조체(설계도)를 만든다.
```swift
struct UserInfo {

}
```
3. 추상화한 데이터를 상수 or 변수로 설정한다.
```swift
struct UserInfo {
	let name: String // Stored Property (상수 저장 속성)
	let nickname: String
	...
}
```
- 여기서 보통 상수로 많이 설정한다. 모델링한 데이터가 변경될 가능성은 적다.
- 구조체(타입) 내에서 상수를 선언할 경우 상수는 속성(Property)라고 부른다.
	- 반대로 global scope or local scope에서 상수를 선언하면 상수라고 부른다.
4. 실제 값을 넣을 때에는 인스턴스를 선언한다.
```swift
struct UserInfo {
	let name: String // Stored Property (상수 저장 속성)
	let nickname: String
	...
}

let user = UserInfo(name: "seonjong", nickname: "Lucas") // 인스턴스 선언
```
- 속성이 저장되는 위치는 인스턴스마다 달라지게 됨. 이러한 속성은 인스턴스 속성이라고 부름


## 주요 기능
+ 실제 활용을 작성

## 코드 예시
+ 실제 코드 예시를 작성

## Keywords
+ 속성, MVVM

## References
- [# Swift 모델링 기초와 다양한 속성 문법들 [1/5] (Xcode 15 버전 최신 강의)](https://www.youtube.com/watch?v=t3_eHKPlR7s)
- GPT-4o