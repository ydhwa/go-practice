# Go 채널

고루틴끼리 데이터를 공유할 수 있는 방법은 무엇일까?

여러 스레드를 사용하는 프로그램에서 데이터 공유는 잠금(lock)을 거는 방식을 주로 사용한다. 이 방법을 메모리 공유 방식(sharing memory approach)이라고 부른다.

Go 언어에는 다음과 같은 말이 있다.

> 공유 메모리로 소통하지 말고 소통을 통해서 메모리를 공유하라.

간단히 말하자면 잠금을 사용한 스레드 간 메모리 공유는 가급적 지양하라는 뜻이다. 대신 고루틴에서 다른 고루틴으로 데이터를 전달하는 방법을 쓰는 것이 좋다. Go 채널을 사용해 데이터를 전달할 수 있다.


## 일반 채널

Go 채널은 다음과 같이 make 키워드를 사용해서 선언한다.

```go
// int형 값을 전달하는 myChannel 채널을 선언하고 초기화
// 이 채널은 고루틴에서 다른 고루틴으로 int 값을 전달한다.
myChannel := make(chan int)

// 다음과 같이 채널을 통해 값을 전달받는다.
// myIntValue는 채널을 통해 전달받은 값
myIntValue := <- myChannel

// 다음과 같이 값을 보낸다.
myChannel <- 4
```

채널을 통해 데이터를 받거나 보내면 해당 고루틴은 송수신이 완료될 때까지 대기한다. 즉 데이터를 보냈지만 기다리는 고루틴이 없다면 해당 고루틴은 대기한다. 반대로 데이터를 받아야 하는데 보내는 고루틴이 없다면 기다려야 한다.

이 방식은 데이터를 동기화하고 최신으로 유지할 수 있으며, 다른 프로그래밍 언어에서 사용하는 잠금 방식보다 문제가 적다.

예제 코드. myChannel이라는 채널을 만들고 runLoopSend()와 runLoopReceive() 함수의 인자로 전달한다. 이 채널을 통해 runLoopSend()는 데이터를 보내고 runLoopReceive()는 데이터를 받는다.

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
    // 채널을 닫음
    // 채널이 닫히면 더 이상 데이터를 보낼 수 없고 panic이 발생한다.
    close(ch)
}

func runLoopReceive(ch chan int) {
    for {
        // 채널이 닫혔는지 확인하는 구문
        // 채널이 열려있으면 ok는 true이고, 채널이 닫혔다면 ok는 false
        // i는 채널을 통해 전달받은 값
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

```
// 실행결과
Received value:  0
Received value:  1
Received value:  2
Received value:  3
Received value:  4
Received value:  5
Received value:  6
Received value:  7
Received value:  8
Received value:  9
```

```go
// 이 코드를
for {
    i, ok := <- ch
    if !ok {
        break
    }
    fmt.Println("Received value: ", i)
}
// 이렇게 더 짧게 작성할 수 있다.
// 채널에 대한 for..range문은 채널이 닫힐 때까지 데이터를 받는다.
for i := range ch {
    fmt.Println("Received value: ", i)
}
```


## 버퍼 채널

버퍼(buffered) 채널은 여러 값을 버퍼에 저장하는 특수한 채널이다. 일반 채널과 다르게 버퍼 채널은 다음에 경우에만 대기한다.

- 버퍼가 비어 있는 채널이 데이터를 기다리는 경우
- 버퍼가 가득 차있는 채널에 데이터를 보내는 경우

버퍼 채널은 다음과 같이 선언한다.

```go
// 10개의 int형 값을 저장할 수 있는 버퍼 채널을 생성
myBufferedChannel := make(chan int, 10)

// 일반 채널과 똑같은 문법으로 버퍼 채널에 데이터를 보낸다. 각 데이터는 버퍼에 추가된다.
myBufferedChannel <- 10

// 버퍼 채널에서 데이터를 받는 방법도 동일하다. 전달받은 데이터는 버퍼에서 삭제된다.
x := <- myBufferedChannel
```