# 조건문과 반복문

## if문

```go
if <condition> {
}
```

```go
// x 값이 10과 같은지 확인
if x == 10 {
}

// if문에서 변수 초기화
if x := getX(); x == 5 {
}
```

```go
// if-else문
if x == 5 {
} else {
}

// if-else if-else문
if x == 5 {
} else if x > 10 {
} else {
}
```

## switch문

Go 언어의 case는 자동으로 break하므로 break 키워드가 없다.

```go
switch x {
    case 5:
        fmt.Println("5")
    case 6:
        fmt.Println("6")
    default:
        fmt.Println("default case")
}
```

```go
// switch문에서 변수 초기화
switch x := getX(); x {
    case 5:
        fmt.Println("5")
    case 6:
        fmt.Println("6")
    default:
        fmt.Println("default case") 
}
```

case문에서 자동으로 브레이크 하지 않고 다음 case 문으로 넘어가야 한다면 `fallthrough` 키워드를 사용한다.

```go
switch x {
    case x > 5:
        // 로직
        fallthrough
    case x > 10:
        // else 로직. x가 10보다 크면 첫 번째와 두 번째 케이스가 실행됨
    default:
        // 기본 case
}
```


# 반복문

```go
for i := 1; i <= 10; i++ {
    // i값 사용 로직
}
```

for문에 필요한 정보는 다음과 같다.

- 초기값 (i := 1) - 생략 가능
- 루프 반복 여부에 대한 조건식 (i <= 10)
- 다음 루프 제어 변수의 값을 나타내는 증감문 (i++)

슬라이스나 배열을 순회하는 경우 `for..range`를 사용한다.

```go
myslice := []string{"one", "two", "three", "four"}
for i, item := range myslice {
    // i(해당 회차의 인덱스)와 item(해당 회차에 참조하는 슬라이스 요소의 값) 사용
}

// 인덱스가 필요 없다면 생략 가능
for _, item := range myslice {
    // item 사용
}

// 인덱스만 필요하다면 다음과 같이 작성
for i := range myslice {
    // i 사용
}
```

```go
// for..range는 요소의 복사본을 반환하므로 원래 값을 수정할 수 없으나, 인덱스로 직접 요소를 참조하면 가능하다.
// 인덱스를 사용해 반복문 안에서 슬라이스 수정하는 예
myslice := []string{"one", "two", "three", "four"}
for i := range myslice {
    myslice[i] = "other"
}

// other other other other 출력
fmt.Println(myslice)
```

다른 프로그래밍 언어에서는 while 반복문도 사용하지만, Go 언어는 오직 for 반복문만을 사용한다.

```go
for i > 5 {
    // 코드
}
```

다른 프로그래밍 언어와 마찬가지로 Go 언어에도 `break`와 `continue` 키워드가 있다. break는 루프가 끝나지 않았어도 반복을 중단할 때 사용하고, continue는 다음 회차로 강제로 넘길 때 사용한다.
