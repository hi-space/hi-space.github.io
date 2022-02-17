---
title: JAVA gRPC 클라이언트 Stub
tags: java grpc 
---

gRPC의 클라이언트 stub은 3가지의 객체로 제공된다. 사용하는 stub에 따라 클라이언트의 수신 처리 방식이 달라진다.

#### BlockingStub

- sync 통신
- 서버로부터 응답이 올 때까지 대기
- Unary RPC, Server Streaming RPC에서만 사용 가능

#### AsyncStub (Stub)

- async 통신
- 서버로부터 오는 응답을 StreamObserver 객체가 대신 받아서 처리
- 모든 방식의 RPC에서 사용 가능

#### FutureStub

- async 통신
- 서버로부터의 응답 도달에 상관 없이 일단 ListenableFuture로 래핑된 객체를 반환
- 서버로부터 응답이 오면 ListenableFuture 객체를 통해 전달받은 메시지를 언래핑
- Unary RPC에서만 사용 가능

<!--more-->

# References

- [gRPC 세 가지 Streaming RPC 구현 (Java)](https://tech.lattechiffon.com/2021/06/30/grpc-%EC%84%B8-%EA%B0%80%EC%A7%80-streaming-rpc-%EA%B5%AC%ED%98%84-java/)
- [sample java grpc client](https://github.com/grpc/grpc-java/blob/master/examples/src/main/java/io/grpc/examples/routeguide/RouteGuideClient.java)
