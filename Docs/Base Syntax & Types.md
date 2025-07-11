
---

## **나의 Challenge**
  
> **기본 어휘‧문법 다지기 (Syntax & Types)** — Swift만의 문법 감각 익히기.
> let/var, 숫자·문자열·튜플, 조건문, 반복문, 함수 선언을 확실히 이해해 이후 **옵셔널, 컬렉션, 프로토콜** 등 심화 개념 학습의 기반을 마련한다.

---
## **Guiding Questions**
- > let vs var를 어떻게 선택해야 할까?
- > 숫자·문자열 리터럴과 **타입 추론**은 어떻게 동작할까?
- > if / guard / switch 구문은 언제 각각 쓰는 것이 좋을까?
- > for-in, while 반복문 성능·가독성 베스트 프랙티스는?
- > 함수 선언 시 매개변수 **라벨·기본값**을 어떻게 활용해야 할까?
---

### **Q1. let vs var를 어떻게 선택해야 할까?**

| **단계**         | **사고 절차**                                                                                                                                                                                                 | **메모**               |
| -------------- | --------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- | -------------------- |
| **① 의도 파악**    | 값이 **불변**(immutable)인가? → let<br>값이 **변할 가능성**이 있는가? → var                                                                                                                                                | Swift는 “가능한 한 불변” 권장 |
| **② 안전성 고려**   | **let 사용 시 _Thread Safety·Logic Bug_ 예방**<br>**스레드** : 여러 작업이 동시에 같은 변수에 손대면 ‘타이밍 싸움’(Race Condition) 진행<br><br>- let은 “아예 못 바꿔” 규칙이어서, 스레드가 몇 개이든 값 충돌이 사라짐<br>- **논리 버그** : 실수로 값을 덮어쓰는 실책도 컴파일 단계에서 막음 | 멀티스레드 환경에서 특히 유리     |
| **③ 퍼포먼스 확인**  | 컴파일러 최적화로 속도 차 미미 <br>→ **가독성·안전** 우선<br>컴파일러는 let을 만나면 “이 변수는 절대 안 바뀌네!”라고 확신하고 **공격적으로 최적화**(불필요한 복사 제거, 레지스터 고정 등)<br><br>var도 빠르지만 _잠재적 변경_을 대비해 복사·검사 코드를 추가                                         | let이 약간 더 최적화 여지     |
| **④ 리팩터링 시그널** | let → var 변경이 잦다면 **모델링 오류** 의심<br><br>- 처음엔 불변이라 생각했는데, 나중에 수정 필요해 var로 바꿨다면 “상태 관리 위치가 맞나?”를 의심<br><br>- 값이 여기저기에서 바뀌면 **관계가 복잡해지고 버그 추적이 어려워짐** → 설계를 다시 점검할 타이밍!                                      | 불필요한 상태(state) 탐지    |
```
func testLetVar() {
    let title = "Swift"
    // title = "Rust"   // 이 줄 주석 풀면 컴파일 오류

    var version = "5.9"
    version = "6.0"
    print(version)

}

testLetVar()   // 함수 호출로 실행!
```

2. **스레드** : 여러 작업이 동시에 같은 변수에 손대면 ‘타이밍 싸움’(Race Condition) 진행
```
final class HitCounter {
    var hits = 0
    func bump() { hits += 1 }
}

let counter = HitCounter()
let group = DispatchGroup()

(0..<10_000).forEach { _ in
    DispatchQueue.global().async(group: group) {
        counter.bump()   // 동시에 1만 번 증가
    }
}

group.wait()
print("Expected 10,000  →  Actual:", counter.hits)
```

```
struct SafeCounter {
    private(set) var hits = 0
    mutating func bump() { hits += 1 }
}

let lock = NSLock()
var safe = SafeCounter()

(0..<10_000).forEach { _ in
    DispatchQueue.global().async(group: group) {
        lock.lock()
        safe.bump()     // 락으로 한 번에 한 명만 접근 허용
        lock.unlock()
    }
}

group.wait()
print("Safe hits:", safe.hits)  // 항상 10,000!
```


