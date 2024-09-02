---
title: "corou : 시작과 오류"
writer: James
date: 2024-09-02 01:00:00 +0900
categories: [devlog, corou]
tags: [devlog, corou, side project]
pin: false
math: true
mermaid: true
image:
  path: https://github.com/user-attachments/assets/a3b8b50c-1c64-46e4-b24d-3d4f043dbfc7
---

## corou

API 명세서를 정리하다가 얼추 필요한 API의 틀을 잡았다 생각해서 나머지는 개발을 진행하다가 추가해도 되겠다 싶어서 코딩을 시작했다.  

## TypeScript-Express

우선 TypeScript로 express 서버를 만들 때는 ts-node 패키지를 설치하여 TypeScript 파일을 직접 실행할 수 있도록 해주어야 한다.  

```shell
npm i ts-node
ts-node src/server
```

여기에, 자주 사용하는 `nodemon`으로 TypeScript 파일을 실행하기 위해서는 이를 위한 json파일을 설정해주어야 한다:  

```json
{
    "watch": ["src"],
    "ext": "ts",
    "ignore": ["src/**/*.spec.ts"],
    "exec": "ts-node src/server.ts"
  }
```

이렇게 만들어두면 `nodemon exec`으로 서버를 시작할 수 있다.  

![exec](/images/2024-09-01-20-34-22.png)  

## MySQL 에러  

거진 몇시간동안 하나의 문제를 붙잡고 있었다. 서버를 실행하면 다음과 같은 오류가 뜨는 것이었다:  

![mysql](/images/2024-09-01-20-52-36.png)

일단 서버는 정상적으로 실행된 것을 확인할 수 있다. 다만 플러그인에 대한 문제가 있었는데, 확인해보니 mysql_native_password라는 플러그인이 로딩되지 않아서 그런거라는데, 레거시 버전이 아닌 현재 배포되는 버전들은 모두 caching_sha2_password를 보안상의 이유로 사용하기 때문에 mysql_native_password를 쓰지 않는다!  

해당 문제를 챗 지피티에 물어보니 여러 가지 제안을 했지만 하나도 되지 않았다.  

Stack Overflow를 뒤져보다가 mysql을 mysql2로 바꾸는 것이 방법이 될 수도 있다고 했는데, 나는 이미 mysql2를 사용하고 있는걸로 알았지만 혹시나 mysql도 있을까봐 `npm un mysql`을 해주어 삭제해주니 에러 문구가 바뀌었다...! 해결된 것은 아니었지만 드디어 무언가 바뀌었다는 것에 굉장히 행복했다...  

일단 챗지피티 사용은 최대한 지양해야 할 것 같다. 그보다는, Stack Overflow를 조금 더 적절하게 활용해서 문제에 대한 원인과 해결법을 알아보는 습관을 들이는게 내 성장에 도움될 것 같다.  

바뀐 오류의 메시지는 다음과 같았다:  

![err2](/images/2024-09-01-21-15-06.png)  
![env](/images/2024-09-01-21-16-07.png)

뭔가 .env에 있는 값들을 제대로 받아오지 않는다는 느낌이 들어 한 번 확인해봤더니 undefined로 나와있었다.  

ormconfig.ts가 projectroot/src/config에 있어서 `dotenv.config({ path: '../../.env' });`로 코드를 작성했는데, 루트에서 가져오는 형식이라 `dotenv.config({ path: '.env' });`로 수정해주니 이 문제는 해결됐다.  

세 번째 오류 메시지는 다음과 같다:   

![err3](/images/2024-09-01-21-18-14.png)
*에러 넘어 에러...*  

이번 문제는 엔터티 파일이 로드되지 않아서였다.  

![엔터티 파일 로딩](/images/2024-09-01-21-21-43.png)  

위와 같이 Address를 entities에 넣어주니 해결이 되었다. 나머지 엔터티도 해당 위치에 넣으면 될 것 같다. 우선은 빠르게 진행하기 위해 명시적으로 엔터티 클래스 배열을 만드는 대신 파일 경로 패턴을 사용하여 해결해주었다.  

```typescript
    entities: [
        'src/entities/*.entity.ts'
    ],
```

명시적인 엔터티 클래스 배열로 설정해두면 명확하고 타입 검사를 미리 할 수 있다는 장점이 있지만 유지보수에 어려움을 겪을 수 있고, 파일 경로 패턴을 사용하여 동적으로 추가해주면 수정할 필요가 없어 편리하다는 장점이 있지만, 경로 문제 처리에 주의가 필요하다. 추후에 모든 엔터티가 확정된 시점에는 모든 엔터티 클래스를 명식해 주는 방식으로 바꿔야 한다.  

---