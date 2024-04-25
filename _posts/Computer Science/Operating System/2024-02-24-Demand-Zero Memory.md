---
title: "Demand-Zero Memory"
writer: Langerak
date: 2024-02-24 12:00:00 +0800
categories: [Computer Science, Operating System]
tags: [Computer Science, Operating System]
pin: false
math: true
mermaid: true
image:
  path: https://github.com/JeongJongMun/JeongJongMun.github.io/assets/101979073/a07765eb-4a1e-41e6-a742-b2ee463bffe4
  lqip: data:image/webp;base64,UklGRpoAAABXRUJQVlA4WAoAAAAQAAAADwAABwAAQUxQSDIAAAARL0AmbZurmr57yyIiqE8oiG0bejIYEQTgqiDA9vqnsUSI6H+oAERp2HZ65qP/VIAWAFZQOCBCAAAA8AEAnQEqEAAIAAVAfCWkAALp8sF8rgRgAP7o9FDvMCkMde9PK7euH5M1m6VWoDXf2FkP3BqV0ZYbO6NA/VFIAAAA
  alt: Operating System
---

> 본 글은 제 개인적인 공부를 위해 작성한 글입니다. 틀린 내용이 있다면 언제든지 피드백을 주시면 감사하겠습니다. 참고로만 활용해주시길 바랍니다.



## Demand-Zero Memory

---

> "we will assume that the heap is an area of **demand-zero memory** that begins immediately after the uninitialized data area and grows upward." - CS:APP <br/>
> 우리는 힙은 초기화되지 않은 데이터 영역 바로 뒤에서 시작하여 위쪽으로 증가하는 **Demand-Zero Memory** 영역이라고 가정합니다.

`Demand-Zero Memory`은 말 그대로 필요할 때(**Demand**) 할당하고 0으로 초기화해주는(**Zero**) 가상 메모리 관리 기법 중 하나이다.

리눅스의 메모리 시스템은 최대한 게으른 방식(Lazy)으로 작동하도록 되어있다.

게으르단 뜻은, 어떤 자원을 요청하거나 동작을 요청했을 때, **그것이 정말 필요해질 때까지 실제 자원을 할당하거나 동작을 실행하지 않는 것**을 의미한다.

프로그램이 커널에게 메모리를 할당해 달라고 요청하면(`sbrk`) 커널은 거의 아무것도 하지 않고(특정한 가상 메모리 영역이 할당되었다는 최소한의 표시만 해두고) 우리에게 할당이 끝났다고 알려준다.

즉, 특정 가상 메모리 영역이 할당되었다는 사실만 표시해두고, 실제 메모리는 할당되지 않은 상태로 남아있다.

이렇게 되면 프로그램은 메모리가 할당된 줄 알고 해당 주소에 쓰기/읽기를 수행할텐데, 처음 수행하는 순간 실제 메모리(페이지)가 할당되어 있지 않기 때문에 Page Fault가 발생할 것이고, 

그제서야 커널은

1. 사용하지 않는 메모리(페이지)를 할당하고
2. 그 페이지를 0으로 채운 뒤에
3. 유저가 원래 하려고 했던 명령어를 다시 실행한다.

이러한 방식을 통해 메모리 사용 효율성을 향상 시키고, 시스템 자원을 절약한다.

하지만, 페이지가 실제로 사용될 때마다 Page Fault 처리가 필요하므로 오버헤드가 발생될 수 있다.

또한, 페이지를 0으로 초기화하는 과정에서 미세한 지연이 발생할 수 있다.

<br/> <br/>

*참고*

- [https://straw961030.tistory.com/231](https://straw961030.tistory.com/231)

- [https://velog.io/@min49590/CS-demand-zero-memory](https://velog.io/@min49590/CS-demand-zero-memory)