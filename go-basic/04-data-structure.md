# 배열

Go 배열(array)은 미리 크기가 정해진 동일한 자료형의 모음이다.

```go
// 배열 선언
var myarray [3]int

// 배열 초기화
myarray[0] = 1
myarray[1] = 2
myarray[2] = 3

// 선언과 동시에 초기화
var myarray = [3]int{1, 2, 3}

// 함수 안에서 선언과 동시에 초기화
myarray := [3]int{1, 2, 3}
```

```go
myarray := [5]int{1, 2, 3, 4, 5}

// 배열의 크기/길이 반환
n := len(myarray)   // 5

// 배열의 일부 선택
// array[<index1>:<index2>+1]
myarray[2:4]        // {3, 4}
myarray[:4]         // {1, 2, 3, 4}
myarray[2:]         // {3, 4, 5}

// 아래 두 배열은 모두 같은 주소를 가리킨다.
mySubArray := myarray[2:4]
```


# 슬라이스

슬라이스(slice)는 동적 배열로, 배열과 비슷하지만 크기를 지정하지 않는 자료구조이다.

정확히는 배열의 일부를 가리키는 포인터이다.

```go
var mySlice []int

// 초기화
mySlice = []int{1, 2, 3, 4, 5}

// 선언과 동시에 초기화
var mySlice = []int{1, 2, 3, 4, 5}
// 빈 슬라이스로 초기화
var mySlice = []int{}
```

초기값이 없는 비어있는 슬라이스의 길이를 미리 지정하고 싶다면 `make` 내장 함수를 사용한다.

```go
// 길이가 5인 int 슬라이스를 선언하고 초기화
var mySlice = make([]int, 5)
```

### 슬라이스의 정보

- 하위 배열의 첫 번째 요소를 가리키는 포인터
- 슬라이스가 참조하는 하위 배열의 길이
- 원본 배열의 용량(capacity: 원본 배열에 남아있는 요소의 개수)은 슬라이스의 길이보다 크거나 같다.

```go
// 슬라이스 초기화. 내부적으로 mySlice는 요소가 5개인 배열을 가리킨다.
var mySlice = []int{1, 2, 3, 4, 5}
// 하위 슬라이스 생성. 슬라이스를 다시 슬라이스해도 원본 배열을 복사하지는 않는다.
var subSlice = mySlice[2:4]

// 슬라이스의 용량
cap(subSlice)       // 3

// 슬라이스의 길이
len(subSlice)       // 2
```

메모리 효율성 때문에 길이와 용량이 중요하다.

메모리의 과다 사용을 피하기 위해서는 배열에서 필요한 부분만 새로운 슬라이스에 복사해야 한다. Go의 가비지 컬렉터(garbage collector) 는 큰 배열이 필요없다는 사실을 인지하고 알아서 이를 삭제한다.

복사 작업은 `copy` 내장 함수를 사용한다.

```go
// 길이가 큰 슬라이스라고 가정
var myBigSlice = []int{1, 2, 3, 4, 5, 6}

// 작은 슬라이스를 생성
var mySubSlice = make([]int, 2)

// myBigSlice에서 mySubSlice로 2개 요소의 값을 복사
copy(mySubSlice[2:4], myBigSlice)
```

Go는 슬라이스에 요소를 추가하는 `append` 함수를 제공한다. 슬라이스 용량이 꽉 차면 append 함수는 모든 데이터를 담을 수 있는 크기의 내부 배열을 가진 슬라이스를 새로 만든다. append 함수는 인자의 개수가 정해지지 않은 가변 함수이다.

```go
var mySlice = []int{1, 2}
mySlice = append(mySlice, 3, 4, 5)
```

`make` 내장 함수를 이용하여 슬라이스를 생성할 수 있는데, 슬라이스의 길이와 용량을 모두 지정해줄 수 있다. 용량을 설정하지 않는 경우 길이와 용량이 같은 슬라이스가 생성된다.

```go
// 길이가 3인 슬라이스 생성
// 길이와 용량 모두 3
var mySlice = make([]int, 3)

// 길이는 3, 용량은 5인 슬라이스 생성
var mySlice = make([]int, 3, 5)
```


# 맵

맵(map)은 키로 해당 값을 찾을 수 있는 키-값(key-value) 쌍의 집합이다. 맵을 사용하면 키로 빠르게 원하는 값을 찾을 수 있으므로 프로그램의 성능을 높일 수 있다.

```go
// 키는 int, 값은 string형의 맵 선언
var myMap map[int]string

// make 함수를 사용하여 초기화
// 초기화하지 않은 맵을 참조하면 에러가 발생한다.
myMap = make(map[int]string)

```