```
struct Counter {
    private(set) var value = 0        // 외부엔 read-only, 내부엔 mutating
    mutating func increment() { value += 1 }
}

let counter = Counter()              // 불변 참조
// counter.value = 2  ❌ 컴파일 오류
```

 > 💡 **비유**
- > let 상자 = “밀봉된 선물” → 뜯을 수 없다.
- > var 상자 = “지퍼백” → 열고 내용물 교체 가능.
- > mutating func = “지퍼백을 열어 내용물을 교체하는 행위”!
> let로 참조를 고정하고, 구조체 내부 _mutating_ 메서드로 필요한 변경만 허용.

---

###  **Q2. 숫자·문자열 리터럴과 타입 추론은 어떻게 동작할까?**

| **단계**        | **사고 절차**                                 | **메모**               |
| ------------- | ----------------------------------------- | -------------------- |
| **① 리터럴 해석**  | 42 → Int 기본, "hi" → String, 3.14 → Double | 플랫폼별 Int 크기(64bit)   |
| **② 컨텍스트 추론** | 좌변·함수 시그니처를 보고 타입 결정                      | 예: let x: UInt8 = 42 |
| **③ 오버로드 해결** | 같은 이름 함수 다중 후보 중 _가장 구체적인_ 타입 선택          | Ambiguous 시 명시 타입 필요 |
| **④ 성능 & 안전** | 추론 자체는 **컴파일 타임**—런타임 비용 없음               | 명확성이 떨어지면 타입 명시      |

|**단계**|**무슨 뜻?**|**초보용 풀이**|**간단 예시**|
|---|---|---|---|
|**① 리터럴 해석**|42 → Int 기본“hi” → String3.14 → Double|“그냥 적은 값”만 보고 Swift가 **가장 흔히 쓰는 타입**으로 해석해요. (Int·Double·String)|swift\nlet a = 42        // Int\nlet b = 3.14      // Double\nlet c = \"hello\"  // String\n|
|**② 컨텍스트 추론**|**왼쪽 타입**이나 **함수 인자 타입**을 참고해 결정|왼쪽에 : UInt8 같이 타입이 적혀 있으면 그대로 맞춥니다.|swift\nlet small: UInt8 = 42   // 42가 UInt8로 추론\nshow(score: 100)         // 함수가 (Int) 받으면 100 → Int\n|
|**③ 오버로드 해결**|같은 이름 함수가 여러 개면 **가장 구체적**(딱 맞는) 타입 선택|두 후보가 모두 가능하면 **더 구체적인**(작은 범위) 타입이 이김. 모호하면 에러!|swift\nfunc printNum(_ n: Int) { ... }\nfunc printNum(_ n: Double) { ... }\n\nprintNum(3)     // Int 버전 호출\nprintNum(3.0)   // Double 버전 호출\n// printNum(.pi) // 정확히 하나 못 고르면 컴파일 오류\n|
|**④ 성능·안전**|**타입 추론**은 _컴파일 단계_ → 실행 속도 **그대로**|속도 손해는 0! 다만 **헷갈리면 타입을 써서** 의도 명확히 하세요.|swift\nlet mystery = 1 + 2.0   // Double로 추론 (1이 1.0으로 승격)\nlet exact: Float = 1.5  // 헷갈리지 않게 명시\n|

---
##  예제

```
import Foundation

// ① 기본 리터럴 해석
let intLit    = 42          // Int (64-bit)
let doubleLit = 3.14        // Double
let stringLit = "hi"        // String

// ② 컨텍스트 추론
let byte: UInt8 = 42        // 42 ➜ UInt8
func greet(times: Int) {
    for _ in 0..<times { print("Hello") }
}
greet(times: 3)             // 3 ➜ Int (함수 시그니처 참고)

// ③ 오버로드 해결
func square(_ n: Int)    -> Int    { n * n }
func square(_ n: Double) -> Double { n * n }

print(square(5))      // Int 버전  ➜ 25
print(square(5.5))    // Double 버전 ➜ 30.25
// print(square(.pi)) // 둘 다 애매? → 컴파일 에러로 알려줌

// ④ 성능 & 안전
let mix     = 1 + 2.0       // Double (1 승격) — 추론 OK
let explicit: Float = 1.5   // 명확히 하고 싶을 땐 타입 명시
print(type(of: mix), type(of: explicit)) // Double Float
```

