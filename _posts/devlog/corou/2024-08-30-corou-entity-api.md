---
title: "corou : 엔터티/API (feat. 바보짓)"
writer: James
date: 2024-08-30 16:00:00 +0900
categories: [devlog, corou]
tags: [devlog, corou, side project]
pin: false
math: true
mermaid: true
image:
  path: https://github.com/user-attachments/assets/a3b8b50c-1c64-46e4-b24d-3d4f043dbfc7
---

## typeorm

TypeORM을 사용하여 데이터베이스와 서비스의 상호작용을 하기로 했다.  
TypeORM은 코드에 있는 '객체'와 데이터베이스에 있는 '데이터'를 편하게 일치시켜주는 툴로, 쿼리문을 직접 작성하지 않고 자동 생성해주는 방식으로 객체와 데이터를 동기화한다.  

사실 이제 막 SQL을 배운 입장에서는 직접 SQL 문을 사용하는 것이 좋을 수도 있겠다 생각하여 원래 아래 방식으로 작성했으나, 그래도 이미 공부해본 내용이기에 새로운 것을 배우고자 typeorm을 사용하기로 했다.  

```typescript
import mysql from 'mysql2/promise';
import dotenv from 'dotenv';

const pool = mysql.createPool({
  host: 'localhost',        
  user: process.env.DB_USER,     
  password: process.env.DB_PASSWORD, 
  database: process.env.DB_DATABASE, 
  waitForConnections: true, 
  connectionLimit: 10,      
  queueLimit: 0             
});

export default pool;
```
*직접 연결*  

```typescript
import 'reflect-metadata';
import { DataSource } from 'typeorm';
import dotenv from 'dotenv';
import { User } from '../entity/user.entity';
import { Routine } from '../entity/routine.entity';
import { Item } from '../entity/item.entity';

dotenv.config({ path: '../../.env' });

const { DB_DATABASE, DB_USER, DB_HOST, DB_PASSWORD } = process.env;

export const AppDataSource = new DataSource({
    type: 'mysql',
    host: DB_HOST,
    port: 3306,
    username: DB_USER,
    password: DB_PASSWORD,
    database: DB_DATABASE,
    synchronize: false, 
    logging: false,
    entities: [User, Routine, Item], 
    migrations: [],
    subscribers: []
});
```
*TypeORM 사용*  

![나 뭐했니.](/images/2024-08-30-23-54-21.png)
*나 뭐한거니...*

## 모델 정의

![엔터티](/images/2024-08-30-23-20-22.png)  

이후에는 모델(엔터티)에 대한 코드를 만들어주었다.  

```typescript
import 'reflect-metadata';
import { Entity, PrimaryGeneratedColumn, Column, OneToMany, JoinColumn } from 'typeorm';
import { Routine } from './routine.entity';
import { Address } from './address.entity';
import { ItemOrder } from './item-order.entity';

@Entity('user')
export class User {
    @PrimaryGeneratedColumn()
    user_key!: number;

    @Column({ type: 'varchar', length: 255, unique: true })
    email!: string;

    @Column({ type: 'varchar', length: 255 })
    password!: string;

    @Column({ type: 'varchar', length: 255 })
    username!: string;

    @Column({ type: 'date' })
    birth_date!: Date;

    @Column({ type: 'char', length: 1 })
    gender!: 'M' | 'F';

    @OneToMany(() => Routine, routine => routine.user)
    @JoinColumn({ name: 'user_key' })
    routines!: Routine[];

    @OneToMany(() => Address, address => address.user)
    @JoinColumn({ name: 'user_key' })
    addresses!: Address[];

    @OneToMany(() => ItemOrder, itemOrder => itemOrder.user)
    @JoinColumn({ name: 'user_key' })
    itemOrders!: ItemOrder[];
}
```

근데 찾아보니 TypeORM 자체가 테이블을 생성해준다고 한다. 그렇다. 나는 쿼리문으로 테이블을 괜히 만들었던 것이었다. 그냥 학습했다고 생각하고 넘어가려고 한다...  
다행인 건, 이미 만들어 놓았으면 매핑만 된다고 하니 중복 생성같은 이상한 짓은 안한다는 것이다.  

## API 명세  

팀원과 같이 하다보니 API명세를 해야 할 필요를 느낀다. 직전 프로젝트는 바로 옆에 앉아 '*이거 만들자, 이거 이렇게 해야한다*'와 같이 주먹구구식으로 했지만, 이번엔 서울과 전주에서 서로 떨어져서 하다 보니 이런 명세서를 만드는 것이 적절해보인다.  

![API명세](/images/images/2024-08-30-23-46-35.png)  

명세를 먼저 끝내 성준님이 작업을 최대한 빨리 시작할 수 한 후에 서버를 구축할 에정이다.  
인터넷에 개발 순서를 확인해보니 API명세를 사실 일찍 해야하는 것 같아 성준님에게 죄송하다...  

---