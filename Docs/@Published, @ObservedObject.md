 @Published, @ObservedObject.md

 #이슈 번호 - ‘@Published, @ObservedObject’

> [!question] GQ1. **@Published, @ObservedObject는 뭐야?**
> GQ2. **@Published, @ObservedObject는 언제 사용해?**
> GQ3. @Published, @ObservedObject는 어떻게 동작해?**

**Description**

- `@Published`는 **값이 바뀌었을 때, 자동으로 알림을 보내주는 속성 래퍼로 `@Published`가 붙어 있으면, 이 값이 `true`나 `false`로 바뀔 때마다 "**나 바뀌었어!" 하고 알려준다. `@ObservedObject`는 다른 곳**에서 만든 **객체의 변화**를 지켜보는 속성 래퍼로@ObservedObject은 다른 뷰의 상태를 관찰한다.

**주요 기능**

- `@Published`는 값이 바뀌었을 때 **자동으로 알림을 보내주는 역할**을 하며, `@ObservedObject`는 그 알림을 받아서 **뷰를 다시 그려주는 역할**을 한다. 예를 들어, `isPlaying` 값이 `true`나 `false`로 바뀌면 `@Published`가 `"나 바뀌었어!"` 하고 주변에 방송을 보내고(`objectWillChange.send()`), `@ObservedObject`는 그 방송을 듣고 있다가 SwiftUI에게 알려서 **화면이 자동으로 다시 그려지게 다**. 이 모든 흐름은 Apple의 **Combine 프레임워크**를 기반으로 동작하며, `@Published`는 Publisher, `@ObservedObject`는 Subscriber처럼 작동한다.


**코드 예시**
## `@Published`

```swift
MusicPlayer.swift

import Foundation
import Combine

class MusicPlayer: ObservableObject {
    @Published var isPlaying = false
}

```

## `@ObservedObject`

```swift
ContentView.swift

import SwiftUI

struct ContentView: View {
    @ObservedObject var playing = MusicPlayer()
    
    var body: some View {
        VStack(spacing: 20) {
            Text(playing.isPlaying ? "재생 중!" : "멈춤!")
                .font(.largeTitle)
                .foregroundColor(playing.isPlaying ? .green : .gray)
            Button {
                playing.isPlaying.toggle()
            } label: {
                Text(playing.isPlaying ? "Pause" : "Play")
                    .font(.title)
                    .padding()
                    .background(playing.isPlaying ? .red : .blue)
                    .foregroundColor(.white)
                    .cornerRadius(10)
            }

        }
    }
}

```

## 작동 흐름

```
graphql
복사
사용자가 버튼을 누름
        ↓
isPlaying 값이 true로 바뀜
        ↓
@Published → objectWillChange.send()
        ↓
@ObservedObject가 알림 받음
        ↓
SwiftUI가 뷰 다시 그림

```

## Combine 프레임워크

- 이 모든 기능은 **Combine**이라는 Apple의 반응형 프로그래밍 시스템을 사용한다.
- `@Published`는 Combine의 `Publisher`로 작동
- `@ObservedObject`는 Combine의 `Subscriber`로 등록


**Keywords**

- Published
- ObservedObject


**References**
- 
 