---
title: "corou : Business Logic"
writer: James
date: 2024-09-03 01:00:00 +0900
categories: [devlog, corou]
tags: [devlog, corou, side project]
pin: false
math: true
mermaid: true
image:
  path: https://media.geeksforgeeks.org/wp-content/uploads/20220224160807/Model1.png
---

*포스트 이미지는 GeeksforGeeks에서 퍼왔습니다*

## 엔터티 수정  

하나 알게 된게, TypeORM의 `@JoinColumn`을 사용하는 것은 외래키로 사용되는 속성을 명시하기 위해서 사용된다.  
즉, 한 엔터티에서만 명시면 되고, 그 엔터티는 외래키를 소유한 엔터티라는 점이다.  

이를 위해 엔터티 파일들을 수정했다.  

![edited](/images/2024-09-03-22-38-24.png)
*User 엔터티는 외래키가 없기 때문에 모두 @JoinColumn을 제거했다*  

## Business Logic

본격적으로 서비스들을 만들어주기 시작했다. MVC 패턴에 따라 비즈니스 로직, 즉 데이터베이스에 접근할 수 있는 곳은 오직 Service이며, Controller는 HTTP통신, Router는 들어오는 요청을 라우팅 하는 방식으로 구성했다.  

![user](/images/2024-09-03-22-39-32.png)

`privvate userRepository = AppDataSource.getRepository(User)` 

위의 코드는 userRepository라는 변수를 초기화하고, 이를 통해 User 엔터티에 대한 데이터베이스 작업을 수행할 수 있게 해준다.  

private을 사용함으로 해당 변수는 `UserService` 클래스 내에서만 접근 가능하게 캡슐화하면 내클래스의 내부 구현을 숨기고, 외부에서 해당 클래스의 상태를 직접 변경하지 못하게 한다. 또한, 데이터가 오직 해당 클래스로부터만 접근 및 수정 가능하기 때문에 데이터의 무결성을 유지할 수 있다.  

이런 방식으로 나머지 엔터티들을 위한 서비스를 만들어주었다.  

![item](/images/2024-09-03-22-43-35.png)

다만, 현재는 반환값 자체가 전체 객체를 주는데 이 또한 서비스에서 해결하도록 추후에 변경해야한다.  

---


