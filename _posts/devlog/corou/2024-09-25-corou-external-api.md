---
title: "corou : 필터링, 외부 API"
writer: James
date: 2024-09-25 00:01:00 +0900
categories: [devlog, corou]
tags: [devlog, corou, side project]
pin: false
math: true
mermaid: true
image:
  path: https://blog.kakaocdn.net/dn/I0fI1/btqCUbSwdwr/WeSNgsCc4KjVyX35SUiSH1/img.png
---

## 필터링  

corou는 루틴과 제품을 검색할 때 필터링을 할 수 있도록 기획되었다. 우선 두 가지 방법이 떠올랐다. 첫번째는 백엔드에서 getRoutineByFilter/getItemByFilter 메소드를 각각의 service파일에 만드는 것이다.  

```typescript
async getRoutineByFilter(
    skin_type?: number,
    skin_problems?: number[] = [],
    age?: number,
    gender?: "M" | "F",
) : Promise<Routine>[] {
    const query = this.routineRepository.createQueryBuilder('routine')
      .leftJoinAndSelect('routine.skinAttributes', 'skinAttribute');
    if (skin_type) {
      query.andWhere('skinAttribute.skin_type = :skin_type', { skin_type });
    }
    if (skin_problems && skin_problems.length > 0) {
      query.andWhere('skinAttribute.skin_problem IN (:...skin_problems)', { skin_problems });
    }
    // 추가 필터링
}
```

두번째는, 프론트엔드에 전체 루틴/제품 목록과 이들에 연관된 속성들을 보내준 후 클라이언트에서 키워드를 가지고 렌더링 할 항목들을 추려서 그리는 것이다.  

백엔드에서 처리한다면 전체 루틴 목록이 크면 클 수록 유리할 것이다. 전체 목록을 보내는 것보다는 서버에서 미리 처리를 하여 필요한 데이터만 보낼 수 있기 때문에 전체 목록이 크면 클 수록 속도 차이가 날 것으로 예상된다. 다만, 이렇게 할 경우 필터를 새로 선택할 때마다 API요청과 응답을 주고 받을 것이며 이는 필연적으로 레이턴시의 증가를 불러온다.  

반면, 프론트엔드에서 처리할 경우에는 처리 속도가 빠르다. 필터가 적용될 때 이미 가지고 있는 전체 데이터를 기반으로 렌더링을 새로 하는 것이기 때문에 서버의 처리를 기다리지 않아도 된다.  

어차피 처음 루틴 페이지에 진입하는 시점에 전체 목록을 받아가야 하기 때문에 두 번째 방식이 더 좋다고 생각하여 해당 방식으로 진행했다.  

## 결제  

결제모듈은 이 프로젝트에서 내가 꼭 써보고 싶었던 외부 API이다. 지난 프로젝트는 정글 발표의 특성상 상업적인 측면을 배제했기에 결제가 들어갈 틈이 없었다. 다만, 당시 멘토님께서 추후 개인 프로젝트를 할 때는 꼭 상업적인 측면을 보여줄 수 있는 프로젝트를 하라고 추천했고, 나 또한 취업 측면에서 이러한 API를 사용해보는 경험이 중요하다고 생각했다.  

사용하기로 한 결제 API는 PortOne이다. PortOne은 테스트용 채널을 만들 수 있었기에 사업자등록증이나 복잡한 절차 없이 바로 API를 사용해볼 수 있었다.  
다만, 외부 API의 사용이 처음이여서 꽤나 오래 걸렸다. 공식 문서를 읽고, 다른 사람들이 사용한 사례를 몇번이고 다시 보았다.  

```typescript
import axios from 'axios';
import dotenv from 'dotenv';

dotenv.config()

const PORTONE_API_BASE_URL = 'https://api.iamport.kr';
const PORTONE_API_KEY = process.env.PORTONE_REST_API_KEY;
const PORTONE_API_SECRET = process.env.PORTONE_API_SECRET;

export const portoneClient = axios.create({
    baseURL: PORTONE_API_BASE_URL,
    headers: {
        'Content-Type': 'application/json',
        'Authorization': `Bearer ${PORTONE_API_KEY}:${PORTONE_API_SECRET}`
    }
});
```

위와 같이 PortOne 요청들을 처리할 `portone.config.ts`를 만들어준 후, 다음과 같이 이를 사용하여 요청을 PortOne 서버로 보내도록 하였다:  

