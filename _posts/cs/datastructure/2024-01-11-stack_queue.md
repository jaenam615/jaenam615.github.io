---
title: "스택과 큐"
writer: James
date: 2024-01-11 15:00:00 +0900
categories: [cs, data structure]
tags: [cs, data structure, 자료구조, 스택, 큐]
pin: false
math: true
mermaid: true
image:
  path:
---

컴퓨터에서 자주 사용되는 스택과 큐라는 자료구조에 대해서 알아보자.

## 스택

<br>
스택은 데이터의 삽입과 삭제가 한 쪽에서만 이루어지는 선형 자료구조이다.   
이러한 삽입과 삭제 구조로 인해 스택에서의 데이  터 삭제는 삽입된 순서의 역순으로 삭제되는 특징을 가지며,  
이를 <b>선입후출</b> 또는 <b>후입선출</b>, 영어로 <b>Last In First Out</b>, 줄여서 <b>LIFO</b>라고 한다.  
스택에서는 삽입과 삭제라는 용어보다는 Push와 Pop이라는 용어를 사용한다.

![pushpop](../../../assets/img/postimg/cs/datastructure/stack_queue/stack_1.png)  
스택의 푸시/팝 연산 <i>출처: https://www.programiz.com/dsa/stack</i>
{:.figcaption}

일반적으로 가장 위 데이터가 쌓여있는 위치를 Top이라고 부른다.

### Overflow/Underflow

<br>
오버플로우는 스택에 삽입 연산을 수행할 때 발생하는 문제이며, 이는 스택을 위해 할당된 저장 공간이 초과되어 더 이상 데이터를 삽입할 수 없는 상황을 말한다.

반대로, 언더플로우는 삭제 연산을 수행할 때 발생하며, 스택에 데이터가 없는 상태에서 삭제를 시도할 때 일어나는 문제이다.

## 큐

<br>
큐는 데이터의 삽입과 삭제가 서로 다른 쪽에서 이루어지는 선형 자료구조이다. 
그럼으로, 스택과는 달리 삽입된 순서로 데이터가 삭제되며 이는 <b>선입선출</b> 형식이며, 영어로 <b>First In First Out</b> 줄여서 <b>FIFO</b>라고 한다.

큐의 앞부분을 Front, 뒷부분을 Rear라고 한다.  
큐의 Front와 Rear는 모두 처음에 -1에서 시작한다(F = R = -1).  
데이터가 삽입이 될 수록 Rear는 한 칸씩 뒤로 이동하며, 데이터가 삭제될 시 Front가 뒤로 한 칸씩 움직인다.
