---
title: "corou : 데이터베이스 재설계 및 타입 주의점"
writer: James
date: 2024-09-04 01:00:00 +0900
categories: [devlog, corou]
tags: [devlog, corou, side project]
pin: false
math: true
mermaid: true
image:
  path: https://github.com/user-attachments/assets/d8e11146-5769-4756-a3f6-026e3e89df33
---

**타입에 주의하자**

## 엔터티 다시 코딩..

기존에 만들어둔 TypeORM 엔터티를 확인해보자:  

![before](/images/2024-09-04-22-09-51.png)

이런 방식으로 조인이 되어있었는데, 저렇게 하면 Item과 Routine이 키로 생성되며 해당 키는 엔터티를 가리키는 키로 내가 기존 ERD로 설계한 키와는 다르다. 내가 생각한 대로 하기 위해서는 위에 넣어둔 routine_key와 item_key를 없애고 아예 아래의 조인 부분에 키를 명시해야 한다.  

![after](/images/2024-09-04-22-12-53.png)  

OneToOne이나 ManyToOne같은 데코레이터에 람다 함수에 대해서는, 사용을 해도 되고 안해도 된다고 한다.  

람다함수를 사용하면 TypeORM이 관계를 설정할 때 어떤 속성을 참조할지 명확하게 지정하기 때문에 최적화에 유리하다. 람다함수를 사용하지 않는다면 대상 엔터티의 식별자를 사용하는데, 내 경우에는 식별자를 가져오는 게 맞기 때문에 이렇게 해도 문제가 없지만 좋은 습관을 들이기 위해 명시하는 것이 좋을 것 같다. 

TypeORM을 처음 사용하다 보니 엔터티 파일들을 수정하는 부분이 꽤 오래 걸리는데, 이 때문에 공식 문서를 참고할 필요를 뼈저리게 느끼고 있어 오늘은 공식 문서를 읽으며 시간을 꽤 보냈다. 

![officialdoc](/images/2024-09-04-22-19-14.png)  

이런 방식으로 (내가 기존에 사용했던 방식) 관계를 설정하면 다음과 같은 테이블이 생성된다:  

![offocialdoc2](/images/2024-09-04-22-20-23.png)

대체 왜 Profile을 참고했는데 속성으로 profileId가 나오는 것일까? 확인해보니 `@JoinColumn` 데코레이터로 사용 시 기본적으로 외래 키 컬럼의 이름을 Id를 붙인 profileId로 생성한다고 한다. 명시적으로 컬럼을 지정하게 되면 해당 컬럼의 이름을 가져온다. 

그러면, `number`로 가져오냐, `Item`으로 가져오냐의 차이는 무엇이냐. 확인해본 결과 둘 다 같은 테이블을 반환하는데 무엇이 다른걸까?  

+--------------+--------------+----------------------------+
|                    routine_detail                        |
+--------------+--------------+----------------------------+
| step_number  | int(11)      | PRIMARY KEY AUTO_INCREMENT |
| description  | varchar(255) |                            |
| routine_key  | int(11)      | FOREIGN KEY                |
| item_key     | int(11)      | FOREIGN KEY                |
+--------------+--------------+----------------------------+

Copilot으로 둘의 차이를 보니 다음과 같이 답한다: 

```shell
속성 타입:

첫 번째 경우: item 속성은 Item 타입입니다. 이는 RoutineDetail 엔티티가 Item 엔티티와의 관계를 나타내며, item 속성을 통해 Item 엔티티의 모든 필드에 접근할 수 있습니다.
두 번째 경우: item_key 속성은 number 타입입니다. 이는 RoutineDetail 엔티티가 Item 엔티티의 기본 키(item_key)만을 저장하고 있음을 나타냅니다.
사용 방식:

첫 번째 경우: RoutineDetail 엔티티를 사용할 때, item 속성을 통해 Item 엔티티의 모든 필드에 접근할 수 있습니다. 예를 들어, routineDetail.item.name과 같이 사용할 수 있습니다.
두 번째 경우: RoutineDetail 엔티티를 사용할 때, item_key 속성은 단순히 Item 엔티티의 기본 키 값을 저장합니다. Item 엔티티의 다른 필드에 접근하려면 추가적인 쿼리가 필요합니다.
```

딱 보면 둘 다 장단점이 있다. 

1번은 더 많은 정보를 한 번에 제공하여 추가 쿼리를 필요로 하지 않는다는 장점이 있지만, 메모리 사용량이 많아진다.  

2번은 메모리 사용량은 적으나, 해당 엔터티의 다른 필드를 접근하려면 추가적인 쿼리를 필요로 한다.  

일단은 2번으로 진행하되, 이후에 필요에 따라 1번도 고려를 해보겠다.  

## 타입 검사  

![image](https://github.com/user-attachments/assets/d8e11146-5769-4756-a3f6-026e3e89df33)
![type](/images/2024-09-04-21-52-39.png)

계속 이러한 오류가 떴다. 

이는 위의 엔터티 관련 부분에서 연계되는 내용으로, Item값을 받아와야 하는 item_key가 number를 받아오기 때문이다. 이는 위의 내용에 따라 2번으로 진행하기로 정했기 때문에 엔터티의 속성을 수정하면 된다.  

