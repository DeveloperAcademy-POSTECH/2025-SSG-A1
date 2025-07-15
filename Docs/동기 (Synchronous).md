
> [!question] GQ1. 동기는 어떻게 동작을 하는거지?  
> [!question] GQ2. 비동기는 어떻게 동작하는거지?  
> [!question] GQ3. 각각 Swift에서는 어떻게 사용이 되는거지?  
> [!question] 추가로 알아야 할 부분은?

**Description**

- 동기 실행은 “직렬 및 순차” 한 작업이 완료될 때까지 다음 줄로 넘어가지 않음
- 같은 스레드(주로 메인 스레드)가 계속 묶임
- 함수 호출은 호출→계산→리턴의 전형적인 호출 스택 흐름
- 호출자가 응답을 받을 때까지 **차단(blocking)**
    

**주요 기능**
- UI 즉답 : 버튼 색 변경, 애니메이션 트리거처럼 수 ms 내에 끝나는 작업
- 모델 동기화 : 프로퍼티 값 계산, 캐시 조회 등 메모리 안에서 빠르게 완료되는 로직
- `DispatchQueue.sync` : 같은 serial 큐에서 “앞선 작업 → 뒷작업” 순서를 보장해야 할 때 사용
- 단순 파일 I/O(몇 KB) : 속도가 충분히 빨라 UI 저하가 없을 때
    
**코드 예시**
```swift
// 1) serial 큐에서 순서를 ‘강제’하고 싶은 상황
let serial = DispatchQueue(label: "com.example.serial")

serial.async {                   // A
    print("First")
}
serial.sync {                    // B (A가 끝날 때까지 BLOCK)
    print("Second")
}

// 2) 데이터 변환을 직렬 루프 안에서 처리
let list = (1...1_000).map { $0 }
let doubled = list.map { $0 * 2 }   // 100 µs 급, 동기 처리 적합
print(doubled[0])
```

> [!NOTE]  
> `sync` 블록(B)은 A가 완료될 때까지 큐를 **블로킹**합니다.  
> UI 쓰레드에서 이런 패턴을 사용하면 스크롤이 멈추거나 앱이 “먹통”처럼 보일 수 있으므로 주의하세요.

**Keywords**
- Blocking / 차단
- Serial Queue / 직렬 큐
- Call Stack(호출 스택)
- `DispatchQueue.sync`
- Main Thread

**References**
- _DispatchQueue.sync(execute:)_ — Apple Developer Documentation
- _Improving App Responsiveness_ — Xcode Instruments Guide

# 비동기 (Asynchronous)

키워드  
비동기

> [!question] GQ1. 동기는 어떻게 동작을 하는거지?  
> [!question] GQ2. 비동기는 어떻게 동작하는거지?  
> [!question] GQ3. 각각 Swift에서는 어떻게 사용이 되는거지?  
> [!question] 추가로 알아야 할 부분은?

**Description**

- 비동기 실행은 “대기 ×, 넘어가기 ⭕”가 핵심입니다. 시간‑오래 걸리는 작업을 `Task`나 `DispatchQueue.async`로 분리해 두면 호출자는 곧바로 다음 코드로 진행합니다.
    
- Swift 5.5+부터 도입된 **async/await**와 **구조화된 동시성**(Task 트리) 덕분에, 스레드 관리 대신 **일시 중단(suspend)·재개(resume)** 라는 논리적 흐름으로 코드를 작성할 수 있습니다.
    

**주요 기능**

- 네트워크·DB I/O — `URLSession.shared.data(for:)`, CloudKit, Core Data async API
    
- 대용량 파일 처리·압축 — 백그라운드 워커 큐에서 CPU 작업 후 메인으로 결과 전송
    
- 타이머·애니메이션 — `Task.sleep`, `Timer.publish`(Combine)
    
- 병렬 계산 — `async let`, `TaskGroup` 으로 CPU 코어 활용 극대화
    
- 라이프사이클 연동 — SwiftUI `.task {}`, `.refreshable {}` 모디파이어로 View 생명주기에 맞춰 자동 취소
    

**코드 예시**

```swift
import SwiftUI

struct PhotoView: View {
    @State private var image: Image? = nil
    @State private var error: String? = nil

    var body: some View {
        VStack {
            image?
                .resizable()
                .scaledToFit()
            if let error { Text(error) }
        }
        .task {                // View 등장 시 자동 실행
            do {
                image = try await fetchRandomPhoto()
            } catch {
                error = error.localizedDescription
            }
        }
    }

    // ▶︎ 네트워크 ‧ 디코딩이 모두 비동기
    func fetchRandomPhoto() async throws -> Image {
        let url = URL(string: "https://picsum.photos/400")!
        let (data, _) = try await URLSession.shared.data(from: url)
        guard let ui = UIImage(data: data) else { throw URLError(.badServerResponse) }
        return Image(uiImage: ui)
    }
}
```

> [!TIP]  
> `await` 앞에서 함수가 **일시 중단**되고, 데이터가 도착하면 자동 **재개**합니다.  
> 메인 스레드를 블로킹하지 않으므로 스크롤·제스처가 부드럽게 유지됩니다.

**Keywords**

- `async` / `await`
    
- `Task` / `Task.detached`
    
- `TaskGroup` / `async let`
    
- Structured Concurrency
    
- Actor / 데이터 레이스 안전
    

**References**

- _Swift Concurrency Overview_ — Apple Developer Documentation
    
- _Task_ (struct) — Apple Developer Documentation
    
- WWDC21 **“Meet async/await in Swift”** Session
    
- _Managing Structured Concurrency_ — Apple Tutorials
    

## 추가로 알아야 할 부분

|주제|왜 중요한가?|Swift API|
|---|---|---|
|**취소(cancellation)**|사용자가 화면을 나가면 네트워크 요청을 중단해 데이터·배터리 절약|`try Task.checkCancellation()`|
|**우선순위**|애니메이션 > 데이터 싱크 등 중요도에 따라 CPU 배분|`Task(priority:)`|
|**Actor**|동일 데이터에 대한 동시 쓰기 충돌 방지|`actor Cache { … }`|
|**Strict Concurrency**|Swift 6부터 기본값 ON, 컴파일 타임 데이터 레이스 탐지|`@preconcurrency`, `Sendable`|

> [!NOTE]  
> “**메인 스레드 = UI 스레드**”라는 규칙만 기억해도 절반은 해결됩니다.  
> UI 갱신은 **동기**, 그 외 I/O·무거운 계산은 **비동기**로 보내면 앱이 멈추지 않습니다.