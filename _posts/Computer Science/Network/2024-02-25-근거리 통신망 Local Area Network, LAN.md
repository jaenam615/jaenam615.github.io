---
title: "근거리 통신망 Local Area Network, LAN"
writer: Langerak
date: 2024-02-25 12:00:00 +0800
categories: [Computer Science, Network]
tags: [Computer Science, Network]
pin: false
math: true
mermaid: true
image:
  path: https://github.com/JeongJongMun/JeongJongMun.github.io/assets/101979073/21360112-1af9-4935-be1f-caa0f47897d1
  lqip: data:image/webp;base64,UklGRpoAAABXRUJQVlA4WAoAAAAQAAAADwAABwAAQUxQSDIAAAARL0AmbZurmr57yyIiqE8oiG0bejIYEQTgqiDA9vqnsUSI6H+oAERp2HZ65qP/VIAWAFZQOCBCAAAA8AEAnQEqEAAIAAVAfCWkAALp8sF8rgRgAP7o9FDvMCkMde9PK7euH5M1m6VWoDXf2FkP3BqV0ZYbO6NA/VFIAAAA
  alt: Network
---

> 본 글은 제 개인적인 공부를 위해 작성한 글입니다. 틀린 내용이 있다면 언제든지 피드백을 주시면 감사하겠습니다. 참고로만 활용해주시길 바랍니다.

## 근거리 통신망 Local Area Network, LAN

---

`근거리 통신망 Local Area Network, LAN`은 네트워크 매체를 이용해 사무실 또는 집과 같은 **가까운 지역을 한데 묶는 컴퓨터 네트워크**이다.

규모에 관계없이 LAN의 가장 큰 특징은 **단일의 제한된 영역에 있는 디바이스를 연결**한다는 점이다.

이와 대조적으로 광역 네트워크 WAN 또는 대도시 지역 네트워크 MAN은 더 넓은 지리적 영역을 포함한다.

현재는 **이더넷 Ethernet**이라는 인터넷 프로토콜인 **TCP/IP**를 사용하는 것이 일반적이다.

대부분의 LAN은 **라우터**를 중심으로 인터넷에 연결하고, 거의 항상 **이더넷**, **와이파이** 또는 둘을 동시에 사용해 해당 네트워크에 포함되는 기기들과 연결한다.

## LAN의 특징

---

1. 전송되는 패킷의 손실과 지연이 적어 안정성과 신뢰성이 높다.
2. 사용자 간의 데이터 공유 및 통신이 쉽고 빠르다.
3. 전송 거리가 짧다.
4. 네트워크에 노드가 많아질수록 충돌이 발생해 성능이 떨어진다.

## LAN의 종류

---

### 1. Client-Server LAN

**중앙 서버**에 연결된 **여러 장치(클라이언트)**들로 구성된다.

서버는 파일 저장소, 애플리케이션 접근, 디바이스 접근, 네트워크 트래픽을 관리한다.

클라이언트는 애플리케이션이나 인터넷을 실행하거나 접근하는 모든 연결된 디바이스들이다.

클라이언트는 케이블이나 무선 연결을 통해 서버에 연결한다.

일반적으로 애플리케이션 제품들은 LAN 서버에 보관할 수 있다. 사용자는 LAN 서버에서 실행되는 애플리케이션을 통해 DB, 이메일, 문서 공유, 인쇄 및 기타 서비스에 접근할 수 있으며, 읽기 및 쓰기 접근 권한은 네트워크 또는 IT 관리자가 관리한다.

대부분의 중대형 기업, 정부, 연구 및 교육 네트워크는 Client-Server LAN이다.

### 2. Peer-To-Peer LAN

중앙 서버가 따로 없다.

Client-Server LAN과 달리 무거운 작업 부하를 처리하지 못해 일반적으로 더 작다.

각 디바이스들은 네트워크 기능을 동등하게 공유한다.

각 디바이스들은 스위치 또는 라우터에 유/무선으로 연결 해 자원과 데이터를 공유한다.

대부분의 홈 네트워크는 Peer-To-Peer LAN이다.

<br/> <br/>

*참고*

- [https://doongdangdoongdangdong.tistory.com/248](https://doongdangdoongdangdong.tistory.com/248)

- [https://ko.wikipedia.org/wiki/근거리_통신망#cite_note-1](https://ko.wikipedia.org/wiki/근거리_통신망#cite_note-1)

- [https://www.cisco.com/c/en/us/products/switches/what-is-a-lan-local-area-network.html#~benefits](https://www.cisco.com/c/en/us/products/switches/what-is-a-lan-local-area-network.html#~benefits)
