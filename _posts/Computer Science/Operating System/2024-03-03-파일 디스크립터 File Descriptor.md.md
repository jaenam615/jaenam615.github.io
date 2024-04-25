---
title: "파일 디스크립터 File Descriptor"
writer: Langerak
date: 2024-03-03 12:00:00 +0800
categories: [Computer Science, Operating System]
tags: [Computer Science, Operating System]
pin: false
math: true
mermaid: true
image:
  path: https://github.com/JeongJongMun/JeongJongMun.github.io/assets/101979073/3ae026eb-f51c-4a32-a61a-dd3854381147
  lqip: data:image/webp;base64,UklGRpoAAABXRUJQVlA4WAoAAAAQAAAADwAABwAAQUxQSDIAAAARL0AmbZurmr57yyIiqE8oiG0bejIYEQTgqiDA9vqnsUSI6H+oAERp2HZ65qP/VIAWAFZQOCBCAAAA8AEAnQEqEAAIAAVAfCWkAALp8sF8rgRgAP7o9FDvMCkMde9PK7euH5M1m6VWoDXf2FkP3BqV0ZYbO6NA/VFIAAAA
  alt: Operating System
---

> 본 글은 제 개인적인 공부를 위해 작성한 글입니다. 틀린 내용이 있다면 언제든지 피드백을 주시면 감사하겠습니다. 참고로만 활용해주시길 바랍니다.



## 파일 디스크립터 File Descriptor

---

**파일 디스크립터 File Descriptor**란 Unix/Linux 계열의 시스템에서 프로세스(Process)가 파일(File)을 다룰 때 사용하는 개념으로, **프로세스에서 특정 파일에 접근할 때 사용하는 추상적인 값**이다.

**특정 동작에 대한 수행 자격을 부여하는 핸들** 또는 **파일 객체를 가리키는 포인터**라고 한다.

> 핸들: 객체나 자원을 가리키는 참조나 식별자

파일 디스크립터는 일반적으로 0이 아닌 정수 값을 가진다.

흔히 유닉스 시스템에서는 **모든 것을 파일**이라고 한다. 일반적인 정규 파일부터 디렉토리, 소켓, 파이프, I/O 등 모든 객체들을 파일로 관리한다.

유닉스 시스템에서 프로세스가 이 **파일들을 접근할 때 파일 디스크립터라는 개념을 사용**한다.

<br/>

프로세스가 실행 중에 파일을 열면, 커널은 해당 프로세스의 파일 디스크립터 숫자 중 사용하지 않는 가장 작은 값을 할당해준다. 그 다음 프로세스가 열려있는 파일에 시스템 콜을 이용해서 접근할 때, 파일 디스크립터(FD) 값을 이용해 파일을 지칭할 수 있다.

기본적으로 할당되는 파일 디스크립터는 아래와 같다.

| 정수값 | 이름 |
| --- | --- |
| 0 | 표준 입력 (stdin) |
| 1 | 표준 출력 (stdout) |
| 2 | 표준 오류 (stderr) |

<br/>

운영체제는 이 파일 디스크립터를 프로세스 별로 관리하며, 파일 디스크립터는 **오픈 파일 테이블**을 가리킨다.

오픈 파일 테이블에는 **디스크립터가 가리키는 파일**과 **현재 오프셋**(얼마나 읽었는지), **읽기 쓰기 여부** 등을 담고 있다.

<br/><br/>

*참고*

- [https://twofootdog.tistory.com/51](https://twofootdog.tistory.com/51)

- [https://ko.wikipedia.org/wiki/파일_서술자](https://ko.wikipedia.org/wiki/파일_서술자)