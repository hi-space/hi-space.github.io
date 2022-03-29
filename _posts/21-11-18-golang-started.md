---
title: "[Go] Started"
tags: go ubuntu
---

<!--more-->

# 0. Prerequired

## Install

### Go

```sh
sudo apt install golang go
```

위 커맨드로 쉽게 설치되고 환경변수도 설정되지만 최신버전은 아닐 수 있다. [공식페이지](https://golang.org/doc/install#requirements)에서 직접 다운받아오자.

```sh
wget https://golang.org/dl/go1.17.3.linux-amd64.tar.gz
tar -C /usr/local -xzf go1.17.3.linux-amd64.tar.gz
```

```sh
# ~/.bashrc
export GOROOT=/usr/local/go # 시스에 설치된 GO 패키지 위치
export GOPATH=$HOME/env/go  # 작업 디렉토리
export PATH=$GOPATH/bin:$GOROOT/bin:$PATH
export PATH=$PATH:$GOPATH/bin:/opt/protoc/bin
```

### 1. Test Codes

```sh
# vi test.go
package main
func main() {
    println("Test")
}
```

### 2. Run

`run` 명령어를 사용하면 컴파일과 동시에 실행하게 된다. 이 때 실행파일은 만들어지지 않는다.

```sh
go run test.go
```

### 3. Build

```sh
go build test.go
```

빌드를 하게 되면 실행파일이 생성된다. 실행파일을 실행시키면 컴파일이 된 결과물을 실행하게 된다.

---

# Methods vs Receiver

Go는 클래스가 없지만 Receiver라는 개념을 사용해 특정 타입에 대한 메소드를 만들 수 있다.

- 함수(function) - 처리의 묶음
- 객체(object) - 변수의 묶음
- 구조체(struct) - 객체를 선언하는 방법
- 리시버(reciever) - 객체와 함수를 연결하는 매개체
- 메소드(method) - 객체에 연결된 함수
- value receiver - 객체를 value로 가져와서 사용하는 리시버
- pointer receiver - 객체를 referene(pointer)로 가져와서 사용하는 리시버

# Constructor & Factory

```go
type Person struct {
    name string
    age  int
}

func NewPerson(name string) *Person {
    return &Person{
        name: name,
    }
}

person := NewPerson("John Doe")
```

# Channel

- 데이터를 주고 받는 통로
- goroutine 간 메시지리를 전달할 수 있는 메시지 큐
- 상대편이 준비될 때 까지 채널에서 대기함 (데이터 동기화)

```go
// create channel instance
var msg chan string = make (chan string)

// send msg
msg <- "sending message"

// receive msg
var msg string = <- msg
```

# References

- [Efective Go in Korean](https://gosudaweb.gitbooks.io/effective-go-in-korean/content/)
- [Go by Example](https://mingrammer.com/gobyexample/)
- [golang docs](https://github.com/golang-kr/golang-doc/wiki)
- [가장 빨리 만나는 Go 언어](http://pyrasis.com/go.html)
- [예제로 배우는 Go 프로그래밍](http://golang.site/go/article/1-Go-%ED%94%84%EB%A1%9C%EA%B7%B8%EB%9E%98%EB%B0%8D-%EC%96%B8%EC%96%B4-%EC%86%8C%EA%B0%9C)