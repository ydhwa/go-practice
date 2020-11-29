# 인터페이스

인터페이스(interface)는 메서드의 집합이다.

```go
type MyInterface interface {
    GetName() string
    GetAge() int
}
```

Go 인터페이스는 구조체 등의 타입으로 구현할 수 있다.
어떤 타입이 인터페이스를 구현하면 인터페이스는 해당 타입의 데이터를 내재할 수 있다.

타입이 어떤 인터페이스를 구현하거나 상속받으려면 단순히 해당 인터페이스의 메서드만 구현하면 된다.

즉, 위에서 선언한 Person 구조체는 MyInterface에 정의된 GetName()과 GetAge() 메서드를 구현하고 있으므로 MyInterface 인터페이스를 상속받는다.

Person이 MyInterface를 구현한다는 것은 다음과 같이 사용할 수 있다는 의미이다.

```go
var myInterfaceValue MyInterface
var p = Person {}
p.name = "ydhwa"
p.age = 25

// 인터페이스 사용
myInterfaceValue = p
myInterfaceValue.GetName()  // returns: ydhwa
myInterfaceValue.GetAge()   // returns: 25

// 다른 사용법
func main() {
    p := Person{"ydhwa", 25}
    printNameAndAge(p)
}

func PrintNameAndAge(i MyInterface) {
    fmt.Println(i.GetName(), i.GetAge())
}
```

인터페이스는 API와 확장성 높은 소프트웨어를 개발할 때 많이 사용된다. 인터페이스를 사용하면 유연한 소프트웨어를 작성할 수 있다.

```go
type PersonWithTitle {
    name string
    title string
    age int
}

func (p PersonWithTitle) GetName() string {
    // <칭호> <빈칸> <이름> 반환
    return p.title + " " + p.name
}

func (p PersonWithTitle) GetAge() int {
    return p.age
}

// PersonWithTitle 타입은 MyInterface를 구현하므로 다음과 같이 사용될 수 있다.
func main() {
    pt := PersonWithTitle{"ydhwa", "Dr.", 25}
    printNameAndAge(pt)
}

func printNameAndAge(i MyInterface) {
    fmt.Println(i.GetName(), i.GetAge())
}
```

인터페이스 타입의 값을 특정 타입으로 변환해야 하는 경우가 있다. 이런 경우를 위해 Go 언어는 타입 단언(type assertion)을 지원한다.

```go
// myInterfaceValue 값이 Person 타입이 아니라면 빈 구조체와 false 반환
// Person 타입이라면 해당 타입 값과 true 반환
person, ok := myInterfaceValue.(Person)
```
