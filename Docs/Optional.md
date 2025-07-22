### <개발자리 학습>

**예시1)**
`let name: String? = "Libby"`
`let age: Int? = 20 // 값이 있을 수도 있고, 없을 수도 있음`

`var body: some View {`
	`VStack {`
		`Text(name)//`
		`Text(age ?? .description ??  "100") // age가 없다면 age 값을 100으로 한다`
		`// 두번째 물음표 앞에 값이 없으면 값을 100으로 한다`
	`}`
`}`

타입 뒤에 물음표를 붙여서 스트링 타입의 값이 있을 수도, nil 값을 받을 수도 있다는 것을 의미 

**예시2)**
`let name: String? = "Libby"`
`let age: Int?` = 20 // 값이 있을 수도 있고, 없을 수도 있음


`let age: Int?`

`var body: some View {`
	`VStack {`
		`Text(name)//`
		`if let age = age { //만약 age가 nil이 아니라면 앞에 age값에 Int 넣어주기` 
			`Text(age.description)`
		`}`
	`}`
`}`

`Struct MyOptional_Previews: PreviewProvider {`
	`Static var previews: some View {`
		`MyOptional(age: nil)`
	`}`
`}`

프리뷰에서 값이 nil이기 때문에 값을 표시하지 않음. 
프리뷰에서 나이를 쓰면, 값이 표시됨. 


## GQs
- Optional Chaining에서 Optional은 몇 개 까지 쓸 수 있을까?
- Swift에서 Ownership 모델은 언제 도입된거야?
- Optional 에서 축약형과 아닌 것을 어떻게 다르게 쓰는거야?


## Description
- 타입 뒤에 물음표를 붙여서 스트링 타입의 값이 있을 수도, nil 값을 받을 수도 있다는 것을 의미 (개발자리)
- 공식 문서에 Enum으로 정의되어 있음. 
			`enum Optional<Wrapped> {`
			
			    `case none`
			
			    `case some(Wrapped)`
			
			`}`
		1. .none: 값이 없음 (nil)
		2. .some(Wrapped): 값이 존재함. (some 내부에 실제 값이 들어 있음)
	- Enum의 효능: 상태 구분에 용이함. 옵셔널은 값이 있을 수도 있고, 없을 수도 있는 타입이기 때문. 
- A type that represents either a wrapped value or the absence of a value.
	- A type: Int, String, Double과 같은 자료형임을 의미
	- either a wrapped value
		- 예) let name: String? = "Libby"
		- "Libby"는 Optional <'String> 안에 감싸진 상태, Optional.some("Libby") 라는 열거형 케이스로 감싸져 있다. 
	- Or the absence of a value
		- 또는 값이 없을 수도 있다
		- let name: String? = nil
	- 종합하면: 
		- Optional.some(value) --> 값이 있음
		- Optional.none(nil) --> 값이 없음
			==GQ: 위의 두 줄이 코드안에 들어있다면 어떤 모습일까?== 
			- 사용 방법
				1. 직접 Optional.some과 Optional.none을 사용하는 예
					`let a: Optional<'Int> = .some(41)`
					`let b: Optional<'Int> = .none`
					`''`
					`print(a) // Optional(42)`
					`print(b) // nil`
					 다르게 쓰는 방법. 
					`let a: Int? = 42`
					`let b: Int? = nil`
				2. 언래핑할 때 .some과 .none이 암묵적으로 쓰이는 예
					`let name: String? = "Alice"`
					`if let unwrappedName = name {`
					 `print("Name is \(unwrappedName)") // name == .some("Alice")`
					 `} else {`
					 `print("no name") // name == .none`
					 `}`
				3. switch 문으로 옵셔널 열거형 직접 처리하기: 옵셔널이 실제로 enum이라는 것을 보여주는 예시
					`let number: Int? = 10`
					
					`switch number {`
					`case .some(let value):` 
						`print("숫자 잇음: \(value)")`
					`case .none: print("No number")`
					`}`
**<전체 선언>**
@frozen enum Optional<'Wrapped> where Wrapped : ~Copyable, Wrapped : ~Escapable
- @frozen
	- 열거형 케이스가 앞으로 바뀌지 않을 것이라는 약속
	- Optional은 항상 .some 혹은  .none의 두가지 케이스만 가질 것이라는 의미
	- 컴파일러는 최적화와 안정성을 확보할 수 있음. [[ABI]] 안정성
- enum Optional<'Wrapped>
	- Optional이 제네릭 열거형(enum) 이며, 내부에 감싸는 타입을 Wrapped라고 한다. 
	- Int? 는 Optional<'Int> 이며, Int 이는 Wrapped 되어 있다. 
- where Wrapped : ~Copyable, Wrapped : ~Escapable
	- Swift5.9 (2023)에서 도입된 ownership 모델 관련 제약
	- ~Copyable
		- Wrapped 타입이 복사가능하지 않을 수 있다는 제약
		- "~" 는 아니다 라는 뜻
		- Optional은 복사 불가능한 타입도 감쌀 수 있다. 
			- [[actor-solated]], [[move-only]] 같은 타입도 감쌀 수 있게 하기 위함.
	- ~Escapable
		- 이 타입은 escapable이 아닐 수도 있다 라는 뜻.
		- Wrapped가 closure 밖으로 탈출 될 수 없는 타입일 수도 있다.
		- Swift는 함수나 클로저 내에서 어떤 값이 탈출 할 수 있는지 정밀하게 관리한다
	- 즉, Optional이 범용적인 타입이기 때문에 어떤 제약이 있는 타입도 감쌀 수 있도록 함. 