### **🗂️ 기억 포인트**

1. **그냥 쓰면 → Int / Double / String**
2. **왼쪽 또는 함수 시그니처**가 있으면 거기에 맞춰 추론
3. **함수 이름이 같으면** “더 딱 맞는 타입”이 선택 (모호하면 오류)
4. 추론은 **컴파일 타임 작업** → 실행 속도 걱정 X, 헷갈릴 땐 타입을 써서 의도를 분명히!
> **✏️ 실험**

```
let a = 1 + 2.0        // Double ← 정수 1이 1.0으로 승격
let b = "Age: " + String(29)
// let c = 1_000 * 3.14 // ⛔️ ambiguous? 아니, Double
```
---
###  **Q3. if / guard / switch 구문은 언제 각각 쓰는 것이 좋을까?**

|**사용 상황**|**추천 구문**|**이유**|
|---|---|---|
|**조건부 흐름 & 짧은 분기**|if|가장 직관적, 한두 가지 조건|
|**조기 반환(Early Exit) 패턴**|guard|성공-기준 코드 흐름을 **한 단계 위로** 끌어올림|
|**다중 값 매칭·패턴 분해**|switch|enum·튜플·값 범위 패턴 매칭|

> **if → guard → switch** 는 _“조건 수와 복잡도”_ & _“가독성”_ 에 따라 자연스럽게 단계가 올라갑니다.
1. > **if** : “딱 한두 갈래만 확인” — 가장 직관적·짧은 코드
2. > **guard** : “실패 or 예외면 바로 빠져나가기” — 성공 흐름을 위로 끌어올려 코드 들여쓰기 최소화
3. > **switch** : “경우의 수가 여러 개일 때” — 패턴 매칭 + _모든 경우_ 강제 처리로 실수 방지
---
### **if** 가볍게 Yes/No만 갈라질 때

```
if isLoggedIn {
    showHome()
} else {
    showLogin()
}
```

- **언제?**
    - 조건이 1 ~ 2개뿐일 때
    - “네? 아니요?”로 흐름만 갈라놓으면 끝날 때
- **이유** : 가장 글자 수가 적고 직관적 — 읽는 사람이 바로 이해
---

### guard “안 되면 일단 나가고, 되면 계속” 패턴

```
func fetch(id: Int?) throws -> Item {
    guard let id else { throw FetchError.invalidID }   // 실패 조기 반환
    return try network.load(id: id)                    // 성공(해피 패스) 로직
}
```

- **언제?**
    - 함수 초반에 _필수 조건_을 체크해야 할 때
    - 실패(early exit)를 처리하고, **성공 로직**을 들여쓰기 없이 이어가고 싶을 때
    
- **이유** : 실패 분기를 위에서 정리해 두면,
    아래쪽 본문이 “성공 흐름”만 남아 **가독성**이 확 좋아짐

---
### **switch** 경우의 수가 셋 이상이거나 패턴 이 필요할 때

```
switch state {
case .idle:          stopAnimating()
case .loading:       showSpinner()
case .success(let data):
    render(data)
case .failure(let err):
    showError(err)
}
```
- **언제?**
    - enum, 튜플, 값 범위 등 **여러 값**을 매칭해야 할 때
    - “모든 경우”를 반드시 다루도록 **컴파일러에게 강제**받고 싶을 때
- **이유** :
    - switch는 _exhaustive_ → 새로운 enum 케이스 추가 시 **컴파일 오류**로 누락을 알려줌
    - 패턴 매칭(case let (.x, y) where y > 0 등)이 if / guard보다 훨씬 깔끔

---

