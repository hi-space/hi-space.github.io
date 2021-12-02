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
export PATH=$PATH:/usr/local/go/bin
```

# 1. Test Codes

```sh
# vi test.go
package main
func main() {
    println("Test")
}
```

# 2. Run

`run` 명령어를 사용하면 컴파일과 동시에 실행하게 된다. 이 때 실행파일은 만들어지지 않는다.

```sh
go run test.go
```

# 3. Build

```sh
go build test.go
```

빌드를 하게 되면 실행파일이 생성된다. 실행파일을 실행시키면 컴파일이 된 결과물을 실행하게 된다.
