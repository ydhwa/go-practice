# sync 패키지

반드시 잠금이 필요하다면 sync 패키지를 사용하면 된다.

고루틴 간의 소통 방법은 채널이 적합하지만 잠금이나 뮤텍스(mutex, mutual exclusion object)를 사용해야 하는 경우가 있다. _예를 들어 Go 표준 패키지인 http 패키지에서 http 서버 객체의 리스너를 제어하는 데 잠금을 사용한다. 리스너는 여러 고루틴에서 접근할 수 있으므로 뮤텍스가 적절하다._

컴퓨터 프로그래밍에서 뮤텍스는 자원(ex) 공유 메모리)에 여러 스레드의 접근을 제어하는 객체를 일컫는다. 뮤텍스는 한 번에 한 개의 스레드만 데이터에 접근을 허용하기 때문에 붙여진 이름이다.

소프트웨어에서 뮤텍스의 워크플로우는 일반적으로 다음과 같다.

1. 특정 스레드가 뮤텍스를 소유한다.
2. 다른 스레드는 뮤텍스를 고유할 수 없다.
3. 뮤텍스를 소유한 스레드는 마음대로 자원에 접근할 수 있다.
4. 작업이 끝나면 뮤텍스를 해제하고 다른 스레드는 뮤텍스를 소유하고자 서로 경쟁한다.

고루틴은 완전한 스레드가 아니다. 뮤텍스를 사용해 여러 고루틴의 동일한 자원에 접근을 제어할 수 있다.


## 간단한 뮤텍스

Go 언어에서 잠금은 sync 패키지에 있는 mutex 구조체의 포인터이다. 뮤텍스는 다음과 같이 선언한다.

```go
var myMutex = &sync.Mutex{}

// myMap이라는 Map[int]int 형 맵에 대한 고루틴의 동시 접근을 제어하는 코드
myMutex.Lock()
myMap[1] = 100
myMutex.Unlock()

// MyMutex를 소유한 고루틴에게만 myMap에 접근을 허용하면 교착 상태를 막을 수 있다.
```


## 읽기-쓰기 뮤텍스(read-write mutex)

Go 언어는 읽기-쓰기 잠금/뮤텍스(read-write mutex)를 지원한다. 읽기-쓰기 잠금은 읽기와 쓰기를 구분하는 잠금이다. 고루틴의 특정 자원에 대한 동시적 읽기를 허용하지만 데이터를 수정하는 동안 모든 읽기/쓰기 작업은 쓰기 잠금이 해제될 때까지 대기한다.

```go
// 읽기-쓰기 잠금은 sync.RWMutex 구조체를 가리키는 포인터다.
var myRWMutex = &sync.RWMutex{}

// 읽기는 RLock()과 RUnlock() 메서드를 사용한다.
myRWMutex.RLock()
fmt.Println(myMap[1])
myRWMutex.RUnlock()

// 쓰기는 Lock()과 Unlock() 메서드를 사용한다.
myRWMutex.Lock()
myMap[2] = 200
myRWMutex.Unlock()
```

Go 언어의 기본 패키지에서 소스코드를 살펴보면 `*sync.RWMutex`가 많이 사용된다.


## 대기 그룹(wait group)

대기 그룹은 현재 실행 중인 고루틴을 기다렸다가 나머지 코드를 실행하는 기능이다.

```go
package main

import (
    "fmt"
    "time"
)

func runLoopSend(n int, ch chan int) {
    for i := 0; i < n; i++ {
        ch <- i
    }
    close(ch)
}

func runLoopReceive(ch chan int) {
    for {
        i, ok := <- ch
        if !ok {
            break
        }
        fmt.Println("Received value: ", i)
    }
}

func main() {
    myChannel := make(chan int)
    go runLoopSend(10, myChannel)
    go runLoopReceive(myChannel)
    time.Sleep(2 * time.Second)
}
```

위 코드에서 메인 고루틴은 2초 동안 다른 고루틴이 끝날 때까지 기다린다. 하지만 완료까지 2초 이상 소요된다면 어떻게 해야 할까? 위와 같은 간단한 슬립(sleep) 로직은 예상대로 동작하지 않을 수 있다. 다음과 같이 수정해보자.

```go
package main

import (
    "fmt"
    "sync"
)

// 전역 대기 그룹 선언
var wg = &sync.WaitGroup{}

func main() {
    myChannel := make(chan int)
    // 대기 그룹의 내부 카운터를 2만큼 증가
    wg.Add(2)
    go runLoopSend(10, myChannel)
    go runLoopReceive(myChannel)
    // wait group의 내부 카운터가 0이 될때까지 대기
    wg.Wait()
}

func runLoopSend(n int, ch chan int) {
    // 함수 실행이 끝나면 대기 그룹 내부 카운터 1만큼 감소
    defer wg.Done()
    for i := 0; i < n; i++ {
        ch <- i
    }
    close(ch)
}

func runLoopReceive(ch chan int) {
    // 함수 실행이 끝나면 대기 그룹 내부 카운터 1만큼 감소
    defer wg.Done()
    for {
        i, ok := <- ch
        if !ok {
            break
        }
        fmt.Println("Received value: ", i)
    }
}
```

WaitGroup 구조체에는 내부 카운터가 있고 이 값이 0이 될 때까지 고루틴을 대기시킨다. wg는 위 프로그램에서 전역적으로 사용할 수 있는 WaitGroup을 가리키는 포인터이다. 고루틴을 실행하기 전에 wg.Add(2) 메서드를 호출해서 대기 그룹의 내부 카운터를 2만큼 증가시킨다.

각 고루틴 함수에는 `defer wg.Done()`이라는 코드가 있는데, defer와 wg.Done() 메서드 조합은 해당 고루틴의 실행이 끝나면 wg.Done() 메서드를 호출한다. 이 메서드는 대기 그룹의 내부 카운터를 1만큼 감소시킨다.

마지막으로 `wg.Wait()` 메서드는 내부 카운터가 0이 될 때까지 부모 고루틴을 대기시킨다. 따라서 모든 고루틴이 종료될 때까지 메인 코루틴은 대기한다.