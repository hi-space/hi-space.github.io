---
title: "[Go] ROS Subscribe"
tags: go ros
---

[goroslib](https://github.com/aler9/goroslib)을 사용하여 Go로 ROS Client를 개발할 수 있다. `goroslib` 외에도 `rosgo`, `rclgo` 라이브러리가 있으나, `goroslib`은 순수 Go로 작성되고 외부 종속성이 없어서 복잡한 설정 없이 사용하기 좋다.
- `rosgo`: maintain 되지 않음
- `rclgo`: C++ 라이브러리를 감싼 wrapper 라이브러리

<!--more-->

## Custom Message Subscribe

```go
// custom message
type RosMsg struct {
	msg.Package `ros:"test_msgs"`
	Header      std_msgs.Header
	Variable1   []float64
	Variable2   []int8
}

func main() {
	// 노드 생성
	n, err := goroslib.NewNode(goroslib.NodeConf{
		Name:          "gw_sub",
		MasterAddress: "127.0.0.1:11311",
	})
	if err != nil {
		log.Fatalf("New ros node error: %v", err)
	}
	defer n.Close()

	log.Printf("[create ros node] %s", master)

	// subscribe 생성
	sub, err := goroslib.NewSubscriber(goroslib.SubscriberConf{
		Node:     n,
		Topic:    "test_topic",
		Callback: onMessage,
	})

	if err != nil {
		log.Fatalf("create subscriber error: %v", err)
	}
	defer sub.Close()

	log.Printf("[subscribe ros node] test_topic")

	// Ctrl-c를 입력받기 전까지 spin
	c := make(chan os.Signal, 1)
	signal.Notify(c, os.Interrupt)
	<-c
}

func onMessage(msg *RosMsg) {
	log.Println(msg)
}
```

### TroubleShooting

```go
type RosMsg struct {
	msg.Package `ros:"test_msgs"`
	variable1   []float64
	variable2   []int8
}
```

```sh
[ERROR] subscriber '/decision' got an error: Client [/gw_sub] wants topic /decision to have datatype/md5sum [decision/DecisionAVV/35ea88e35f32563f4642665626726daf], but our version has [decision/DecisionAVV/ae6faff013df2c53532c3bb51419f486]. Dropping connection.
```

이 경우는 ROS 데이터를 받을 구조체 포맷이 subscribe 메세지 포맷과 상이할 경우 발생한다. `header` 필드가 없어서 발생했던 문제로, 추가해서 동일하게 포맷을 맞춰줬다.

```go
type RosMsg struct {
	msg.Package `ros:"test_msgs"`
	header      std_msgs.Header
	variable1   []float64
	variable2   []int8
}
```

포맷을 맞추고 다시 subscirbe 하니 onMessage 함수가 콜 되지만 값이 읽어지지 않는 에러가 발생했다.

```sh
panic: reflect.Value.Interface: cannot return value obtained from unexported field or method
```

구조체 변수를 대문자로 선언하지 않아 발생한 문제였다. 

```go
type RosMsg struct {
	msg.Package `ros:"test_msgs"`
	Header      std_msgs.Header
	Variable1   []float64
	Variable2   []int8
}
```
