---
title: "Namespace, Module, Package, Library, Framework"
writer: Langerak
date: 2024-03-05 12:00:00 +0800
categories: [Programming Language, Python]
tags: [Programming Language, Python]
pin: false
math: true
mermaid: true
image:
  path: https://github.com/JeongJongMun/JeongJongMun.github.io/assets/101979073/8345af70-efa8-4ae7-add5-51d6e6a7d1dd
  lqip: data:image/webp;base64,UklGRpoAAABXRUJQVlA4WAoAAAAQAAAADwAABwAAQUxQSDIAAAARL0AmbZurmr57yyIiqE8oiG0bejIYEQTgqiDA9vqnsUSI6H+oAERp2HZ65qP/VIAWAFZQOCBCAAAA8AEAnQEqEAAIAAVAfCWkAALp8sF8rgRgAP7o9FDvMCkMde9PK7euH5M1m6VWoDXf2FkP3BqV0ZYbO6NA/VFIAAAA
  alt: Python
---

> 본 글은 제 개인적인 공부를 위해 작성한 글입니다. 틀린 내용이 있다면 언제든지 피드백을 주시면 감사하겠습니다. 참고로만 활용해주시길 바랍니다.


## **Module**

---

**하나의 스크립트 프로그램**. 즉, 함수/변수/클래스 등을 모아놓은 파일.

확장자(.py)를 포함하지 않는 파일 이름 자체를 모듈 이름으로 사용

왜 사용하는가?

1. 다른 파일에서 재사용
2. 전체 코드가 너무 커서 여러 파일로 나눠 정리

## **Namespace**

---

```python
def hello():
	print("Hello, World!")
```

```python
import test_module

test_module.hello()
```

`hello()` 함수를 호출할 때, `test_module.`을 붙히지 않으면?

파이썬은 실행된 파일인 `main.py`에서만 `hello()`라는 함수를 찾을 것이고, 에러가 발생할 것임.

`.` 앞에 있는 import 하는 영역을 만드는 이 구조를 **Namespace**라고 한다. 

## **Package**

---

나뉘어진 코드들을 하나의 **Module**로 불러와 사용할 수 있게 해줌.

**패키지는 모듈의 묶음.**

![image](https://github.com/JeongJongMun/JeongJongMun.github.io/assets/101979073/f50b9c98-0608-4944-b089-b6a1001c90da){: width="500" height="500" .center}

Package를 불러온다면, Module1.py, Module2.py, Module3.py를 모두 불러온 것임.

```python
import package.module1
form package.module2 import function2

package.module1.function1()
function2()
```

일반 디렉토리가 아닌 패키지임을 인식하기 위해서는 각 폴더마다 `__init__.py`라는 파일을 생성해야 한다.

## **Library**

---

**Package를 모아놓은 개념**

**예시**

- **numpy -** 다차원 배열과 행렬 연산 특화 라이브러리
- **requests -** HTTP 요청을 주고 받는 데 사용되는 라이브러리

## **Framework**

---

**사전적 정의:** 프로그래밍에서 특정 운영 체제를 위한 응용 프로그램 표준 구조를 구현하는 클래스와 라이브러리 모임

라이브러리와 프레임워크의 **가장 큰 차이점**은 **프로그래밍 할 때 규칙이 정해져 있다**는 것이다.

**프레임워크는 프로그래머가 프레임워크에 정해둔 틀 안에 필요한 코드를 작성하고, 라이브러리는 사용자가 필요한 상황에 가져다 쓴다.**

**예시**

- **django -** 웹 앱을 개발하기 위한 프레임워크
- **flask -** 상동
- **PyTorch -** 딥러닝 및 머신 러닝 작업을 위한 프레임워크
- **TensorFlow -** 상동

결론적으로 **Framework $\subset$ Library $\subset$ Package $\subset$ Module**이라고 볼 수 있다.

<br/> <br/>

*참고*

- [[Python 기초] Module & Package 이해하기 1](https://livetodaykono.tistory.com/20)

- [[python] 모듈, 패키지, 라이브러리, 프레임워크 차이](https://etloveguitar.tistory.com/138)