> [!TIP] ** 간단 규칙**
- > 조건 1 ~ 2개 → if
- > “안 되면 바로 return/throw” → guard
- > 3개 이상, enum·범위·복합 패턴 → switch
---
### **Q4. for-in, while 반복문 성능·가독성 베스트 프랙티스는?**

| **비교 항목** | for-in                              | while             |
| --------- | ----------------------------------- | ----------------- |
| **가독성**   | ✅ 컬렉션 순회에 직관적                       | ❌ 루프 변수·조건 분리 필요  |
| **안전성**   | ✅ 범위·컬렉션 끝 자동 처리                    | ❌ 인덱스 초과·무한 루프 주의 |
| **성능**    | 둘 다 O(n) → **차이 미미** (Swift 5.9 기준) |                   |
| **특수 용도** | for case let 패턴 매칭                  | 조건부 스트림 처리, 무한 루프 |
> [!TIP] **for-in이 기본!**

> “컬렉션(배열, 딕셔너리, 세트)이나 범위를 순회할 땐 **for-in**을 쓰자”가 스위프트의 불문율입니다.

| **비교 항목** | **for-in**                                            | **while**                                  |
| --------- | ----------------------------------------------------- | ------------------------------------------ |
| **가독성**   | for num in numbers처럼 “무엇을 돌고 있나”가 바로 보임               | 루프 변수(i), 조건(i < n)을 따로 적어야 해서 한눈에 파악이 어려움 |
| **안전성**   | 배열·범위 끝까지 자동으로 돌고 멈춤 <br>→ 인덱스 초과, 무한 루프 위험 ↓         | 조건을 잘못 쓰면 무한 루프·인덱스 초과 발생 가능               |
| **성능**    | 두 문법 모두 **O(n)** 반복 <br>→ 현대 컴파일러(5.9 기준)에서 차이 **미미** | 동일                                         |
| **특수 용도** | for case let 패턴 매칭, <br>for try await 등 풍부한 확장 가능     | “조건이 복잡하거나, 스트림·무한 루프” 같은 특수 케이스 전담        |

---

### **1.** **컬렉션·시퀀스 = for-in 고정**

```
let fruits = ["🍎", "🍋", "🍑"]
for fruit in fruits {
    print(fruit)
}
```

- 훨씬 읽기 쉽고, 실수(인덱스 초과) 걱정이 없습니다.

---
### **2.** **인덱스가 필요하다면 enumerated()**

```
for (index, fruit) in fruits.enumerated() {
    print("\(index)번째 과일은 \(fruit)")
}
```
- 인덱스 변수(index)와 값(fruit)을 한꺼번에 제공해 줍니다.
- 손수 var i = 0 … i += 1 관리하는 while 루프보다 **안전 + 간결**.
---
### **3.** **무한 루프 + 비동기 대기 → while 패턴**

```
while await !Task.isCancelled {          // 작업 취소되면 탈출
    let msg = try await socket.read()    // 비동기 스트림에서 데이터 수신
    handle(msg)
}
```

- **“언제 끝날지 모르는 스트림”** 같은 경우에는 while이 자연스럽습니다.
- for-in으로는 표현하기 어려운 **동적 종료 조건**을 걸 수 있기 때문이죠.
---
### **✅ 실전 선택 가이드**

1. **배열·딕셔너리·범위**
    → **for-in** (필요하면 .enumerated()).
2. **복잡한 조건, 무한 루프, 스트림**
    → **while** (+ break / continue / await 등으로 제어).
3. **성능**은 둘 다 거의 같으므로,
    **“읽기 쉬움 + 안전”** 기준으로 문법을 고르면 됩니다!