```typescript
import { injectable } from 'tsyringe';
import { portoneClient } from '../config/portone.config';
import dotenv from 'dotenv';

dotenv.config()

const PORTONE_REST_API_KEY = process.env.PORTONE_REST_API_KEY;
const PORTONE_API_SECRET = process.env.PORTONE_API_SECRET;

@injectable()
export class PortoneService {
    async fetchAccessToken(): Promise<string> {
        try {
            console.log('api key', process.env.PORTONE_REST_API_KEY)
            console.log('api secret', process.env.PORTONE_API_SECRET)
            const response = await portoneClient.post('/users/getToken', {
                imp_key: PORTONE_REST_API_KEY,
                imp_secret: PORTONE_API_SECRET
            });

            const accessToken = response.data.response.access_token;
            console.log('Access Token:', accessToken);
            return accessToken;
        } catch (error) {
            console.error('Error fetching access token:', error);
            throw new Error('Failed to fetch access token');
        }
    }


    async getPayment(impUid: string): Promise<any> {
        try {
            const accessToken = await this.fetchAccessToken();

            const response = await portoneClient.get(`/payments/${impUid}`, {
                headers: {
                    'Authorization': `Bearer ${accessToken}`,
                    'Content-Type': 'application/json'
                }
            });

            console.log('Payment details:', response);
            return response.data.response;
        } catch (error) {
            console.error('Error querying payment:', error);
            throw new Error('Failed to query payment');
        }
    }
}
```

절차는 다음과 같다:  

1. 서비스(corou) 클라이언트에서 PortOne 서버로 결제 정보와 함께 결제 요청을 보내면, 요청 성공 시 imp_uid라는 결제 id를 받게 된다.  
2. 서비스 클라이언트에서 해당 id를 서비스(corou) 서버에 구현한 GET /api/payment로 보내면 서비스 서버는 PortOne 서버에서 접근 권한 토큰을 받아 PortOne서버로 GET /payments/:imp_uid를 보내어 해당 결제건에 대한 정보를 받는다.  
3. 결제건에 대한 정보를 서비스 서버는 서비스 클라이언트로 보내어 결과를 사용자에게 출력할 수 있도록 한다.  
4. 서비스 클라이언트는 결제한 장바구니에 대한 주문 내역을 만들기 위해 서비스 서버로 POST /api/order/itemorder을 요청한다.  

사용해보고 나열해보니 꽤 간단명료하지만, 생각보다 정확한 절차를 파악하기가 처음엔 어려웠다.  

![tosspayments](/images/2024-09-24-03-57-57.png)
![making payment](/images/2024-09-24-03-58-30.png)
![popup](/images/2024-09-24-03-58-45.png)
*서버로부터 성공 응답을 받으면 팝업으로 사용자에게 보여준 후 화면이 넘어간다*  

## OAuth (카카오)  

카카오 로그인은 결제보다 더한놈이다. OAuth 또한 MotionBeat 프로젝트때 써보고 싶었지만 사용해보지 못한 것이였다. Google OAuth도 사용해보고 싶었지만 프론트엔드 파트너인 성준님의 부담도 고려하여 Kakao 하나로만 진행하기로 했다.  

PortOne을 직전에 사용해봐서 그런지 비슷하게 구현해보려고 했다.

```typescript
import axios from "axios";
import dotenv from "dotenv";

dotenv.config();

const KAKAO_URL = "https://kauth.kakao.com/";
const KAKAO_KEY = process.env.KAKAO_REST_API_KEY;

export const kakaoClient = axios.create({
  baseURL: KAKAO_URL,
  headers: {
    "Content-Type": "application/json",
    Authorization: `Bearer ${KAKAO_KEY}`,
  },
});
```

우선 `kakao.config.ts`를 비슷하게 구현했다. ~~이게 나중에 발목을 잡는다~~  

![REST Kakao](https://developers.kakao.com/docs/latest/ko/assets/style/images/kakaologin/kakaologin_sequence.png)  

카카오는 절차를 꽤나 디테일하게 알려준다 (그치만 그대로 따르지는 않았다).

다른 사람들의 블로그를 참고해보니 인가 코드를 받는 전체 과정을 서비스 클라이언트에서 처리하는 경우가 많았다. 우리도 해당 부분은 전체 프론트 처리로 진행했다.  

```typescript
const SocialKakao: React.FC = () => {
  const REST_API_KEY = process.env.REACT_APP_KAKAO_KEY;
  const REDIRECT_URI = "http://localhost:3001/kakao/oauth";
  const kakaoLink = `https://kauth.kakao.com/oauth/authorize?client_id=${REST_API_KEY}&redirect_uri=${REDIRECT_URI}&response_type=code`;

  const loginHandler = () => {
    window.location.href = kakaoLink;
  };

  return (
    <>
      <KakaoBtnWrapper>
        <KakaoBtn onClick={loginHandler}>
          <img src={kakaoIcon} alt="kakaoIcon" />
          카카오로 시작하기
        </KakaoBtn>
      </KakaoBtnWrapper>
    </>
  );
};
export default SocialKakao;
```
*서비스 클라이언트에서 GET /oauth/authorize로 인가 코드를 받아온다*  

```typescript
  useEffect(() => {
    console.log(authorizationCode);
    if (authorizationCode) {
      axios
        .post(`${backPort}/api/kakao/login`, {
          code: `${authorizationCode}`,
        })
        .then((response) => {
          const data = response.data;
          console.log(data);

          navigate("/");
        })
        .catch((error) => {
          console.error("오류 발생", error);
        });
    }
  }, [authorizationCode, navigate]);
```

이후 백엔드에 구현한 API엔드포인트인 POST /api/kakao/login으로 인가 코드를 보내도록 했다.  

```typescript
//kakao.controller.ts

