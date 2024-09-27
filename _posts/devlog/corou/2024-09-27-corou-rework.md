---
title: "corou : 다시 돌아가기"
writer: James
date: 2024-09-27 00:01:00 +0900
categories: [devlog, corou]
tags: [devlog, corou, side project]
pin: false
math: true
mermaid: true
image:
  path: https://blog.kakaocdn.net/dn/I0fI1/btqCUbSwdwr/WeSNgsCc4KjVyX35SUiSH1/img.png
---

## 문제점

기본적인 구현 자체는 모두 완료되었다. 단순한 회원가입 및 로그인부터, 루틴 등록 및 조회, 댓글, 제품 구매까지 처음에 기획했던 모든 요소들이 One-Flow로 작동한다.  

그러나, 처음에 데이터베이스의 설계가 미흡했던 부분들이 기능들을 추가하는 데에 어려움을 준다.  

우선, 현재 서비스의 알파이자 오메가는 '루틴'이다. 이 루틴에 각 단계를 설명하는 RoutineDetail, 이 RoutineDetail에 연결된 Item, SkinAttribute와 연관을 지어주는 RoutineSkinRelation, 피부 타입과 달리 루틴 등록자가 직접 넣어 검색에 용이하게 하는 Tag, 그리고 해당 Tag와 루틴을 연결시켜주는 SkinTagRelation 등 많은 부분들이 루틴과 밀접하게 연관되어있다.  

처음에 데이터베이스 설계를 하고 ERD를 만들 때 정규화의 개념은 숙지하고 있었어서 도메인 원자성, 부분종속성제거, 이행종속성제거까지 하여 3NF로 만들려고 하다보니 엔터티가 많이 쪼개지게 되었고, 이 때문에 쿼리문 작성이 쉽지만은 않았다.  

[정규화 포스트](https://jaenam615.github.io/posts/sqld4/)  

정규화로 인해 나누어진 엔터티들을 함께 반환하다 보니 조인이 많아지고 이에 따라 비정규화도 고려를 해보았으나, 일단 처음으로 RDBMS를 사용하는 만큼 정규화된 상태에서 진행을 하기로 했다.  

```typescript
const routine = await this.dataSource
    .createQueryBuilder(Routine, 'routine')
    .leftJoinAndSelect('routine.routineDetails', 'routineDetails')
    .leftJoin('routine.user', 'user')
    .addSelect(['user.username'])
    .leftJoinAndSelect('routine.routine_skin_relations', 'routineSkinRelation')
    .leftJoinAndSelect('routine.routine_tag_relations', 'routineTagRelation')
    .where('routine.routine_key = :routine_key', { routine_key })
    .andWhere('routineTagRelation.routine = :routine_key', { routine_key })
    .getOne();
```
*현업자 기준에선 별로 복잡하지 않을 수 있겠지만, 이렇게 하는 것이 생각보다 나는 어려웠다...*  

이 뿐만이 아니라, 현재 관계를 세운 인스턴스들 간의 삭제와 수정 연산 또한 문제가 생긴다. 나는 단순하게 `{cascade : true}`로 하면 함께 삭제되지 않나 싶었는데 해당 부분에서 오류가 나는 것을 보아 그렇지는 않는 것 같다. 이 부분은 새벽 간 수정하여 API테스팅을 자세하게 해봐야 한다.  


## 지금부터  

그래서 다시 돌아보기로 했다. 번거로울 수 있겠지만, 필요하다면 데이터베이스 엔터티 부분부터 모두 손 볼 예정이다. 여기에 더해, 복잡한 쿼리 연산이 다수 포함되어 있게 된 이상, Redis를 사용하여 이런 연산에 자원이 낭비되지 않도록 하는 것이 필수로 보인다. 현재 사용할 줄 아는 성능 테스팅 툴은 K6밖에 없어 이를 사용해서 Redis사용 전/후를 나누어 API테스팅을 해보아야 한다.  

