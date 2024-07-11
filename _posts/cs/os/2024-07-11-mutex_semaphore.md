---
title: "동기화 - 뮤텍스와 세마포어"
writer: James
date: 2024-06-27 22:00:14 +0900
categories: [cs, os]
tags: [cs, os, 뮤텍스, 세마포어, mutex, semaphore, 운영체제]
pin: false
math: true
mermaid: true
image:
  path: https://i.namu.wiki/i/-dSjzFwZzIQOe8Fm05sgvzuO6HaKhVilB-M1dtuy80qEnwem-xr6wET8hfP78ar5gHDn3iVQZjlYfLlJylBfyA.webp
---

## 동기화  

컴퓨터 과학에서 동기화(Syncrhonization)이란 다수의 스레드나 프로세스 사이에 발생하는 작업의 조율 및 데이터 일관성 유지를 위한 메커니즘이다. 보통 공유 자원에 대한 접근을 조절하고, 데이터의 일관성을 유지하기 위해 사용된다. 

> 공유 자원의 보호:  
>> 여러 실행 흐름이 공유 자원에 동시에 접근할 때, 데이터의 일관성을 유지하기 위해 동기화가 필요하다. 하나의 실행 흐름이 공유 자원을 사용하는 동안 다른 실행 흐름이 접근하지 못하도록 제어한다.  
>> **상호 배제**  

> 경쟁 상태 방지:  
>> 여러 실행 흐름이 동시에 공유 자원을 수정하려고 하면 접근이 어떤 순서로 이루어졌느지에 따라 실행 결과가 같지 않고 달라지는 사오항이 발생한다.  
>> 이를 **Race Condition (경쟁 상태)**라고 하는데, 이는 말 그대로 하나의 자원을 놓고 여러 실행 흐름이 경쟁하는 상황을 말한다.  

> 데이터 일관성 보장:  
>> 데이터를 여러 곳에서 동시에 접근하거나 수정할 떄 발생할 수 있는 일관성 문제를 해결하며, 예측 가능한 데이터 상태를 유지한다.  

## 동기화 기법  

동기화를 구현하기 위한 주요 기법들로는 뮤텍스와 세마포어가 있다. 

### 뮤텍스  

MUTual EXclution의 약자로, 공유 자원에 대한 접근을 단일 스레드만 가능하도록 제한하는 기법이다. Mutual Exclusion.이라는 말을 직역하면 '상호 배제'인데, 임계 영역(Critical Section)을 설정하여 여러 실행 흐름이 동시에 접근하지 못하도록 설계하는 방법이다.  

뮤텍스는 Lock이라는 것을 제공하여 이에 대한 획득/해제 연산들을 통해 임계 영역에 접근할 수 있는 실행 흐름을 결정한다. Lock을 보유한 실행 흐름만이 임계 영역에 접근할 수 있으며, 다른 스레드/프로세스가 해당 임계 영역에 접근하기 위해서는 Lock의 해제를 기다려야 한다.  

Node.js에서는 `async-mutex`라는 라이브러리를 사용하여 뮤텍스를 손쉽게 구현할 수 있다.  

```javascript
import { Mutex } from 'async-mutex'

const mutex = new Mutex();

let data = 0;

async function criticalSection(){
    const release = await mutex.acquire(); 
    try{
        data++;
        console.log("Data value is:", data);
    } finally {
        release();
    }
}

async function simulateConcurrency() {
    await Promise.all([
        criticalSection(),
        criticalSection(),
        criticalSection()
    ]);
}

simulateConcurrency().then(() => {
    console.log('All operations completed.');
}).catch(err => {
    console.error('Error during operations:', err);
});
```
공유된 자원 Data에 대해 안전하게 접근할 수 있으며 단일 실행 흐름만 접근할 수 있도록 설계를 위와 같이 할 수 있다.  

뮤텍스를 사용할 때는 데드락을 발생시킬 수 있는 다른 3가지 원인들을 고려하여 적절하게 데드락을 회피 또는 예방하여야 한다.  

### 세마포어  

세마포어 역시 뮤텍스와 같이 공유된 자원에 대한 접근을 제어하는 데에 사용된다. 특정 자원의 개수를 나타내는 카운터로 구성되어 있으며, 세마포어를 통해 해당 카운터를 조작하여 동시에 접근할 수 있는 실행 흐름의 수를 제어한다.  

세마포어는 P(Produce)와 V(Verbose) 두 연산들을 통해 카운터를 값을 조작하여 접근 가능한 실행 흐름의 수를 제어한다.  

- P 연산은 카운터가 0보다 크면 접근을 허용하며 카운터의 값을 감소시키고, 0이라면 호출한 실행 흐름을 대기 상태로 전환한다.  
- V 연산은 세마포어의 카운터를 증가시키며, 대기중인 실행 흐름이 있다면 깨워서 실행 가능하게 만든다.  

뮤텍스와 달리 여러 실행 흐름이 접근할 수 있는 자원의 최대 접근 가능한 수를 관리한다.  

```javascript
class Semaphore{
    constructor(counter){
        //최대로 접근 가능한 실행 흐름의 수
        this.count = counter; 
        //대기중인 작업 큐
        this.queue = [];
    }

    async produce(){
        return new Promise((resolve)=>{
            const tryAcquire = () => {
                if (this.count > 0){
                    this.count --;
                    resolve();
                } else {
                    this.queue.push(tryAcquire);
                }
            };
            tryAcquire();
        });
    }

    verbose(){
        this.count++;
        if(this.queue.length > 0){
            const next = this.queue.shift();
            next();
        }
    }
}
```

<hr>