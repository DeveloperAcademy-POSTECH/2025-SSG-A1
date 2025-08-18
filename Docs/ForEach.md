# Description
- SwiftUI에서 배열이나 범위를 기반으로 반복적으로 뷰를 생성할 때 사용하는 문법

---

# 주요 기능
- 배열 또는 범위를 반복하여 여러 개의 뷰를 화면에 표시
- `id:`를 이용해 각 항목을 고유하게 구분
- 배열 요소가 `Identifiable` 프로토콜을 따를 경우 `id:`를 생략

---
# ​기본 문법
```Swift
ForEach(반복할_범위_or_배열, id: \.자기고유값) { 항목 in
    // 반복해서 만들 뷰
}
```
- 배열이나 범위를 받아서 그 안의 각 항목마다 뷰를 만들어 줌
- id
	- SwiftUI가 각 항목을 구분할 수 있도록 도와주는 고유 식별자(아이디)
	- `\.self`
		- 자기 자신을 ID로 사용한다
		- 배열의 각 값 자체가 고유하다면 `\.self` 사용
	- `\.속성이름`
		- 구조체 등에서 특정 고유값 사용
	- [[Identifiable]]
		- 구조체에 `id`가 있다면 `id:` 생략 가능
		- [[UUID()]]를 활용해 고유한 id 생성 가능

---
# 코드 예시
- `\.self`
```Swift
struct ContentView: View {
    let fruits = ["사과", "바나나", "포도"]

    var body: some View {
        VStack {
            ForEach(fruits, id: \.self) { fruit in
                Text(fruit)
            }
        }
    }
}
```
- `\.속성이름`
```Swift
struct User {
    let name: String
    let age: Int
    let id: Int
}

let users = [
    User(name: "Eun", age: 20, id: 1),
    User(name: "Min", age: 25, id: 2),
    User(name: "Soo", age: 30, id: 3)
]

ForEach(users, id: \.id) { user in
    Text(user.name)
}
```
 - `Identifiable`
```Swift
struct Animal: Identifiable {
    let id = UUID()      // 고유 ID
    let name: String
}

struct ContentView: View {
    let animals = [
        Animal(name: "호랑이"),
        Animal(name: "사자"),
        Animal(name: "여우")
    ]

    var body: some View {
        VStack {
            ForEach(animals) { animal in
                Text(animal.name)
            }
        }
    }
}
```

---
# Keywords
- 반복문
- id
- [[Identifiable]]
- .self
- 선언형 UI
