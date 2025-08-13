
GQ: LazyVStack을 Carousel이 대신할 수 있을까? 

## Description

- ScrollView와 TabView를 조합해 리스트를 가로로 스와이프 할 수 있도록 하는 것
![[c3Carousel.jpeg]]
## 주요 기능

[](https://github.com/DeveloperAcademy-POSTECH/2025-SSG-A1/blob/main/Templates/Template.md#%EC%A3%BC%EC%9A%94-%EA%B8%B0%EB%8A%A5)

- 실제 활용을 작성

## 코드 예시
`import SwiftUI`
`struct Carousel: View {`
    `var body: some View {`
        `TabView {`
            `ForEach(0..<4, id: \.self) { _ in`
            `//가로 스와이프 하는 화면 갯수`
                `VStack {`
                    `HStack {`
                        `Image("image1")`
                            `.resizable()`
                            `.scaledToFill()`
                            `.frame(width: 40, height: 40)`
                            `.clipped()`
                            `.shadow(radius: 5)`
                        `Text("강아지")`
                            `.font(.headline)`
                            `.padding(.bottom, 10)`
                    `}`
                    `HStack {`
                        `Image("image2")`
                            `.resizable()`
                            `.scaledToFill()`
                            `.frame(width: 40, height: 40)`
                            `.clipped()`
                            `.shadow(radius: 5)`
                        `Text("고양이")`
                            `.font(.headline)`
                            `.padding(.bottom, 10)`
                    `}`
                    `HStack {`
                        `Image("image3")`
                            `.resizable()`
                            `.scaledToFill()`
                            `.frame(width: 40, height: 40)`
                            `.clipped()`
                            `.shadow(radius: 5)`
                        `Text("투슬리스")`
                            `.font(.headline)`
                            `.padding(.bottom, 10)`
                    `}`
                    `HStack {`
                        `Image("image4")`
                            `.resizable()`
                            `.scaledToFill()`
                            `.frame(width: 40, height: 40)`
                            `.clipped()`
                            `.shadow(radius: 5)`
                        `Text("스티치")`
                            `.font(.headline)`
                            `.padding(.bottom, 10)`
                    `}`
                `}`
            `}`
            
        `}`
        `.tabViewStyle(PageTabViewStyle(indexDisplayMode: .automatic))`
        `//TabView는 기본적으로 하단 탭바 형식인데, 이 모디파이어를 붙이면 페이지 형식으로 변한다.` 
        `//indexDisplayMode는 하단 네개의 점 .automatic: 시스템 상황에 맞게 표시 여부 결정, .always: 항상 표시, .never: 절대 표시 안함`
        `.frame(height: 300)`
    `}`
    
`}`
![[carouselpractice.mov]]
## Keywords

VStack

- 수직 방향으로 뷰를 쌓는다
- 기본적으로 상위 뷰의 너비를 최대한 활용하여 뷰들을 배치
- 자식 뷰의 수가 적거나 레이아웃이 간단할 때 유용

LazyVGrid

- 뷰를 그리드 형태로 배치
- columns라는 매개변수를 사용하여 열의 수를 지정
- GridItem을 사용하여 각 열의 너비와 유연성을 설정할 수 있음
- 필요한 경우에만 뷰를 생성하므로, 많은 수의 뷰를 효율적으로 처리 가능

`struct GridItem {`
	`var size: GridItem.Size`
	`var spacing: CGFloat?`
	`var alignment: Alignment?`
`}`

`let columns = Array(repeating: GridItem(.flexible(), spacing: 12), count: 4)`

	`LazyVGrid(columns: columns, spacing: 12) {`
		`ForEach(0..<20) { i in`
			`Rectangle()`
				`.fill(Color.blue)`
				`.frame(height: 100)`
				`.overlay(Text("\(i)").foregroundColor(.white))`
		`}`
`}`

이 예시의 경우, 4개의 열을 만들고, 남은 공간을 균등하게(.flexible)하게 나눠 가진다.

크기 조절 방법

.fixed

.flexible

.adaptive

GridItem 선언 방법
1. 직접선언
    
    ```swift
    let columns = [
    	GridItem(.flexible()),
    	GridItem(.flexible()),
    	GridItem(.flexible())
    ]
    ```

2. **Array(repeating:)으로 반복 선언**
    
    ```swift
    let columns = Array(repeating: GridItem(.flexible(), spacing: 12), count: 4)
    ```

3. **인라인으로 직접 전달도 가능**

```swift
LazyVGrid(columns: [
		GridItem(.flexible()),
		GridItem(.flexible())
]) {
// ...
}
```

## References

https://developer.apple.com/documentation/swiftui/liststyle/carousel/