---
title: "corou : 쿼리 검색 기능과 장바구니, 주문"
writer: James
date: 2024-09-20 11:00:00 +0900
categories: [devlog, corou]
tags: [devlog, corou, side project]
pin: false
math: true
mermaid: true
image:
  path: https://github.com/user-attachments/assets/39dca1c3-0b51-40b6-9204-cab6e8b6eda7
---

## 검색 API

신규 루틴을 등록할 때, 각 단계에 제품을 선택해야 한다.  

처음 테스팅할 때는 개발자로써 넣은 더미데이터의 item_key를 알고 있었기 때문에 단순히 이를 전달하면 되지만, 실제 사용자는 이런 key값을 알 리가 없다. 그렇다면 제품의 이름을 넣고 찾아야 하는데, 제품 이름이 '웰라쥬 리얼 시카 카밍 95 트러블 앰플 50ml (2개입)'와 같이 실제 상품일 경우 이를 다 치는 것 또한 UX측면에서 적절하지 않다. 이를 해결하기 위해 검색에 사용할 수 있는 API를 구현했다.  

```typescript
    async searchItem(query: string): Promise<Item[]> {
        return this.itemRepository.createQueryBuilder('item')
            .where('item.item_name LIKE :query', { query: `%${query}%` })
            .orWhere('item.brand_name LIKE :query', { query: `%${query}%` })
            .getMany();
    }
```

이런 방식으로 사용자가 입력한 쿼리 문자열을 기반으로 제품을 검색한다. 부분 일치를 찾기 위해 와일드카드 %를 사용했고, 클라이언트 측에서는 해당 API를 문자열 3자 이상을 입력 시 계속 호출하도록 했다. 이렇게 찾은 제품은 객체 배열로 클라이언트에 전달하여 목록을 띄울 수 있도록 했다.  

![img](/images/2024-09-20-22-50-18.png)  
*루틴에 대해서도 비슷하게 구현했다*  

## 장바구니와 주문  

장바구니 엔터티를 먼저 새로 만들어줘야 했는데, 생각해보니 장바구니 자체가 사용자 `user`와 제품 `item`의 관계를 표현해주는 징검다리여서 `routine_skin_relation`이나 `routine_tag_relation`과 같은 형태로 만들어주었다.  

```typescript
import { Entity, PrimaryGeneratedColumn, Column, ManyToOne, JoinColumn } from 'typeorm';
import { User } from './user.entity';
import { Item } from './item.entity';

@Entity()
export class Cart {
    @PrimaryGeneratedColumn()
    cart_key!: number;

    @Column()
    user_key!: number;

    @Column()
    item_key!: number;

    @Column()
    quantity!: number;

    @ManyToOne(() => User, (user) => user.carts)
    @JoinColumn({ name: 'user_key' })
    user!: User;

    @ManyToOne(() => Item, (item) => item.carts)    
    @JoinColumn({ name: 'item_key' })
    item!: Item;
}
```

```typescript
import { inject, injectable } from 'tsyringe';
import { Repository } from 'typeorm';
import { REPOSITORY_TOKENS } from '../config/constants';
import { Cart } from '../entities/cart.entity';

@injectable()
export class CartService {
    constructor(
        @inject(REPOSITORY_TOKENS.CartRepository) private cartRepository: Repository<Cart>
    ) { }

    async addToCart(user_key: number, item_key: number, quantity: number): Promise<Cart> {
        const cart = this.cartRepository.create({ user_key, item_key, quantity });
        return this.cartRepository.save(cart);
    }

    async getCart(user_key: number): Promise<Cart[]> {
        return this.cartRepository.find({ where: { user_key } });
    }

    async deleteCart(cart_key: number): Promise<void> {
        await this.cartRepository.delete(cart_key);
    }

    async updateCart(cart_key: number, quantity: number): Promise<void> {
        await this.cartRepository.update(cart_key, { quantity });
    }
}
```
서비스 계층은 단순하게 구현했다. 크게 복잡한 로직이 없는 부분이라 추가하기에 수월했다.  

이제 이렇게 장바구니에 담긴 제품을 사용자가 구매하기로 하면 ItemOrder 인스턴스를 만들어줌과 동시에 PortOne으로 해당 제품들의 가격을 전달해주어 결제처리 하는 것을 트랜잭션으로 해야한다.

---   

## 드디어...

조금씩 끝이 보인다.  

지금 성준님을 최대한 예의바르게 쪼면서 작업을 하고 있는데, 그렇다면 도의적으로 내가 일정관리와 태스크관리를 맡는 것이 맞는 것 같아 Issues로 정리하여 성준님에게 전달했다.  

![issue](/images/2024-09-20-22-53-34.png)
![img2](/images/2024-09-20-22-50-59.png)  

우선 어제 드렸던 태스크인 메인페이지에 루틴과 제품 연동은 마쳤고, 위 태스크들을 마무리하면 얼추 프로젝트는 끝난다.  

