
#이슈 번호 - ‘키워드’

> [!question] GQ1. 왜 zstack에서 bottom 정렬을 했는데 Image는 그대로고, text만 정렬이 될까? view에서 layout은 어떻게 배치되는지 그 원리가 궁금하다.GQ를 쓰세요 GQ2. GQ를 쓰세요


**Description**

- SwiftUI에서 뷰(View)의 위치와 크기는 **자동 레이아웃 시스템**에 따라 정해지며, 이 시스템은 선언형 UI 패러다임에 기반하여 **간결하면서도 유연한 인터페이스 배치**를 가능하게 다. 하지만 실제로는 "왜 이 위치에 뷰가 생기지?" "왜 정렬이 의도한 대로 안 되지?" 같은 혼란이 생기곤 한다.  이를 이해하기 위해선 SwiftUI의 **레이아웃 동작 원리, Stack 구조, Alignment 종류**를 함께 살펴볼 필요가 있다.

**주요 기능**

- SwiftUI의 레이아웃 시스템은 각 뷰의 크기와 위치를 자동으로 계산하고 배치해주며, 주로 `HStack`, `VStack`, `ZStack` 같은 스택을 통해 뷰들을 정렬합니다. 이때 각 스택은 방향에 따라 서로 다른 Alignment 타입을 사용하며, 예를 들어 `HStack`은 세로 정렬(`.top`, `.center`, `.bottom`), `VStack`은 가로 정렬(`.leading`, `.center`, `.trailing`)을 사용합니다.
- ZStack처럼 겹치는 구조에서는 `.bottomTrailing`처럼 가로+세로를 모두 지정하는 Alignment가 필요합니다. 하지만 일부 뷰는 이러한 정렬을 따르지 않을 수 있어, 원하는 배치가 되지 않는 경우도 발생합니다. 이런 상황에서는 **정렬 가이드(Alignment Guide)**를 활용해 뷰의 위치를 **숫자 기반으로 직접 제어**할 수 있습니다.
- SwiftUI의 배치는 기본적으로 다음과 같은 순서로 작동합니다: 부모 뷰가 자식 뷰에게 크기를 제안하고, 자식 뷰가 자신의 크기를 선택한 후, 부모가 그 자식을 자신의 좌표 공간 안에 배치합니다. 이렇게 **제안 → 선택 → 배치**라는 구조를 통해 유연하고 예측 가능한 레이아웃을 제공합니다. 또한 SwiftUI는 모든 좌표를 가장 가까운 픽셀로 반올림하여, anti-aliasing 없이도 선명한 UI를 자동으로 렌더링합니다.
- 실제 개발에서는 이 원리를 이해함으로써, 정렬이 의도와 다를 때 원인을 분석하고, 필요한 경우 정렬 가이드나 frame 조정을 통해 **정밀하게 뷰를 배치하는 데 활용**할 수 있습니다

**코드 예시**

- - SwiftUI가 뷰의 크기를 결정하고 배치하는 원리
    
    **Layout의 기본 동작**![[ㅁㅁㅁㅁ.png]]
