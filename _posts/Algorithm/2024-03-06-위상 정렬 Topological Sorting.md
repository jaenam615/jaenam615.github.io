---
title: "위상 정렬 Topological Sorting"
writer: Langerak
date: 2024-03-06 12:00:00 +0800
categories: [Algorithm, Graph]
tags: [Algorithm, Graph]
pin: false
math: true
mermaid: true
image:
  path: https://github.com/JeongJongMun/JeongJongMun.github.io/assets/101979073/a8a86378-df92-430a-b0a3-af09e8ac3fe6
  lqip: data:image/webp;base64,UklGRpoAAABXRUJQVlA4WAoAAAAQAAAADwAABwAAQUxQSDIAAAARL0AmbZurmr57yyIiqE8oiG0bejIYEQTgqiDA9vqnsUSI6H+oAERp2HZ65qP/VIAWAFZQOCBCAAAA8AEAnQEqEAAIAAVAfCWkAALp8sF8rgRgAP7o9FDvMCkMde9PK7euH5M1m6VWoDXf2FkP3BqV0ZYbO6NA/VFIAAAA
  alt: Graph
---
> 본 글은 제 개인적인 공부를 위해 작성한 글입니다. 틀린 내용이 있다면 언제든지 피드백을 주시면 감사하겠습니다. 참고로만 활용해주시길 바랍니다.

## 개요

---

위상 정렬은 **순서가 정해져 있는 작업**을 차례로 수행해야 할 때 그 순서를 결정해주기 위해 사용하는 알고리즘이다.

그래프의 흐름을 **조건**으로 해석하자.

위상 정렬은 여러 개의 답이 존재할 수 있고, **DAG(Directed Acyclic Graph)**에만 적용 가능하다.

DAG = 사이클이 없는 유향 그래프

사이클이 있는 경우 왜 위상 정렬이 불가능한가?

- 위상 정렬은 시작점이 존재해야 하는데 사이클 그래프는 시작점이 없다.

### 위상 정렬의 결과

1. **현재 그래프가 위상 정렬이 가능한가**
2. **위상 정렬이 가능하다면 그 결과는 무엇인가**

<br/>

## 과정

---

큐로 구현

1. 진입 차수가 0인 노드를 큐에 삽입 (시작점)
2. **큐에서 원소를 꺼내 연결된 모든 에지를 제거**
3. **에지 제거 이후에 진입 차수가 0이 된 노드를 큐에 삽입**
4. **큐가 빌 때까지 2번~3번 반복. 모든 노드를 방문하기 전에 큐가 빈다면 Cycle 존재. 
모든 원소를 방문했다면 큐에서 꺼낸 순서가 위상 정렬의 결과**

![image](https://github.com/JeongJongMun/JeongJongMun.github.io/assets/101979073/2b163b0f-5662-48a2-9baa-657bc8a6e9b0){: width="500" height="500" .center}

![image](https://github.com/JeongJongMun/JeongJongMun.github.io/assets/101979073/a7313625-d104-4fae-b33d-67b9eea1b962){: width="500" height="500" .center}

![image](https://github.com/JeongJongMun/JeongJongMun.github.io/assets/101979073/af5244eb-2c29-4bc8-b018-2e6a24be00ae){: width="500" height="500" .center}

![image](https://github.com/JeongJongMun/JeongJongMun.github.io/assets/101979073/5968e202-cd89-4ff9-8719-7aedcb3e95f9){: width="500" height="500" .center}

![image](https://github.com/JeongJongMun/JeongJongMun.github.io/assets/101979073/29ffe9ea-b0c4-4535-a1e8-211dfcaddabe){: width="500" height="500" .center}

![image](https://github.com/JeongJongMun/JeongJongMun.github.io/assets/101979073/30eacbb9-e02a-4066-860b-e18107ed8fbd){: width="500" height="500" .center}

![image](https://github.com/JeongJongMun/JeongJongMun.github.io/assets/101979073/8c95a41e-cf65-4fdd-b165-60f6ff215032){: width="500" height="500" .center}

![image](https://github.com/JeongJongMun/JeongJongMun.github.io/assets/101979073/8d3a976d-a36b-4feb-a590-119d2d2f0ee7){: width="500" height="500" .center}

![image](https://github.com/JeongJongMun/JeongJongMun.github.io/assets/101979073/ae4ef7ea-d867-4023-a88d-c4a3ccacb8ab){: width="500" height="500" .center}

<br/>

## 시간 복잡도

---

- $O(V+E)$

<br/>

## 구현

---

```python
import sys
from collections import deque
input = sys.stdin.readline

def topology_sort(indegree, graph, n):
    q = deque()
    result = []
    for i in range(1, n + 1):
        if indegree[i] == 0:
            q.append(i)

    while q:
        now = q.popleft()
        result.append(now)
        for i in graph[now]:
            indegree[i] -= 1
            if indegree[i] == 0:
                q.append(i)

    print(*result)

def main():
    v, e = map(int, input().split())
    indegree = [0] * (v + 1)
    graph = [[] for _ in range(v + 1)]
    for _ in range(e):
        a, b = map(int, input().split())
        graph[a].append(b)
        indegree[b] += 1
    
    topology_sort(indegree, graph, v)

if __name__ == "__main__":
    main()

'''
INPUT
4 5
1 2
1 3
2 3
2 4
3 4

ANS
1 2 3 4
'''
```

<br/><br/>

*참고*

- [https://m.blog.naver.com/ndb796/221236874984](https://m.blog.naver.com/ndb796/221236874984)