### 동기(synchronous) 프로그래밍
동기(synchronous)란, 어떤 작업을 실행할 때 그 작업이 끝나기를 기다리는 방식을 의미한다. 즉, 작업이 완료될 때까지 다음 코드의 실행을 멈추고 기다리는 것이다. 이러한 방식은 작업의 순서를 보장하고, 작업이 끝날 때까지 결과를 기다리는 것이 가능하다.
### 비동기(asynchronous)
비동기(asynchronous)란, 어떤 작업을 실행할 때 그 작업이 완료되지 않더라도 다음 코드를 실행하는 방식을 의미한다. 즉, 작업이 완료되지 않았더라도 결과를 기다리지 않고 다음 코드를 실행하는 것이다. 이러한 방식은 작업이 오래 걸리는 경우 시간을 절약하고, 병렬적인 작업 처리가 가능하다. 동기 방식으로 파일을 읽는다면 파일을 읽기 시작한 이후에 다음 코드를 실행하지 않고 파일이 읽혀지기를 기다린다. 반면에 비동기 방식으로 파일을 읽는다면 파일을 읽는 작업이 실행되는 동안 다른 작업을 수행할 수 있다.

> 경험적으로 비동기를 사용하기 적합한 경우는 파일을 I/O 하거나 네트워크 통신(API 호출) 등에 적합하다.

| 기준 | 동기(Synchronous) 프로그래밍 | 비동기(Asynchronous) 프로그래밍 |
|------|-------------------------------|----------------------------------|
| **장점** | 예측 가능성 높음<br>오류 처리 간단<br>코드 가독성 좋음 | 효율적인 리소스 활용<br>비블로킹 UI 제공<br>높은 처리량 가능 |
| **단점** | 리소스 활용 불효율<br>UI 블로킹 가능성 | 복잡한 오류 처리<br>콜백 지옥 가능성 |
| **적합한 사용** | 작은 규모의 프로젝트<br>순차적 실행이 중요한 작업 | 대규모 데이터 처리<br>사용자 경험을 최우선으로 고려하는 웹 애플리케이션 개발 |
| **코드 예시** | ```python<br>result = do_some_task()<br>print(result)``` | ```python<br>async def async_task():<br>    result = await do_some_task()<br>    print(result)``` |
| **주요 고려 사항** | 작업 처리 순서의 명확성과 예측 가능성<br>간단한 작업 흐름 및 오류 관리 | 동시 작업 처리의 필요성<br>비동기 작업의 복잡도 및 오류 관리의 필요성 |


## 동기 코드 예시
```swift
func functionA() {
    print("Function A 시작")

    functionB() // B 함수 호출 (동기)

    print("Function A 종료")
}

func functionB() {
    print("Function B 시작")

    // B 함수의 작업 수행

    print("Function B 종료")
}

// 메인 스레드에서 function A 호출
functionA()
```

함수를 실행하면 다음과 같이 출력됩니다.
```swift
Function A 시작
Function B 시작
Function B 종료
Function A 종료
```

## 비동기 코드 예시
```swift
func functionA() async {
    print("Function A 시작")

    await functionB() // 비동기 호출

    print("Function A 종료")
}

func functionB() async {
    print("Function B 시작")

    // B 함수의 작업 수행 (예: 네트워크 요청, 파일 읽기 등)
    try? await Task.sleep(nanoseconds: 1_000_000_000) // 예시로 1초 대기

    print("Function B 종료")
}

// 비동기 함수는 Task에서 호출해야 함
Task {
    await functionA()
}
```

위 코드를 실행하면 아래와 같이 출력됩니다.
```swift
Function A 시작
Function B 시작
```

이때, "Function A 종료", "Function B 종료"는 출력되지 않습니다. 왜 그럴까요?

- Task는 백그라운드에서 비동기적으로 실행되므로, 메인 함수 (main.swift or Playground 상단 등)가 종료되면 아직 실행 중인 Task도 중단될 수 있습니다.
- `try? await Task.sleep(nanoseconds: 1_000_000_000)` 코드에서 대기하는 코드가 발생하고, 메인 스레드는 그대로 진행되고, 메인 [[2week_Lucas_20250530_thread]]가 먼저 종료가 되어버려서 functionB 종료, functionA 종료가 출력되기 전에 종료되어버립니다.

##### 동기 코드처럼 동작시키려면?
아래 코드처럼 작성해주시면 됩니다.
```swift
import Foundation

func functionA() async {
    print("Function A 시작")

    await functionB() // 비동기 호출

    print("Function A 종료")
}

func functionB() async {
    print("Function B 시작")

    // B 함수의 작업 수행 (예: 네트워크 요청, 파일 읽기 등)
    try? await Task.sleep(nanoseconds: 1_000_000_000) // 예시로 1초 대기

    print("Function B 종료")
}

// 비동기 함수는 Task에서 호출해야 함
Task {
    await functionA()
}

// 종료 방지를 위한 임시 sleep
sleep(2)
```
- `sleep(2)` 코드를 추가함으로써 메인 스레드가 종료되지 않도록 합니다.
- 그러면 아래처럼 모든 프린트문이 출력되는 것을 확인하실 수 있습니다.
```
Function A 시작
Function B 시작
Function B 종료
Function A 종료
```

## 문법
#### async
```swift
func functionA() async -> Int
```

- 함수 이름 뒤에 `async`라는 키워드를 사용하면 해당 함수를 비동기로 실행하겠다는 의미입니다.
- 만약 함수 수행 중에 Error가 발생할 가능성이 있어 `throws` 키워드를 사용해야 한다면 아래의 코드와 같이 `async` 키워드 뒤에 `throws` 키워드를 사용하도록 합니다.

```swift
func functionA() async throws -> Int
```

#### await
```swift
func functionA() async {
    print("Function A 시작")

    await functionB() // 비동기 호출
}
```

- `async`로 선언한 함수를 호출하기 위해서는 `await` 키워드와 함께 호출합니다.
- `await` 뒤에 호출되는 함수는 반드시 비동기로 수행되는 함수가 와야합니다.
- async 함수를 호출하기 위해서는 asynchronous context 내부에서만 호출할 수 있고 결국 await 또한 asynchronous context 내부에서만 사용될 수 있습니다.
- 만약 await로 호출하는 함수가 Error 발생의 가능성이 있다면 아래의 코드와 같이 await 앞에 try 키워드를 사용합니다.

```swift
func callAsyncFunction() async {
	do {
		try await addNumWithAsync()
	} catch {
		print(error)
	}
}
```

-`await` 키워드를 사용하여 `async` 함수를 호출하게 되면 해당 지점을 **Suspension Point**라고 합니다.

#### Task
```swift
Task {
    await functionA()
}
```
- Task는 비동기로 수행할 작업을 클로저를 통해 전달하여 asynchronous context를 생성합니다.
- 전달된 작업들은 Task 인스턴스 생성과 동시에 수행하게 됩니다.
- 또 다른 예로 SwiftUI에서 View의 instance method로 task라는 modifier를 사용할 수 있는데 해당 View가 생성될때 수행할 비동기 작업을 수행할 수 있습니다.

```swift
Image(systemName: "info.circle")
	.task {
    	await callAsyncFunction()
    }
```