- Root View : ContentView의 상위 뷰로 SafyArea를 제외한 영역(ignoreSatyArea 를 사용시 전체화면을 포함)
- ContentView : Body 영역의 최상위뷰로 항상 [Layout neutral](https://www.notion.so/4-Layout-neutral-21fef75c63838059b697cb2c43769b5e?pvs=21)이다. bounds는 하위뷰의 bounds를 따른다. 따라서 Body에 의해 결정됨
- Text : Body가 있는 뷰

따라서 ContentView와 Text는 동일 뷰로 취급할 수 있다.

이 두 개의 뷰로 레이아웃 프로세스를 알아보자.

Layout Procedure

1. Parent proposes a size for child : Parent는 Child에게 size를 제안
2. Child chooses its own size : Child는 Parent의 사이즈를 고려해(무시할 수도 있음) 자신의 size를 결정함
3. Parent places child in parent’s coordinate space : Parent는 Child의 결정을 존중 후 자신의 좌표 공간안에 Child를 위치시킴
![[ㅈㅈㅈㅈ.png]]
![[ㅂㅂㅂㅂ.png]]
![[ㅋㅋㅋ.png]]
![[ㅌㅌㅌ.png]]



4. SwiftUI rounds coordinates to nearest pixel : SwiftUI는 view의 모서리를 가장 가까운 pixel로 반올림한다.
![[ㅎㅎ.png]]
![[ㅅㅅ.png]]

Alignment
각 스택마다 서로 다른 타입을 받아들인다. Hstack은 가로 방향으로 쌓는 것이므로, 세로 방향에 대한 정렬인 VerticalAlignment 타입을.(- .top, .bottom. .center) Vstack은 세로 방향으로 쌓는 것이므로, 가로 방향에 대한 정렬인 HorizentalAlignment 타입을.(- .leading, .trailing, .center) Zstack은 가로와 세로축에 대한 정보가 모두 필요하므로 두가지 값을 가진 Alignment 타입을 받아들인다.(- .topleading, .toptrailing, .bottomleading, .bottomtrailing)

SwiftUI의 정렬 가이드(Alignment Guide)
뷰(View)를 정확히 어디에 위치시킬지 "숫자"로 알려주는 규칙으로 보통은 Hstack, Vstack, Zstack같은 걸 쓰면 알아서 정렬되는데, 근데 가끔 **정렬이 내 맘처럼 되지 않을 때, 정밀하게 맞추고 싶을 때** 가이드를 사용합니다.

다양한 정렬의 종류
|정렬 종류|설명|
|---|---|
|`.leading`, `.trailing`|가장 기본적인 정렬 방법 (왼쪽, 오른쪽)|
|`Alignment Guide`|더 세밀한 정렬 위치를 수치로 지정|
|**Implicit (암묵적)**|SwiftUI가 자동으로 정하는 기본 위치|
|**Explicit (명시적)**|내가 직접 정해주는 위치|
|`TextAlignment`|텍스트 안에서 줄별 정렬 방식|
|`Frame Alignment`|프레임 안에서 뷰의 정렬 방식|
|`Container Alignment`|전체 VStack, HStack, ZStack 안에서 정렬 방식|

예를 들면 .leading 이라는 정렬 키워드는...

- 텍스트에서도 쓰고
- 프레임에서도 쓰고
- VStack 안에서도 쓰고
- 정렬 가이드에서도 쓰여요.

**이 모든 게 상황마다 다른 의미를 갖기 때문에** 헷갈립니다.



**Keywords**

- `Stack`
- `HStack` / `VStack` / `ZStack`
- `Alignment`
- `Alignment Guide`
- `Layout neutral`

**References**

- [https://developer.apple.com/videos/play/wwdc2019/237/](https://developer.apple.com/videos/play/wwdc2019/237/)
- [https://104hsh96.tistory.com/59](https://104hsh96.tistory.com/59)
- [https://medium.com/gdsc-tuk/ios-스터디-5주차-ch24-4ce4b157ce85](https://medium.com/gdsc-tuk/ios-%EC%8A%A4%ED%84%B0%EB%94%94-5%EC%A3%BC%EC%B0%A8-ch24-4ce4b157ce85)
- [https://m.blog.naver.com/leedjlee/222049106681](https://m.blog.naver.com/leedjlee/222049106681)
- [https://velog.io/@rafa/애플공식문서뽀개기스유1편](https://velog.io/@rafa/%EC%95%A0%ED%94%8C%EA%B3%B5%EC%8B%9D%EB%AC%B8%EC%84%9C%EB%BD%80%EA%B0%9C%EA%B8%B0%EC%8A%A4%EC%9C%A01%ED%8E%B8)
- [https://sujinnaljin.medium.com/swiftui-swiftui-의-layout-3-단계-cf70ba00f88e]
- (https://sujinnaljin.medium.com/swiftui-swiftui-%EC%9D%98-layout-3-%EB%8B%A8%EA%B3%84-cf70ba00f88e)





