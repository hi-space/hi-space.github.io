---
title: Microservice Architecture
tags: backend
---

![](/assets/images/20-05-10-microservice-architecture-2021-11-02-10-29-42.png)

- 각자 별도의 프로세스에서 실행되며,
  HTTP 자원 API 같이 가벼운 매커니즘으로 통신하는 작은 서비스를 모아 하나의 어플리케이션을 만드는 아키텍처. 
- 각각의 서비스는 각각의 비즈니스 기능을 담당하고 완전 자동화된 절차에 따라 독립적으로 배포 가능
- 중앙 집중형 관리 방식은 최소한으로 사용
각 서비스는 서로 다른 프로그래밍 언어나 서로 다른 데이터 저장 기술 사용 가
작고 독립적이며 분산된 마이크로서비스 사용

<!--more-->

### Pros

- 작은 서비스들로 나누고, 각 서비스를 독립적으로 만듦 → loosely-coupled
- 대용량 분산 환경에 적합
- 복잡도 감소
- 유연한 배포
- 재사용성 → 확장성
- 서비스별 hw/sw 플랫폼/기술의 도입 및 확장이 자유롭다.
- 개발자가 이해하기 쉽고 개발/운영 매 단계의 생산성이 높다.
- 지속적인 개발/디플로이가 biz capability 단위로 관련된 소수의 인원의 - 책임하에 이루어질 수 있다.
- fault isolation 특성이 좋다

### Cons

- 장애추적, 모니터링이 어렵다
- 여러 서비스에 걸쳐져 있는 feature의 경우, transaction 을 다루기 어렵고 test가 어렵다.
- 서비스 간 dependency가 있는 경우 release 가 까다롭다
- 서비스 갯수가 많고 유동적이기 때문에 CI/CD 및 서비스 관리 상의 문제가 발생할 수 있다.

## Monolithic vs Microservice 

| Monolithic Architecture | Microservice |
| -- | -- |
| ![](/assets/images/20-05-10-microservice-architecture-dd.png) | ![](/assets/images/20-05-10-microservice-architecture-2021-11-02-10-33-34.png) | 
| 전체 애플리케이션은 단일 단위로 설계/개발/배포 | 각 서비스는 단일 비즈니스 작업 지원 |
| App 간 통신을 하기 위해 내부 프로시저 \(함수 호출 사용\) | 서비스 간 통신을 하기 위해 일반적으로 REST API 사용 |
| 단일 프로그래밍 언어 | 각 서비스는 그 특성에 맞춰 자유롭게 프로그래밍 언어, 프레임워크 선택 가능 |
| 특정 모듈 수정 시 전체 빌드 및 배포 | 다른 서비스에 영향을 미치지 않고 독립적으로 빌드/배포/Scaling |
| 애플리케이션 범위가 증가하면 더 복잡 |  |
| 특정 부분 Scaling 을 위해 전체 애플리케이션이 Scaling 되어야 함 |  |

## SOA

![](/assets/images/20-05-10-microservice-architecture-soa.png)

* 서비스 지향 아키텍처
* 애플리케이션을 서비스로 나눈다는 점에서 비슷
* 서비스 간 연결은 ESB (Enterprise Service Bus) 라는 미들웨어를 사
* 소프트웨어 벤더들이 EBS 제품 팔기에 급급했고, "서비스" 에 집중한다는 사상 자체는 가치 있었지만 하나의 유행처럼 지나감
* Microservice는 중앙집중적인 ESB 대신 REST API 또는 경량화된 메시징을 이용해 각 서비스를 처리 (API 로 연결해야 지속적인 개발, 배포 가능)

# Deploy

## Container vs VM

![](/assets/images/20-05-10-microservice-architecture-2021-11-02-10-44-28.png)

## Container 기반 Deploy

* 각 서비스를 컨테이너에 올려서 배포할 수 있다. 컨테이너는 OS 커널을 공유하고 있기 때문에, VM과 달리 하나의 OS에 여러 컨테이너가 올라갈 수 있다.    -&gt;크기가 작고, 리소스를 훨씬 적게 사용
* 컨테이너를 프로세스를 묶어서 샌드박스 형태로 제공하고 각자의 port name space와 file system을 갖고 있다. 
* 컨테이너 별로 메모리와 CPU 등 리소스를 제한 가능
* 서비스를 Container Image로 패키징해서 하나의 host 안에 여러 Container를 실행할 수 있다
* Kubernetes 같은 Cluster 매니저를 이용해 Container를 관리
* VM에 비해 이미지가 빠르게 빌드되고 OS 부팅 없이 빠르게 실행된다.
* 특정 서비스에서 요청이 많아졌을 때, 로드 밸런서가 해당 부하를 감지하고 해당 서비스를 EC2 인스턴스에 도커 컨테이너를 이용해 배포할 수 있다.

![](/assets/images/20-05-10-microservice-architecture-2021-11-02-10-45-55.png)

## Serverless Deploy

* VM 이나 Container와 다른 방식의 Deploy 방식
* 마이크로서비스를 배포하기 위해서 각 서비슬르 패키징한 ZIP 을 메타데이터와 함께 Lambda에 업로드 하기만 하면 된다
* Serverless는 실제 서버가 없는 것은 아니지만 서버, VM, Container 등에 대해 고민하지 않고 애플리케이션 개발에 집중할 수 있는 형태이다
* 시간, 메모리, 네트워크 등 사용량에 따라 비용 지불
* 각 서비스가 상태를 저장할 수 없고, 요청이 있을 때 마다 별도의 인스턴스가 실행되는 방식
* 3rd party Message Broker에서 메시지를 받는 방식과는 맞지 않음
* 각 서비스는 작은 단위로 빠르게 실행되지 않으면 타임 아웃으로 종료

#### Lambda

Lambda 는 stateless 서비스로, 몇가지 요청이 있을 때 실행된다.
  * 웹 서비스 요청을 이용해 직접 실행
  * 다른 AWS 서비스에서 발생하는 요청에 의해 자동 실행 \(event\)
  * API Gateway가 HTTP 요청을 받아서 해당 Lambda 함수를 자동으로 실행
  * 스케줄러를 이용해 주기적으로 실행

# References

- [https://futurecreator.github.io/2018/09/14/what-is-microservices-architecture/](https://futurecreator.github.io/2018/09/14/what-is-microservices-architecture/)
- [https://www.nginx.com/blog/deploying-microservices/](https://www.nginx.com/blog/deploying-microservices/)
