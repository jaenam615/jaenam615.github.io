---
title: "시스템 콜 System Call"
writer: Langerak
date: 2024-02-14 12:00:00 +0800
categories: [Computer Science, Operating System]
tags: [Computer Science, Operating System]
pin: false
math: true
mermaid: true
image:
  path: https://github.com/JeongJongMun/JeongJongMun.github.io/assets/101979073/17607422-85bd-4fc2-b0bb-6465ab4d41fb
  lqip: data:image/webp;base64,UklGRpoAAABXRUJQVlA4WAoAAAAQAAAADwAABwAAQUxQSDIAAAARL0AmbZurmr57yyIiqE8oiG0bejIYEQTgqiDA9vqnsUSI6H+oAERp2HZ65qP/VIAWAFZQOCBCAAAA8AEAnQEqEAAIAAVAfCWkAALp8sF8rgRgAP7o9FDvMCkMde9PK7euH5M1m6VWoDXf2FkP3BqV0ZYbO6NA/VFIAAAA
  alt: Operating System
---

> 본 글은 제 개인적인 공부를 위해 작성한 글입니다. 틀린 내용이 있다면 언제든지 피드백을 주시면 감사하겠습니다. 참고로만 활용해주시길 바랍니다.

## 시스템 콜(System Call)

---

**시스템 호출** 또는 **시스템 콜(System Call),** 간단히 **시스콜(Syscall)**은 **응용 프로그램**이 **운영체제**의 **커널**에게 서비스를 요청하거나 하드웨어와 상호작용하기 위한 **인터페이스**이다.

보통 C나 C++와 같은 고급 언어로 작성된 프로그램들은 직접 시스템 콜을 사용할 수 없기 때문에 **API**를 통해 시스템 콜에 접근한다.

시스템 콜이 생성되면 CPU는 **사용자 모드**에서 **커널 모드**로 전환하고 시스템 콜에 의해 정의된 특정 작업을 수행한다.

<br/>

운영 체제의 구성 상, **커널과 응용 프로그램은 CPU의 권한 수준이나 하드웨어 접근 능력이 다르다.**

**커널은 운영체제의 핵심** 부분이며 메모리에 물리 주소가 일치하는 링커 구조를 가지고 특정 메모리 위치에서 동작한다.

이때 커널의 기계어 코드에서 사용하는 모든 주소는 **물리 주소로 고정**되어 컴파일되고 로드되어 실행된다.

따라서 커널은 부팅 과정에서 전체 메모리 자원 중 일정 부분을 점유하여 동작하고 인터럽트 등 **CPU 시스템의 모든 하드웨어 접근이 가능**하다.

<br/>

하지만 **응용 프로그램**은 사용자에게 서비스를 제공하는 프로그램이며 커널이 제공하는 자원을 사용하므로 상황에 따라 다른 위치의 메모리를 점유하고 실행된다.

응용 프로그램은 사용자 요청에 따라 실행되는 프로그램이므로 **물리 주소를 확정할 수 없다.**

따라서 응용 프로그램은 **MMU(Memory Management Unit)**를 통해 **논리 주소**를 물리 주소로 변환한다.

그리고 응용 프로그램은 CPU의 권한 수준이 사용자 공간(User Space)에서 동작하며 특정한 기계어 명령어 실행이 불가능하다.

이와 같은 상황에서 응용 프로그램이 파일 시스템을 사용하는 등 기타 여러 상황을 커널에 의존해야만 한다.

<br/>

**응용 프로그램에서 커널의 서비스를 사용하는 방법이 시스템 호출이다.**

| 특징 | 커널 | 응용 프로그램 |
| --- | --- | --- |
| 권한 수준 | 높음 (시스템 공간) | 낮음 (사용자 공간) |
| 하드웨어 접근 | 직접 가능 | 커널을 통해 간접적으로 가능 |
| 메모리 위치 | 물리 주소 사용 | 논리 주소 사용 (MMU를 통해 변환) |
| 개발 방식 | 물리 주소 기반 컴파일 | 논리 주소 기반 컴파일 |
| 실행 환경 | 특정 메모리 위치에서 고정 | 상황에 따라 메모리 위치 변경 |
| 시스템 호출 | 사용 가능 | 시스템 호출을 통해 커널 서비스 이용 |

