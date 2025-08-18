>[!question]
>GQ1. import Foundation를 하여 스위프트데이터를 구현했다. foundation은 또 무슨 일을 할까?

## Description
>Access essential **data types**, **collections**, and **operating-system services** to define the base layer of functionality for your app.

- data types : Date, Data, URL, UUID, measurement
- collections : - NSArray/NSMutableArray, NSDictionary/NSMutableDictionary, NSSet/NSMutableSet, NSOrderedSet, NSCountedSet, NSCache (objective-C에서 호환성이 필요할 때만 사용)
- operating-system services : 
	- swift는 플랫폼 독립적인 언어
		- 운영체제와 관련 없는 기능 들은 swift standard Library에서 제공하고, 관련있는 기능은 foundation에서 제공한다.
	- 데이터 저장 및 지속성, 텍스트 처리, 날짜 및 시간 계산, 정렬 및 필터링, 네트워킹 등 앱과 프레임워크에 필요한 기본 기능을 제공합니다. Foundation에서 정의한 클래스, 프로토콜 및 데이터 유형은 macOS, iOS, watchOS 및 tvOS SDK 전체에서 사용됩니다.
## 주요 기능
- **문자열·숫자 포맷팅**: DateFormatter, NumberFormatter, ByteCountFormatter, RelativeDateTimeFormatter
    
- **날짜/시간 처리**: Calendar, Date, DateComponents로 날짜 연산과 포맷
    
- **국제화(I18N)**: Locale, TimeZone로 지역/시간대 대응
    
- **파일·디렉터리 관리**: FileManager, 샌드박스 경로 접근, 파일 읽기/쓰기
    
- **네트워킹**: URLSession으로 HTTP 통신(다운로드/업로드, async/await 지원)
    
- **동시성·작업 관리**: OperationQueue, 타이머, 런루프, 락
    
- **데이터 직렬화**: Codable(Swift) + JSONEncoder/Decoder, PropertyListSerialization
    
- **시스템 정보/설정**: ProcessInfo, UserDefaults


## 코드 예시
- UUID, Date, Calendar, DateComponents
```swift
**import** Foundation
**import** SwiftData

@Model
**final** **class** Prediction: Identifiable {
    **var** id: UUID = UUID() //foundation
    **var** year: Int
    **var** month: Int
    **var** day: Int
    **var** timeline: Int
    **var** passengers: Int

    **var** asDate: Date {
        **let** components = DateComponents( //foundation
            year: year,
            month: month,
            day: day,
            hour: timeline
        )
        **return** Calendar.current.date(from: components)! //foundation
    }

    **init**(year: Int, month: Int, day: Int, timeline: Int, passengers: Int) {
        **self**.year = year
        **self**.month = month
        **self**.day = day
        **self**.timeline = timeline
        **self**.passengers = passengers
    }

}
```

- 단위/측정값 변환 measurement
```swift
import Foundation

let length = Measurement(value: 5, unit: UnitLength.kilometers)
let inMeters = length.converted(to: .meters)
print(inMeters) // 5000.0 m
```

- 네트워킹, 직렬화
```swift
import Foundation

struct Todo: Decodable {
    let id: Int
    let title: String
    let completed: Bool
}

func fetchTodo() async throws -> Todo {
    let url = URL(string: "https://jsonplaceholder.typicode.com/todos/1")! //foundation(문자열을 네트워크 리소스 경로로 변환)
    let (data, _) = try await URLSession.shared.data(from: url) //네트워킹 api/ 비동기 http 요청 메서드
    return try JSONDecoder().decode(Todo.self, from: data) //JSON -> Swift 타입으로 디코드(역직렬화)
}

Task {
    do {
        let todo = try await fetchTodo()
        print(todo.title)
    } catch {
        print("네트워크 오류:", error)
    }
}
```
: 원격 서버에서 json을 받아서 swift 구조체로 디코드 하고 비동기 실행, 제목 출력.

## Keywords
- [[Swift Standard Library]]

## References
- https://developer.apple.com/documentation/foundation
- 