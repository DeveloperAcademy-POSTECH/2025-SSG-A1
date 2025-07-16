
GQ1. C2에서 나는 @EnvironmentObject를 썼는데 어떤 역할이었을까?
GQ2. @EnvironmentObject의 정의는 무엇이며 어떤 코드들이랑 같이 사용될까?

## Description

[](https://github.com/DeveloperAcademy-POSTECH/2025-SSG-A1/blob/main/Templates/Template.md#description)SwiftUI에서 사용하는 의존성 주입(Dependency Injection) 방식 중 하나로, MistakeManager라는 공유 데이터 객체를 CalView 내부에서 사용하기 위해 언한 것. @EnvironmentObject란? - SwiftUI에서 앱 전체 또는 상위 뷰에서 제공한 객체를 하위 뷰에서 접근할 수 있도록 하는 속성 래퍼.

## 주요 기능

- **@EnvironmentObject를 쓰는 이유**
	- 의존성 주입: 뷰 안에서 직접 객체를 만들지 않아도 된다.
	- 데이터 공유: 여러 뷰에서 같은 데이터를 사용 가능
	- 자동 업데이트: 내부 데이터(@Published)가 바뀌면 UI 자동 반영
- @StateObject와의 차이점: @StateObject는 View 내부에서 직접 생성하고 관리하기 때문에 소유자 View에서 사용된다.

## 코드 예시

TBU

## Keywords

자주 쓰는 조합

1. @EnvironmentObject + ObservableObject + @Published : MVVM형 상태 관리
2. .environmentObject() + Preview: 뷰 미리보기용 필수코드
3. @StateObject + .environmentObject(): 루트 View에서 객체 생성 후 자식에게 공유
4. NavigationStack + .environmentObject(): 이동할 때 데이터 유지

## References

https://developer.apple.com/documentation/swiftui/environmentobject