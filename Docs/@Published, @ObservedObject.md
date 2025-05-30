 @Published, @ObservedObject.md

 ## **나의 Challenge**

> 이번 주 학습할 Swift 주제 또는 개념을 간결하게 설명하세요.
> 

 **@Published, @ObservedObject를 공부하자.**

## **Guiding Questions**

> 이번 챌린지를 진행하면서 발생한 GQ를 작성해보세요!
> 
- **@Published, @ObservedObject는 뭐야?**
- **@Published, @ObservedObject는 언제 사용해?**
- **@Published, @ObservedObject는 어떻게 동작해?**
- **어떻게 사용해? → 한번 해볼까?**

## **Guiding Activities**

> 이번 챌린지를 시도할 GA를 작성해보세요!
> 
- [x]  GPT에게 물어보기
- [x]  직접 실습해보자

---

## `@Published`

> `@Published`는 뭐야??
> 
- `@Published`는 **값이 바뀌었을 때, 자동으로 알림을 보내주는 속성 래퍼**

> `@Published`는 언제 **사용해?**
> 

```swift
MusicPlayer.swift

import Foundation
import Combine

class MusicPlayer: ObservableObject {
    @Published var isPlaying = false
}

```

- `isPlaying`이라는 변수가 있어요.
- `@Published`가 붙어 있으면, 이 값이 `true`나 `false`로 바뀔 때마다 "**나 바뀌었어!" 하고 알려줘요.**

## `@ObservedObject`

> `@ObservedObject`는 뭐야??
> 
- `@ObservedObject`는 **다른 곳**에서 만든 **객체의 변화**를 지켜보는 속성 래퍼

> `@ObservedObject`는 언제 **어떻게 사용해?**
> 

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

- `MusicPlayer`라는 클래스는 `@Published var isPlaying`을 가지고 있어요.
- `ContentView`는 `@ObservedObject`로 그걸 **지켜보고** 있다가 ****`isPlaying` 값이 바뀌면 → 버튼의 글자도 **자동으로 바뀌어요**!

![image.png](attachment:64b9376c-0470-4737-b395-a82953a32103:image.png)

![image.png](attachment:8c6e91a3-dc72-4da6-b68a-e4a5489f2fb1:image.png)

## 요약!

| 역할 | 설명 | 키워드 |
| --- | --- | --- |
| 값이 바뀌면 알리기 | 어떤 값이 바뀌었는지 알려줘요 | `@Published` |
| 그 변화를 지켜보기 | 값이 바뀌면 화면도 다시 바꿔줘요 | `@ObservedObject` |

---

## 1. `@Published`는 어떻게 작동할까?

### 내부에서 무슨 일이 일어날까?

```swift
MusicPlayer.swift

var isPlaying = false {
    didSet {
        objectWillChange.send()
    }
}

```

- `didSet`: 값이 바뀐 후 실행되는 코드
- `objectWillChange.send()`: "나 곧 바뀔 거야!" 하고 주변에 **방송을 날려주는 역할**📣

---

## 2. `@ObservedObject`는 어떻게 반응할까?

`@ObservedObject`는 뷰(View)가 어떤 객체(Object)를 **관찰하는 도구로**

```swift
ContentView.swift

@ObservedObject var player = MusicPlayer()

```

이 말은 "이 `player`라는 친구의 데이터가 바뀌면, 나도 뷰를 다시 그릴 거야!"라는 뜻이야.

---

### 실제 작동 순서

1. `player.isPlaying = true`로 값이 바뀌면
2. `@Published`가 `objectWillChange.send()`를 호출해서 "데이터 바뀐다~!"라고 알림
3. `@ObservedObject`가 그 알림을 듣고
4. SwiftUI가 "오! 데이터 바뀌었네?" 하고 **뷰를 다시 그림!**

---

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

---

## 🎯 핵심 요약

| 항목 | 역할 |
| --- | --- |
| `@Published` | 값이 바뀌면 자동으로 알림을 보냄 |
| `objectWillChange` | Combine 프레임워크의 `Publisher`, 즉 방송 장치 |
| `@ObservedObject` | 이 방송을 듣고 뷰를 다시 그림 |
| SwiftUI | 값이 바뀌었다는 걸 감지하면 화면을 다시 만들어줌 |

---

## Combine 프레임워크

- 이 모든 기능은 **Combine**이라는 Apple의 반응형 프로그래밍 시스템을 사용해요.
- `@Published`는 Combine의 `Publisher`로 작동
- `@ObservedObject`는 Combine의 `Subscriber`로 등록

- 주주주의!!
    - “나 바뀜!” 이라고 알림을 두번 주면 안됨!!!!!!!!!
    
    ![image.png](attachment:08e9f5f2-24c6-472e-828c-b7af3abcc9cf:image.png)
    
    ![image.png](attachment:b0b1656a-ff9f-45c0-b4e5-c424174ea91e:image.png)
    
    이렇게 하면**`@Published`도 send()하고** didSet도 send()해서 **중복 호출**이 생김.
