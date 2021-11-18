---
title: "[Go] gRPC Server"
tags: go backend
---

<!--more-->

# Go Web Framework

## Gin

- HTTP 웹 프레임워크
- middleware 지원: logger, authorization, gzip, db 등
- json 유효성 검사
- route 그룹화
- 오류 관리

## Echo

고성능 미니멀리즘 웹 프레임워크

- HTTP/2 지원
- middleware
- 데이터 바인딩: json, xml, etc.
- 템플릿 렌더링

## ETC

- Buffalo
- Revel
- Gorilla (not framework, just toolkit)

# 0. Prerequired

## gRPC

```sh
sudo apt install protobuf-compiler
sudo apt install golang-goprotobuf-dev

go install google.golang.org/protobuf/cmd/protoc-gen-go@v1.26
go install google.golang.org/grpc/cmd/protoc-gen-go-grpc@v1.1
```

## Generate gRPC code

serveice를 만들기 위해 `.proto` 파일을 컴파일 한다

```sh
protoc -I . helloworld/helloworld.proto --go_out=plugins=grpc:.
# or
protoc --go_out=. --go_opt=paths=source_relative \
    --go-grpc_out=. --go-grpc_opt=paths=source_relative \
    helloworld/helloworld.proto
```

# 1. 모듈 생성

```sh
go mod init {module-name}
```

명령어를 실행하면 `go.mod` 파일이 생성된다. 이 파일 내에서 다른 모듈을을 import 할 수 있다.

```sh
go get -u google.golang.org/grpc
go get -u github.com/golang/protobuf/protoc-gen-go
go get -u github.com/gin-gonic/gin
go get github.com/gorilla/mux
go get github.com/gorilla/websocket
```

필요한 모듈을 받아오기 위해서 `go get`을 이용해 관련 패키지를 import 해온다. 그럼 자동으로 `go.mod` 파일에 자동으로 추가된다.

# 2. Gin test

```go
package main
 
import "github.com/gin-gonic/gin"
 
func main() {
    r := gin.Default()
    r.GET("/ping", func(c *gin.Context) {
        c.JSON(200, gin.H{
            "message": "pong",
        })
    })
    r.Run()
}
```

# References

- [Go Download and install](https://golang.org/doc/install)
- [Go gRPC Quickstart](https://grpc.io/docs/languages/go/quickstart/)
- [Go Web Framework](https://medium.com/devtechtoday/top-7-golang-web-frameworks-in-2020-and-beyond-9ca2a89eb904)