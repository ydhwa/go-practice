# select 구문

select 구문은 여러 채널을 동시에 제어할 수 있는 중요한 기능이다. select를 사용해서 여러 채널에 데이터를 보내거나 받을 수 있고 먼저 활성화된 채널의 코드를 실행할 수 있다.

```go
// 이 select 구문은 2개의 채널을 사용.
// 1. 데이터를 받는 ch 채널
// 2. time.After() 함수. 이 함수는 select 구문에서 많이 사용한다. 설정한 일정 시간이 지나면 데이터가 도착하며 이 시간 동안 채널을 대기시킨다. 채널에서 데이터를 받거나 보내는 작업에 타임아웃(timeout)을 설정할 때 이 함수를 사용한다.
select {
    case i := <- ch:
        fmt.Println("Received value: ", i)
    case <- time.After(1 * time.Second):
        fmt.Println("timed out")
}
```

```go
// 타임아웃 채널과 수신, 송신 채널을 모두 포함하는 select 구문
// 이 예제에서는 3개의 채널 ch1, ch2, time.After()가 사용된다.
// 그 중 가장 먼저 끝나는 채널의 case가 실행된다.
select {
    case i := <- ch1:
        fmt.Println("Received value on channel ch1: ", i)
    case ch2 <- 10:
        fmt.Println("Sent value of 10 to channel ch2")
    case <- time.After(1 * time.Second):
        fmt.Println("time out")
}
```

```go
// select문은 default case도 지원한다. 준비된 채널이 없을 경우 default case가 즉시 실행된다.
// time 패키지가 ch1과 ch2를 대기시키고 있다면 default case가 실행된다.
// 여러 채널이 동시에 끝나면 임의의 채널 case를 실행한다.
select {
    case i := <- ch1:
        fmt.Println("Received value on channel ch1: ", i)
    case ch2 <- 10:
        fmt.Println("Sent value of 10 to channel ch2")
    default:
        fmt.Println("No channel is ready")
}
```
