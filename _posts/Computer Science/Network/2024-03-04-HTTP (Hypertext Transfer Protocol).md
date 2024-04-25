---
title: "HTTP: Hypertext Transfer Protocol"
writer: Langerak
date: 2024-03-04 12:00:00 +0800
categories: [Computer Science, Network]
tags: [Computer Science, Network]
pin: false
math: true
mermaid: true
image:
  path: https://github.com/JeongJongMun/JeongJongMun.github.io/assets/101979073/a9126340-e867-446f-ba8d-86701c1e1851
  lqip: data:image/webp;base64,UklGRpoAAABXRUJQVlA4WAoAAAAQAAAADwAABwAAQUxQSDIAAAARL0AmbZurmr57yyIiqE8oiG0bejIYEQTgqiDA9vqnsUSI6H+oAERp2HZ65qP/VIAWAFZQOCBCAAAA8AEAnQEqEAAIAAVAfCWkAALp8sF8rgRgAP7o9FDvMCkMde9PK7euH5M1m6VWoDXf2FkP3BqV0ZYbO6NA/VFIAAAA
  alt: Network
---

> 본 글은 제 개인적인 공부를 위해 작성한 글입니다. 틀린 내용이 있다면 언제든지 피드백을 주시면 감사하겠습니다. 참고로만 활용해주시길 바랍니다.

## URL (Uniform Resource Locator)

---

**URL**이란 웹 상에서 자원을 구별하는 **식별자**이다.

웹에서 자원이란 HTML 파일, 이미지, 오디오 등이 될 수 있다.

**URL은 웹에서 접근 가능한 자원의 주소를 표현하는 방식이다.**

$e.g.\;www.naver.com/path/picture.gif$

위의 URL에서 $www.naver.com$은 **Host Name**에 해당하고,

$/path/picture.gif$는 **Path Name**이다.

## HTTP 개요

---

**클라이언트** PC의 웹 브라우저와 **웹 서버**는 **HTTP(Hypertext Transfer Protocol)**라는 **응용 계층**의 프로토콜을 사용한다.

통신은 **요청(Request)와 응답(Response)**로 이루어지는 데 이 과정에서 주고 받는 정보들을 **HTTP 메시지**라고 한다.

클라이언트는 서버로부터 정보를 받아서 표시하고, 서버는 URL 정보를 가지고 있고, 해당하는 응답을 보내준다.

## HTTP의 간략한 역사

---

