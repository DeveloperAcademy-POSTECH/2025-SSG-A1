
> [!question] GQ1. let vs var를 어떻게 선택해야 할까?  
> [!question] GQ2. 숫자·문자열 리터럴과 **타입 추론**은 어떻게 동작할까?  
> [!question] GQ3. if / guard / switch 구문은 언제 각각 쓰는 것이 좋을까?  
> [!question] GQ4. for‑in, while 반복문 성능·가독성 베스트 프랙티스는?  
> [!question] GQ5. 함수 선언 시 매개변수 **라벨·기본값**을 어떻게 활용해야 할까?

**Description**

- Swift의 기본 문법 감각을 다져 이후 옵셔널, 컬렉션, 프로토콜 등 심화 개념 학습의 기반을 마련합니다. 특히 **불변·가변 값 관리(let/var)**, **타입 추론 규칙**, **제어 구문 선택 기준**, **반복문 베스트 프랙티스**, **함수 시그니처 설계**를 사례 중심으로 체득합니다.
    

**주요 기능**

- **불변(immutability) 기반 안전성** ― let 사용으로 스레드 레이스·논리 버그 예방
- **타입 추론 자동화** ― 컴파일 타임에 타입 확정, 가독성·작성 속도 향상
- **제어 구문 3단계(if→guard→switch)** ― 가독성과 오류 방지 최적화
- **컬렉션 반복 최적(for‑in·enumerated)** ― 인덱스 관리 오류 감소, 스트림 처리는 while
- **의미 있는 함수 라벨 + 기본값** ― 오버로드 최소화, 호출부를 “문장”처럼 설계

---

### Q1. let vs var를 어떻게 선택해야 할까?

| **단계**         | **사고 절차**                                                   | **메모**                       |
| -------------- | ----------------------------------------------------------- | ---------------------------- |
| **① 의도 파악**    | 값이 **불변**(immutable)인가? → `let` 값이 **변할 가능성**이 있는가? → `var` | Swift는 “가능한 한 불변” 권장         |
| **② 안전성 고려**   | `let` 사용 시 **Thread Safety·Logic Bug** 예방 스레드 충돌·실수 덮어쓰기 차단 | 멀티스레드 환경에서 특히 유리             |
| **③ 퍼포먼스**     | 컴파일러가 `let`을 만나면 공격적 최적화(복사 제거, 레지스터 고정)                    | `var`도 빠르지만 잠재적 변경 대비 검사가 추가 |
| **④ 리팩터링 시그널** | `let → var` 변경이 잦다면 **모델링 오류** 의심                           | 불필요한 상태(state) 탐지            |

```swift
func testLetVar() {
    let title = "Swift"
    // title = "Rust"   // ⛔️ 컴파일 오류
    var version = "5.9"
    version = "6.0"       // ✅ 가변 값 수정
    print(version)
}
```

> 💡 **비유**  
> `let` 상자 = “밀봉된 선물” → 뜯을 수 없다.  
> `var` 상자 = “지퍼백” → 열고 내용물 교체 가능.

---

### Q2. 숫자·문자열 리터럴과 타입 추론은 어떻게 동작할까?

|**단계**|**무슨 뜻?**|**예시**|
|---|---|---|
|**① 리터럴 해석**|42 → `Int`, "hi" → `String`, 3.14 → `Double`|`let a = 42` (Int)|
|**② 컨텍스트 추론**|좌변 타입·함수 시그니처 참고|`let x: UInt8 = 42`|
|**③ 오버로드 해결**|가장 **구체적** 타입 후보 선택|`printNum(3)` → `Int` 버전|
|**④ 성능 & 안전**|추론은 컴파일 타임·런타임 비용 0|모호하면 타입 명시|

```swift
let intLit    = 42          // Int (64‑bit)
let doubleLit = 3.14        // Double
let greeting  = "hi"         // String

let byte: UInt8 = 42        // 42 ➜ UInt8
func square(_ n: Int)    -> Int    { n * n }
func square(_ n: Double) -> Double { n * n }
print(square(5))   // 25, Int 버전
print(square(5.5)) // 30.25, Double 버전
```

---

### Q3. if / guard / switch 구문은 언제 각각 쓰는 것이 좋을까?

| **사용 상황**         | **추천 구문** | **이유**                         |
| ----------------- | --------- | ------------------------------ |
| 조건부 흐름 & 짧은 분기    | `if`      | 가장 직관적                         |
| 조기 반환(Early Exit) | `guard`   | 실패 분기 위로 올려 가독성↑               |
| 다중 값 매칭·패턴 분해     | `switch`  | enum·튜플 범위 등 exhaustiveness 검사 |

```swift
func fetch(id: Int?) throws -> Item {
    guard let id else { throw FetchError.invalidID }   // guard로 실패 조기 반환
    return try network.load(id: id)
}
```

---

### Q4. for‑in, while 반복문 성능·가독성 베스트 프랙티스는?

|**비교 항목**|**for‑in**|**while**|
|---|---|---|
|가독성|✅ 컬렉션 순회 직관적|루프 변수·조건 분리 필요 ⛔️|
|안전성|✅ 범위·컬렉션 끝 자동 처리|인덱스 초과·무한 루프 위험|
|성능|둘 다 O(n) → 차이 미미|동일|
|특수 용도|`for case let` 패턴 매칭, `for try await`|조건부 스트림·무한 루프|

```swift
let fruits = ["🍎", "🍋", "🍑"]
for (idx, fruit) in fruits.enumerated() {
    print(idx, fruit)
}

var i = 0
while i < 3 {
    print("while loop", i)
    i += 1
}
```

---

### Q5. 함수 선언 시 매개변수 라벨·기본값을 어떻게 활용해야 할까?

|**단계**|**사고 절차**|**메모**|
|---|---|---|
|① 라벨 가독성|외부 라벨은 **문장처럼** 읽히게|`move(from:to:)`|
|② 첫 매개변수 규칙|Swift 5.2+부터 첫 파라미터도 라벨 있음(생략 가능)|필요 시 `_` 로 생략|
|③ 기본값 제공|옵션·희귀 옵션 → 기본값으로 API 간소화|오버로드 대신 기본값|
|④ Variadic & inout|다수 인자·수정 필요 시 사용|꼭 필요한 곳에만|

```swift
func drawLine(from start: CGPoint,
              to end: CGPoint,
              dashed: Bool = false,
              width: CGFloat = 1.0) {
    // ...
}

drawLine(from: .zero, to: CGPoint(x: 10, y: 0))                 // 기본값 사용
```

---

**Keywords**

- let / var, immutability, thread safety, type inference, literal, if, guard, switch, early exit, for‑in, enumerated, while, pattern matching, parameter label, default parameter, variadic, inout
    

**References**

- _The Swift Programming Language_ — Basics, Control Flow, Functions (Swift.org)
- Apple Developer Documentation — _Swift Standard Library_
- WWDC21 **“Meet async/await in Swift”**
- WWDC22 **“Swift: Modern Concurrency and Beyond”**