---
title: "동적 메모리 할당: malloc(), void*, calloc(), realloc(), free(), memset()"
writer: Langerak
date: 2024-02-28 12:00:00 +0800
categories: [Computer Science, Operating System]
tags: [Computer Science, Operating System]
pin: false
math: true
mermaid: true
image:
  path: https://github.com/JeongJongMun/JeongJongMun.github.io/assets/101979073/19965cce-9963-43d7-924c-94d42c262483
  lqip: data:image/webp;base64,UklGRpoAAABXRUJQVlA4WAoAAAAQAAAADwAABwAAQUxQSDIAAAARL0AmbZurmr57yyIiqE8oiG0bejIYEQTgqiDA9vqnsUSI6H+oAERp2HZ65qP/VIAWAFZQOCBCAAAA8AEAnQEqEAAIAAVAfCWkAALp8sF8rgRgAP7o9FDvMCkMde9PK7euH5M1m6VWoDXf2FkP3BqV0ZYbO6NA/VFIAAAA
  alt: Operating System
---

> 본 글은 제 개인적인 공부를 위해 작성한 글입니다. 틀린 내용이 있다면 언제든지 피드백을 주시면 감사하겠습니다. 참고로만 활용해주시길 바랍니다.

## 동적 메모리 할당/해제/설정 함수들

---

**데이터 영역**과 **스택 영역**에 할당되는 메모리의 크기는 **컴파일 타임(Compile Time)**에 미리 결정된다.

하지만 **힙 영역**의 크기는 프로그램이 실행되는 도중인 **런타임(Run Time)**에 사용자가 직접 결정하게 된다.

이렇게 런타임에 메모리를 할당 받는 것을 **메모리 동적 할당 (Dynamic Allocation)**이라고 한다.

- 프로그램이 실행하는 순간 프로그램이 사용할 메모리 크기를 고려해 메모리의 할당이 이루어지는 **정적 메모리 할당**과 대조적이다.

동적으로 할당된 메모리 공간은 프로그래머가 **명시적으로 해제**하거나 **가비지 컬렉션(Garbage Collection)**이 일어나기 전까지 그대로 유지된다.

C/C++와 같이 가비지 컬렉션이 없는 언어의 경우, 동적 할당하면 사용자가 해제하기 전까지는 메모리 공간이 계속 유지된다.

동적 할당은 **힙 세그먼트**에 할당하므로 프로세스가 종료되면 운영 체제에 메모리 리소스를 반납되므로 해제된다.

그러나 프로세스가 계속 실행될 때에는 동작 할당된 영역은 유지되므로 사용이 완료된 영역은 **반납**해야 한다.

## C 함수들

---

### 1. `malloc()`: Memory Allocate

프로그램이 실행 중일 때 사용자가 직접 힙 영역에 메모리를 할당한다.

```c
// malloc() 함수의 원형
#include <stdlib.h>
void *malloc(size_t size);

// malloc() 함수의 사용
int *arr = (int *)malloc(sizeof(int) * 5); // 5 크기의 int 배열. 총 20바이트 할당
```

1. **기능**
    
    메모리 동적 할당
    
2. **매개변수**
    
    할당 받고자 하는 메모리의 크기 (바이트 단위)
    
    (`size_t` 타입은 부호 없는 정수)
    
3. **반환 값**
    
    성공 시 → 할당한 메모리의 첫 번째 바이트를 가리키는 주소 값을 반환
    
    실패 시 → NULL을 반환
    

### 2. `void *` (void 포인터)

`malloc`은 단순히 메모리만 할당하는 함수이기 때문에 개발자가 어떠한 데이터 형을 저장하는지 알 수 없다.

따라서 `void *`를 반환하여 개발자가 알맞은 용도로 변환하여 사용할 수 있도록 만든 것이다.

가령, int형 데이터를 저장하기 위해서는 반환되는 `void *`를 `int *`로 변환해야 한다.

```c
// 반환되는 void*를 int*로 변환
int *i = (int*)malloc(sizeof(int));
```

