>[!question]
>GQ1. Foundation을 알려면, swift 언어가 기본적으로 주는 기능을 알아야 한다. swift standard library에서는 어떤 기능을 제공할까?

## Description
The Swift standard library implements the basic data types, algorithms, and protocols you use to write apps in Swift. It includes high-performance fundamental data types such as String and Array, along with generic algorithms such as sort() and filter(_:). Powerful protocols that describe shared traits and behaviors define reusable building blocks for higher level types.

Swift 표준 라이브러리는 Swift로 앱을 작성할 때 사용하는 기본 데이터 타입, 알고리즘, 프로토콜을 구현합니다. 여기에는 String이나 Array 같은 고성능의 기본 데이터 타입과 sort(), filter(_:) 같은 제네릭 알고리즘이 포함되어 있습니다.

공통된 특성과 동작을 설명하는 강력한 프로토콜들은 상위 수준 타입을 위한 재사용 가능한 구성 요소로 작동합니다.

- 스위프트 표준 라이브러리는 비단 ios, macos 뿐만 아니라, 리눅스와 윈도우 같은 환경에서도 동작하는 기능들을 가진다.

## 주요 기능

### Library가 제공하는 기능 예시
- 데이터 타입 : Int, Double, String
    
- 데이터 구조 : Array, Dictionary, Set
    
- 전역 함수 : print( :seperator:terminator:), abs( :)
	    - 출력함수, 절대값 함수
- 추상화 프로토콜: Collection, Equatable
    
- 모든 타입에 적용할 수 있는 기능을 사용자가 커스텀 할 수 있는 프로토콜: CustomDebugStringConvertible,  CustomReflectable
    
- 반복적으로 사용되는 코드(boilerplate code)가 필요한 구현을 제공하는 프로토콜 : OptionSet

## 코드 예시
- 추상화 프로토콜
	- equatable
		- 값을 비교할 수 있게 하는 프로토콜
		```swift
		// 책 정보를 나타내는 구조체
		// Equatable을 채택하면 "이 두 책이 같은 책인가?" 비교 가능
		
		struct Book: Equatable {
		    let title: String
		    let author: String
		}
		
		// 같은 책인지 비교
		let b1 = Book(title: "SwiftUI", author: "세린")
		let b2 = Book(title: "SwiftUI", author: "세린")
		let b3 = Book(title: "iOS", author: "Paran")
		
		print(b1 == b2) // true → 제목, 저자 모두 같음
		print(b1 == b3) // false → 내용이 다름
		```

- 모든 타입에 적용할 수 있는 기능을 사용자가 커스텀 할 수 있는 프로토콜
	```swift
	**import** Foundation
	
	**struct** Message {
	    **let** from: String
	    **let** contents: String
	    **let** date: Date
	}
	
	**let** messages = [
	    Message(from: "Sandy", contents: "Hey, what's going on tonight?", date: messageDates[0]),
	    Message(from: "Michelle", contents: "Studying for Friday's exam. You guys aren't?", date: messageDates[1]),
	    Message(from: "Christian", contents: "Nope. That's what tomorrow is for. Let's get food, I'm hungry!", date: messageDates[2]),
	    Message(from: "Michelle", contents: "Maybe. What do you want to eat?", date: messageDates[3])
	]
	
	**extension** Message: CustomDebugStringConvertible {
	    **public** **var** debugDescription: String {
	        **return** "[\(date) From: \(from)] \(contents)"
	    }
	}
	
	debugPrint(messages[1]) 
	//-> "[2025-07-22 10:31:21 +0000 From: Michelle] Studying for Friday's exam. You guys aren't?"
	```

- boilerplate 코드를 줄여주는 프로토콜
```swift
	import SwiftUI
	// 옵션 정의
	struct MusicOptions: OptionSet {
	    let rawValue: Int
	    
	    static let loop        = MusicOptions(rawValue: 1 << 0) // 0001
	    static let shuffle     = MusicOptions(rawValue: 1 << 1) //0010
	    static let highQuality = MusicOptions(rawValue: 1 << 2) // 0100
	}
	
	struct ContentView: View {
	    // 현재 선택된 옵션
	    let settings: MusicOptions = [.loop, .shuffle]
	    
	    var body: some View {
	        VStack(spacing: 12) {
	
	            if settings.contains(.loop) {
	                Text("🔁 Loop is ON")
	            }
	
	            if settings.contains(.shuffle) {
	                Text("🔀 Shuffle is ON")
	            }
	
	            if settings.contains(.highQuality) {
	                Text("🎵 High Quality is ON")
	            } else {
	                Text("🎵 High Quality is OFF")
	            }
	        }
	        .padding()
	    }
	}
```

- 만약 optionset을 사용하지 않은 코드라면?
```swift
	import SwiftUI
	
	// 옵션을 enum으로 정의
	enum MusicOption {
	    case loop
	    case shuffle
	    case highQuality
	}
	
	struct ContentView: View {
	    // 현재 선택된 옵션을 배열로 저장
	    let settings: [MusicOption] = [.loop, .shuffle] //Hight Quality가 없음
	
	    var body: some View {
	        VStack {
	            if settings.contains(where: { $0 == .loop }) {
	                Text("🔁 Loop is ON")
	            }
	
	            if settings.contains(where: { $0 == .shuffle }) {
	                Text("🔀 Shuffle is ON")
	            }
				//settings에 highQuality가 없기 때문에 OFF가 출력 될 것.
	            if settings.contains(where: { $0 == .highQuality }) {
	                Text("🎵 High Quality is ON")
	            } else {
	                Text("🎵 High Quality is OFF")
	            }
	        }
	        .padding()
	    }
	}
```

## Keywords
[[제네릭 (Generic)]]
[[파운데이션 (foundation)]]

## References
https://developer.apple.com/documentation/swift/swift-standard-library
https://www.swift.org/documentation/standard-library/


