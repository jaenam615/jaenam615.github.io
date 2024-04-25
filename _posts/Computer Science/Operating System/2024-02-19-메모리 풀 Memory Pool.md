---
title: "메모리 풀 Memory Pool"
writer: Langerak
date: 2024-02-19 12:00:00 +0800
categories: [Computer Science, Operating System]
tags: [Computer Science, Operating System]
pin: false
math: true
mermaid: true
image:
  path: https://github.com/JeongJongMun/JeongJongMun.github.io/assets/101979073/eaf4396f-45d7-4022-a961-77add794a10d
  lqip: data:image/webp;base64,UklGRpoAAABXRUJQVlA4WAoAAAAQAAAADwAABwAAQUxQSDIAAAARL0AmbZurmr57yyIiqE8oiG0bejIYEQTgqiDA9vqnsUSI6H+oAERp2HZ65qP/VIAWAFZQOCBCAAAA8AEAnQEqEAAIAAVAfCWkAALp8sF8rgRgAP7o9FDvMCkMde9PK7euH5M1m6VWoDXf2FkP3BqV0ZYbO6NA/VFIAAAA
  alt: Operating System
---

> 본 글은 제 개인적인 공부를 위해 작성한 글입니다. 틀린 내용이 있다면 언제든지 피드백을 주시면 감사하겠습니다. 참고로만 활용해주시길 바랍니다.

## 메모리 풀

---

`메모리 풀`이란 **메모리 단편화를 방지하기 위해 사용되는 기술**이다.

메모리 풀은 **이미 할당된 메모리 덩어리**로, 필요에 따라 할당 및 할당 해제할 수 있는 작은 고정 크기 블록으로 나뉜다. 

필요한 메모리 공간을 필요한 크기/개수만큼 사용자가 직접 지정하여 미리 **할당 받아 놓고 필요할 때마다 사용하고 반납하는 기법**이다.

객체의 메모리 할당/해제가 잦은 프로그램에서 성능을 개선하고 메모리 단편화를 줄이는데 유용한 기술이다.

### 장점

1. 메모리 풀의 블록들은 일반적으로 모두 동일한 크기이므로 메모리를 쉽게 관리할 수 있고 `외부 단편화`가 발생하지 않는다.
2. 필요한 크기만큼 할당을 해놓기 때문에 `내부 단편화` 또한 생기지 않는다.
3. 큰 메모리 블록을 미리 할당하고 이를 고정 크기 블록으로 나누기 때문에 메모리를 반복적으로 요청하고 해제하는 `오버헤드`를 피할 수 있다.
4. 메모리 할당을 그룹화하여 `캐시의 지역성을 개선`함으로써 프로그램의 데이터 접근 패턴을 개선하고 `캐시 미스`를 줄일 수 있다는 점이 있다.

### 단점

1. `너무 작은 메모리 풀`을 사용하는 경우 메모리가 부족하여 충돌 가능하다.
2. `너무 큰 메모리 풀`을 사용하는 경우 메모리 낭비 가능하다.
3. 운영 체제의 메모리 관리에 의존하지 않고 **프로그램이 자체적으로 메모리를 관리**하기 때문에 메모리 관련 문제를 `디버깅`하기 어렵다.
4. 메모리 풀을 할당해놓으면 사용하지 않는 순간에도 계속 할당해놓으므로 `메모리 누수`가 있는 방식이다.
<br/> <br/>

*참고*
- [https://shung2.tistory.com/928](https://shung2.tistory.com/928)

- [https://jeong-pro.tistory.com/91](https://jeong-pro.tistory.com/91)