![image](https://github.com/JeongJongMun/JeongJongMun.github.io/assets/101979073/0ee94ab1-676b-4b04-8c24-46a2c0b7abd1){: width="500" height="500" .center}

### 3. `free()`

힙 영역에 할당받은 메모리 공간을 다시 **운영체제로 반환**해 주는 함수

사용이 끝난 메모리를 해제해 주지 않으면, 메모리가 부족해지는 현상인 **메모리 누수(Memory Leak)**가 발생할 수 있다.

주소 `ptr`은 무조건 할당 받은 상태여야 하고, 할당된 블록의 시작을 가리켜야 한다.

- 그렇지 않다면 `free`는 아무 동작도 하지 않는다. 더 나쁜 점은 `free`는 반환 값이 없기에 에러를 찾기 어렵다.

`free()` 함수를 통해 해제한 공간의 값은 그대로 남아있고, **state**가 **allocated**에서 `freed`로 바뀌는 것일 뿐이다.

```c
// free() 함수의 원형
#include <stdlib.h>
void free(void *ptr);

// free() 함수의 사용
int *arr = (int *)malloc(5 * sizeof(int));
free(arr);
```

1. **기능**
    
    메모리 할당 해제
    
2. **매개변수**
    
    할당 해제하고자 하는 메모리의 시작 주소를 가리키는 포인터
    

### 4. `calloc()`: Clear Allocate

malloc() 함수와 마찬가지로 힙 영역에 메모리를 동적으로 할당해주는 함수이다.

`elt_size` 크기의 변수를 `elt_count`개 만큼 저장할 수 있는 메모리 공간을 할당하라는 의미를 갖는다.

```c
// calloc() 함수의 원형
#include <stdlib.h>
void* calloc(size_t elt_count, size_t elt_size)

// calloc() 함수의 사용
int *arr = (int *)calloc(5, sizeof(int));
```

1. **기능**
    
    메모리 동적 할당 및 값을 **0으로 초기화**
    
2. **매개변수**
    1. `elt_count`
        
        메모리의 개수
        
    2. `elt_size`
        
        메모리의 크기
        
3. **반환 값**
    
    성공 시 → 할당한 메모리의 첫 번째 바이트를 가리키는 주소 값을 반환
    
    실패 시 → NULL을 반환
    


> **malloc() vs calloc()**    
> **malloc()**은 할당된 공간의 값을 바꾸지 않는다.   
> **calloc()**은 할당된 공간의 값을 모두 0으로 바꾼다.   
> 배열을 할당하고 모두 0으로 초기화 할 필요가 있을 경우에 `calloc()`을 쓰면 편하다.    
> 아래 예제는 `malloc()`과 `calloc()`의 동작이 동일하다.
> ```c
> ptr_arr = (int*) malloc(arr_len * sizeof(int));
> ptr_arr = (int*) calloc(arr_len, sizeof(int));
> ```
{: .prompt-tip }

### 5. `realloc()`: Re Allocate

**이미 할당된 메모리 블록의 크기를 바꾸어 재할당 할 때 사용한다.**

만약 기존의 메모리 위치에 충분한 공간이 있다면 바로 이어서 추가 메모리 공간을 할당한다.

아니라면 메모리의 다른 공간에 기존의 데이터를 복사한 후, 이어서 추가 메모리 공간을 할당하게 된다.

```c
// realloc() 함수의 원형
#include <stdlib.h>
void *realloc(void *ptr, size_t size);
```

1. **기능**
    
    이미 할당된 메모리 블록의 크기를 변경
    
2. **매개변수**
    1. `*ptr`
        
        바꾸고자 하는 메모리 공간을 가리키는 포인터 
        
        (NULL이 전달되면 `malloc()`과 같은 동작을 수행)
        
    2. `size`
        
        해당 메모리 공간에 재할당 할 크기
        

### 6. `memset()`: Memory Setting

메모리의 값을 원하는 크기만큼 **특정 값으로 설정**하는 함수이다.

1 바이트 단위로 설정하기 때문에 `0`이 아니거나 `char 타입`이 아닌 값을 넣으면 제대로 된 값이 나오지 않는다!

```c
// memset() 함수의 원형
#include <string.h>
void* memset(void* _Dst, int _Val, size_t _Size);

// memset() 함수의 사용
int arr[5];
memset(arr, 0, sizeof(int) * 5);
```

1. **기능**
    
    메모리의 값을 원하는 크기만큼 특정 값으로 설정
    
2. **매개변수**
    1. `_Dst`
        
        변경하고자 하는 메모리의 시작 주소를 가리키는 포인터
        
    2. `_Val`
        
        설정하고 하는 값
        
    3. `_Size`
        
        길이
        
        보통 **길이 * sizeof(데이터타입)**의 형태로 작성
        
3. **반환 값**
    
    성공 시 → 첫 번째 인자로 들어간 `_Dst` 반환
    
    실패 시 → `NULL` 반환


<br/> <br/>

*참고*

- [[C] malloc, calloc, realloc을 이용한 메모리 동적 할당](https://dsnight.tistory.com/51)

- [[Malloc-lab] malloc, calloc, realloc, free, brk, sbrk, mmap 기본 개념 정리](https://bo5mi.tistory.com/162)

- [[C언어/C++] 메모리 초기화 memset 함수 사용법 & 예제](https://coding-factory.tistory.com/673)

- [동적 메모리 할당](https://ko.wikipedia.org/wiki/동적_메모리_할당)

- CS:APP