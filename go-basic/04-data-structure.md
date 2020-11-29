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

// 맵 선언과 동시에 초기화
myMap = make(map[int]string){}

// 초기값 설정
myMap = map[int]string{1: "first", 2: "second", 3: "third"}

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


# 구조체

구조체(struct)는 여러 자료형의 집합이다.

```go
type myStruct struct {
    intField int
    stringField string
    sliceField []int
}

// 구조체의 초기화
// 구조체 리터럴(struct literal) 초기화: 값과 키를 열거하는 초기화 방식
var s = myStruct {
    intField: 3,
    stringField: "three",
    sliceField = []int{1, 2, 3},
}

// 축약하여 초기화
var s = myStruct{3, "three", []int{1, 2, 3}}

// 점 표기법(dot notation) 사용하여 초기화
var s = myStruct{}
s.intField = 3
s.stringField = "three"
s.sliceField = []int{1, 2, 3}

// 구조체 포인터 선언하기
var sPtr = &myStruct {
    intField: 3,
    stringField: "three",
    sliceField: []int{1, 2, 3},
}

// 구조체 포인터에 점 표기법을 사용하면 포인터를 역참조하지 않아도 된다.
var s = &myStruct{}
s.intField = 3
s.stringField = "three"
s.sliceField = []int{1, 2, 3}
```

이름이 소문자로 시작하는 구조체의 필드는 다른 패키지에서 접근할 수 없다. 구조체나 필드를 다른 패키지에서 접근하고 싶다면 구조체의 필드명의 첫 글자를 대문자로 선언해야 한다.


# 메서드

메서드(method)는 타입에 연결하는 함수다.

```go
// 구조체 정의
type Person struct {
    name string
    age int
}

// 메서드를 타입에 연결
// 여기서 (p Person)는 메서드의 리시버(receiver)라고 부른다.
func (p Person) GetName() string {
    return p.name
}

// 메서드 사용하기
var p = Person {
    name: "ydhwa",
    age: 25,
}
p.GetName()

// 다른 메서드 작성
func (p Person) GetAge() int {
    return p.age
}
```


# 타입 임베딩

Go 언어에서 상속(inherit)에 가장 가까운 개념은 타입 임베딩(type embedding)이다.

다음 Person 구조체의 모든 필드와 메서드를 포함하는 Student 구조체를 만들어보자.

```go
type Person struct {
    name string
    age int
}
func (p Person) GetName() string {
    return p.name
}
func (p Person) GetAge() int {
    return p.age
}
```

```go
type Student struct {
    Person
    studentId int
}
func (s Student) GetStudentID() int {
    return s.studentId
}
```

Student 구조체에는 필드명이 없는 Person 타입이 포함되어 있다. Student는 Person 타입의 모든 익스포트된 필드와 메서드를 상속받는다.
Student 타입에서 Person 타입의 메서드와 필드에 바로 접근할 수 있다는 뜻이다.

```go
s := Student{}
// 임베딩된 'Person'의 메서드 호출
s.GetAge()
s.GetName()
```

임베딩된 타입의 익스포트된 메서드와 필드는 부모나 임베딩하는 타입에 속한다.
