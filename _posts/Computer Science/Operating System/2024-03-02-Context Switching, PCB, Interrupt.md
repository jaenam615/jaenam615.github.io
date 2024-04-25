---
title: "Context Switching, PCB, Interrupt"
writer: Langerak
date: 2024-03-02 12:00:00 +0800
categories: [Computer Science, Operating System]
tags: [Computer Science, Operating System]
pin: false
math: true
mermaid: true
image:
  path: https://github.com/JeongJongMun/JeongJongMun.github.io/assets/101979073/7159ad84-0593-4d10-872c-cb1d5d32e866
  lqip: data:image/webp;base64,UklGRpoAAABXRUJQVlA4WAoAAAAQAAAADwAABwAAQUxQSDIAAAARL0AmbZurmr57yyIiqE8oiG0bejIYEQTgqiDA9vqnsUSI6H+oAERp2HZ65qP/VIAWAFZQOCBCAAAA8AEAnQEqEAAIAAVAfCWkAALp8sF8rgRgAP7o9FDvMCkMde9PK7euH5M1m6VWoDXf2FkP3BqV0ZYbO6NA/VFIAAAA
  alt: Operating System
---

> 본 글은 제 개인적인 공부를 위해 작성한 글입니다. 틀린 내용이 있다면 언제든지 피드백을 주시면 감사하겠습니다. 참고로만 활용해주시길 바랍니다.



## Context Switching

---

> 멀티프로세스 환경에서 CPU가 **어떤 하나의 프로세스를 실행하고 있는 상태**에서 **인터럽트 요청에 의해 다음 우선 순위의 프로세스가 실행되어야 할 때** 기존의 프로세스 상태 또는 레지스터 값(Context)을 저장하고 CPU가 다음 프로세스를 수행하도록 새로운 프로세스의 **상태 또는 레지스터 값(Context)을 교체하는 작업**을 `Context Switching`이라고 한다.
> 

Context Switching때 **CPU는 아무런 일을 하지 못한다.** 따라서 잦은 Context Switching은 **오버헤드**를 불러온다.

Context Switching을 하는 주체는 **OS 스케줄러**이다.

## Context

---

사용자와 다른 사용자, 사용자와 시스템 또는 기기 간의 상호작용에 영향을 미치는 사람, 장소, 개체 등의 현재 상태/상황을 규정하는 정보들을 말한다.

OS에서 Context는 **해당 프로세스를 실행하기 위한 해당 프로세스의 정보**들이다.

Context는 프로세스의 **PCB(Process Control Block)에 저장**된다.

## PCB (Process Control Block)

---

특정한 프로세스를 관리할 필요가 있는 정보를 포함하는 운영체제 커널의 자료 구조이다.

**PCB는 운영체제가 프로세스를 표현한 것이다.**

PCB는 프로세스의 중요한 정보를 포함하고 있기 때문에, 일반 사용자가 접근하지 못하도록 보호된 메모리 영역 안에 저장된다.

일반적으로 다음과 같은 정보가 포함되어 있다.

1. **프로세스 식별자 (PID)**
2. **프로세스 상태 (Process State)**
    - 생성, 준비, 실행, 대기, 완료
3. **프로그램 계수기 (Program Counter)**
    - 이 프로세스가 다음에 실행할 명령어의 주소를 가리킨다.
4. **CPU 레지스터 및 일반 레지스터**
5. **CPU 스케줄링 정보**
    - 우선 순위, 최종 실행시각, CPU 점유시간 등
6. **메모리 관리 정보**
    - 해당 프로세스의 주소 공간 등
7. **프로세스 계정 정보**
    - 페이지 테이블, 스케줄링 큐 포인터, 소유자, 부모 등
8. **입출력 상태 정보**
    - 프로세스에 할당된 입출력장치 목록, 열린 파일 목록 등

## 인터럽트 Interrupt

---

**인터럽트**는 CPU가 프로그램을 실행하고 있을 때, 주변 장치나 입출력 하드웨어 등의 장치에 **예외 상황이 발생하여 처리가 필요할 경우에 CPU에게 알려 처리할 수 있도록 하는 것**이다.

