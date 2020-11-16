# 함수

```go
func main() {
    // 함수 구현
}
```

main 함수는 Go 프로그램에서 가장 먼저 실행되는 함수이다. 프로그램 진입 패키지인 main 패키지에 속한다.

```go
// 매개변수가 있는 함수
func add(a int, b int) {
    // a + b
}

// 자료형이 같은 매개변수는 다음과 같이 축약하여 표현할 수 있다.
func add(a, b int) {
    // a + b
}

// 반환하는 값이 있는 경우
func add(a, b int) int {
    return a + b
}

// 다중 반환
func addSubtract(a, b int) (int, int) {
    return a + b, a - b
}
```

네임드 리턴(named return): 반환하는 값을 미리 함수의 헤더에 정의하는 방식

```go
func addSubtract(a, b, int)(add, sub int) {
    add = a + b
    sub = a - b
    return
}
```

Go 언어에서 함수는 일급(first-class) 함수이다. 다음과 같이 함수를 변수에 바인드하고 값처럼 사용할 수 있다.

```go
var adder = func(a, b int) int {
    return a + b
}
var subtractor = func(a, b, int) int {
    return a - b
}
var addResult = adder(3, 2)
var subResult = subtractor(3, 2)
```

함수를 다른 함수의 인자로 사용할 수 있다.

```go
func execute(op func(int, int) int, a, b int) int {
    return op(a, b)
}

var adder = func(a, b int) int {
    return a + b
}
execute(adder, 3, 2)
```

가변 함수(variadic function): 인자의 개수를 미리 지정하지 않은 함수

```go
func infiniteAdder(inputs ...int) (sum int) {
    for _, v := range inputs {
        sum += v
    }
    return
}

infiniteAdder(1, 2, 3, 4)   // 1 + 2 + 3 + 4
```

## 다른 패키지 함수 호출

Go 언어에는 다른 정적 언어에 있는 접근 제어자가 없다. 대신 public 함수의 이름은 대문자로 시작해야 한다. 이런 함수는 익스포트된(exported) 함수라고 부르며, 소문자로 시작하는 함수는 패키지 내에서만 접근할 수 있는 보이지 않는(unexpected) 상태라고 한다.

```go
package adder

func Add(a, b int) int {
    return a + b
}
```

다른 패키지에서 Add 함수 호출

```go
package main

// adder 패키지 임포트
import "adder"

func main() {
    // adder.Add(...) 함수 호출
    // 익스포트 된 함수는 <패키지명>.<함수명>으로 호출한다.
    result := adder.Add(1, 2)
    // result 변수 사용
}
```

adder 패키지에서 함수명을 Add에서 add로 변경하면 위 예제는 동작하지 않는다.

```go
package main

import (
    "fmt"   // 가장 많이 사용되는 Go 기본 패키지로, 사용자의 표준 출력 장치에 정보를 표시하는 패키지이다. 문자열 포맷팅과 표준 입력을 읽는 기능도 제공된다.
)

func main() {
    fmt.Println("Hello world")
}
```

```go
package main

import (
    "fmt"
    "math/rand" // 난수를 생성하는 패키지이다. rand 패키지 폴더는 math 패키지 폴더 내부에 있으므로 패키지 이름이 단순히 rand가 될 수 없다.
)

func main() {
    fmt.Println("랜덤 정수 생성", rand.Intn(10))    // 0에서 9 사이의 난수 생성
}
```


# 클로저

함수는 클로저(closure)가 될 수 있다. 클로저는 함수 바깥에 정의된 변수를 참조하는 함수 값(function value)이다. 해당 변수에 대한 접근과 수정이 가능하다.

```go
// adder 함수의 클로저는 sum 변수에 접근할 수 있다.
// 즉, sum의 최신 값을 기억하고 변경한다.
func adder() func(int) int {
    sum := 0
    return func(x int) int {
        sum += x
        return sum
    }
}
```

```go
func adder() func(int) int {
    sum := 0
    return func(x int) int {
        sum += x
        return sum
    }
}

func main() {
    // adder()는 클로저를 반환한다.
    sumClosure := adder()   // sum 값은 0
    sumClosure(1)           // sum 값은 0 + 1 = 1
    sumClosure(2)           // sum 값은 1 + 2 = 3
}
```