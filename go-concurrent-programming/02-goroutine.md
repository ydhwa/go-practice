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

// main()과 runSomeLoop()는 동시에 실행된다.
func main() {
    go runSomeLoop(10)
    // main 고루틴 2초 동안 대기
    // 
    time.Sleep(2 * time.Second)
    fmt.Println("Hello, playground")
}
```

```
// 실행결과
Printing:  0
Printing:  1
Printing:  2
Printing:  3
Printing:  4
Printing:  5
Printing:  6
Printing:  7
Printing:  8
Printing:  9
Hello, playground
```

고루틴은 메모리와 자원 측면에서 매우 가볍다. 따라서 Go 프로그램에는 보통 수백에서 수천 개의 고루틴이 실행된다. 많은 개발자가 간단한 API를 통해 막강한 기능을 쓸 수 있는 점을 Go 언어의 장점으로 뽑는다.