@injectable()
export class KakaoController {
  constructor(
    private userService: UserService,
    private kakaoService: KakaoService
  ) {}

  async requestToken(req: Request, res: Response): Promise<void> {
    const { code } = req.body;

    try {
      const token = await this.kakaoService.requestToken(code);
      res.status(200).json(token);
    } catch (error) {
      res.status(400).json({ message: "카카오 토큰 발급 실패." });
    }
  }

  async kakaoLogin(req: Request, res: Response): Promise<void> {
    const { code } = req.body; // 클라이언트로부터 받은 인가 코드

    try {
      // 1. 인가 코드로 토큰 요청
      const token = await this.kakaoService.requestToken(code);

      // 2. 액세스 토큰으로 카카오 사용자 정보 요청
      const kakaoUser = await this.kakaoService.getUserInfo(token.access_token);

      // 카카오에서 받아온 정보
      console.log(kakaoUser);
      const username = kakaoUser.kakao_account.profile.nickname;
      const email = `${username}@corou.com`;

      // 3. 서비스에 사용자가 존재하는지 확인
      let user = await this.userService.getUserByEmail(email);

      // 4. 사용자가 존재하지 않으면 자동으로 회원가입 처리
      if (!user) {
        res.status(300).json({ email, password: "kakaoacc1234!" });
        return;
      }

      console.log("check here");
      // 5. 로그인 처리: 세션을 생성하거나 JWT 발급
      const jwtToken = generateToken({ user_key: user.user_key });

      // 6. 프론트엔드에 JWT 전송
      res.status(200).json({
        token: jwtToken,
        user: { user_key: user.user_key, username: user.username },
      });
    } catch (error: any) {
      res.status(400).json({ message: error.message });
    }
  }
}
```

해당 API요청은 위의 requestToken 메서드로 넘어가는데, 여기서 인가 코드를 가지고 POST /oauth/token으로 요청하여 토큰을 발급받는다.  

```typescript
// kakao.service.ts
    async requestToken(code: any): Promise<any> {
        try {
            console.log("코드 받아와지는지", code);
            const response = await kakaoClient.post(`/oauth/token`, null, {
                params: {
                    grant_type: "authorization_code",
                    client_id: process.env.KAKAO_REST_API_KEY,
                    redirect_uri: process.env.KAKAO_REDIRECT_URI,
                    code,
                },
            });
            const token = {
                access_token: response.data.access_token,
                refresh_token: response.data.refresh_token,
            };
            console.log(token);
            return token;
        } catch (error: any) {
            if (error.response) {
                console.error("Error response from Kakao: ", error.response.data);
            } else {
                console.error(
                    "Error fetching data from Kakao: ",
                    error.message || error
                );
            }
            throw new Error("Failed to fetch access token from Kakao");
        }
    }
```

다음이 문제였는데, 카카오 인증 토큰을 받아오면 이를 사용해서 사용자 정보를 받아와야 한다.  

```typescript
    async getUserInfo(accessToken: string): Promise<any> {
        console.log('access:', accessToken);
        const response = await kakaoClient.get("/v2/user/me", {
            headers: {
                Authorization: `Bearer ${accessToken}`,
            },
        });
        console.log('response:', response);
        return response.data;
    }
```

위와 같이 구현하니까 되지 않는다. response가 실패했다고 오는 것 아닌가.  

여기서의 문제는 내가 API를 확인을 못했던 부분에 있다.  

GET /v2/user/me를 사용하여 사용자 정보를 받아오는 것은 kakaoClient에 쓴 BASE_URL인 `https://kauth.kakao.com/`이 아닌 `https://kapi.kakao.com/`으로 보내야 했다.  

```typescript
    async getUserInfo(accessToken: string): Promise<any> {
        console.log('access:', accessToken);
        const response = await axios.get("https://kapi.kakao.com/v2/user/me", {
            headers: {
                Authorization: `Bearer ${accessToken}`,
            },
        });
        console.log('response:', response);
        return response.data;
    }
```
*바로 axios로 변경해줬다*  

이렇게 하니 정상적으로 사용자 정보는 받아와졌지만, 사업자 등록증 없이 받아올 수 있는 정보는 사용자의 nickname이 최대였다... 어차피 카카오 로그인 버튼을 누르고 할 것이라 상관이 없을 것 같아 `${username}@corou.com`이라는 이메일 형식 같아보이도록만 만들어준 후 해당 이메일이 데이터베이스에 있으면 로그인, 없으면 회원가입하도록 구현했다.  

---  

이렇게 내 파트를 마친 후에 성준님 할 일을 정리해줬다. ~~일 찾아드렸습니다 ㅎㅎ~~

![for front](/images/2024-09-24-04-15-37.png)  

서비스를 실제로 사용하면서 테스팅하니 넣어야 할 것이 산더미지만 다 해낼 수 있을 것이라 믿는다 ㅎㅎ.  

![front done](/images/2024-09-24-04-16-46.png)  
*그동안 정리해드린 것들 다 해낸 것 보세요!*