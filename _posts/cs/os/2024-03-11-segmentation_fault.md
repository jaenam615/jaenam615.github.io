---
title: "세그멘테이션 오류"
writer: James
date: 2024-03-11 20:33:00 +0900
categories: [cs, os]
tags: [cs, os, 운영체제]
pin: false
math: true
mermaid: true
image:
  path:
---

## 세그멘테이션 오류

크래프톤 정글에서 C관련 과제들을 할 때마다 단골로 등장하던 오류이다.  
놀랍게도 해당 오류가 발생할 때 큰 의의를 두지 않았고 단지 '내가 무언가 잘못하고 있구나' 정도로 생각해왔다.

### 개요

세그멘테이션 오류는 프로그램이 권한이 없는 메모리 공간에 접근을 시도할 때,  
또는 잘못된 접근 방식으로 <i>(예: READ-ONLY공간에 WRITE를 시도할 때)</i> 일어나는 오류이다.  
이렇게 오류를 일으킴으로 사용자가 메모리를 오염시키는 것을 막아주고,  
디버깅 난이도가 높은 메모리 버그를 사용자에게 알려주는 역할도 한다.

세그멘테이션 오류가 발생하면 사용자는

### 흔한 세그멘테이션 오류 발생 상황

흔하게 세그멘테이션 오류를 발생시키는 상황에는 다음이 있다:

1. 문자열 리터럴 조작

   > 문자열 리터럴은 메모리의 READ-ONLY 부분에 저장되어 있기 때문에,  
   > 문자열 리터럴의 값을 변경시키려고 하면 세그멘테이션 오류가 발생한다.

2. 해제(free)된 메모리 영역 접근

   > 포인터가 가리키는 메모리 공간이 반환된 이후에 역참조가 일어나면 세그멘테이션 오류가 발생한다.  
   > 이러한 포인터는 '댕글링 포인터' 또는 '허상 포인터' 라고 한다.

3. 배열의 크기를 벗어난 인덱스 접근

   > 크기가 3인 배열 arr[2]가 있다고 가정할 때, arr[3]에 접근을 시도하면 오류가 발생한다.

4. scanf()의 부적절한 사용

   > scanf 함수는 변수의 주소를 입력값으로 받는다.
   > 변수의 주소가 아닌 값 그 자체를 직접적으로 받으면 오류가 발생한다.

5. 스택 오버플로우

   >

6. 초기화 되지 않은 포인터 역참조
   >

<hr>
