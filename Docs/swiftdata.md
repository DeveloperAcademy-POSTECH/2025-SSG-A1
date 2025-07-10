> [!question] GQ1. GQ를 쓰세요
> GQ. swiftdata는 데이터를 어떻게 저장하는 걸까?

## Description

- **SwiftUI에 최적화된 로컬 데이터 저장 솔루션**
- 내부적으로는 Core Data를 기반으로 하되, Swift 언어의 매크로(@Model)를 사용해 복잡한 설정 없이도 **간결한 코드로 영속성 데이터 관리를 가능하게 한다.
- SwiftUI의 상태 기반 뷰 구조와 잘 통합되며, 선언형 코드 스타일을 유지할 수 있다.
- (기존의 coredata는 uikit기반의 명령형 코드에 최적화 되어있었음.)

## 주요 기능
- @Model
	- SwiftData에서 관리할 **데이터 구조를 정의**할 때 사용
	- 클래스를 @Model로 선언하면 자동으로 저장 가능한 객체로 변환됨
	```
	@Model 
		class TrainingRecord { 
			var failureText: String 
			var savedDate: Date 
		}
	```
- ModelContatiner
	- 앱 전체에서 데이터를 저장하고 꺼내 쓸 수 있는 **공용 저장소** 
	- 우리가 만든 데이터(예: 할 일 목록, 사용자 정보 등)를 영구 저장하는 곳.
		- document 디렉토리에 default.sqlite라는 파일로 자동 저장된다.
		- 저장소의 위치와 이름도 설정할 수 있다.
```
	- @main
		struct MyApp: App {
		    var body: some Scene {
		        WindowGroup {
		            ContentView()
		        }
		        .modelContainer(for: Task.self) //저장소 자동 설정
		    }
		}
```
```
let config = ModelConfiguration(for: [Task.self], url: URL.documentsDirectory.appending(path: "MyData.store")) //저장소 직접 설정.
```

- Model Context
	- 실제 데이터를 **삽입, 삭제, 수정, 저장**하는 주체
	- SwiftUI 뷰에서는 @Environment(\.modelContext)로 쉽게 접근 가능
	- 자동으로 변경 사항을 추적하며, 실행 취소 등도 지원
	```
	struct TrainingListView: View { 
		@Environment(\.modelContext) private var modelContext 
		... 
		Button("삭제", role: .destructive) { 
			withAnimation { modelContext.delete(record) } 
		}
	```

- query
	- 저장된 데이터 (Model)을 (필터링/정렬/조건 등을 활용해서) **가져오는데(read) 사용하는 속성래퍼
	- 조건이 간단할 때 사용한다.
	- 데이터 저장소와 뷰가 자동으로 동기화
	```
	struct TaskListView: View {
    @Query(filter: #Predicate<Task> { !$0.isDone }) var tasks: [Task]

    var body: some View {
        List(tasks) { task in
            Text(task.title)
        }
    }
}
	```

## 코드 예시

```
**struct** ContentView: View {

    //crud를 수행
    @Environment(\.modelContext) **private** **var** context

    //변경된 데이터를 뷰에 반영(read)
    @Query **private** **var** items: [DataItem]

    **var** body: **some** View {
        VStack {
            Text("버튼을 눌러서 데이터를 추가해라")
            Button("아이템 추가"){
                addItem()
            }
            List {
                ForEach (items) { item **in**
                    HStack {
                        Text(item.name)
                        Spacer()
                        Button {
                            updateItem(item)
                        } label: {
                            Image(systemName: "pencil")
                        }
                    }
                }
                .onDelete{ indexes **in**
                    **for** index **in** indexes {
                        deleteItem(items[index])
                    }
                }
            }
        }
        .padding()
    }
    
    //추가
    **func** addItem() {
        **let** item = DataItem(name: "아이템")
        context.insert(item)
    }
    
	//삭제
    **func** deleteItem(_ item: DataItem) {
        context.delete(item)
    }
    
	//수정
    **func** updateItem(_ item: DataItem) {
        //edit the item data
        item.name = "업데이트 아이템"
        //save the context
        **try**? context.save()
    }
}
```


```
@Model
**class** DataItem: Identifiable {
    **var** id: String
    **var** name: String
    **init**(name: String) {
        **self**.id = UUID().uuidString
        **self**.name = name
    }
}
```

```
**@main**
**struct** swiftdataexampleApp: App {
    **var** body: **some** Scene {
        WindowGroup {
            ContentView()
        }
        .modelContainer(for: DataItem.**self**)
    }
}
```
## Keywords

- coredata
- crud
- foundation

## References

- https://mini-min-dev.tistory.com/329
- https://www.youtube.com/watch?v=Y7FLskzCTGE&ab_channel=AppleDeveloper
- https://www.youtube.com/watch?v=yxtCRHloVEw&t=525s&ab_channel=AppleDeveloper
- https://www.youtube.com/watch?v=krRkm8w22A8&ab_channel=CodeWithChris
- https://www.youtube.com/watch?v=krRkm8w22A8&ab_channel=CodeWithChris