인터럽트 당한 시점의 **레지스터**와 **PC(Program Counter)**를 저장한 후 CPU의 제어를 **인터럽트 서비스 루틴**에 넘긴다.

### 왜 인터럽트를 사용하는가?

입출력 연산이 CPU 명령 수행속도보다 현저히 느리기 때문이다.

오래 걸리는 입출력 연산을 CPU가 매번 기다린다면 효율이 낮아지니, 연산 결과가 나올 때까지 다른 일을 시키고, 입출력 연산이 끝나면 CPU에게 작업 완료를 알려주는 것이 **인터럽트**이다.

인터럽트는 크게 **하드웨어 인터럽트**와 **소프트웨어 인터럽트**로 나눌 수 있다.

- **하드웨어 인터럽트**
    
    키보드, 마우스와 같은 하드웨어가 발생시킨 인터럽트를 말한다.
    
- **소프트웨어 인터럽트**
    
    프로그램이 오류를 범하여 **Exception**이 발생하거나 사용자 프로그램이 커널 함수 사용을 위해 호출하는 **System Call**이 발생하는 경우를 말한다.
    

### 인터럽트 과정

가령, 프로세스 A 실행 중 디스크에서 어떤 데이터를 읽어오라는 명령을 받았다

1. 프로세스 A는 **시스템 콜**을 통해 인터럽트를 발생시킨다.
2. CPU는 현재 진행 중인 기계어 코드를 완료한다.
3. 현재까지 수행중이었던 Context를 해당 프로세스의 PCB (Process Control Block)에 저장한다.
4. PC (Program Counter)에 다음에 실행할 명령의 주소를 저장한다.
5. 인터럽트 벡터를 읽고 ISR (Interrupt Service Routine) 주소값을 얻어 ISR로 점프하여 루틴을 실행한다.
6. 해당 코드를 실행한다.
7. 해당 일을 다 처리하면, 대피시킨 레지스터를 복원한다.
8. ISR의 끝에 IRET 명령어에 의해 인터럽트가 해제된다.
9. IRET 명령어가 실행되면, 대피시킨 PC 값을 복원하여 이전 실행 위치로 복원한다.

### 관련 용어

- **IRET (Interrupt Return) 명령어**
    
    이전 태스크로 다시 돌아가는 어셈블리 명렁어. ISR의 마지막 명령어이다.
    
- **ISR (Interrupt Service Routine)**
    
    인터럽트 핸들러 (Interrupt Handler)라고도 한다.
    
    인터럽트가 접수되면 각각의 인터럽트에 대응하는 특정 기능을 처리하는 기계어 코드 루틴이 프로그램되어 있다. (커널이 실행)
    
    $e.g.$ 키보드 자판을 눌러 키보든 이에 해당하는 인터럽트 서비스 루틴이 실행된다.
    
- **인터럽트 벡터 Interrupt Vector**
    
    인터럽트 발생 시 처리해야 할 인터럽트 핸들러(서비스 루틴)의 주소를 인터럽트 별로 보관하고 있는 테이블이다.
    
    인텔x86에서는 IDT (Interrupt Descriptor Table)라고 한다.

<br/><br/>

*참고*

- [https://jeong-pro.tistory.com/93](https://jeong-pro.tistory.com/93)

- [https://ko.wikipedia.org/wiki/문맥_교환](https://ko.wikipedia.org/wiki/문맥_교환)

- [https://ko.wikipedia.org/wiki/프로세스_제어_블록](https://ko.wikipedia.org/wiki/프로세스_제어_블록)

- [https://ko.wikipedia.org/wiki/인터럽트](https://ko.wikipedia.org/wiki/인터럽트)

- [https://ko.wikipedia.org/wiki/인터럽트_벡터](https://ko.wikipedia.org/wiki/인터럽트_벡터)

- [https://velog.io/@adam2/인터럽트#인터럽트-핸들러](https://velog.io/@adam2/인터럽트#인터럽트-핸들러)

- [https://doh-an.tistory.com/31](https://doh-an.tistory.com/31)