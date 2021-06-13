# SwiftPropertyAndMethod


### 연산 프로퍼티
- 연산 프로퍼티는 실제 값을 저장하는 프로퍼티가 아니라, 특정 상태에 따른 값을 연산하는 프로퍼티입니다.
- 인스턴스 내/외부의 값을 연산하여 적절한 값을 돌려주는 접근자(getter)의 역할이나 은닉화된 내부의 프로퍼티 값을 간접적으로 설정하는 설정자(setter)의 역할을 할 수도 있습니다.
- 클래스, 구조체, 열거형에 연산 프로퍼티를 정의할 수 있습니다.
- '굳이 메서드를 두고 왜 연산 프로퍼티를 쓸까?'라는 의문이 들 수도 있으니 그 이유를 생각해 보겠습니다.
- 인스턴스 외부에서 메서드를 통해 인스턴스 내부 값에 접근하려면 메서드를 두 개(접근자, 설정자) 구현해야 합니다.
- 또한 이를 감수하고 메서드로 구현한다 해도 두 메서드가 분산 구현되어 코드의 가독성이 나빠질 위험이 있습니다.
- 타인의 코드를 보는 프로그래머의 입장에서는 프로퍼티가 메서드 형식보다 훨씬 더 간편하고 직관적이기도 합니다.
- 다만 연산 프로퍼티는 접근자인 get 메서드만 구현해둔 것처럼 읽기 전용 상태로 구현하기 쉽지만, 쓰기 전용 상태로 구현할 수 없다는 단점이 있습니다.
- 메서드로는 설정자 메서드만 구현하여 쓰기 전용 상태로 구현할 수 있지만 연산 프로퍼티는 그것이 불가능합니다.
- (코드) 메서드로 구현된 접근자와 설정자
```swift
struct CoordinatePoint {
  var x: Int // 저장 프로퍼티
  var y: Int // 저장 프로퍼티
  
  // 대칭점을 구하는 메서드 - 접근자
  // Self는 타입 자기 자신을 뜻합니다.
  // Self 대신 CoordinatePoint를 사용해도 됩니다.
  func oppositePoint() -> Self {
    return CoordinatePoint(x: -x, y: -y)
  }
  
  // 대칭점을 설정하는 메서드 - 설정자
  mutating func setOppositePoint(_ ooposite: CoordinatePoint) {
    x = -opposite.x
    y = -opposite.y
  }
}

var yagomPosition: CoordinatePoint = CoordinatePoint(X: 10, y: 20)

// 현재 좌표
print(yagomPosition) // 10, 20

// 대칭 좌표
print(yagomPosition.oppositePoint()) // -10, -20

// 대칭 좌표를 (15, 10)으로 설정하면
yagomPosition.setOppositePoint(CoordinatePoint(x: 15, y: 10))

// 현재 좌표는 -15, -10으로 설정됩니다.
print(yagomPosition) // -15, -10
```
- oppositePoint() 메서드로 대칭점을 구할 수 있으며 setOppositePoint(_:) 메서드로 대칭점을 설정해줘야 합니다.
- 위와 같은 코드에서는 접근자와 설정자 이름의 일관성을 유지하기 힘들며, 해당 포인트에 접근할 때와 설정할 때 사용되는 코드를 한 번에 읽기도 쉽지 않습니다.
- 하지만 연산 프로퍼티를 사용하면 이 두 메서드를 좀 더 간결하고 확실하게 표현할 수 있습니다.
- (코드) 연산 프로퍼티의 정의와 사용
```swift
struct CoordinatePoint {
  var x: Int // 저장 프로퍼티
  var y: Int // 저장 프로퍼티
  
  // 대칭 좌표
  var oppositePoint: CoordinatePoint { // 연산 프로퍼티
    // 접근자
    get {
      return CoordinatePoint(x: -x, y: -y)
    }
    
    // 설정자
    set(opposite) {
      x = -opposite.x
      y = -opposite.y
    }
  
  }
}

var yagomPosition: CoordinatePoint = CoordinatePoint(x: 10, y: 20)

// 현재 좌표
print(yagomPosition) // 10, 20

// 대칭 좌표
print(yagomPosition.oppositePoint) // -10, -20

// 대칭 좌표를 (15, 10)으로 설정하면
yagomPosition.oppositePoint = CoordinatePoint(x: 15, y: 10)

// 현재 좌표는 -15, -10으로 설정됩니다.
print(yagomPosition) // -15, -10
```
- 이런 식으로 연산 프로퍼티를 사용하면 하나의 프로퍼티에 접근자와 설정자가 모두 모여있고,
- 해당 프로퍼티가 어떤 역할을 하는지 좀 더 명확하게 표현 가능합니다.
- 인스턴스를 사용하는 입장에서도 마치 저장 프로퍼티인 것처럼 편하게 사용할 수 있습니다.
- 설정자의 매개변수로 원하는 이름을 소괄호 안에 명시해주면 set 메서드 내부에서 전달받은 전달인자를 사용할 수 있습니다.
- 관용적인 표현으로 newValue로 매개변수 이름을 대신할 수 있습니다.
- 그럴 경우에는 매개변수를 따로 표기하지 말아야 합니다.
- 또, 접근자 내부의 코드가 단 한 줄이고, 그 결과값의 타입이 프로퍼티의 타입과 같다면 return 키워드를 생략해도 그 결과값이 접근자의 반환값이 됩니다.
