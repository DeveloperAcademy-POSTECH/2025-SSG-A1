
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