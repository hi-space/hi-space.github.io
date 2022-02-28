---
title: Edge Cloud System
tags: cloud iot architecture
---

![](/assets/images/22-02-05-edge-cloud-system-edge-cloud-system.png)

대량의 센서 데이터 처리, 대량 데이터의 배치 프로세싱, 장기간 데이터에 대한 통계 분석 등의 경우에 Edge에서 처리하기가 어렵기 때문에 Edge computing과 Cloud computing이 상호 연동되어 데이터를 처리해야 한다.

IoT 디바이스에서 수집되는 데이터를 이용해 클라우드에서 처리하기 위해서는 Edge device와 Edge Clous Service와 연동하는 시스템 구조가 필요하다. 

AWS에서는 IoT Core, GCP 에서는 IoT Edge 라는 이름으로 서비스를 제공하고 있다.

<!--more-->
### IoT Edge

- Sensor: 디바이스에서 얻어지는 센서 데이터들을 클라우드로 publish
- Actuator: 클라우드에서 전달받은 제어 메세지는 controller 를 통해 actuator에 전달되고 edge 디바이스가 컨트롤 된다.


## [AWS IoT Core](https://aws.amazon.com/ko/iot-core/)

Connected Device가 쉽고 안전하게 클라우드 애플리케이션 및 다른 디바이스와 상호작용할 수 있게 도와주는 관리형 클라우드 서비스

- 서버를 프로비저닝하거나 관리하지 않고도 쉽고 안정적으로 디바이스 플릿을 연결하고 관리하며 크기를 조정합니다.
- MQTT, HTTPS, MQTT over WSS, LoRaWAN을 포함하여 선호하는 통신 프로토콜을 선택합니다.
- 상호 인증 및 포괄적인 암호화로 디바이스 연결 및 데이터를 보안합니다.
- 정의한 비즈니스 규칙에 따라 즉시 디바이스 데이터를 필터링 및 변환하고 이를 기반으로 운영할 수 있습니다.

## [GCP Iot Edge](https://cloud.google.com/blog/topics/developers-practitioners/what-cloud-iot-core)

![gcp iot edge](/assets/images/22-02-05-edge-cloud-system-iot-core.jpeg)

- Device Manager
  - Device identity management 
  - Support for configuring, updating, and controlling individual devices
  - Role-level access control
  - Console and APIs for device deployment and monitoring

- Protocol Bridge
  - Device와 GCP를 연결하기 위한 bridge (MQTT, HTTP 지원)
  - Bi-directional messaging
  - Automatic load balancing
  - Global data access with Pub/Sub

# References

- [https://kr.machbase.com/iot-edge-cloud/](https://kr.machbase.com/iot-edge-cloud/)
 