![image](https://github.com/JeongJongMun/JeongJongMun.github.io/assets/101979073/033d0086-1a9b-41d9-92e9-955818ca7c4e)

- HTTP/0.9: GET 메서드만 지원, HTTP 헤더 없음
- HTTP/1.0: 메서드, 헤더 추가 (→ HTML 이외 다른 파일 전송 가능)
- HTTP/1.1: 현재 HTTP/1.1 주로 사용, 우리에게 가장 중요한 버전
- HTTP/2: 성능 개선
- HTTP/3: 진행 중, TCP 대신 UDP 사용, 성능 개선

**HTTP/1.1**, **HTTP/2**는 **TCP** 기반이며, **HTTP/3**은 **UDP** 기반이다.

## HTTP 특성

---

1. **클라이언트-서버 구조이다.**
2. **웹은 신뢰성이 중요하기에 전송 계층의 TCP를 사용한다.**
    - **80번 포트** 사용
    - TCP 연결을 위한 메시지 교환이 완료되면, HTTP 메시지들을 서버와 클라이언트 간에 송수신한다. 이후 메시지 교환이 끝나면 TCP 연결을 종료한다.
3. **HTTP는 무상태성 Stateless하다.**
    - 서버는 이전 클라이언트의 요청에 대해 저장하지 않아 동일한 객체를 요구해도 방금 전에 보냈다는 사실을 모르므로 다시 보낸다. 이전 요청에 대한 기록을 남기는 것은 복잡하며, 단순화하기 위해 **Stateless**하다.
    - 이러한 Stateless를 보완하기 위해 **쿠키**, **세션**이 등장하게 되었고, 쿠키와 세션을 보완하기 위해 **Oauth**, **JWT**가 등장하게 되었다.
4. **HTTP는 비연결성 Connectionless하다.**
    - 클라이언트와 서버가 연결을 맺은 후에 응답을 마치면 기존의 연결을 끊는다.
    - 연결을 유지하기 위한 리소스를 줄여 더 많은 연결을 할 수 있다.
    - **connection, keepalive**와 같은 HTTP 헤더를 사용하여 비연결성을 보완할 수 있다.
        - 연결이 이루어지고 난 뒤 각각의 자원들을 요청하고 모든 자원에 대한 응답이 돌아온 후에 연결을 종료한다.

## HTTP 메시지

---

HTTP 메시지는 클라이언트와 서버 사이에서 데이터가 교환되는 방식이다.

**요청 Request과 응답 Response**이 있다.

### 메시지 구성

HTTP 메시지는 몇 줄의 **텍스트 정보로 구성**된다.

그러나 개발자는 이런 메시지를 직접 작성할 필요가 거의 없고, **구성 파일/API/기타 인터페이스**에서 **HTTP 메시지를 자동으로 완성**한다.

### 요청과 응답의 구조

![image](https://github.com/JeongJongMun/JeongJongMun.github.io/assets/101979073/7113ec2c-bcf6-4e90-be3a-1cdd17e883e2)

### Start Line

항상 첫 번째 줄에 위치하고 응답에서는 **Status Line**이라고 부른다.

요청에선 **메소드, URL, 프로토콜 버전**이 포함되고,

응답에선 **상태 코드, 프로토콜 버전**이 포함된다.

### HTTP Headers

**요청을 지정**하거나, 메시지에 포함된 **본문을 설명하는 헤더의 집합**이다.

### Empty Line

**헤더와 본문을 구분하는 빈 줄**이 있다. 그 줄을 말한다.

### Body

**요청과 관련된 데이터**나 **응답과 관련된 데이터 또는 문서**를 포함한다.

요청과 응답의 유형에 따라 선택적으로 사용한다.

> Start Line과 HTTP Headers를 묶어 Head라고 이야기하고, Body는 Payload라고도 말한다.
> 

## 상태 코드

---

상태 코드는 **3자리 숫자**로 만들어져 있으며, 첫 번째 자리는 **1에서 5**까지 제공된다.

첫번째 자리가 4와 5인 경우는 정상적인 상황이 아니기에 사이트 관리자는 즉시 알아야 하는 정보이다.

- **1xx (정보**) : 요청을 받았으며, 프로세스를 계속 진행한다.
- **2xx (성공)** : 요청을 성공적으로 받았으며 인식했고 수용했다.
- **3xx (리다이렉션)** : 요청 완료를 위해 추가 작업 조치가 필요하다.
- **4xx (클라이언트 오류)** : 요청의 문법이 잘못되었거나 요청을 처리할 수 없다.
- **5xx (서버 오류)** : 서버가 명백히 유효한 요청에 대한 충족을 실패했다.

| 상태 코드 | 상태 | 의미 |
| --- | --- | --- |
| 100 | Continue | 서버는 헤더가 받았고 바디를 기다리는 중 |
| 101 | Switching Protocols | 클라이언트가 서버에게 프로토콜을 바꾸자고 제안했고 서버가 수락함 |
| 200 | OK | 요청이 성공했고 요청한 결과 데이터를 응답으로 보냈음 |
| 201 | Created | 요청이 성공했고 새로 만들어진 URL을 응답으로 보냄. 웹 서비스나 웹 애플리케이션에서 사용됨 |
| 301 | Moved Permanently | 요청한 내용이 다른 경로로 옮겨졌음. 이후 옮겨진 경로로 요청해야 함 |
| 302 | Found | 요청한 내용이 다른경로로 옮겨졌음. 임시로 옮겨진 것이라 이후에도 동일한 경로로 요청해야 함 |
| 304 | Not Modified | 요청한 내용은 갱신되지 않았음 |
| 400 | Bad Request | 요청에 문제가 있음 |
| 401 | Unauthorized | 요청이 인증되지 않음 |
| 403 | Forbidden | 요청한 내용은 접근 금지됨 |
| 404 | Not Found | 요청한 내용을 찾을 수 없음 |
| 500 | Internal Server Error | 서버에 문제가 있지만, 서버가 정확한 문제에 대해 더 구체적으로 설명할 수 없음 |

## HTTP 메소드

---

HTTP 메소드는 클라이언트가 웹 서버에 어떤 동작을 요청할 것인지를 정의하는데 사용된다.

- **GET** : 서버에게 조회할 리소스를 요청한다. (Read, 조회)
- **POST** : 서버에게 본문(Body)에 생성할 데이터를 삽입하여 전송한다. (Create, 생성)
- **PUT** : 서버에게 본문에 수정할 데이터를 삽입하여 전송한다. (Update, 수정)
- **DELETE** : 서버에게 삭제할 리소스를 요청한다. (Delete, 삭제)
- **PATCH** : PUT과 비슷하지만 일부만 수정한다는 점에서 다르다.
- **HEAD** : GET과 동일하지만 메시지 본문을 포함하지 않고, 메시지 헤더만 반환한다. 데이터 양이 줄어들기 때문에 빠르게 서버의 상태를 조회할 수 있고, 응답 헤더의 Content-Length 또한 동일하기 때문에 리소스 양에 대한 조회만 할 때에는 HEAD 메소드가 유용할 수 있다.

<br/><br/>

*참고*

- [https://velog.io/@dami/CSNetwork#http-메세지request](https://velog.io/@dami/CSNetwork#http-메세지request)

- [https://dkrnfls.tistory.com/289](https://dkrnfls.tistory.com/289)

- [https://hanamon.kr/네트워크-http-메세지-message-요청과-응답-구조/](https://hanamon.kr/네트워크-http-메세지-message-요청과-응답-구조/)