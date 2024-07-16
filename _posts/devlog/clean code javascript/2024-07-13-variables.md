---
title: "클린 코드 자바스크립트 - 변수 다루기"
writer: James
date: 2024-07-13 01:00:00 +0900
categories: [devlog, clean code javascript]
tags: [devlog, clean code javascript, javascript]
pin: false
math: true
mermaid: true
image:
  path: https://img-b.udemycdn.com/course/750x422/4253080_1e23.jpg
---

## var를 지양하자  

예약어로는 `var`대신 `let`과 `const`를 사용하는 것이 좋다.   

`let`과 `const`는 ES2015 버전부터 생긴 문법이기에 그 이전에는 `var` 예악어를 통해서만 변수를 만들 수 있었지만, 자바스크립트가 발전함에 따라 생겼다.  

`var`는 함수 스포크이지만, `let`과 `const`는 블록 단위의 스코프를 가진며, 이에 더해 Temporal Dead Zone이라는 속성까지 가질 수 있는데 이는 안전하게 코드를 작성할 수 있게 해준다. 안전하다는게 무슨 말이냐면,  

```javascript
var name = '이름';
var name = '이름2';
var name = '이름3'; 
var name = '이름3';
```
`var`을 사용할 시 위와 같이 선언해도 아무런 문제가 생기지 않는다. 이를 `console.log()`를 사용해 호출하면 가장 마지막 값이 나온다.  

```javascript
console.log(name) //undefined

var name = '이름3';
```
위와 같이 해도 에러가 나오지 않고 undefined로 나온다.  
에러가 나오지 않고 재할당을 할 수 있으며, 재할당을 넘어서 선언할 수도 있는데 이는 편리할 수도 있겠지만 위험할 수도 있다. 코드가 길어짐에 따라 이러한 코드들이 많아지면 의도하지 않은 선언에 대해 에러를 파악하기가 매우 어려워진다.  

반면, `let`과 `const`를 사용하여 같은 코드를 작성하면 바로 에러가 나온다.  

`let`과 `const`간의 차이는 재할당에 있다.`let`은 선언 이후 언제든지 재할당할 수 있다.  

```javascript
let name;

name = '이름';
console.log(name); //이름
name = '이름2';
console.log(name); //이름2
name = '이름3';
console.log(name); //이름3
```

반면, `const`는 선언 이후 값의 재할당이 불가능하다.  

## Function Scope & Block Scope  

