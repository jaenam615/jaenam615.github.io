---
title: "corou : AWS RDS, Details, Attributes, Tags"
writer: James
date: 2024-09-12 01:00:00 +0900
categories: [devlog, corou]
tags: [devlog, corou, side project]
pin: false
math: true
mermaid: true
image:
  path: https://images.velog.io/images/chaeri93/post/cbd6fdb0-45cc-473e-b29b-ddd0be24856c/rds.png
---

## AWS RDS

오늘은 정말 많은 것을 수정했다. 우선, 성준님과 보다 수월하게 협업하기 위해 데이터베이스를 AWS RDS에 올렸다.  

AWS RDS를 내 애플리케이션과 연동하기 위해서는 새 데이터베이스 인스턴스를 생성한 후 연결 및 보안란 아래에 있는 엔드포인트를 `host`에 넣어주고, 인스턴스를 생성할 때 등록한 username과 password, 그리고 database명도 같이 변경해주면 된다. dotenv를 사용하여 같은 이름으로 로컬 환경에 사용할 세트와 클라우드 환경에 사용할 세트를 주석으로 관리하면 편리하게 연결할 데이터베이스를 변경할 수 있다.  

마지막으로, 인바운드 규칙에 허용할 IP/포트를 설정하면 되는데, 일단은 0.0.0.0/0으로 모든 인커밍을 허용했다. (보안문제로 권장하지는 않는다고는 하지만, 민감한 정보를 사용할 생각은 없기 때문에 일단은 이렇게 설정했다.)

![rds](/images/2024-09-13-22-11-14.png)

이렇게 AWS RDS로 데이터베이스 서버 인스턴스를 생성하고 나면 실제 데이터베이스를 구축해야 하는데, 개인적으로는 쉘을 사용해서 조작하는 것이 편하다고 느껴졌다. 

![cmd](/images/2024-09-13-22-16-16.png)  

위와 같이 데이터베이스를 만들어주고 난 후에는 실제 테이블의 스키마를 이식해야 하는데, 나는 TypeORM으로 데이터베이스를 설계해뒀기 때문에 이식이 굉장히 편했다. DataSource의 `synchronize`를 true로 변경만 하면 서버를 실행하는 시점에 데이터베이스가 기존에 짜둔대로 이식된다. 물론 쿼리문으로 `ADD CONSTRAINT`한 것들도 있기 때문에 이들은 추가해줘야 했다.  

```typescript
export const AppDataSource = new DataSource({

    type: 'mysql',
    host: DB_HOST,
    port: 3306,
    username: DB_USER,
    password: DB_PASSWORD,
    database: DB_DATABASE,
    synchronize: true,
    logging: false,
    entities: [User, Address, Routine, RoutineDetail, Item, SkinAttribute, UserSkinRelation, RoutineSkinRelation, RoutineTagRelation, ItemOrder, OrderDetail, Review, Tag],

});
```

기존에 `skin_attribute`엔터티에 대해서는 인스턴스들을 시딩해뒀기 때문에 시딩까지 완료해주면 AWS RDS 데이터베이스 세팅은 끝이다.  

---  

## 협업  

corou의 기능들을 함께 확인하기 위해 성준님과 디스코드로 화면공유를 하며 애플리케이션을 탐방했다.  

![success!](/images/2024-09-13-06-43-19.png)  

우선 새로운 데이터베이스이기 때문에 계정 생성을 했다.  

![user](/images/2024-09-13-22-22-07.png)
![relation](/images/2024-09-13-22-22-49.png)  

사용자와 사용자의 피부 관련 정보가 데이터베이스에 담긴 것을 확인할 수 있다. 이 부분은 기존 로컬에서도 테스팅이 잘 되던 부분이라, 단순히 클라우드 데이터베이스가 제대로 작동하는지에 대한 테스트 용도가 컸다.  

이후 Routine 생성을 함께 테스팅했는데, 생각보다 빈 부분들이 많았다. 이래서 기획 단계에서 보다 꼼꼼하게 구상을 해야 했는데 아직 설계하는 능력이 많이 부족하다고 느꼈다.  

우리의 서비스는 Routine별로 연관된 피부 타입과 피부 고민, 적정 나이대와 성별, 그리고 태그가 있어야 했는데 이 부분이 만들어져있지 않았다.  

이를 해결하기 위해 루틴과 피부타입을 다대다로 연결해주는 `routine_skin_relation`엔터티와 `tag`엔터티를 추가로 만들고 이를 기존의 `createRoutine`메소드에서 함께 처리하도록 했다. 

