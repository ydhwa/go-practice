# 1~7장 질문거리 정리

## GoPath란 무엇이고 어떻게 사용하는가?

- [워크스페이스 설정](./01-workspace.md)
- <http://amazingguni.github.io/blog/2016/05/go-chapter-2-1-%EC%84%A4%EC%B9%98_%EB%B0%8F_%EC%8B%9C%EC%9E%91>

GoPath는 현재 작업중인 Go 워크스페이스 디렉터리의 경로이며, 사용자의 프로젝트 뿐만이 아니라 오픈소스 코드를 내려받을 경우 저장되는 공간이기도 하다.

UNIX는 `$Home/go`, Windows는 `%USERPROFILE%\go`가 기본 GoPath로 지정되며, 사용자가 원하는 대로 지정할 수 있다.

## Go 언어에서 while 반복문은 어떻게 사용하는가?

- [반복문](./06-condition-iteration.md#반복문)

Go 언어는 오직 for 반복문만을 사용하므로, while 반복문과 같은 동작을 하는 반복문 역시 for 반복문으로 구현한다.

```go
for i > 5 {
    // 코드
}
```

## 네임드 리턴이란 무엇인가?

- [함수](./03-function-closure.md#함수)

네임드 리턴이란 반환하는 값을 미리 함수의 헤더에 정의하는 방식이다.

```go
func addSubtract(a, b int) (add, sub int) {
    add = a + b
    sub = a - b
    return
}
```

## 함수와 메서드의 차이는 무엇인가?

- [메서드](./04-data-structure.md#메서드)

함수와 메서드는 기본적으로 비슷한 기능을 하나, 메서드는 타입에 연결하는 함수를 뜻한다. 즉, 메서드는 타입에 종속적인 함수이다.

## 타입 단언이란?

- [인터페이스](./05-interface.md)

인터페이스 타입의 값을 특정 타입으로 변환해야 하는 경우 사용하는 방법이다. 변환하고자 하는 타입과 일치하지 않다면 빈 구조체와 false를 반환하고, 변환하고자 하는 타입과 일치한다면 해당 타입의 값과 true를 반환한다.

```go
person, ok := myInterfaceValue.(Person)
```

## defer 키워드의 역할은 무엇인가?

- [defer](./07-panic-recover-defer.md#defer)

`defer`는 해당 코드의 부모 함수가 결과를 반환한 뒤에 실행되도록 해주는 키워드이다.

defer는 스택을 기반으로 후입선출 방식을 따르므로, defer를 여러 번 사용할 경우 나중에 선언한 defer일수록 먼저 실행된다.

## panic 키워드의 역할은 무엇인가?

- [panic](./07-panic-recover-defer.md#panic)

`panic` 내장 함수를 호출하면 프로그램을 중단하고 패닉 메시지를 반환한다. panic을 제때 처리하지 않으면 프로그램이 종료되므로 신중하게 사용해야 한다.

panic 함수의 인자는 반환할 패닉 메시지이다.

## panic을 제어하는 방법은 무엇인가?

- [defer와 recover를 사용하여 프로그램을 중단시키지 않고 panic 사용하기](./07-panic-recover-defer.md#defer와-recover를-사용하여-프로그램을-중단시키지-않고-panic-사용하기)

defer와 recover 내장함수를 사용하여 프로그램 중단 없이 panic을 예외처리할 수 있다.

```go
// 실습 코드
package main

import "fmt"

func main() {
    panicTest(true)
    fmt.Println("Hello world")
}

func checkPanic() {
    // panic이 발생하지 않으면 recover()은 nil을 반환하고, 발생한다면 메시지를 반환한다.
    // defer 없이 recover()만 사용한다면 제대로 작동하지 않는다.
    if r := recover(); r != nil {
        fmt.Println("A panic was captured, message: ", r)
    }
}

func panicTest(p bool) {
    // defer와 recover 조합
    defer checkPanic()
    if p {
        panic("panic requested")
    }
}
```

## 배열과 슬라이스의 차이점은 무엇인가?

- [배열](./04-data-structure.md#배열)
- [슬라이스](./04-data-structure.md#슬라이스)

배열이란 미리 크기가 정해진 동일한 자료형의 모음이다. 슬라이스는 동적 배열로, 배열과 비슷하나 크기를 지정하지 않는 자료구조이다. 정확히는 배열의 일부를 가리키는 포인터이다.

```go
// 배열 선언
var myArray [3]int

// 슬라이스 선언
var mySlice []int
```

## 인터페이스란 무엇인가?

- [인터페이스](./05-interface.md)

인터페이스란 메서드의 집합이다. Go 인터페이스는 구조체 등의 타입으로 구현할 수 있는데, 어떤 타입이 인터페이스를 구현하면 인터페이스는 해당 타입의 데이터를 내재할 수 있다.

타입이 어떤 인터페이스를 구현하거나 상속받으려면 단순히 해당 인터페이스의 메서드만 구현하면 된다.

인터페이스는 API와 확장성 높은 소프트웨어를 개발할 때 많이 사용된다. 인터페이스를 사용하면 유연한 소프트웨어를 작성할 수 있다.

## 구조체란 무엇인가?

- [구조체](./04-data-structure.md#구조체)

구조체는 여러 자료형의 집합이다.

```go
type myStruct struct {
    intField int
    stringField string
    sliceField []int
}
```

이름이 소문자로 시작하는 구조체의 필드는 다른 필드에서 접근할 수 없다. 구조체나 필드를 다른 패키지에서 접근하고 싶다면 구조체 필드명의 첫 글자를 대문자로 선언해야 한다.

## 맵은 어떻게 사용하는가?

- [맵](./04-data-structure.md#맵)

맵은 키로 해당 값을 찾을 수 있는 키-값 쌍의 집합이다.

```go
// 선언(key: int / value: string)
var myMap map[int]string

// 초기화
myMap = make(map[int]string)

// 맵 선언과 동시에 초기화
myMap = make(map[int]string) {}

// 초기값 설정
myMap = map[int]string {1: "first", 2: "second", 3: "third"}

// 정의된 맵에 키-값 삽입
myMap[4] = "fourth"

// 특정 키의 값 찾기
var x = myMap[4]

// 함수 안에서 특정 키가 맵 안에 존재하는지 확인
// myMap 안에 5번 키가 없다면 ok = false
// 존재한다면 ok = true / x = 해당 값
x, ok := myMap[5]

// 특정 키를 맵에서 삭제
delete(myMap, 4)
```
