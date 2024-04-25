---
title: "Client-Server / Peer-To-Peer 구조"
writer: Langerak
date: 2024-02-23 12:00:00 +0800
categories: [Computer Science, Network]
tags: [Computer Science, Network]
pin: false
math: true
mermaid: true
image:
  path: https://github.com/JeongJongMun/JeongJongMun.github.io/assets/101979073/88b4d5f2-e39f-40d4-ba8a-2387cc8b028b
  lqip: data:image/webp;base64,UklGRpoAAABXRUJQVlA4WAoAAAAQAAAADwAABwAAQUxQSDIAAAARL0AmbZurmr57yyIiqE8oiG0bejIYEQTgqiDA9vqnsUSI6H+oAERp2HZ65qP/VIAWAFZQOCBCAAAA8AEAnQEqEAAIAAVAfCWkAALp8sF8rgRgAP7o9FDvMCkMde9PK7euH5M1m6VWoDXf2FkP3BqV0ZYbO6NA/VFIAAAA
  alt: Network
---

> 본 글은 제 개인적인 공부를 위해 작성한 글입니다. 틀린 내용이 있다면 언제든지 피드백을 주시면 감사하겠습니다. 참고로만 활용해주시길 바랍니다.

## 개요

---

`클라이언트 서버 모델`은 서비스 요청자인 클라이언트와 서비스 자원의 제공자인 서버 간에 작업을 분리해주는 분산 애플리케이션 구조이자 네트워크 아키텍처를 나타낸다.

`피어-투-피어 모델`은 소수의 서버에 집중하기보다는 망구성에 참여하는 기계들의 계산과 대역폭 성능에 의존하여 구성되는 통신망이다. 피어 노드들이 서로 클라이언트와 서버 역할을 동시에 네트워크 위에서 하게 된다.

## 클라이언트-서버 구조

---

`클라이언트`는 서비스를 사용하는 사용자 혹은 사용자의 단말기를 가리키는 말이다.

- 서버에 연결 및 통신을 시작한다.
- 보통 간헐적으로 연결되고, 동적인 IP 주소를 가진다.
- 클라이언트 간에 직접 통신하지 않고, 서버를 거쳐서 간접적으로 통신한다.
    - $e.g.\;$HTTP, IMAP, FTP

`서버`는 서비스를 제공하는 컴퓨터이며, 다수의 클라이언트를 위해 존재하기 때문에 일반적으로 매우 큰 용량과 성능을 가지고 있다.

- Always-On Host: 클라이언트를 위한 항시 대기
- Permanent IP Address: 영구적인 IP 주소
- Often in data centers for Scaling: Scale-Out이 가능하다.

대표적인 예로는 `월드 와이드 웹(WWW)`이 있다.

웹사이트에서는 웹서버가 서버 역할을 하고, 사용자가 쓰는 웹 브라우저가 클라이언트 프로그램이 된다.

모든 네트워크 응용 프로그램은 클라이언트 서버 모델에 기초하고 있다.

클라이언트-서버 모델에서 근본적인 연산은 `트랜잭션`이다.

클라이언트-서버 트랜잭션은 네 단계로 구성된다.

![image](https://github.com/JeongJongMun/JeongJongMun.github.io/assets/101979073/8ab58539-82af-4478-bd0d-c5e3be6212f2)

1. 클라이언트가 서비스를 필요로 할 때, 클라이언트는 한 개의 `요청 Request`을 서버로 보내는 것으로 트랜잭션을 개시한다.
    
    가령, 웹 브라우저가 파일을 필요로 할 때, 웹 서버로 요청을 보낸다.
    
2. 서버는 `요청 Request`을 받고, 해석하고, 서버 자신의 자원을 적절한 방법으로 조작한다.
    
    가령, 웹 서버가 브라우저로부터 `요청 Request`을 받을 때, 디스크 파일을 읽는다.
    
3. 서버는 `응답 Response`을 클라이언트로 보내고, 그 후에 다음 요청을 기다린다.
    
    가령, 웹 서버는 이 파일을 클라이언트로 돌려보낸다.
    
4. 클라이언트는 응답을 받고, 이것을 처리한다.
    
    가령, 웹 브라우저가 서버로부터 페이지를 한 개 받은 후, 이것을 스크린에 디스플레이한다.
    

> **클라이언트-서버 트랜잭션 VS 데이터베이스 트랜잭션** <br/>
> 클라이언트-서버 트랜잭션은 데이터베이스 트랜잭션이 아니며, 원자성 같은 ACID 특성을 하나도 가지지 않는다.
> 이 문맥에서 트랜잭션은 단순히 클라이언트와 서버가 수행한 일련의 단계를 의미한다.
{: .prompt-info }

또한 클라이언트와 서버는 머신이나 호스트가 아닌 `프로세스`이다.

한 개의 호스트는 서로 다른 많은 클라이언트와 서버를 동시에 실행할 수 있으며, 클라이언트와 서버 트랜잭션은 동일하거나 다른 호스트에 존재할 수 있다.

클라이언트 서버 모델은 클라이언트와 서버의 호스트로의 매핑에 관계없이 동일하다.

## 피어-투-피어 구조

---

No Always-On Server

- 항시 대기 중인 서버가 없다.

Arbitrary end systems directly communicate

- 임의의 종단 간에 직접적으로 통신한다.

`피어`는 다른 피어들에게 서비스를 요청하고, 그 결과 다른 피어들로부터 서비스를 제공받는다.

- `Self Scalability`: 새로운 피어들이 새로운 서비스 수요와 함께 새로운 서비스 가용 능력(Capacity)를 가져온다. 즉, P2P에 참여하는 클라이언트 피어들이 서버 역할까지 하는 것이다.

피어들은 간헐적으로 연결되어 있으며 IP 주소는 동적으로 변화한다.

- 클라이언트-서버 구조보다 악의적인 유저를 막기 상대적으로 어렵다.
- 서버를 통한 퀄리티 향상이 어렵다.

대표적인 예로는 `P2P File Sharing(e.g. Torrent)`등이 있다.
<br/> <br/>

*참고*
- [https://ko.wikipedia.org/wiki/클라이언트_서버_모델](https://ko.wikipedia.org/wiki/클라이언트_서버_모델)

- [https://ko.wikipedia.org/wiki/P2P](https://ko.wikipedia.org/wiki/P2P)

- CS:APP