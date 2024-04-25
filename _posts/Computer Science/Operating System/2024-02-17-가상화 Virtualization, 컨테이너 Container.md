---
title: "가상화 Virtualization, 컨테이너 Container"
writer: Langerak
date: 2024-02-17 12:00:00 +0800
categories: [Computer Science, Operating System]
tags: [Computer Science, Operating System]
pin: false
math: true
mermaid: true
image:
  path: https://github.com/JeongJongMun/JeongJongMun.github.io/assets/101979073/96e534ca-944f-487c-a5a1-f0998eba78bd
  lqip: data:image/webp;base64,UklGRpoAAABXRUJQVlA4WAoAAAAQAAAADwAABwAAQUxQSDIAAAARL0AmbZurmr57yyIiqE8oiG0bejIYEQTgqiDA9vqnsUSI6H+oAERp2HZ65qP/VIAWAFZQOCBCAAAA8AEAnQEqEAAIAAVAfCWkAALp8sF8rgRgAP7o9FDvMCkMde9PK7euH5M1m6VWoDXf2FkP3BqV0ZYbO6NA/VFIAAAA
  alt: Operating System
---

> 본 글은 제 개인적인 공부를 위해 작성한 글입니다. 틀린 내용이 있다면 언제든지 피드백을 주시면 감사하겠습니다. 참고로만 활용해주시길 바랍니다.

## 개요

---

![image](https://github.com/JeongJongMun/JeongJongMun.github.io/assets/101979073/ae96f309-1b59-41f1-8b84-9f4e9594f2f9)
_가상화_

가상화란 **물리적 컴퓨터 하드웨어**를 보다 효율적으로 활용할 수 있도록 해주는 **프로세스**이다.

가상화는 소프트웨어를 사용하여 프로세서, 메모리, 스토리지 등과 같은 단일 컴퓨터의 하드웨어 요소를 일반적으로 `가상 머신(Virtual Machine)`이라고 하는 다수의 가상 컴퓨터로 분할할 수 있도록 해주는 **컴퓨터 하드웨어 상의 추상화 계층을 제공**한다.

실제 컴퓨터 하드웨어에서 일부만 실행되지만, 각각의 가상 머신은 자체 `운영 체제(Operating System)`을 실행하며 `독립적인 컴퓨터`인 것처럼 작동한다.

e.g. 구글 클라우드, 네이버 클라우드들은 자신들의 데이터 센터에 있는 컴퓨팅 자원을 가상화하여, 클라우드 서비스 사용자에게 나누어 준다.

## 하이퍼바이저 Hypervisor

---

![image](https://github.com/JeongJongMun/JeongJongMun.github.io/assets/101979073/5673eab5-ff05-4308-8b9f-0eb79b415e07)
_VMware Virtualization Layer_

하이퍼바이저는 가상화 계층(Virtualization Layer)을 구현해주는 소프트웨어이다.

하드웨어 위에서 가상 머신을 생성하고, 필요한 만큼 자원을 할당해주고, 가상 머신의 요청을 처리해주는 등 가상화를 도와주는 매니저이다.

물리 하드웨어와 가상 머신의 영역을 분리하고 자신이 그 사이에서 중간 관리자, 즉 인터페이스 역할을 한다.

e.g. VMware, VirtualBox

## 가상화 VS 컨테이너화

---

![image](https://github.com/JeongJongMun/JeongJongMun.github.io/assets/101979073/dcd43847-672d-42c5-9385-7e811f9b2346)

가상화는 하드웨어에서 전체 컴퓨터를 재생성하며, 이는 다시 전체 OS를 실행한다. OS는 애플리케이션을 실행한다.

컨테이너는 **애플리케이션과 해당 애플리케이션이 의존하는 소프트웨어 라이브러리 및 환경 변수 등만을 실행**하면서 `OS 커널`**을 공유**한다.

- 컨테이너는 애플리케이션 가상화로 VM과 달리 OS를 포함하지 않고, 하이퍼바이저를 사용하지 않는다.
- 따라서 컨테이너는 VM에 비해 가볍고, 배포가 빠르고, 자원을 효율적으로 사용할 수 있다.
- 하드웨어와 호스트 OS는 그대로 둔 채 애플리케이션 영역만 캡슐화하여 격리하는 방식이다.

e.g. Docker, Kubernetes

> 커널 Kernel <br/>
> 운영체제 중 항상 메모리에 올라가 있는 운영체제의 핵심 부분 <br/>
> 하드웨어와 응용 프로그램 사이에서 인터페이스를 제공하는 역할을 하며 컴퓨터 자원들을 관리하는 역할을 한다.
> 즉, 커널은 인터페이스로써 응용 프로그램 수행에 필요한 여러가지 서비스를 제공하고, 여러가지 하드웨어(CPU, 메모리) 등의 리소스를 관리하는 역할을 한다.
{: .prompt-info }

![image](https://github.com/JeongJongMun/JeongJongMun.github.io/assets/101979073/7872ccbb-5a31-417f-91ff-820e8ef5b0ac){: width="250" height="250" .center}

<br/>
*참고*
- [https://selog.tistory.com/entry/가상화-Virtualization가상화-개념-쉽게-이해하기](https://selog.tistory.com/entry/가상화-Virtualization가상화-개념-쉽게-이해하기)
- [[[가상화] 3. 가상머신(VM)과 하이퍼바이저 쉽게 이해하기](https://selog.tistory.com/entry/가상화-가상머신VM과-하이퍼바이저-쉽게-이해하기)](https://selog.tistory.com/entry/가상화-가상머신VM과-하이퍼바이저-쉽게-이해하기)
- [https://minkwon4.tistory.com/295](https://minkwon4.tistory.com/295)