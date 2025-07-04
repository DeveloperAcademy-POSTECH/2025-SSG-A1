- Stack 종류
    
    ![image.png](attachment:beac9dc2-4f18-44ac-bd49-f0073a4a788c:10d7da96-621a-431d-923e-6edadc250762.png)
    
- GQ
    
    
    ![image.png](attachment:6d4d2a5a-a31f-446f-84f7-c6316acf952e:image.png)
    
    ![image.png](attachment:a3f5f1b2-5bde-4dd2-ad2e-dddc4fe14926:d51042d6-d2e5-4137-a7fb-e42c41e1c317.png)
    
    ![image.png](attachment:10730e06-c308-4cbd-8715-f13730b3dd96:image.png)
    
    - 이렇게
        
        ![image.png](attachment:e20f6260-fdd8-4a11-b402-4334dd3fd8c1:image.png)
        
    
    모르겠다. 왜 zstack에서 bottom 정렬을 했는데 Image는 그대로고, text만 정렬이 될까?
    
    view에서 layout은 어떻게 배치되는지 그 원리가 궁금하다.
    
- SwiftUI가 뷰의 크기를 결정하고 배치하는 원리
    
    **Layout의 기본 동작**
    
    ![image.png](attachment:76916582-fece-453e-9e65-284589e1d0a9:image.png)
    
    - Root View : ContentView의 상위 뷰로 SafyArea를 제외한 영역(ignoreSatyArea 를 사용시 전체화면을 포함)
    - ContentView : Body 영역의 최상위뷰로 항상 [Layout neutral](https://www.notion.so/4-Layout-neutral-21fef75c63838059b697cb2c43769b5e?pvs=21)이다. bounds는 하위뷰의 bounds를 따른다. 따라서 Body에 의해 결정됨
    - Text : Body가 있는 뷰
    
    → **따라서 ContentView와 Text는 동일 뷰로 취급할 수 있다.** 
    
    1. **Root View**
    2. **Text** 
    
    이 두 개의 뷰로 레이아웃 프로세스를 알아보자.
    
    **Layout Procedure**
    
    1. Parent proposes a size for child : Parent는 Child에게 size를 제안
    2. Child chooses its own size : Child는 Parent의 사이즈를 고려해(무시할 수도 있음) 자신의 size를 결정함
    3. Parent places child in parent’s coordinate space : Parent는 Child의 결정을 존중 후 자신의 좌표 공간안에 Child를 위치시킴
    
    ![image.png](attachment:827e0bf4-5b68-49bb-a956-ec7810da63ff:image.png)
    
    ![image.png](attachment:ebc8bc89-55ca-4a54-ba98-7a2db57b9d41:image.png)
    
    ![image.png](attachment:678ce243-0672-4cbb-8791-f92319bf6fd9:image.png)
    
    ![image.png](attachment:9a92cb75-09b4-4685-a64b-b1e780bb8a0c:image.png)
    
    1. SwiftUI rounds coordinates to nearest pixel : SwiftUI는 view의 모서리를 가장 가까운 pixel로 반올림한다.
    
    이것은 SwiftUI에서 알아서 처리하는 부분으로 왼쪽의 anti-aliasing대신 우리는 항상 Crispy하고 Clear한 edge를 갖게 된다.
    
    ![image.png](attachment:719b0962-e85f-44f4-8d26-ce03415665e7:image.png)
    
    ![image.png](attachment:1e3a09ad-b8a3-44f4-9936-1b54930bccc0:image.png)
    

- Alignment
    
    각 스택마다 서로 다른 타입을 받아들인다.
    
    Hstack은 가로 방향으로 쌓는 것이므로, 세로 방향에 대한 정렬인 VerticalAlignment 타입을.
    
    - .top, .bottom. .center
    
    Vstack은 세로 방향으로 쌓는 것이므로, 가로 방향에 대한 정렬인 HorizentalAlignment 타입을.
    
    - .leading
    - .trailing
    - .center
    
    Zstack은 가로와 세로축에 대한 정보가 모두 필요하므로 두가지 값을 가진 Alignment 타입을 받아들인다.
    
    - .topleading
    - .toptrailing
    - .bottomleading
    - .bottomtrailing
    
    ### SwiftUI의 정렬 가이드(Alignment Guide)
    
    : 뷰(View)를 정확히 어디에 위치시킬지 "숫자"로 알려주는 규칙
    
    보통은 Hstack, Vstack, Zstack같은 걸 쓰면 알아서 정렬되는데, 근데 가끔 **정렬이 내 맘처럼 되지 않을 때, 정밀하게 맞추고 싶을 때** 가이드를 사용합니다.
    
    다양한 정렬의 종류
    
    | 정렬 종류 | 설명 |
    | --- | --- |
    | `.leading`, `.trailing` | 가장 기본적인 정렬 방법 (왼쪽, 오른쪽) |
    | `Alignment Guide` | 더 세밀한 정렬 위치를 수치로 지정 |
    | **Implicit (암묵적)** | SwiftUI가 자동으로 정하는 기본 위치 |
    | **Explicit (명시적)** | 내가 직접 정해주는 위치 |
    | `TextAlignment` | 텍스트 안에서 줄별 정렬 방식 |
    | `Frame Alignment` | 프레임 안에서 뷰의 정렬 방식 |
    | `Container Alignment` | 전체 VStack, HStack, ZStack 안에서 정렬 방식 |
    
    예를 들면 .leading 이라는 정렬 키워드는...
    
    - 텍스트에서도 쓰고
    - 프레임에서도 쓰고
    - VStack 안에서도 쓰고
    - 정렬 가이드에서도 쓰여요.
    
    **이 모든 게 상황마다 다른 의미를 갖기 때문에** 헷갈립니다.
    

https://seons-dev.tistory.com/entry/View-layout

https://developer.apple.com/videos/play/wwdc2019/237/

https://104hsh96.tistory.com/59

https://medium.com/gdsc-tuk/ios-%EC%8A%A4%ED%84%B0%EB%94%94-5%EC%A3%BC%EC%B0%A8-ch24-4ce4b157ce85

https://m.blog.naver.com/leedjlee/222049106681

https://velog.io/@rafa/%EC%95%A0%ED%94%8C%EA%B3%B5%EC%8B%9D%EB%AC%B8%EC%84%9C%EB%BD%80%EA%B0%9C%EA%B8%B0%EC%8A%A4%EC%9C%A01%ED%8E%B8

https://sujinnaljin.medium.com/swiftui-swiftui-%EC%9D%98-layout-3-%EB%8B%A8%EA%B3%84-cf70ba00f88e