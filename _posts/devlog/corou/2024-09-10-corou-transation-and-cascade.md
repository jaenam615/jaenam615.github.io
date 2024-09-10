---
title: "corou : 캐스케이딩과 트랜잭션"
writer: James
date: 2024-09-10 01:00:00 +0900
categories: [devlog, corou]
tags: [devlog, corou, side project]
pin: false
math: true
mermaid: true
image:
  path: https://jaenam615.github.io/images/2024-09-09-22-38-43.png
---

## Cascading  

컨트롤러를 구성하던 중, 루틴을 삭제하는 deleteRoutine을 구현해야 했다.  

이미 deleteRoutine 서비스는 routine.service.ts에 구현이 되어 있었으나, 이에 대한 컨트롤러를 구현하던 중, routine이 삭제되면 이에 종속적은 routineDetail을 모두 삭제해야 했기 떄문에 고민이 생겼다. 원래 내 생각은, 컨트롤러에서 for loop로 `routineDetailService.deleteRoutineDetail`을 반복하여 실행하고, 마지막에 `routineService.deleteRoutine`을 해줄 생각이었다. 근데 최근 SQL을 공부했을 때 배웠던 `ON DELETE CASCADE`가 생각났다. 그렇다면, 데이터베이스 레벨에서 연계되어 삭제되도록 설정하는 것이 옳을지, 애플리케이션 레벨에서 코드로 삭제하는 게 옳을지가 고민이 됐다.  

두 방식 모두 각각의 장단점이 있어 이를 저울질했다:  

**데이터베이스 수준에서의 ON DELETE CASCADE** 
- 데이터 무결성을 유지할 수 있다.
- 자동화가 됨으로 별도로 삭제 로직을 구현할 필요가 없다.  
- 데이터베이스 내부에서 처리되기 때문에 최적화가 되어 성능이 좋다.  
- 잘못된 삭제 트리거로 의도치 않게 데이터 손실이 발생할 수 있다.  

**애플리케이션 레벨에서 코드로 구현**  
- 특정 상황에서의 예외 처리 등 보다 유연하다.  
- 로깅이 가능하여 오류를 발견하기 쉽다.  
- 코드가 복잡해진다.  
- 삭제 순서를 잘못 지정할 시 데이터 무결성 문제가 발생할 수 있다.  
- 여러 번의 데이터베이스 호출로 인해 네트워크 오버헤드가 발생할 수 있다.  

더 진행하다 보면 둘 모두 병행해서 쓸 수도 있겠지만, 현재 단계에서 연결된 자식 인스턴스들이 많은 경우가 없기 때문에 우선은 코드의 가시성에 더 비중을 두어 애플리케이션 단계에서 처리하기로 했다.  

## 트랜잭션  

이렇게 진행하다 보니 하나 더 고려해야 할 것이 생겼는데, 비록 try-catch문을 사용하고 있긴 이는 원자성을 보장하지 않는다.  

일례로, 루틴에 대한 삭제를 하는데 Detail들만 삭제되고 Routine은 삭제되지 않으면 단계가 없는 빈 껍데기 Routine이 남는다는 것이다. 즉, 앞서 말한대로 컨트롤러에서 for loop로 `routineDetailService.deleteRoutineDetail`을 반복하여 실행하고, 마지막에 `routineService.deleteRoutine`을 해주면 문제가 생길 수 있다는 것이다.  

이를 방지하기 위해 특정 메소드들을 원자적으로 만들 필요가 있는데, 이를 위한 것이 typeorm의 DataSource에 준비되어 있다.  

```typescript
// routine.controller.ts
    async deleteRoutine(req: Request, res: Response): Promise<void> {
        const routine_key = req.params.routine_key;
        try {
            const routine = await this.routineService.deleteRoutine(Number(routine_key));
            res.status(200).json(routine);
        } catch (error) {
            res.status(500).json({ message: '루틴 삭제에 실패했습니다.' });
        }
    }
```

```typescript
// routine.service.ts
    async deleteRoutine(routine_key: number): Promise<Routine> {
        return this.dataSource.transaction(async transactionalEntityManager => {
            const routine = await transactionalEntityManager.findOne(Routine, {
                where: { routine_key },
                relations: ['routineDetails']
            });
            if (!routine) {
                throw new Error('해당 루틴을 찾을 수 없습니다.');
            }
            await transactionalEntityManager.remove(routine);
            return routine;
        });
    }
```