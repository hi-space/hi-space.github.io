---
title: "Message Queue"
tags: backend
---

# Message Queue

- Message Oriented Middleware (MOM)는 비동기 메시지를 사용하는 다른 응용 프로그램 사이에서 데이터 송수신을 의미. MOM 을 구현한 시스템을 Message Queue 라고 한다.

- 비동기적으로 작업을 처리하기 때문에 유연성을 제공하고 Service Oriented Architecture (SOA)의 개발에 잘 맞는다.
  
- MQ는 프로세스 또는 프로그램 인스턴스가 데이터를 교환할 때 사용. 이 때 사용하는 프로토콜은 AMQP (Advanced Message Queuing Protocol)
  - AMQP : 메시지 지향 미들웨어를 위한 open standard application layer protocol
  
- 대용량 데이터를 처리하기 위한 배치 작업, 비동기 데이터 등에 사용됨. 
  
- 사용자의 요청이 많아질 수록 요청을 처리하는데에 지연이 발생할 수 있기 때문에 Message Broker를 둬서 프로그램에 작업을 분산하기 위해 사용한다.

<!--more-->

# RabbitMQ

# ZeroMQ

# Kafka

---

# References

- [ZMQ의 메시지 패턴](https://soooprmx.com/zmq-how-to/)