```
import Foundation

// 1️⃣ 기본 for-in ─ 0부터 9까지 출력
for num in 0..<10 {
    print("기본 for-in:", num)
}

// 2️⃣ 컬렉션 순회 ─ 배열 요소 출력
let fruits = ["🍎", "🍋", "🍑"]
for fruit in fruits {
    print("컬렉션 순회:", fruit)
}

// 3️⃣ 인덱스 동시 ─ (index, value) 쌍 사용
for (i, v) in fruits.enumerated() {
    print("인덱스 동시 → \(i): \(v)")
}

// 4️⃣ 패턴 매칭 ─ Result 배열에서 실패만 추출
let results: [Result<Int, Error>] = [.success(1),
                                     .failure(NSError(domain: "", code: -1)),
                                     .success(2)]
for case let .failure(err) in results {
    print("패턴 매칭(실패):", err.localizedDescription)
}

// 5️⃣ 조건 필터 ─ 짝수만 출력
let nums = Array(1...10)
for n in nums where n.isMultiple(of: 2) {
    print("조건 필터(짝수):", n)
}

// 6️⃣ Stride(간격) ─ 0, 2, 4, 6, 8 출력
for x in stride(from: 0, to: 10, by: 2) {
    print("Stride 간격 2:", x)
}

// 7️⃣ 사전(딕셔너리) 순회
let dict = ["A": 1, "B": 2]
for (key, value) in dict {
    print("딕셔너리 → \(key): \(value)")
}

// 8️⃣ 비동기 시퀀스 ─ 파일 라인 읽기 (Playground에선 async 함수 안에서 실행)
func readFileLines() async throws {
    let text = "line1\nline2\nline3\n"
    for try await line in text.split(separator: "\n").async {
        print("비동기 시퀀스:", line)
    }
}
// Task { try await readFileLines() }  // 필요 시 호출

// 9️⃣ 옵셔널 언래핑 ─ 배열 안 옵셔널 값들 중 nil 제외
let optionalArray: [Int?] = [1, nil, 3]
for case let value? in optionalArray {
    print("옵셔널 언래핑:", value)
}

// 🔟 범위 패턴 ─ 점수(score)가 1~5에 해당할 때만 실행
let score = 3
for case 1...5 = score {
    print("범위 패턴: 1~5 중 \(score)")
}
```

forEach
- forEach는 **짧고 깔끔**하지만,
    **중간에 멈출 수 없고**(break ·continue 불가) 메모리 관리(self 캡처)도 신경 써야 해요.
    그래서 _“끝까지 돌려도 되는 간단 작업”_ 에 쓰면 편하고,
    복잡한 흐름 제어가 필요하면 **for in**·**while**이 더 적합합니다.

> **실행 방법**
##### 
1. > 컬렉션·시퀀스 = for-in 고정.
2. > 루프 내부에서 **인덱스가 필요한** 경우 enumerated() 사용.
3. > 무한 루프 + 비동기 대기 = while await !Task.isCancelled { ... }.
    

---


### **Q5. 함수 선언 시 매개변수 라벨, 기본값을 어떻게 활용해야 할까?**

| **단계**                 | **사고 절차**                         | **메모**                                |
| ---------------------- | --------------------------------- | ------------------------------------- |
| **① 라벨 가독성**           | 외부 라벨은 **문장처럼** 읽히게               | move(from:to:), login(user:password:) |
| **② 첫 매개변수 규칙**        | Swift 5.2+는 첫 파라미터도 라벨 있음(생략 가능)  | 필요 시 _ 로 생략                           |
| **③ 기본값 제공**           | Bool·옵션·희귀 옵션 → 기본값으로 **API 간소화** | 서명 오버로드 대신 기본값                        |
| **④ Variadic & inout** | 다수 인자·수정 필요 시 사용                  | 꼭 필요한 곳에만                             |

> **✏️ 예제**

```
// 읽기 쉬운 함수 서명
func drawLine(from start: CGPoint,
              to end: CGPoint,
              dashed: Bool = false,
              width: CGFloat = 1.0) { ... }

// 호출
drawLine(from: .zero, to: CGPoint(x: 10, y: 0))
drawLine(from: p1, to: p2, dashed: true, width: 2)
```

- > 기본값 덕분에 **오버로드 4개** 대신 **1개**로 API 단순화.
- > 매개변수 순서 바꾸지 말고 **의미 그룹** 묶기(시작점→끝점→스타일).

