# 고루틴

고루틴(Goroutine)은 경량 스레드이다. 고루틴으로 정의된 코드는 런타임에 다른 고루틴과 동시에 실행된다.

모든 Go 함수는 고루틴에 속한다. 예를 들어 프로그램의 진입점인 main 함수는 **메인 고루틴** 에 속한다.

```go
// 고루틴 구현. 동시에 수행할 함수 앞에 go 키워드를 붙여주면 된다.
go somefunction()
```

모든 고루틴은 다른 고루틴과 독립적으로 실행된다.

```go
package main

import (
    "fmt"
    "time"
)

func runSomeLoop(n int) {
    for i := 0; i < n; i++ {
        fmt.Println("Printing: ", i)
    }
}

func main() {
    go runSomeLoop(10)
    // main 고루틴 2초 동안 대기
    time.Sleep(2 * time.Second)
    fmt.Println("Hello, playground")
}
```
