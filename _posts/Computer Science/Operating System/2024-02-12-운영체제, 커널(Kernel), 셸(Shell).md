---
title: "운영체제, 커널(Kernel), 셸(Shell)"
writer: Langerak
date: 2024-02-12 12:00:00 +0800
categories: [Computer Science, Operating System]
tags: [Computer Science, Operating System]
pin: false
math: true
mermaid: true
image:
  path: https://github.com/JeongJongMun/JeongJongMun.github.io/assets/101979073/2ccedd3a-2fce-4108-b88b-d5694e98d719
  lqip: data:image/webp;base64,UklGRpoAAABXRUJQVlA4WAoAAAAQAAAADwAABwAAQUxQSDIAAAARL0AmbZurmr57yyIiqE8oiG0bejIYEQTgqiDA9vqnsUSI6H+oAERp2HZ65qP/VIAWAFZQOCBCAAAA8AEAnQEqEAAIAAVAfCWkAALp8sF8rgRgAP7o9FDvMCkMde9PK7euH5M1m6VWoDXf2FkP3BqV0ZYbO6NA/VFIAAAA
  alt: Operating System
---

> 본 글은 제 개인적인 공부를 위해 작성한 글입니다. 틀린 내용이 있다면 언제든지 피드백을 주시면 감사하겠습니다. 참고로만 활용해주시길 바랍니다.

## 운영체제와 응용프로그램

---

```
운영체제(OS): Window, UNIX, Linux, Mac OS 등
응용 프로그램: 한글, 엑셀, 메모장 등 컴퓨터 내의 다양한 프로그램
```
![image](https://github.com/JeongJongMun/JeongJongMun.github.io/assets/101979073/aa017608-3966-46d3-8669-3bfe1430eaf1){: width="250" height="250" .center}
_운영체제와 응용프로그램_

하드웨어를 관리하고, 컴퓨터 시스템의 자원들을 효율적으로 관리하며, 응용 프로그램과 하드웨어 간의 인터페이스로써 다른 응용 프로그램들이 유용한 작업을 할 수 있도록 환경을 제공해준다.

즉, 사용자가 컴퓨터를 편리하고 효과적으로 사용할 수 있도록 환경을 제공해주는 **시스템 소프트웨어**

### 운영체제의 목적

1. 사용자에게 컴퓨터의 프로그램을 쉽고 효율적으로 실행할 수 있는 환경 제공
2. 컴퓨터 시스템 하드웨어 및 소프트웨어 자원을 여러 사용자 간에 효율적으로 할당, 관리, 보호
3. 운영체제는 제어 프로그램으로서 사용자 프로그램의 오류나 잘못된 자원 사용을 감시하는 것과 입출력 장치 등의 자원에 대한 연산과 제어를 관리

## 커널 Kernel

---

![image](https://github.com/JeongJongMun/JeongJongMun.github.io/assets/101979073/6fffe91c-e8ae-4dbc-96e8-b6817fda60f0){: width="250" height="250" .center}
_커널이 응용 소프트웨어를 컴퓨터 하드웨어와 연결한다._

컴퓨터와 전원을 켜면 운영체제는 이와 동시에 수행된다. 소프트웨어가 컴퓨터 시스템에서 수행되기 위해서는 메모리에 그 프로그램이 올라가 있어야 한다. 마찬가지로 운영체제 자체도 소프트웨어로서 전원이 켜짐과 동시에 메모리에 올라가야 한다.

하지만 운영체제처럼 규모가 큰 프로그램이 모두 메모리에 올라간다면 한정된 메모리 공간의 낭비가 심할 것이다.

따라서 운영체제 중 항상 필요한 부분 만을 전원이 켜짐과 동시에 메모리에 올려놓고, 그렇지 않은 부분은 필요할 때 메모리에 올려서 사용하게 된다. 이때 메모리에 상주하는 운영체제의 부분을 `커널`이라고 한다.

> 즉, 커널은 메모리에 상주하는 부분으로써 운영체제의 핵심 부분을 뜻한다.   
> (그래서 보통 ‘운영체제’ 라고 하면 커널을 뜻하기도 한다.)

## 셸 Shell

---

셸은 사용자와 운영체제 간의 인터페이스 역할을 수행한다. 여기서 인터페이스란 사용자가 운영체제에게 정보, 명령, 신호 등을 교환하기 위한 규칙이다. 사용자가 셸을 통해 운영체제에게 명령을 입력하고, 그 결과를 확인할 수 있다.

셸은 **명령어 해석기(Parser)**와 **실행기(Executor)**로 구성되어 사용자의 명령을 분석하고 실행한다. 셸은 텍스트 기반의 `CLI(Command Line Interface)`와 그래픽 기반의 `GUI(Graphical User Interface)`가 있다.

사용자는 셸을 통해 **운영체제와 상호작용하고 작업을 수행**할 수 있으며, 셸을 통해 커널을 호출해 운영체제 기능과 서비스를 조작할 수 있다.

일반적으로 운영체제에는 여러 종류의 셸이 제공되며, 예를 들어 유닉스와 리눅스에서는 `Bash`, 윈도우에서는 `Command Prompt`와 `PowerShell`이 대표적인 셸이다.

참고

- [https://github.com/JulSaMo/CS-start/blob/main/Computer%20Science/Operating%20System/SystemCall(%EC%8B%9C%EC%8A%A4%ED%85%9C%EC%BD%9C%2C%20%EC%8B%9C%EC%8A%A4%ED%85%9C%ED%98%B8%EC%B6%9C).md](<https://github.com/JulSaMo/CS-start/blob/main/Computer%20Science/Operating%20System/SystemCall(%EC%8B%9C%EC%8A%A4%ED%85%9C%EC%BD%9C%2C%20%EC%8B%9C%EC%8A%A4%ED%85%9C%ED%98%B8%EC%B6%9C).md>)
- [https://ko.wikipedia.org/wiki/%EC%9A%B4%EC%98%81%EC%B2%B4%EC%A0%9C](https://ko.wikipedia.org/wiki/%EC%9A%B4%EC%98%81%EC%B2%B4%EC%A0%9C)
- [https://gyoogle.dev/blog/computer-science/operating-system/Operation%20System.html](https://gyoogle.dev/blog/computer-science/operating-system/Operation%20System.html)
- [https://goodmilktea.tistory.com/23](https://goodmilktea.tistory.com/23)
- [https://jerryjerryjerry.tistory.com/172](https://jerryjerryjerry.tistory.com/172)
