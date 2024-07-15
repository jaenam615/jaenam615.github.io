---
title: "Direct Memory Access"
writer: James
date: 2024-06-27 22:00:14 +0900
categories: [cs, os]
tags: [cs, os, dma, 운영체제]
pin: false
math: true
mermaid: true
image:
  path: https://img1.daumcdn.net/thumb/R1280x0/?scode=mtistory2&fname=https%3A%2F%2Fblog.kakaocdn.net%2Fdn%2Fc40xHV%2Fbtq1sBMm3S9%2FK4dlqveKubQJNSfkBcV0tK%2Fimg.png
---

## DMA  

DMA란 CPU를 거치지 않고 주변 장치가 메모리와 직접 데이터를 주고받을 수 있게 하는 기술이다. 이를 가능케 하는 것은 DMA 컨트롤러로, 주변 장치는 DMA 컨트롤러를 이용해서 메모리에 직접 접근하여 전송이 가능하다. 

빠른 CPU에 비해 느린 입출력 장치들은 전체 컴퓨터 성능을 저하시킬 수 밖에 없다. 즉, CPU는 이 느린 입출력 장치들을 기다려야 하는데, I/O장치에 직접 메모리를 데이터 교환 권한을 줌으로써 CPU는 대기하지 않고 원래 해야하는 일을 진행할 수 있다.  

## 구조 

I/O 컨트롤러는 일반적으로 다음과 같은 구성 요소로 이루어진다:

- 제어 레지스터: 장치의 상태를 설정하고 명령을 전달하는 레지스터
- 데이터 레지스터: 전송할 데이터를 저장하는 레지스터
- 상태 레지스터: 장치의 현재 상태를 나타내는 레지스터
- 버퍼 메모리: 데이터 전송을 효율적으로 하기 위해 사용되는 임시 저장 공간

## 작동 방식 

DMA를 이용하여 입출력을 처리하는 과정은 다음과 같다:  

1. CPU는 입출력에 필요한 정보, 즉 소스의 위치와 양, 그리고 목적지에 필요한 정보를 DMA 컨트롤러에 전달한다. 이 때 소스와 목적지 중 하나는 메모리일 것이다.  
2. DMA 컨트롤러는 소스에서 목적지로 데이터를 보내도록 I/O 컨트롤러에 요청하고, 이 과정을 CPU가 처음에 지시한 양이 될 때까지 반복한다.  
3. 원하는 양의 입출력이 끝나면 DMA 컨트롤러는 인터럽트 컨트롤러에 신호를 보내어 인터럽트를 발생, CPU에 입출력 작업이 모두 끝났음을 알린다.  
