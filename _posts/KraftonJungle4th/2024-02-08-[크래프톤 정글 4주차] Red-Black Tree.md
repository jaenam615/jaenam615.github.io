---
title: "[크래프톤 정글 4주차] Red-Black Tree"
writer: Langerak
date: 2024-02-08 20:50:00 +0800
categories: [KraftonJungle4th, WIL]
tags: [KraftonJungle4th, WIL]
pin: false
math: true
mermaid: true
image:
  path: https://github.com/JeongJongMun/JeongJongMun.github.io/assets/101979073/606fb575-ffce-4656-b694-4e14f54f2654
  lqip: data:image/webp;base64,UklGRpoAAABXRUJQVlA4WAoAAAAQAAAADwAABwAAQUxQSDIAAAARL0AmbZurmr57yyIiqE8oiG0bejIYEQTgqiDA9vqnsUSI6H+oAERp2HZ65qP/VIAWAFZQOCBCAAAA8AEAnQEqEAAIAAVAfCWkAALp8sF8rgRgAP7o9FDvMCkMde9PK7euH5M1m6VWoDXf2FkP3BqV0ZYbO6NA/VFIAAAA
  alt: KraftonJungle
---

## 주간 요약

> C언어로 Red-Black Tree 구현  
> → → → → → → → → → → → →

- **키워드**
  - BST
  - RB Tree
  - AVL Tree
  - 포인터, 포인터의 연산
  - 가상화
  - GCC
  - 동적 메모리 할당
  - malloc, calloc, realloc
- **백준**
  1. 특정한 최단 경로
  2. 뒤집기
  3. 선분 위의 점
  4. 예산
  5. 기타 레슨
  6. 2차원 배열의 합
  7. 합 구하기
  8. 블로그
  9. Maximum Subarray
  10. 기타줄
  11. N과 M (8)
  12. **특정한 최단 경로**
  13. **가장 긴 증가하는 부분 수열 3**
  14. **최단경로**

## 만족

---

팀원 모두가 각자 RB Tree를 구현하였다.

포인터, 구조체 등을 사용하면서 C언어에 익숙해질 수 있었고, 메모리 관리에 대해 조금은 알게 되었다.

Red-Black Tree와 AVL 트리를 일주일 내내 보다 보니 삽입/삭제 과정에 익숙해졌다.

## 아쉬움

---

Red-Black Tree의 삭제 과정을 공부하는데 부족한 부분이 있어, rbtree-lab 과제가 끝난 날에 추가로 학습을 하였다. 본 주차 때 더 집중하였다면 일정을 준수 할 수 있었을 듯 하다.

## 회고

---

알고리즘 주차가 끝나고, 한 주간 Red-Black Tree를 C언어로 구현하였다. 팀원들과 파트를 나눠 구현을 하여도 되었지만 우리 팀은 각자 구현하여도 가능할 것이라고 판단하였기에 따로 구현을 하였다.

결론적으로 모두가 구현을 하긴 했으나, 합치는 과정에서 조금 힘든 점이 있었다.

발표 때 보여줄 하나의 팀 코드가 필요했기에 세 명의 각기 다른 함수들을 보며 가장 괜찮은 코드를 선택해서 합치기로 했다. 근데 대부분의 함수는 합치는데 문제가 없었지만, `rotate`함수가 문제였다.

트리에서 회전을 진행할 시에 어떠한 노드를 기준으로 회전하는 지에 따라 함수 본문의 코드들이 달라진다. 그리고 우리 팀원들과 나는 기준 노드를 모두 다르게 선택했다 ㅋㅋㅋ…

C언어 고수 명헌님이 없었다면 괜찮은 함수를 선택하지도 못하고 그냥 한 명의 코드로 진행했을거다…

나는 C++로 알고리즘 문제를 풀어왔지만, 포인터와 구조체는 많이 사용해본 적이 없어서 따라가기 수월하지는 않았다. 그래도 며칠 보다보면 나름 익숙해져서 다행이었다.

앞으로 malloc-lab과 OS 때도 계속 C언어를 사용할 거라고 하니까 이 참에 익숙해지고 가는 게 좋을 듯하다.

이번 주의 WIL 끝.
