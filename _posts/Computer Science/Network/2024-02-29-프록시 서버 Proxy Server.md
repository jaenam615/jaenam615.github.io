---
title: "프록시 서버 Proxy Server"
writer: Langerak
date: 2024-02-29 12:00:00 +0800
categories: [Computer Science, Network]
tags: [Computer Science, Network]
pin: false
math: true
mermaid: true
image:
  path: https://github.com/JeongJongMun/JeongJongMun.github.io/assets/101979073/74bea9a8-b891-4648-a00f-25efe93962c2
  lqip: data:image/webp;base64,UklGRpoAAABXRUJQVlA4WAoAAAAQAAAADwAABwAAQUxQSDIAAAARL0AmbZurmr57yyIiqE8oiG0bejIYEQTgqiDA9vqnsUSI6H+oAERp2HZ65qP/VIAWAFZQOCBCAAAA8AEAnQEqEAAIAAVAfCWkAALp8sF8rgRgAP7o9FDvMCkMde9PK7euH5M1m6VWoDXf2FkP3BqV0ZYbO6NA/VFIAAAA
  alt: Network
---

> 본 글은 제 개인적인 공부를 위해 작성한 글입니다. 틀린 내용이 있다면 언제든지 피드백을 주시면 감사하겠습니다. 참고로만 활용해주시길 바랍니다.


## Proxy Server

---

**프록시 서버**는 **클라이언트**가 자신을 통해서 **다른 네트워크 서비스에 간접적으로 접속**할 수 있게 해주는 컴퓨터 시스템이나 응용 프로그램을 가리킨다.

서버와 클라이언트 사이에 중계기로서 대리로 통신을 수행하는 것을 가리켜 **프록시**, 그 중계 기능을 하는 것을 **프록시 서버**라고 부른다.

<br/>

## 프록시 서버의 목적

---

프록시 서버의 사용 목적은 다양하다.

1. 익명으로 컴퓨터를 유지 (주로 보안을 위하여)
2. 캐시를 사용하여 리소스로의 접근을 빠르게 하기 위해. 
    
    웹 프록시는 웹 서버로부터 웹 페이지를 캐시로 저장하는 데 흔히 쓰인다.
    
3. 네트워크 서비스나 콘텐츠로의 접근 정책을 적용하기 위해. 이를테면 원치 않는 사이트를 차단한다.
4. 사용률을 기록하고 검사하기 위해. 이를테면 회사는 인터넷 이용을 파악
5. 보안 및 통제를 뚫고 나가기 위해.
6. 바이러스 전파, 악성 루머 전파, 다른 정보를 빼낼 목적으로
7. 역으로 IP 추적을 당하지 않을 목적으로
8. 전달에 앞서 악성 코드를 목적으로 전달된 콘텐츠를 검사하기 위해
9. 데이터 유출 보호를 위해 밖으로 나가는 콘텐츠를 검사
10. 지역 제한을 우회하기 위해

## 프록시 서버의 작동

---

프록시 서버는 사용자를 인터넷에서 분리하는 중간 서버 역할을 한다.

프록시 서버가 없다면 트래픽이 장치에서 웹 사이트로 직접 흐르게 되고, 

프록시 서버를 사용한다면 **요청한 웹 서버로 이동하기 전에 트래픽이 장치에서 프록시로 이동한다.**

웹 사이트의 모든 응답은 프록시 서버로 돌아가서 사용자에게 전달된다.

**작동 과정**

1. 브라우저에 웹 사이트 주소를 입력한다.
2. 프록시 서버가 사용자의 요청을 수신 받는다.
3. 프록시 서버는 접근하려는 웹 서버로 요청을 전달한다.
4. 웹 서버는 응답(웹 사이트 데이터)를 프록시 서버로 응답한다,
5. 프록시 서버가 사용자에게 응답을 전달한다.
   
<br/> <br/>

*참고*

- [https://ko.wikipedia.org/wiki/프록시_서버](https://ko.wikipedia.org/wiki/프록시_서버)

- [https://surfshark.com/ko/blog/proxy-server](https://surfshark.com/ko/blog/proxy-server)