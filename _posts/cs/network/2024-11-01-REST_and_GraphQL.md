---
title: "REST vs GraphQL"
writer: James
date: 2024-11-01 09:00:00 +0900
categories: [cs, network]
tags: [cs, network, REST, GraphQL]
pin: false
math: true
mermaid: true
image:
  path: https://blog.postman.com/wp-content/uploads/2023/12/GraphQL-vs-REST-1.jpg
---

## REST vs GraphQL

RESTful API와 GraphQL은 두 형태의 API로, 둘 다 클라이언트와 서버 간 데이터 교환을 위해 사용하는 인터페이스이다.  

RESTful API는 REST라는 설계 원칙을 따르는 API이다. REST의 지침으로는, 통신하고자 하는 기기들이 클라이언트와 서버로 분리되어 있어야 하며, 상태 정보를 저장하지 않아 이용자에 상관 없이 같은 값을 반환해야 한다. 추가적으로, 네트워크 프로토콜에서 제공하는 캐싱 기능을 적용할 수 있어야 한다. 즉, RESTful API란 이러한 상기 지침들을 만족하는 통신 인터페이스인 것이다.  

GraphQL 또한 일정 부분 비슷한 API 아키텍처 원칙을 구현한다. 클라이언트-서버 모델을 사용하는 것은 물론, 상태를 저장하지 않으며, 같은 HTTP 통신 프로토콜을 기반으로 한다.  

REST와 GraphQL모두 리소스를 중심으로 데이터 교환을 설계한다. 여기서 리소스란, 클라이언트가 API를 통해 접근 및 조작할 수 있는 모든 데이터/객체를 뜻한다.  

## 주요 차이점: GraphQL과 REST

REST API는 애플리케이션 통신을 위한 아키텍처 개념인 반면, GraphQL은 사양, API 쿼리 언어 및 도구 세트이다. GraphQL은 HTTP를 사용하여 단일 엔드포인트에서 작동한다.

또한 REST는 새로운 API를 만드는 데 더 중점을 두고 개발되어 왔지만 GraphQL은 API 성능과 유연성에 중점을 둔다.

### 클라이언트 측 요청
REST 요청 작동에 사용되는 요소는 다음과 같다:  

- 동작을 결정하는 HTTP 동사
- HTTP 동사를 작동시킬 리소스를 식별하는 URL
- 기존 서버 측 리소스 내에서 객체를 생성하거나 수정하려는 경우 구문 분석할 파라미터 및 값

이와 대조적으로 GraphQL 요청에 사용되는 요소는 다음과 같다:

- 읽기 전용 데이터를 가져오기 위한 쿼리
- 데이터 수정을 위한 변형
- 이벤트 기반 또는 스트리밍 데이터 업데이트 수신을 위한 구독


### 클라이언트에 반환되는 데이터
REST 아키텍처에서는 서버가 지정한 전체 리소스 구조로, 서버에서 클라이언트로 데이터가 반환된다.

REST에서 GET /posts는 다음을 반환한다: 

[

  {

    "id": 1,

    "title": "First Post",

    "content": "This is the content of the first post."

  },

  {

    "id": 2,

    "title": "Second Post",

    "content": "This is the content of the second post."

  },

  {

    "id": 3,

    "title": "Third Post",

    "content": "This is the content of the third post."

  }

]

GraphQL을 사용할 경우 클라이언트가 제공한 구조에 지정된 데이터만 반환된다: 

GET /graphql?query{post(id: 1) {id title content}}는 첫 번째 게시물만 반환한다.

{

  "data": {

    "posts": [

      {

        "id": "1",

        "title": "First Post",

        "content": "This is the content of the first post."

      },

]}}

### 서버 측 스키마
GraphQL은 서버 측 스키마를 사용하여 데이터 및 데이터 서비스를 정의하는데, 이는 REST API와는 다르다. 

GraphQL 스키마 정의 언어로 작성된 스키마에는 다음과 같은 세부 정보가 포함된다.

- 각 객체에 속하는 객체 유형 및 필드
- 각 필드에 대한 작업을 정의하는 서버 측 Resolver 함수
