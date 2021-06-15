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
- (코드) 매개변수 이름을 생략한 설정자
```swift
struct CoordinatePoint {
  var x: Int // 저장 프로퍼티
  var y: Int // 저장 프로퍼티
  
  // 대칭 좌표
  var oppositePoint: CoordinatePoint { // 연산 프로퍼티
    // 접근자
    get {
      // 이곳에서 return 키워드를 생략할 수 있습니다.
      return CoordinatePoint(x: -x, y: -y)
    }
    
    // 설정자
    set {
      x = -newValue.x
      y = -newValue.y
    }
  }
}
```
- 굳이 대칭점을 설정해줄 필요가 없으면 읽기 전용으로 연산 프로퍼티를 사용할 수도 있습니다.
- 연산 프로퍼티를 읽기 전용으로 구현하려면 아래처럼 get 메서드만 사용합니다.
- (코드) 읽기 전용 연산 프로퍼티
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
  }
}

var yagomPosition: CoordinatePoint = CoordinatePoint(x: 10, y: 20)

// 현재 좌표
print(yagomPosition) // 10, 20

// 대칭 좌표
print(yagomPosition.oppositePoint) // -10, -20

// 설정자를 구현하지 않았으므로 오류!!
yagomPosition.oppositePoint = CoordinatePoint(x: 15, y: 10)
```

### 프로퍼티 감시자
- 프로퍼티 감시자(Property Observers)를 사용하면 프로퍼티의 값이 변경됨에 따라 적절한 작업을 취할 수 있습니다.
- 프로퍼티 감시자는 프로퍼티의 값이 새로 할당될 때마다 호출합니다.
- 이때 변경되는 값이 현재의 값과 같더라도 호출합니다.
- 프로퍼티 감시자는 지연 저장 프로퍼티에 사용할 수 없으며 오로지 일반 저장 프로퍼티에만 적용할 수 있습니다.
- 또한 프로퍼티 재정의(override)해 상속받은 저장 프로퍼티 또는 연산 프로퍼티에도 적용할 수 있습니다.
- 물론 상속받지 않은 연산 프로퍼티에는 프로퍼티 감시자를 사용할 필요가 없으며 할 수도 없습니다.
- 연산 프로퍼티의 접근자와 설정자를 통해 프로퍼티 감시자를 구현할 수 있기 때문입니다.
- 연산 프로퍼티는 상속받았을 때만 프로퍼티 재정의를 통해 프로퍼티 감시자를 사용합니다.
- 프로퍼티 감시자에는 프로퍼티의 값이 변경되기 직전에 호출하는 willSet 메서드와 프로퍼티의 값이 변경된 직후에 호출하는 didSet 메서드가 있습니다.
- willSet 메서드와 didSet 메서드에는 매개변수가 하나씩 있습니다.
- willSet 메서드에 전달되는 전달인자는 프로퍼티가 변경될 값이고, didSet 메서드에 전달되는 전달인자는 프로퍼티가 변경되기 전의 값입니다.
- 그래서 매개변수의 이름을 따로 지정하지 않으면 willSet 메서드에는 newValue가, didSet 메서드에는 oldValue라는 매개변수 이름이 자동 지정됩니다.
- (코드) 프로퍼티 감시자
```swift
class Account {
  var credit: Int = 0 {
    willSet {
      print("잔액이 \(credit)원에서 \(newValue)원으로 변경될 예정입니다.")
    }
    didSet {
      print("잔액이 \(oldValue)원에서 \(credit)원으로 변경되었습니다.")
    }
  }
}

let myAccount: Account = Account()
// 잔액이 0원에서 1000원으로 변경될 예정입니다.

myAccount.credit = 1000
// 잔액이 0원에서 1000원으로 변경되었습니다.
```
- 클래스를 상속받았다면 기존의 연산 프로퍼티를 재정의하여 프로퍼티 감시자를 구현할 수도 있습니다.
- 연산 프로퍼티를 재정의해도 기존의 연산 프로퍼티 기능(접근자와 설정자, get과 set 메서드)은 동작합니다.
- (코드) 상속받은 연산 프로퍼티의 프로퍼티 감시자 구현
```swift
class Account {
  var credit: Int = 0 { // 저장 프로퍼티
    willSet {
      print("잔액이 \(credit)원에서 \(newValue)원으로 변경될 예정입니다.")
    }
    didSet {
      print("잔액이 \(oldValue)원에서 \(credit)원으로 변경되었습니다.")
    }
  }
  
  var dollarValue: Double { // 연산 프로퍼티
    get {
      return Double(credit) / 1000.0
    }
    
    set {
      credit = Int(newValue * 1000)
      print("잔액을 \(newValue)달러로 변경 중입니다.")
    }
  }
}

class ForeignAccount: Account {
  override var dollarValue: Double {
    willSet {
      print("잔액이 \(dollarValue)달러에서 \(newValue)달러로 변경될 예정입니다.")
    }
    
    didSet {
      print("잔액이 \(oldValue)달러에서 \(dollarValue)달러로 변경되었습니다.")
    } 
  }
}

let myAccount: ForeignAccount = ForeignAccount()
// 잔액이 0원에서 1000원으로 변경될 예정입니다.
myAccount.credit = 1000
// 잔액이 0원에서 1000원으로 변경되었습니다.

// 잔액이 1.0달러에서 2.0달러로 변경될 예정입니다.
// 잔액이 1000원에서 2000원으로 변경될 예정입니다.
// 잔액이 1000원에서 2000원으로 변경되었습니다.

myAccount.dollarValue = 2 // 잔액을 2.0달러로 변경 중입니다.
// 잔액이 1.0달러에서 2.0달러로 변경되었습니다.
```
- Note: 입출력 매개변수와 프로퍼티 감시자
  - 만약 프로퍼티 감시자가 있는 프로퍼티를 함수의 입출력 매개변수의 전달인자로 전달한다면 항상 willSet과 didSet 감시자를 호출합니다.
  - 함수 내부에서 값이 변경되든 변경되지 않든 간에 함수가 종료되는 시점에 값을 다시 쓰기 때문입니다.
