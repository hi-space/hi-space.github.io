---
title: "[Go] 테스트 코드"
tags: go
---

<!--more-->

# testing 패키지

golang의 표준 test 패키지이다.

- `go test` 명령을 통해 테스트 코드를 실행 할 수 있다. 
- 폴더 내에 있는 `*._test.go` 파일들을 테스트 코드로 인식하고 일괄적으로 테스트 한다.
- 테스트 함수는 `TestXxx` 와 같은 이름을 갖고, 테스트 method임을 알린다. 
- 입력으로는 `testing.T` 포인터 하나를 받고, 출력은 없다.
- 정리하면 test 코드를 만들기 위한 조건은 다음과 같다.
  - 파일명: `**_test.go`
  - 패키지명: `**._test`
  - 함수명: `func TestXxx(t *testing.T)`

    ```go
    package func_test

    func TestFunc(t * testing.T) {
        t.Fatal("Fatal")
        t.Error("Error")
        t.Fail("Fail")
        t.Log("Log")
    }
    ```

## testify 패키지

### Install

```sh
go get github.com/stretchr/testify/assert
```

testify는 크게 3가지의 기능을 제공한다.
- Easy Assertion
- Mock
- Test suite interface and function

### Assert

assert 방식을 통해 테스트 케이스들의 결과를  성공/실패로 판단한다. 샐패한 경우에만 에러메시지를 확인하면 되기 때문에 편리하다.

```go
assert.Equal(t, expected, actual, "equal")
assert.NotEqual(t, expected, actual, "not equal")
assert.NoError(t, err)
```

### Mock

### Suite

# References

- [https://umi0410.github.io/blog/golang/how-to-backend-in-go-testcode/](https://umi0410.github.io/blog/golang/how-to-backend-in-go-testcode/)
- [https://etloveguitar.tistory.com/64](https://etloveguitar.tistory.com/64)
