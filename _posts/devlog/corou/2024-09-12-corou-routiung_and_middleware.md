---
title: "corou : 라우터, 미들웨어, 폴링"
writer: James
date: 2024-09-12 01:00:00 +0900
categories: [devlog, corou]
tags: [devlog, corou, side project]
pin: false
math: true
mermaid: true
image:
  path: https://miro.medium.com/v2/resize:fit:1400/1*kJpzW9u-f2TH6bkzd4CC8A.png
---

## 라우터  

현재 상태에서 필요한 라우터들을 모두 구현했다. 초기 피그마로 구상했던 기능들은 모두 엔드포인트를 만들어주어 이제 기획단계에서 잠시 보류했던 주문서 페이지를 만들면 되는데, 이를 위해서는 우선 토이 결제모듈을 공부해봐야 한다.  

![figma](/images/2024-09-12-22-06-30.png)
*figma 기획*

![routers](/images/2024-09-12-22-10-21.png)

## 미들웨어  

미들웨어는 현재 인증 미들웨어와 Rate Limiting 미들웨어가 구현이 되어있다. 둘 다 정글에서 Motion Beat를 만들 때 구현했던 거라 큰 어려움은 없었지만 Rate Limiting 옵션에서 `max` 필드가 `limit`으로 바뀐줄 몰라서 조금 찾아봐야 했다.  

![rate limit](/images/2024-09-12-22-13-09.png)

추후 캐싱과 보안 관련 미들웨어를 추가적으로 구현할 생각이 있지만, 둘 다 테스팅 단계에서는 오히려 마이너스가 될 수도 있다고 판단하여 일시적으로 보류해두기로 했다.  

## BCRYPT  

웹 서비스에서는 보안을 위해 해시함수를 사용하여 비밀번호를 데이터베이스에 저장하기 전에 '해싱'해야 하는데, 그 중 가장 보편적으로 사용되는 Bcrypt를 사용하기로 했다.  

해싱을 해둔 이후에 걱정해야 할 보안적인 문제로는 브루트포스 방식으로 비밀번호를 찾으려는 공격이 있는데 Bcrypt는 salt라는 개념을 통해 랜덤한 값을 비밀번호에 더해 rainbow table을 방지할 수 있다.  

> rainbow table 이란?  
>> 해시된 값에 대한 원래 비밀번호를 모두 갖고 있는 테이블로, 해시값을 역산하여 원래 비밀번호를 찾을 수 있도록 한다

여기에 추가적으로, Bcrypt는 원래부터 느리게 작동하도록 디자인되어 공격하는 입장에서 브루트포스 식으로 비밀번호를 구하기 어려워진다.  

## 폴링  

특정 루틴이나 상품을 조회하고 있을 때 새로운 리뷰가 달리면 해당 페이지에 반영되도록 설계하고자 했다. 기존에 사용 경험이 있는 Websocket을 이용하기엔 연결을 뚫어줘야 하기도 했고, 자원적인 측면에서 비효율적일 것 같아 다른 방법을 찾아보았는데 그 중 가장 적합한 방식으로 Short Polling을 선택했다.  

정확하게 말하자면, 인터벌이 긴 Short Polling을 사용하기로 했는데 Long Polling 대비 효율이 좋지 않은 경우가 많지만 리뷰같은 굳이 실시간 반영이 필요하지 않은, 어느정도 텀을 두고 반영되어도 괜찮은 것에 대해서는 오히려 최적화되도록 구현할 수 있다고 판단했다.  

```typescript
//review.controller.ts
    async getReviewByRoutine(req: Request, res: Response): Promise<void> {
        const { routine_key } = req.params;
        const lastPolled = req.query.lastPolled ? new Date(req.query.lastPolled as string) : new Date(0);
        try {
            const reviews = await this.reviewService.getReviewByRoutine(Number(routine_key), lastPolled);
            res.status(200).json(reviews);
        } catch (error) {
            res.status(500).json({ message: '리뷰 조회에 실패했습니다.' });
        }
    }

    async getReviewByItem(req: Request, res: Response): Promise<void> {
        const { item_key } = req.params;
        const lastPolled = req.query.lastPolled ? new Date(req.query.lastPolled as string) : new Date(0);
        try {
            const reviews = await this.reviewService.getReviewByItem(Number(item_key), lastPolled);
            res.status(200).json(reviews);
        } catch (error) {
            res.status(500).json({ message: '리뷰 조회에 실패했습니다.' });
        }
    }
```

```typescript
//review.service.ts
    async getReviewByRoutine(routine_key: number, lastPolled?: Date): Promise<Review[]> {
        const whereClause: any = { routine_key };
        if (lastPolled) {
            whereClause.review_at = MoreThan(lastPolled);
        }

        const reviews = await this.reviewRepository.find({
            where: whereClause,
            order: { review_at: 'DESC' }
        });
        if (!reviews) {
            return [];
        }
        return reviews;
    }
    async getReviewByItem(item_key: number, lastPolled?: Date): Promise<Review[]> {
        const whereClause: any = { item_key };
        if (lastPolled) {
            whereClause.review_at = MoreThan(lastPolled);
        }
        const reviews = await this.reviewRepository.find({
            where: whereClause,
            order: { review_at: 'DESC' }
        });
        if (!reviews) {
            return [];
        }
        return reviews;
    }
```

Short Polling을 구현하기 위해, 우선 HTTP요청을 받는 컨트롤러 메소드에서 요청을 받은 시점을 구해 서비스로 넘겨주도록 했다. 서비스는 인자로 이 시간을 받아 해당 시점 이후의 리뷰들을 반환한다.  

사실 폴링 자체가 프론트엔드에 치중한 기술이기 때문에 클라이언트는 이러한 타임스탬프를 기준으로 새로 받은 리뷰들을 출력하는 것이다.  

---