```typescript
async createRoutine(
        user_key: number,
        routine_name: string,
        steps: number,
        for_gender: 'M' | 'F' | 'A',
        for_skin: number,
        for_age: number,
        for_problem: Array<string>,
        details: Array<{
            item_key: number;
            step_name: string;
            description: string;
        }>,
        tags: Array<string>
    ): Promise<Routine> {
        const user = await this.userService.getUserByKey(user_key);
        if (!user) {
            throw new Error('해당 유저를 찾을 수 없습니다.');
        }
        return this.dataSource.transaction(async transactionalEntityManager => {
            const newRoutine = await transactionalEntityManager.save(Routine, {
                user,
                routine_name,
                steps,
                for_gender,
                for_age,
            });
            await this.routineSkinRelationService.addRoutineSkinRelation(
                newRoutine.routine_key,
                for_skin,
                transactionalEntityManager
            );
            for (const problem of for_problem) {
                await this.routineSkinRelationService.addRoutineSkinRelation(
                    newRoutine.routine_key,
                    Number(problem),
                    transactionalEntityManager
                );
            }
            for (const detail of details) {
                await this.routineDetailService.createRoutineDetail(
                    newRoutine.routine_key,
                    detail.item_key,
                    detail.step_name,
                    detail.description,
                    transactionalEntityManager
                );
            }
            console.log(tags);
            const tagKeys = [];
            for (const tag of tags) {
                const tagKey = await this.tagService.createTag(tag);
                tagKeys.push(tagKey);
            }
            console.log(tagKeys);
            for (const tagKey of tagKeys) {
                await this.routineTagRelationService.addRoutineTagRelation(
                    newRoutine.routine_key,
                    tagKey,
                    transactionalEntityManager
                );
            }
            console.log('루틴 태그 등록 완료')
            return newRoutine;
        });
    }
```
*받아야 하는 데이터도 많아졌고, 코드 자체도 길어졌다*  

코드를 확인해보면, 우선 루틴 틀을 만들고 이 루틴 틀에 기존에 없었던 `for_gender`과 `for_age` 속성을 넣어 추후 필터링 할 수 있도록 했다. 다만, 나이와 성별은 단일 값을 넣을 수 있기 때문에 이렇게 처리하기 수월했던 반면, 피부 문제와 태그는 여러 값을 하나의 루틴 인스턴스와 연결해야 하기 떄문에 위에서 말한대로 별도의 엔터티로 처리했다.  

이들을 등록하고 연결해주는 부분이 `addRoutineSkinRelation`메소드를 사용한 부분들이다. 피부 타입은 5가지만 구상하였고, 피부 고민도 6가지만 구상해두어 간단하게 하나의 엔터티로 관리하기 때문에 하나의 `routineSkinRelationService`로 처리할 수 있었다.  

```typescript
    async addRoutineSkinRelation(routine_key: number, attr_key: number, transactionalEntityManager: EntityManager): Promise<RoutineSkinRelation> {
        const relation = this.routineSkinRelationRepository.create({
            routine_key,
            attr_key
        });
        return transactionalEntityManager.save(RoutineSkinRelation, relation);
    }
```

다만 이 부분에서 create를 못하던 문제가 있었는데, 확인해보니 `routine_skin_relation` 엔터티는 등록이 되지 않았던 거였다. 

```typescript
export const AppDataSource = new DataSource({

    type: 'mysql',
    host: DB_HOST,
    port: 3306,
    username: DB_USER,
    password: DB_PASSWORD,
    database: DB_DATABASE,
    synchronize: true,
    logging: false,
    entities: [User, Address, Routine, RoutineDetail, Item, SkinAttribute, UserSkinRelation, RoutineSkinRelation, RoutineTagRelation, ItemOrder, OrderDetail, Review, Tag],

});
```

위 객체의 entities부분에서 빼먹었었다. 새로운 엔터티를 만들 땐 꼭 Repository에도 등록하고, DataSource객체에도 명시하도록 하자.  

Tag는 사용자가 자유롭게 10개까지 등록할 수 있기 때문에 어떻게 구현할지가 고민이었다. 어쨌든간에 그냥 있는 데이터가 아니라, 이를 사용하여 루틴들을 찾을 수 있어야 하기 때문에 더 신경써야 했다.  

적용하기로 한 방식은 다음과 같다:  

```typescript
@injectable()
export class TagService {
    constructor(
        @inject(REPOSITORY_TOKENS.TagRepository) private tagRepository: Repository<Tag>,
    ) { }

    async createTag(tag_name: string): Promise<number> {
        const tagExists = await this.tagRepository.findOne({ where: { tag_name } })
        if (tagExists) {
            console.log(tagExists);
            return tagExists.tag_key;
        } else {
            const newTag = this.tagRepository.create({
                tag_name
            });
            const savedTag = await this.tagRepository.save(newTag);
            return savedTag.tag_key;
        }
    }

    // 피부속성 조회 by key
    async getTagByKey(tag_key: number): Promise<Tag> {
        const tag = await this.tagRepository.findOneBy({ tag_key });
        if (!tag) {
            throw new Error('태그를 찾을 수 없습니다.');
        }
        return tag;
    }
}
```

태그를 프론트에서 스트링 배열로 보내주도록 하고, 만약 해당 태그가 이미 데이터베이스에 등록이 되어있다면 이에 대한 key를 반환하고, 없다면 새로 생성한 후에 해당 key를 반환하도록 했다. 여기서도 문제가 터졌었는데, create는 저장을 하지 않는다는 거를 아예 생각도 못하고 있었다. 왜 create를 했는데 반환값이 undefined일까 굉장히 오래 고민하며 기존에 만든 코드들을 봤는데, save를 안했을 뿐이었다(...).  

그리고 `skin_attribute`에 `routine_skin_relation`을 만들어준 것과 같이 `routine_tag_relation`을 만들어주어 어떤 루틴이 어떤 태그들을 달고 있는지에 대한 관계를 정의하도록 하면 Routine을 생성하는 부분은 완성이다!  

루틴 디테일에 사용될 테스트용 상품들을 등록해주고 루틴을 생성하면 제대로 등록된 것을 볼 수 있다. (38트만에 성공한 건 무시해주세요...)

![items](/images/2024-09-13-22-41-37.png)

![routine](/images/2024-09-13-22-42-34.png)

---
