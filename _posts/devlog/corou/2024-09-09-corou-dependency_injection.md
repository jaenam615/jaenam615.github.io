---
title: "corou : 의존성 주입 오류"
writer: James
date: 2024-09-09 01:00:00 +0900
categories: [devlog, corou]
tags: [devlog, corou, side project]
pin: false
math: true
mermaid: true
image:
  path: https://github.com/user-attachments/assets/a7adbf8d-50ce-4448-b157-825e82e730b8
---

## 의존성 주입  

```shell
Error resolving UserController: Error: Cannot inject the dependency "userService" at position #0 of "UserController" constructor. Reason:
    Cannot inject the dependency at position #0 of "UserService" constructor. Reason:
        Attempted to resolve unregistered dependency token: "UserRepository"
```

이 오류를 토요일에 약 5시간동안 고치지 못했다. 오류를 읽어보면 userRepository가 register되지 않아 이를 의존성으로 갖는 userService를 주입을 못하는 상황이 계속 발생하는 듯 했다.  

그렇지만, 아래를 보면 분명 register을 했는데 계속 이런 일이 발생하는 것이었다:  

```typescript
import 'reflect-metadata';
import { DataSource } from 'typeorm';
import dotenv from 'dotenv';
import { container } from 'tsyringe';
import { REPOSITORY_TOKENS } from './constants';

import { User } from '../entities/user.entity';

import { UserService } from '../services/user.service';

dotenv.config({ path: '.env' });

const { DB_DATABASE, DB_USER, DB_HOST, DB_PASSWORD } = process.env;

if (!DB_DATABASE || !DB_USER || !DB_HOST || !DB_PASSWORD) {
    throw new Error('Missing database configuration environment variables.');
}

export const AppDataSource = new DataSource({
    type: 'mysql',
    host: DB_HOST,
    port: 3306,
    username: DB_USER,
    password: DB_PASSWORD,
    database: DB_DATABASE,
    synchronize: false,
    logging: false,
    entities: [User, Address, Routine, Item, SkinAttribute, ItemOrder, OrderDetail, Review],
});


export const initializeDatabase = async () => {
    try {
        await AppDataSource.initialize();
        console.log('Database connection established successfully');

        const repositories = [
            { name: 'UserRepository', entity: User },
            // other entities
        ];

        for (const repo of repositories) {
            container.register(repo.name, { useValue: AppDataSource.getRepository(repo.entity) });
        }

        container.register(UserService, { useClass: UserService });
        // other services

    } catch (error) {
        console.error('Error initializing database:', error);
    }
}
```

이 문제를 해결하기 위해 여러 코드를 상당 부분 간소화했는데, 그럼에도 계속 같은 오류가 발생하니 AI를 통해 문제점을 파악하고자 했지만 이 또한 별 소용 없었다.  

콜스택을 트레이싱 하다 보니 user.router.ts가 문제인 듯 하여 이를 유심히 봤다.  

주입 컨테이너가 제대로 설정이 되지 않아 register가 되지 않는 느낌이 컸는데, 분명 register 아래에 콘솔을 찍어보면 제대로 register을 했다는 로그만 남을 뿐이었다.  

```typescript
//server.ts
import app from './app';
import userRouter from './routes/user.router';
import { initializeDatabase } from './config/ormconfig';

const PORT = process.env.PORT || 3000;

initializeDatabase().then(() => {
    app.use('/api/user', userRouter);

    app.get('/', (req, res) => {
        res.send('Hello World');
    });

    app.listen(PORT, () => {
        console.log(`Server is running on port ${PORT}`);
    });
}).catch((error) => {
    console.error('Error starting the server:', error);
});
```

```typescript
// ormconfig.ts
export const initializeDatabase = async () => {
    try {
        await AppDataSource.initialize();
        console.log('Database connection established successfully');

        const repositories = [
            { name: REPOSITORY_TOKENS.UserRepository, entity: User },
            { name: REPOSITORY_TOKENS.AddressRepository, entity: Address },
            { name: REPOSITORY_TOKENS.RoutineRepository, entity: Routine },
            { name: REPOSITORY_TOKENS.ItemRepository, entity: Item },
            { name: REPOSITORY_TOKENS.SkinAttributeRepository, entity: SkinAttribute },
            { name: REPOSITORY_TOKENS.ItemOrderRepository, entity: ItemOrder },
            { name: REPOSITORY_TOKENS.OrderDetailRepository, entity: OrderDetail },
            { name: REPOSITORY_TOKENS.ReviewRepository, entity: Review },
        ];

        for (const repo of repositories) {
            container.register(repo.name, { useValue: AppDataSource.getRepository(repo.entity) });
        }
    } catch (error) {
        console.error('Error initializing database:', error);
    }
}
```

진입점 (server.ts)에서 ormconfig에서 만들어둔 `initializeDatabase`를 `.then()`을 사용해서 순차적으로 데이터베이스 연결 -> 서버 오픈 순서로 작성했는데도 문제가 생겼다.  

user.router.ts에서 UserController가 resolve될 때, router를 import한 시점에 바로 controller를 resolve해서 생긴 문제였다.  

이 문제를 고치기 위해 많은 시도를 거친 후 드디어 문제를 잡았다.  

```typescript
import { Router } from 'express';
import { UserController } from '../controllers/user.controller';

const router = Router();
const userController = new UserController();

router.post('/register', userController.createUser);

export default router;
```
*모듈 형태*  

모듈 형태로 되어있던 라우터들을 함수형으로 바꾸니 해결이 되었다. 모듈 형태의 코드는 import 될 때 한 번 실행되기 때문에 UserController의 인스턴스를 생성하는데, 이 시점에 엔터티 레포지토리들이 등록이 안되어 있었기에 생긴 문제였다.  

```typescript
//routes.ts
import { Express } from 'express';
import { setupUserRouter } from './user.router';
// import routineRouter from './routine.router';

export function setupRoutes(app: Express) {
    app.use('/api/user', setupUserRouter());
    
    // app.use('/api/routine', setupRoutineRouter());
}

//user.router.ts
import { Router } from 'express';
import { container } from 'tsyringe';
import { UserController } from '../controllers/user.controller';
import { authMiddleware } from '../middlewares/auth.middleware';

export function setupUserRouter(): Router {
    const router = Router();
    const userController = container.resolve(UserController);

    router.get('/check/:username', (req, res) => userController.checkUsername(req, res));
    router.get('/self', authMiddleware, (req, res) => userController.getSelf(req, res));
    router.get('/:user_key', (req, res) => userController.getUserByKey(req, res));
    router.get('/', (req, res) => userController.getAllUsers(req, res));
    router.post('/register', (req, res) => userController.createUser(req, res));
    router.post('/login', (req, res) => userController.loginUser(req, res));
    router.post('/:user_key/address', authMiddleware, (req, res) => userController.addAddress(req, res));
    router.put('/:user_key/address', authMiddleware, (req, res) => userController.updateAddress(req, res));

    return router;
}
```

이렇게 난관(...)을 거치고 API들을 postman을 통해 테스트하니 잘 작동하는 것을 확인했다.  

![logintest](/images/2024-09-09-22-38-43.png)

![succ](/images/2024-09-09-22-39-30.png)
--- 

