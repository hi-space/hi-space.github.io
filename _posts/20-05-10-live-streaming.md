---
title: Live Streaming
tags: backend
---

![](/assets/images/20-05-10-live-streaming-2021-11-02-10-50-34.png)

라이브 스트리밍 방식은 동영상을 끊김 없이 사용자의 동영상 플레이어에 전달하는 것이 목적이다.

<!--more-->

# RTMP

* Flash 용으로  작성된 짧은 대기 시간의 TCP 기반 프로토콜
* 라이브 스트리밍의 표준이였다.
* 스마트폰, 웹브라우저에서 기본적으로 RTMP를 재생할 수 없다.

# HLS (HTTP Live Streaming)

![](/assets/images/20-05-10-live-streaming-hls.png)

* RTMP에 비해 확장성이 뛰어나고 유연한 프로토콜
* 라이브 스트림을 제공하기 위해 기존 HTTP CDN을 사용하기 위해서 만들어짐
* 다양한 장치와 브라우저에서 지원이되고 CDN과 완벽하게 호환되어 빠르게 배포 가능 \(확장성 좋음\)
* 5~30 sec 정도의 지연이 있을 수 있다.

HLS에서 서버는 HTTP로 요청을 받아 Player에 응답을 주는 역할을 한다. 요청받은 파일을 읽어서 특별한 변형 없이 HTTP response에 데이터를 보낸다. 

기존 스트리밍 방식과 마찬가지로 HLS도 카메라로 촬영한 영상을 코덱을 이용해 압축해서 \(H.264/AAC\) 서버로 보낸다. 기존과 다른 것은 동영상 정보를 전달할 때 stream을 segment 해서 보내는 것이다. 기존 스트리밍 서버에서는 플레이어가 스트리밍 서버에 연결한 스트리밍 규격에 맞게 데이터를 변형해야 한다. 

![](/assets/images/20-05-10-live-streaming-hls-segment.png)

단방향 미디어 스트리밍 파이프 라인을 보면, 미디어 캡쳐 기기가 있고 영상을 보내는 사람 쪽에 미디어 엔진과 인코더가 있다. 수신측에는 Decoder아 있지만 기본적으로 동일한 stack 이다.

Stream Segment는 일정한 시간마다 입력받은 미디어 데이터를 분할해 파일을 만들고, 그 분할한 파일에 접근할 수 있는 메타데이터\(m3u8\)를 생성한다. HTTP는 양방향이 아니기 때문에 클라이언트가 요청을 해야만 서버로부터 응답을 받을 수 있다. 즉, 사용자는 클라이언트가 다음에 볼 영상을 요청할 때마다 응답으로 잘게 쪼개진 동영상을 보내줘야 한다. 

![](/assets/images/20-05-10-live-streaming-hls-segment-cdn.png)

## MPEG-Dash

* 웹 표준이나 HLS와 동일한 결함이 있음. 

## WebRTC

* 1:1 스트리밍에 중점
* 대기 시간이 짧고 브라우저 내에서 작동하기 위해 추가 플러그인이 별도 필요하지 않다
* P2P 로 인해 서버와 end-user 간 데이터 교환 트래픽이 적다. 연결이 설정되면 두 peer 간 트래픽이 직접 흐른다.
* Peering Network를 사용하기 때문에 근처에 다른 node가 있어야 한다.
* Android / IOS에 대한 기본 모바일 플랫폼 지

브라우저를 통해 Real TIme Communications를 가능하게하는 웹용 프레임워크. 이 프로토콜은 특별한 플러그인 없이 브라우저 간에 P2P 연결을 사용하여 거의 동시에 통신을 교환한다. 여러 브라우저가 서로 직접 통신할 수 있어서 개인, 소규모 그룹 간 스트리밍에 적합하다. 

![](/assets/images/20-05-10-live-streaming-webrtc.png)

HLS의 경우 송출되는 스트림은 반드시 파일로 저장되고 트랜스코딩 절차를 거친 후 사용자에게 전달되어야 하지만 WebRTC는 송출 시의 stream 그대로 사용자에게 전달하여 빠른 latency를 가진다.

![](/assets/images/20-05-10-live-streaming-webrtc-stream.png)

솔루션을 구축하기 위해 HTTP 기반 \(HLS / MPEG-DASH\) WebRTC 프로토콜 조합을 사용하는 것이 추천된다. 재생 시간을 지연시키거나 여러 장치에서 재생을 동기화하려는 경우, 참조할 시간을 제어하기 위해서 메타데이터 및 타임 코드를 사용해 HTTP Live Streaming 을 사용하여 재생할 수 있다.

![](/assets/images/20-05-10-live-streaming-webrtc-p2p.png)

WebRTC는 P2P 통신이 가능하나, 1:N 을 대응하기 위해선 별도 서버가 필요하다.

* Signalling Server : 클라이언트들의 통신을 조정하기 위한 메타데이터 교환 서버
* STUN/TURN Server : NAT 및 방화벽 대응을 위한 서버
  * STUN : 외부 네트워크 주소를 얻는데 사용
  * TURN : P2P 연결이 실패한 경우 트래픽을 중계하는데 사용

# References

- [https://www.html5rocks.com/ko/tutorials/webrtc/infrastructure/](https://www.html5rocks.com/ko/tutorials/webrtc/infrastructure/)