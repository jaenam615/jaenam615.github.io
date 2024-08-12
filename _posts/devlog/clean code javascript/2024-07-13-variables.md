---
title: "클린 코드 자바스크립트 : 변수 다루기"
writer: James
date: 2024-07-17 01:00:00 +0900
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

```javascript
var global = '전역'

if (global === '전역'){
  var global = '지역';

  console.log(global) //지역
}

console.log(global) // 지역
```

var는 함수 단위 스코프이기 때문에 if문에서 변경해도 전역적으로 바뀐다.  

```javascript
let global = '전역'

if (global === '전역'){
  let global = '지역';

  console.log(global) //지역
}

console.log(global) // 전역
```

let으로 변경하면 위와 같이 안전하게 작동한다. 이와 같이 안전하게 코딩하기 위해 let과 const를 다루어야 한다.  

let과 const중 const를 지향해야 더욱 안정적인데, const는 재할당이 불가능하기 때문에 왜 써야하는가 싶을 수 있다. const는 배열과 객체를 다룰 때 더욱 효과적이다.  

```javascript
const person = {
  name: 'nam',
  age: 30
};

person.name = 'lee';
person.age = 40

console.log(person); //{name: 'lee', age: 40}
```
객체의 원소값을 변경할 때는 도트연산자를 통해 변경이 가능하다.  

```javascript
const people = [{
  name: 'nam',
  age: 30
}];

people.push({
  name: 'lee',
  age: 40
} 

console.log(person); //[{name:'nam', age: 30}, {name: 'lee', age: 40}]
```
push메소드를 사용해서 추가할 수 있다.  

즉, const는 재할당만 금지하며, 객체 또는 배열 등 참조값들을 조작할 때는 이상이 없다는 말이다.  

## 전역 공간 사용 최소화  

전역 공간이란 window와 global로 나뉜다. 브라우저에서 사용하는 경우에는 window가 최상위이며, node.js 환경에서 사용하는 경우에는 global이 최상위이다. 즉 전역 공간이란 최상위 공간이라고 보면 된다.  

전역 공간에서 변수를 선언하면 여러 파일들에서 서로 침범할 수 있다. 일반적으로 파일을 나누면 스코프도 나뉠거라고 생각을 하고 코드를 작성하겠지만, 전역변수로 설정을 하면 파이들 간 나누어지지 않는다. 특히 var를 사용하면 전역 공간을 오염시킨다.   

즉, 사람이 생각하기엔 분리가 되어있는데, 실제로는 분리가 되어있지 않기 떄문에 많은 오류를 야기할 수 있다.  
 
1. 전역 변수를 선언하지 않는다.   
2. 지역 변수를 사용한다.   
3. window, global을 조작하지 않는다.   
4. const와 let 지향한다.  
5. IIFE, Module, Closure 스코프를 나눈다.  

## 임시 변수 제거하기  

- 명령형으로 가득한 로직  
- 어디서 어떻게 잘못되었는지 파악이 어려움으로 디버깅이 어려움

해결하기 위해서는 다음의 방법이 있다:  
1. 함수를 나눈다. 
2. 바로 반환을 한다.  
3. 고차함수를 사용한다. (map, filter, reduce)  
4. 선언형 코드로 바꿔보는 연습을 한다.  

## 호이스팅 주의  

호이스팅이란 인터프리터가 코드를 실행하기 전에 함수, 변수, 클래스, 또는 임포트(import)의 선언문을 해당 범위의 맨 위로 끌어올리는 것처럼 보이는 현상이다.  

1. 변수가 선언된 줄 이전에 해당 범위에서 변수 값을 사용할 수 있는 경우 ("값 호이스팅")
2. 변수가 선언된 줄 이전에 해당 범위의 변수를 참조할 수 있지만 `ReferenceError`를 던지지 않고 값이 항상 정의되지 않음인 경우 ("선언 호이스팅")
3. 변수를 선언하면 변수가 선언된 줄 앞의 범위에서 동작이 변경
4. 선언의 부작용은 선언이 포함된 나머지 코드를 평가하기 전에 발생

> 선언과 할당이 런타임 시기에 분리가 되는 것  

코드의 가독성 및 유지보수를 위해 호이스팅이 일어나지 않도록 하는 것이 바람직하며, 이 때문에 let/const를 이용한 함수 표현식을 사용하는 것이 권장된다.  

<hr>