---
title: "Exception: 위치를 기록하는 동안 액세스 위반이 발생했습니다. (0 < nx ≤ n)"
writer: Langerak
date: 2024-03-07 12:00:00 +0800
categories: [Trouble Shooting, C++]
tags: [Trouble Shooting, C++]
pin: false
math: true
mermaid: true
image:
  path: https://github.com/JeongJongMun/JeongJongMun.github.io/assets/101979073/9b2e2cd0-1ce7-4984-91c7-1563e1e9404b
  lqip: data:image/webp;base64,UklGRpoAAABXRUJQVlA4WAoAAAAQAAAADwAABwAAQUxQSDIAAAARL0AmbZurmr57yyIiqE8oiG0bejIYEQTgqiDA9vqnsUSI6H+oAERp2HZ65qP/VIAWAFZQOCBCAAAA8AEAnQEqEAAIAAVAfCWkAALp8sF8rgRgAP7o9FDvMCkMde9PK7euH5M1m6VWoDXf2FkP3BqV0ZYbO6NA/VFIAAAA
  alt: Trouble Shooting
---

> 본 글은 제 개인적인 공부를 위해 작성한 글입니다. 틀린 내용이 있다면 언제든지 피드백을 주시면 감사하겠습니다. 참고로만 활용해주시길 바랍니다.


## 문제점

---

![image](https://github.com/JeongJongMun/JeongJongMun.github.io/assets/101979073/51c52012-70ce-43ae-937b-f4e44b348190)

BFS 문제를 푸는 중에 나는 아래와 같이 C++ 코드를 작성했다.

```cpp
if (0 < ny <= n and 0 < nx <= m and not visit[ny][nx] and tomato[ny][nx] == 0)
```

파이썬에서는 당연하게도 `0 < ny < n` 와 같이 조건을 사용할 수 있었지만, C++은 조금 다른 것 같다.

연산자 우선 순위(L → R)에 의해 `0 < ny`가 먼저 연산되고 나온 결과 `bool` 값을 가지고 `bool <= n` 을 계산하는 것이다.

내가 의도한 바로는 ny가 0보다 작다면 조건에 걸리지 않아야 한다.

하지만 -2를 넣어보자.

1. `0 < -2` → false
2. `false <= n` → true (문제에서 n > 2 이다.)

즉, true가 나오게 된다.

<br/>

## 해결

---

아래와 같이 작성하자.

```cpp
if (0 < ny and ny <= n and 0 < nx and nx <= m and not visit[ny][nx] and tomato[ny][nx] == 0)
```

<br/><br/>

*참고*

- [[What does this syntax (0 < x < 10) do in C++?](https://stackoverflow.com/questions/49868881/what-does-this-syntax-0-x-10-do-in-c)](https://stackoverflow.com/questions/49868881/what-does-this-syntax-0-x-10-do-in-c)
