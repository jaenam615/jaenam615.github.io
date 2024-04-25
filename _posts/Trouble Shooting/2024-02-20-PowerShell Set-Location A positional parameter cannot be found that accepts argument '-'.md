---
title: "PowerShell: Set-Location: A positional parameter cannot be found that accepts argument '-'"
writer: Langerak
date: 2024-02-20 12:00:00 +0800
categories: [Trouble Shooting, PowerShell]
tags: [Trouble Shooting, PowerShell]
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

![Image](https://github.com/JeongJongMun/JeongJongMun.github.io/assets/101979073/2b9d1b0c-4c04-44b2-a368-7f38fe48dbc9)
위와 같이 - 문자가 있는 경로로 이동하려 할 때 에러가 발생했다.

## 해결

---

![Image](https://github.com/JeongJongMun/JeongJongMun.github.io/assets/101979073/3d287c26-7ee3-458e-ad95-7e46e1eefbed)
따옴표로 경로를 묶어주어 해결하였다.
<br/> <br/>
*참고*
- [https://stackoverflow.com/questions/51459313/cd-program-files-error-positional-parameter-cannot-be-found](https://stackoverflow.com/questions/51459313/cd-program-files-error-positional-parameter-cannot-be-found)