## 주요 기능

- You use the Optional type whenever you use optional values, even if you never type the word Optional.
	`let age: Int? = 20`
	과
	`let age: Optional<'Int> = Optional.some(20)`
	는 같은 표현
	즉, ? 는 Optional의 축약형
- Swift’s type system usually shows the wrapped type’s name with a trailing question mark (?) instead of showing the full type name.
	- Swift의 타입 시스템은 일반적으로 Int? 의 형식을 가진다. 
- The types of shortForm and longForm in the following code sample are the same: 값이 있음을 표현하는 두 가지 방법
	`let shortForm: Int? = 10`
	`let longForm: Optional<Int> = 10`
	둘 다 Optional<'Int> 타입이고, ==값은 .some(10)==
- Optional.none is equivalent to the nil literal: 값이 없음을 표현하는 두 가지 방법
	==`let value1: Int? = nil`==
	==`let value2: Int? = Optional.none`==
	`print(value1 == value2)  // true`



## 코드 예시

```
let imagePaths = ["star": "/glyphs/star.png",
                  "portrait": "/images/content/portrait.jpg",
                  "spacer": "/images/shared/spacer.gif"]
```

언래핑이 필요하다
	- 언래핑: 값이 있는 지 확인하고 꺼내는 절차
	- 언래핑 방법들
		- Optional Binding
			- 옵셔널에 값이 존재하는 경우, 새로운 상수나 변수에 연결해서 안전하게 사용
			- if let 바인딩: 값이 있을 때만 분기 실행
				`if let starPath = imagePaths["star"] {`
					`print("The star image is at '\(starPath)'")`
				`} else {`
					`print("Couldn't find the star image")`
					`}`
			- guard let 바인딩: 값 없으면 조기 종료, 이후 스코프에서 비 옵셔널로 사용
			- switch
				`switch imagePaths["star"] {`
				`case .some(let path):`
					`print("Found it at \(path)")`
				`case .none:`
					`print("No image found.")`
				`}`
		- nil 병합 연산자 ??:  Optional 값이 nil이면 기본값 제공
			`let defaultImagePath = "/images/default.png"`
			`let heartPath = imagePaths["heart"] ?? defaultImagePath`
			`print(heartPath)`
			- "heart" 키가 딕셔너리에 없기 때문에 nil 반환: "/images/default.png" 출력
			- 여러 ?? 연산자 체이닝
				`let shapePath = imagePaths["cir"] ?? imagePaths["squ"] ?? defaultImagePath`
				`print(shapePath)`
				- imagePaths["cir"] -> 없음(nil)
				- imagePaths["squ"] -> 없음(nil)
				- defaultImagePath -> 사용됨. "/images/default.png" 출력
				- lazy evaluation: 왼쪽이 nil이라고 확인될 때만 오른쪽을 평가함
				- 오른쪽에 여러개 옵셔널 추가할 수 있지만, 가독성이나 컴파일러 처리 성능을 고려할 때에는 3~4개가 적당함. 
		- 옵셔널 체이닝: ? 를 이용해서 옵셔널 값이 존재하는 경우에만 이어지는 연산을 수행함.
			 `if imagePaths["star"]?.hasSuffix(".png") == true {`
				`print("The star image is in PNG format")`
				 }
			- 딕셔너리를 조회해서 --> String? 타입을 반환
			- 키 "Star"가 없으면 -> nil 반환
			- 있으면-> .some("/glyphs/star.png")를 반환
			
			- imagePaths["star"]?.hasSuffix(".png") 
			는 
			`if let path = imagePaths["star"] {`
				`path.hasSuffix(".png")`
			`} else {`
				`nil`
			`}` // 와 같은 뜻
			 - 표현의 결과는 Bool
				 - 값이 있고, .png로 끝나면 .some(true)
				 - 값이 있고, .pag로 끝나지 않으면 some(false)
				 - 값이 없으면 nil
		- 강제 언래핑
			- Optional 값이 반드시 있다고 확신할 때 !를 붙이면 그 값을 직접 꺼낼 수 있음.
			- 만약 옵셔널이 nil이라면 앱이 크래시됨 (앱 심사할 때 이거 있으면 빠꾸먹음)
			
			let number = Int("42")!
			print(number)  // Prints: 42
			'
			let isPNG = imagePaths["star"]!.hasSuffix(".png")
			print(isPNG) // Prints "true"
				- imagePaths["star"]은 옵셔널 문자열이고, 그것을 강제 언래핑하여 String으로 바뀜
				- 그 뒤에 .hasSuffix(".png") 메서드 호출
			- 테스트 코드 혹은 빠르게 프로토타이핑 할 때
			- nil이 절대로 안나온다는것이 로직상으로 확정된 경우
			- 불확실하거나 외부 입력 기반일 때
## Keywords

- [[ABI]]
-  [[actor-solated]], 
- [[move-only]]

## References

- https://www.youtube.com/watch?v=yjQqvXvU2SU&list=PL1PI2d-NlJ8sseacReEJzJLrPQRwzonU1 (개발자리)
- https://developer.apple.com/documentation/swift/optional (공식문서)