---
title: "자바스크립트 Call, Apply, Bind"
writer: James
date: 2024-12-16 01:00:00 +0900
categories: [language, javascript]
tags: [langauge, javascript]
pin: false
math: true
mermaid: true
image:
  path: https://wonism.github.io/static/js-401cd01e8522c5d16b93f7a237b81943.jpg
---

## 그동안

과제 전형과 여러 개의 기술 면접 준비, 그리고 시험까지 병행하다보니 블로그를 쓸 여유가 없어서 글을 쓰는 게 오랜만이다.  
아무래도 앞으로 더욱 바빠질 것 같아 보다 편한 마음으로 블로그를 써보려고 한다.

오늘의 주제는, 기술 면접때 제대로 대답 못했던 call, apply, bind 메서드에 대한 내용이다.  
솔직히, 문법을 많이 신경 못 쓴건 사실인 것 같다. 운영체제, 자료구조, 네트워크 등등 보다 개념적인 부분들에 집중했는데, 결국 개발자는 코드를 작성하는 사람이란 것을 항상 기억해야겠다.

## 함수 메서드

`call()`, `apply()`, `bind()`는 자바스크립트에서 함수 호출 시 this값을 명시적으로 설정하는 방법이다. Function.prototype에 정의된 메서드이며, 모든 함수는 기본적으로 Function객체의 인스턴스이기에 이 메서드들을 사용할 수 있다.

본래 함수는 선언한 뒤에 소괄호를 사용하여 호출이 되어야 한다 (함수 표현식이나, 화살표 함수같은 경우는 호출하려면 변수에 할당해야 한다).

```javascript
function example(a, b) {
  return a + b;
}

console.log(example(1, 2)); // 3
```

이러한 익숙한 방식과 별개로 call()과 apply()를 사용해서도 함수를 호출할 수 있다.

```javascript
console.log(example.call(null, 1, 2)); //3
console.log(example.apply(null, [1, 2])); //3
```

call()과 apply()의 차이는 인자가 전달되는 방식에 있는데, call()은 인자를 개별적으로 전달하며, apply()는 모든 인자를 하나의 배열에 넣어 해당 배열을 인자로 전달한다.

call()과 apply()를 사용한 위 코드 예시를 보면 공통적으로 `null`이라는 인자가 있는데, 이는 `this`를 대체하는 객체이다.

```javascript
const obj = {
  name: "jae",
  greet: function () {
    console.log("hi," + this.name);
  }
};

const obj2 = {
  name: "hee"
};

obj.greet(); // hi, jae
obj.greet.call(obj2); // hi, hee
```

위 코드를 보면, 분명 obj의 name은 jae인데, call 메서드를 사용해서 `greet`메서드가 참조하는 this가 obj2의 name으로 바뀌어서 출력된다.  
즉, call을 사용하면 this를 정의할 수 있으며, 이를 통해 다를 객체의 파라미터나 메서드를 자기 자신의 것 마냥 접근하여 사용할 수 있는 것이다.

이 때 주의할 점은, 파라미터의 이름이 같아야 한다. obj의 greet 메서드는 `this.name`을 출력하기 때문에에 call에 첫번째 인자로 주어진 객체는 name필드가 반드시 있어야 한다.

이러한 메서드들의 사용 예시로는 함수의 인자를 접근 및 조작하는 것이 있다.

함수는 `arguments`라는 속성을 모두 가지고 있는데, 함수에 들어온 인자를 유사 배열 형식으로 반환한다. (생긴 건 배열과 같지만 배열의 메서드는 사용 불가)

```javascript
function argCheck() {
  console.log(arguments);
}

argCheck(1, "a", true); // [1, 'a', true]
```

배열의 메서드를 사용할 수 없기 때문에 join이나 slice와 같은, 배열 메서드들이 기본적으로는 사용이 불가능하다.

```javascript
function argCheck() {
  console.log(arguments.join()); // 불가능
}

function argCheck2() {
  console.log(Array.prototype.join.call(arguments, ", "));
}

argCheck2(1, "a", true); // 1, a, true
```

그러나, 위처럼 하면 Array.prototype.join 이라는 메서드가 지정하는 this가 `arguments`가 되고, 받는 인자가 `,`이 되는 것이다.  
Array.prototype.join이 참조하는 this 객체는 다음과 같다:

```javascript
{
  '0': 1,
  '1': 'a',
  '2': true,
  length: 3
}
```

bind는 call과 apply랑은 살짝 다른데, 함수가 가리키는 this만 변경하고 호출은 직접 하지 않는다.  
새로운 this를 정의하고 새로운 함수를 만들어 반환한다고 보면 된다.

```javascript
const obj = {
  name: "jae",
  greet: function () {
    console.log("hi," + this.name);
  }
};

const obj2 = {
  name: "hee"
};

const greet2 = obj.greet.bind(obj2);
greet2(); // hi, hee
```

즉, call, apply, bind는 참조하는 this를 바꿔서 함수가 다른 객체 안에 있는 것마냥 조작할 수 있다.
