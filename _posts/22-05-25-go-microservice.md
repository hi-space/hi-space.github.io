---
title: "MSA 아키텍처 설계"
tags: go architecture msa
---

<!--more-->

# MSA

애플리케이션의 오랜 시간에 걸쳐 유지 보수가 될 때 점점 더 복잡해지는 경향이 있다. 이러한 문제를 방지하기 위해 MSA가 등장하게 됐다. MSA는 수많은 애플리케이션들이 독립적이고 격리된 서비스들의 집합으로 분할된다. 분리된 서비스들은 네트워크 프로토콜을 통해 통신한다. 

SOA(Service-Oriented Architecture) 보다 단순함에 집중하는 형태이다. ESB(Enterprise Service Bus)와 같은 인프라 구성 요소들은 피하고 SOAP와 같은 복잡한 통신 프로토콜 대신 더 간단한 통신 수단이나 AMQP(Advanced Message Queuing Protocol) 메시징이 선호된다.

복잡한 소프트웨어들을 분리함으로써, 각 서비스들은 별도의 기술 조합으로 개발될 수 있고, 서비스를 독립적으로 개발, 배포, 운영할 수 있다.

## 기본 설계 목표

클라우드 애플리케이션의 핵심 설계 목표 중 하나는 확장성(Scalability) 이다. 이는 필요시에 유동적으로 애플리케이션의 자원을 늘리거나 줄이는 것을 의미한다. 이를 실현하기 위해서는 하나의 애플리케이션은 작은 가상머신 인스턴스를 사용하고, 인스턴스의 갯수를 추가하거나 줄이면서 scale을 조정한다. (scale-out)

> **Scale Out**: 수평적 확장. 인스턴스들의 갯수를 증가시켜서 확장한다. 저렴한 하드웨어를 사용해 무한대로 확장이 가능하다. 큰 서버의 경우는 비용이 기하급수적으로 늘어날 수 있다.  
> **Scale Up**: 수직적 확장. 기존 인스턴스들에 자원을 더 할당하여 확장한다. 자원의 수가 무한대로 커질 수 없어서 한계가 있다.

Scale Oute을 하기 위해서는 고려해야 하는 사항들이 있다.
- **Statelessness**:  각 인스턴스는 어떤 종류의 내부적인 상태값을 가져서는 안된다. (데이터가 메모리나 파일 시스템에 저장되는 형태) 다른 인스턴스에 의해 서비스 될 수도 있기 때문이다. 그렇기 때문에 DB와 같은 저장 영역은 별도 관리해야한다.
- **쉬운 배포**: 인스턴스들을 빠르게 배포하기 위해, 가능한 한 자동화되어야 한다.
- **Recovery**: 오토스케일링을 할 때 인스턴스들이 예고없이 종료될 수 있기 때문에, 문제가 발생했을 때 그에 맞는 대처가 가능해야 한다.

## 통신

### Request / Response 패턴 (Synchronous 통신)
서비스간 통신을 위한 프로토콜이 필요하다. 사실상 표준은 RESTful로, request/response 구조를 따른다. 하지만 이 패턴은 많은 서비스에 걸쳐 있는 복잡한 프로세스들로 시스템을 구현할 때 한계가 있다. (ex, 새로운 사용자가 생성됐을 때 여러개의 서비스에서 이를 인지할 필요가 있는 경우)  

### Publish / Subscribe 패턴 (Asynchronous 통신)
대안적인 통신 패턴은 publish/subscribe 패턴이다. 각 서비스들은 다른 서비스들에서 감지할 수 있는 이벤트를 내보내고, 다른 서비스에서는 필요한 이벤트를 구독하여 알아차릴 수 있게 한다. 이 아키텍처들은 Message Broker를 통해 발행된 메시지들을 받아들이고, 구독자들에게 특정 경로로 전달한다. 일반적으로 중간 저장소로 queue를 사용한다.  
이 패턴은 서비스 서로간의 결합을 분리하는 좋은 방법이다. 이벤트가 전송할 때 어떤 서비스를 향해 갈지, 어떤 서비스로부터 받을 지 고민하지 않아도 된다. 또한 확장도 유리하다.

## Why Go?

MSA는 작은 서비스들 간에 애플리케이션의 책임을 분리시키는 아키텍처이다. Go는 운영 환경에서의 의존성과 가상머신에 대해 필요성이 거의 없는 단일 바이너리로 컴파일 되기 때문에, 이동이 용이한 MSA에 최적화되어 있다.
