---
layout: post
title: "스택 & 큐"
date: 2024-02-07 20:00:00 +0900
categories: [cs,datastructure]
tags: []
---

## 스택
## 큐
{:toc .large-only}

컴퓨터에서 자주 사용되는 스택과 큐라는 자료구조에 대해서 알아보자.

## 스택

스택은 데이터의 삽입과 삭제가 한 쪽에서만 이루어지는 선형 자료구조이다.   
이러한 삽입과 삭제 구조로 인해 스택에서의 데이터 삭제는 삽입된 순서의 역순으로 삭제되는 특징을 가지며,  
이를 <b>선입후출</b>, 또는 <b>후입선출</b>, 영어로 <b>Last In First Out</b>, 줄여서 <b>LIFO</b>라고 한다.  
스택에서는 삽입과 삭제라는 용어보다는 Push와 Pop이라는 용어를 사용한다. 

![pushpop](../../../assets/img/postimg/cs/datastructure/stack_queue/stack_1.png)
스택의 푸시/팝 연산 <i>이미지 출처: https://www.programiz.com/dsa/stack</i>
{:.figcaption}

## 큐