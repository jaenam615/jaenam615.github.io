---
title: "C++ 언어"
writer: James
date: 2024-10-13 01:00:00 +0900
categories: [language, cpp]
tags: [langauge, cpp]
pin: false
math: true
mermaid: true
image:
  path: https://fiverr-res.cloudinary.com/images/q_auto,f_auto/gigs/255857524/original/c22824fa23eacc7840c2b185a9a7676419fb53ad/do-c-and-c-plus-plus-projects-and-code.png
---

## C++  

C++는 C 언어의 기본 문법을 기반으로 하여 일부 기능을 확장한 프로그래밍 언어이다. 주요 확장 내용으로는 객체 지향 프로그래밍에 필요한 클래스, 상속, 동적 바인딩과 같은 기능이 있으며, C++는 템플릿과 예외 처리 기능도 제공하여 코드의 재사용성과 오류 관리가 용이하다.  

## C++ 프로그램의 작성 및 빌드  

크게 두가지 유형의 소스 파일이 있는데, 소스 프로그램 파일과 헤더 파일이다.  

- 소스 프로그램 파일 : 처리하고자 하는 작업을 수행하는 C++ 프로그램 명령어들을 담고 있는 파일로, 확장자 .cpp를 사용한다.
- 헤더 파일 : 클래스, 함수의 원형, 매크로, 전역변수, 상수 등 여러 소스 파일에 공통적으로 선언되는 내용을 담고 있는 파일이며 #include라는 선햋어리기 지시어에 의해 소스 프로그램 파일에 삽입되어 함께 컴파일된다. 확장자로는 .h, .hpp등을 사용한다.  

## 선행처리

C++ 프로그램을 컴파일하기 전에 소스 프로그램을 가공하여 컴파일러가 실제로 번역할 소스 프로그램을 만든다. 선행처리기 지시어 `#`로 처리를 지시할 수 있으며, 대표적인 선행처리로는 헤더파일을 삽입하는 `#include`, 매크로를 선언 및 해제하는 `#define, #undef`, 조건부로 컴파일하는 `#if, #endif, #ifdef, #ifndef`가 있다.  

C++ 표준 라이브러리에서 제공되는 헤더 파일의 경우 `#include <iostream>`과 같이 선행처리한다.  

## 입출력 스트림

`std::cout` 객체 : 표준 출력 스트림 객체로, 데이터를 문자열로 변환하여 출력한다.  

`<<` : 출력 연산자(삽입 연산자)로 여러 개를 사용할 수 있다.  

`std:cin` 객체 : 표준 입력 스트림 객체로, 문자열을 입력 변수의 자료형의 값으로 변환하여 입력한다.  

`>>` : 입력 연산자(추출 연산자)로 이 또한 여러 개 사용할 수 있다.  

예: 
```cpp
#include <iostream>

int main(){
  int a = 10;
  char str[100];

  std::cout << "a의 값은";
  std::cout << a << "입니다." << std::endl;

  std::cin >> a >> str;
  std::cout << a << str;

  return 0;
}
```

출력 및 입력:  
```shell
a의 값은 10 입니다.
input> 20 james had a little lamb
20 james
```

## 명칭공간  

특정한 명칭들이 인식되는 프로그램의 부분으로, 다른 영역의 명칭 선언과 무관하게 명칭고간 내에서 자유롭게 명칭을 선언하여 사용할 수 있다. 단, 명칭공간 외부에서 해당 변수를 호출하기 위해서는 소속 명칭공간을 지정해서 사용해야 한다.  

```cpp
namespace myNSpc {
  int count; 
}

myNSpc::count = 0;
```

동일한 명칭이라도, 서로 다른 명칭공간에서 정의되었다면 별개의 변수로 구분하며, 특정 명칭공간에 속하지 않는 기본 명칭공간이 전역 명칭공간도 있다.  

```cpp
#include <iostream>

namespace NameSpace1 { int a = 10; }
namespace NameSpace2 { int a = 20; }
int a = 30;
namespace NameSpace1 { int b = 50; }

int main(){
  int a = 40;
  std::cout << NameSpace1::a << std::endl;
  std::cout << NameSpace2::a << std::endl;
  std::cout << ::a << std::endl;
  std::cout << a << std::endl;
  std::cout << NameSpace1::b << std::endl;
  return 0;
}
```

```shell
10
20
30
40
50
```

> 만약 int a = 40이라는 지역변수 선언이 없다면 전역변수 a를 출력한다.  

