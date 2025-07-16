>[!question]
>GQ1. [[뷰빌더(ViewBuilder)]]의 정의 코드에 나오는 @resultBuilder의 정체가 뭘까?

## Description
- 여러 줄로 쓰인 코드 블록을 내부적 으로 "한 개의 값" 으로 만들어 주는 문법 도구

## 주요 기능

- 선택한 여러 요소들을 순서대로 전달하면서 **하나의 새로운 값을 단계별로 만들어낼 수 있게 한다**
- 예를 들어 VStack 안에 여러 개의 뷰를 넣었을 때, Swift는 그 뷰들을 **자동으로 TupleView라는 내부 타입으로 묶어서** VStack의 자식으로 **하나의 뷰처럼 저장**할 수 있게 한다. 즉, 여러 개의 뷰를 **하나의 뷰로 변환해주는 역할.**
- 이전에는 이 기능이 @_functionBuilder라는 이름으로 제공됐는데, 언더스코어(_)가 붙은 건 **일반 사용자가 쓰기 위한 것이 아니었음**을 의미한다. resultbuilder역시 사용자 측면이 아닌 언어개발자 측면이다.


## 코드 예시
```swift
@resultBuilder
struct SimpleStringBuilder {
static func buildBlock(_ parts: String...) -> String {
        parts.joined(separator: "\\n")
    }
}
```

모든 result builder는 **반드시 buildBlock()이라는 정적 메서드를 하나 이상 제공해야 한다.** 이 메서드는 어떤 데이터를 받아서 가공해야 한다.

위 예제에서는 **가변인자(String)**을 받아서 **줄바꿈(\n)으로 합쳐 하나의 문자열로 반환한다.**

SimpleStringBuilder 구조체는 **result builder가 된 것이고**, 이 기능이 필요한 곳에서 @SimpleStringBuilder를 붙여서 **여러 문자열을 줄바꿈으로 자동 결합**할 수 있다.

- 사용은 다음과 같다.
    ```swift
    @SimpleStringBuilder
    func makeList() -> String {
        "스위프트 스터디"
        "열심히 할게요"
        "화이팅.."
    }
    ```


## Keywords
- [[뷰빌더(ViewBuilder)]]


## References
https://www.hackingwithswift.com/swift/5.4/result-builders

https://medium.com/hcleedev/swift-resultbuilder%EB%8A%94-%EB%AC%B4%EC%97%87%EC%9D%BC%EA%B9%8C-a97ab7a47be6