<br/>

## 시스템 콜의 특징

---

1. 다양한 작업을 수행하기 위한 다양한 유형의 시스템 콜이 있다.
    
    가령, 파일 시스템 접근, 네트워크 통신, 메모리 관리, 프로세스 관리 등 **하드웨어 자원에 접근해서 작업을 수행**할 때 시스템 콜을 호출한다.
    
2. 시스템 콜은 커널이 제공하는 시스템 자원의 사용과 연관된 함수라고 볼 수 있다. 일반적으로 고급 프로그래밍 언어를 사용할 때 제공하는 **라이브러리 함수를 통해 호출**되기 때문이다. 라이브러리 함수는 사용자 모드에서 실행되며, 해당 기능을 수행하기 위해 운영체제의 커널 모드로 전환된다.
    
    대부분의 프로그래밍 언어에서는 시스템 콜을 추상화하여 **사용하기 쉽게 라이브러리 함수 형태로 제공**한다.
    
3. 시스템 콜은 운영체제의 핵심적인 기능에 접근하는 역할을 하므로, 보안과 안정성이 매우 중요하다. **운영체제의 자원과 기능은 시스템 콜을 통해서만 제공**한다.

<br/>

## 시스템 콜의 간략한 과정

---
![Image](https://github.com/JeongJongMun/JeongJongMun.github.io/assets/101979073/17607422-85bd-4fc2-b0bb-6465ab4d41fb)
_System Call Process_
1. 사용자 모드에 있는 응용 프로그램이 시스템 콜을 호출하여 커널의 기능을 사용할 수 있게 한다.
2. 시스템 콜을 호출하면 사용자 모드에서 커널 모드로 전환된다.
3. 커널에서 시스템 콜을 처리하면 커널 모드에서 사용자 모드로 되돌아가 작업을 계속한다.

<br/>

## 시스템 콜의 유형

---

1. **프로세스 제어 (Process Control)**
    1. `fork()` - 프로세스를 복제한다.
    2. `execve()` - 새로운 프로그램을 실행한다.
    3. `wait()` - 자식 프로세스가 종료될 때까지 대기한다.
    4. `exit()` - 프로세스를 종료한다.
    5. `kill()` - 프로세스를 강제 종료한다.
2. **파일 조작 (File Manipulation)**
    1. `open()` - 파일을 열고 읽거나 쓰기 위한 준비를 한다.
    2. `read()` - 열린 파일에서 데이터를 읽는다.
    3. `write()` - 열린 파일에 데이터를 쓴다.
    4. `close()` - 열린 파일에 데이터를 쓴다.
3. **장치 관리 (Device Manipulation)**
    1. `open()` - 파일 또는 장치를 열고 읽거나 쓰기 위한 준비를 한다.
    2. `read()` - 열린 파일 또는 장치에서 데이터를 읽는다.
    3. `write()` - 열린 파일 또는 장치에 데이터를 쓴다.
    4. `close()` - 열린 파일 또는 장치에 데이터를 쓴다.
4. **정보 유지 (Information Maintenance)**
    1. `getpid()` - 현재 프로세스의 ID를 반환한다.
    2. `getuid()` - 현재 프로세스의 사용자 ID를 반환한다.
    3. `time()` - 현재 시간을 반환한다.
    4. `clock()` - 프로세스가 실행된 시간을 반환한다.
5. **통신 (Communication)**
    1. `socket()` - 소켓을 생성한다.
    2. `bind()` - 소켓을 주소에 연결한다.
    3. `listen()` - 소켓을 연결 요청을 수락하도록 설정한다.
    4. `accept()` - 연결 요청을 수락하고 새로운 소켓을 반환한다.
    5. `connect()` - 서버에 연결한다.
<br/> <br/>

*참고*
- [https://jerryjerryjerry.tistory.com/172](https://jerryjerryjerry.tistory.com/172)
- [https://cstaleem.com/user-mode-vs-kernel-mode-trap-vs-interrupt](https://cstaleem.com/user-mode-vs-kernel-mode-trap-vs-interrupt)
- [https://ko.wikipedia.org/wiki/시스템_호출](https://ko.wikipedia.org/wiki/시스템_호출)