```
import Foundation

//───────────────────────────────────────────────
// ① 라벨 가독성 예시 ─ move(from:to:)
//───────────────────────────────────────────────
func move(from start: String, to end: String) {
    print("🛫 \(start) → 🛬 \(end)")
}

// 호출부 ― 외부에서 보면 한 문장처럼 읽힌다
move(from: "Seoul", to: "Tokyo")
// 출력: 🛫 Seoul → 🛬 Tokyo


//───────────────────────────────────────────────
// ② 첫 매개변수 라벨 생략 예시 ─ play(_ song:)
//───────────────────────────────────────────────
struct Song { let title: String }
func play(_ song: Song) {                 // 첫 파라미터 라벨을 _ 로 생략
    print("▶️ Playing:", song.title)
}

play(Song(title: "Imagine"))              // 'play(…)' 만으로도 의미가 분명


//───────────────────────────────────────────────
// ③ 기본값(Default Value) 예시 ─ showAlert()
//───────────────────────────────────────────────
func showAlert(title: String,
               message: String? = nil,    // 옵션 → nil 이 기본
               animated: Bool = true) {   // Bool → true 가 기본
    print("🚨", title,
          message == nil ? "" : "(\(message!))",
          animated ? "[애니메이션]" : "[즉시]")
}

// 기본값을 쓰면 매개변수가 자동 생략된다
showAlert(title: "완료")                   // 🚨 완료 [애니메이션]
showAlert(title: "오류", animated: false)  // 🚨 오류 [즉시]


//───────────────────────────────────────────────
// ④ Variadic(가변 인자) + inout 예시
//───────────────────────────────────────────────

// ④-1. 가변 인자 ...  → 인자 개수 제한 없음
func sum(_ nums: Int...) -> Int {          // ... 덕분에 개수 마음대로
    nums.reduce(0, +)                      // reduce 로 모두 더함
}
print("합계:", sum(1, 2, 3, 4, 5))         // 합계: 15

// ④-2. inout      → 함수 안에서 값 직접 바꿔서 돌려주기
func swap(_ a: inout Int, _ b: inout Int) {
    let temp = a                           // temp 로 값 보관
    a = b
    b = temp                               // 두 변수의 값 교환 완료
}

var x = 10, y = 20
swap(&x, &y)                               // & 기호로 inout 전달
print("x:", x, "y:", y)                    // x: 20 y: 10
```

|**기능**|**왜 쓰나요?**|**키워드·표현**|
|---|---|---|
|**외부 라벨**|호출문이 “문장”처럼 읽히게|move(from:to:)|
|**첫 라벨 생략**|명사 한 개만 받을 때 더 짧게|_ song:|
|**기본값**|자주 같은 옵션 → 코드 짧아짐|message: String? = nil|
|**가변 인자**|개수 모를 때 한꺼번에 받기|nums: Int...|
|**inout**|함수 안에서 변수 값을 교환·수정|swap(&x, &y)|

---
### **📌 한눈에 정리**

| **주제**  | **핵심 원칙**                             |
| ------- | ------------------------------------- |
| let/var | **가능한 한 let** – 변하지 않는 값은 불변으로        |
| 타입 추론   | 컨텍스트 기반, 모호할 땐 **명시 타입**              |
| 제어 구문   | **if(간단) → guard(조기탈출) → switch(패턴)** |
| 반복문     | 컬렉션 = for-in, 인덱스필요시 enumerated()     |
| 함수 시그니처 | **의미 있는 라벨** + **기본값**으로 오버로드 최소화     |
## **Guiding Activities**
- > [ ] **사칙연산 계산기** Playground 구현
- > [ ] _The Swift Programming Language_ → Basics 챕터 정독 & 노트 작성
- > [ ] GPT에게 let/var 실무 사례 질문 후 요약
- > [ ] 타입 추론 vs 명시적 타입 선언 비교 실험 (실행 시간·가독성 평가)

---
## **새로 추가할 노드**

> Swift_Syntax.md