특정 명칭공간이나 명칭공간 내의 특정 이름을 자주 사용하는 경우, `using`을 써서 명칭공간 지정을 간소화할 수 있다.  

```cpp
using namespace std;

using std::cout;
using std::endl;
```

javascript에서의 `import express from "express"`와 `import { Request, Response } from "express"`와 비슷한 느낌이다. 위에 둔 출력 프로그램을 예시로 수정을 해보면 다음과 같다:  

```cpp
#include <iostream>

using namespace std; // using문 추가
namespace NameSpace1 { int a = 10; }
namespace NameSpace2 { int a = 20; }
int a = 30;
namespace NameSpace1 { int b = 50; }

int main(){
  int a = 40;
  cout << NameSpace1::a << endl;
  cout << NameSpace2::a << endl;
  cout << ::a << endl;
  cout << a << endl;
  cout << NameSpace1::b << endl;
  return 0;
}
```

## 키워드와 식별자  

키워드란 C++언어에서 미리 용도를 정해 놓은 단어로, 반드시 정해진 용도로만 사용되어야 한다. 예를 들어, 위에서 살펴본 `using`, `namespace`, `return`등이 키워드이다.  

식별자란 여러 가지 대상을 구분하기 위해 만든 이름으로, 변수, 함수, 클래스 등의 이름을 의미한다. 식별자 명명에는 다음의 규칙이 있다:  

- 첫 자는 비 숫자 문자를 사용 (영문 대소문자 또는 '_')  
- 이후의 문자는 비 숫자 문자와 숫자 사용 가능  
- 키워드 사용 불가  

> 식별자 예시
>> myname : lower case
>> my_name : snake case
>> myName : camel case
>> MyName : pascal case
>> MYNAME : upper case
>> name3 : 숫자를 포함한 식별자  

## 자료형  

### 기본 자료형  

![types](/images/2024-10-13-23-37-23.png)  

정수 자료형은 고정 소수점 방식으로 숫자를 표현한다. 
- 문자를 표현하는 자료형인 char과 정수를 표현하는 자료형인 int, short, long, long long이 있으며, int는 현재 기준 32비트로 사용된다.  

signed와 unsigned, 즉 부호 및 비부호 개념이 있는데, 이 떄문에 자리수는 unsigned가 더 많지만 signed는 음수를 표현할 수 있다.  

- bool 또한 정수 자료형이다.  

실수 자료형은 부동소수점 방식으로 숫자를 표현한다.  
- float: 부호 1비트, 지수부 8비트, 가수부 23비트로 구성
- double: 부호 1비트, 지수부 11비트, 가수부 52비트로 구성 
  
### 복합 자료형  

- 배열 (array)  
- 구조체 (struct) 
- 클래스 (class)
- 열거형 (enum)
- 공용체 (union)
- 포인터, 참조 (*, &)

## 변수  

프로그램이 실행되는 동안 기억하고 있어야 하는 값들을 저장하는 메모리 영역으로, 선언시 자료형과 이름이 지정되어야 한다.  

변수의 초기화 방법:  

```cpp
//형식 1
int total = 0;

//형식 2 
int total(0);

//형식 3
int total{0};
```

### static과 extern  

static은 선언된 파일 내에서 사용 가능한 변수이며, extern은 외부 소스파일에서 선언된 변수를 끌어다 쓰는 것이다. static으로 선언된 변수는 extern으로 불러올 수 없다.  

```cpp
//file1.cpp

int a = 0;
static int b = 10;
int f(){
  int c, d = 20, 30; 
}
```

```cpp
//file2.cpp

#include <iostream>
using namespace std;

extern int a;
static int b = 50;
int g(){
  cout << a << b << endl  
}
```

file2.cpp를 실행했을 때 :  
```shell
0 50 
```

### const와 constexpr 한정어  

const 한정어를 사용하면 변수의 값을 수정할 수 없으며, 초기화를 통해서만 값을 정할 수 있다: `const double PI = 3.14`  

constexpr는 값을 컴파일할 때 평가한다는 의미이며 실행 중 값을 평가하는 것에 비해 효율적인 동작이 가능케 한다. 단, 값을 컴파일 할 때 평가하기 때문에 사용을 적절하게 해야한다.  

```cpp
int a;
std::cin >> a;
const int b = 20;
const int C1 = a;
constexpr int C2 = a + 10; // 컴파일 시에는 a의 값을 모르기 때문에 오류가 난다
constexpr int C3 = b + 100; // 컴파일 시에 b의 값은 알기 때문에 계산이 가능하다  
```

