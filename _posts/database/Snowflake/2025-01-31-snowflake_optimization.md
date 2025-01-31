---
title: "Snowflake 최적화 전략"
writer: James
date: 2025-01-31 15:00:00 +0900
categories: [db, snowflake]
tags: [db, snowflake, sql]
pin: false
math: true
mermaid: true
image:
  path:
---

## 들어가며

지난 2주간 업무를 배우며 밀접하게 사용해온 기술 중 하나는 Snowflake입니다.

Snowflake에 대한 학습을 하고 싶었기에 공부하며 주제를 찾아보던 와중, 데이터 최적화 전략에 대해 살펴보면 좋을 것 같다는 생각이 들었습니다. 전통적인 RDBMS에서의 최적화 전략과 어느 부분이 맞닿아 있고, 어떤 부분에서 차이가 있는지에 포커스를 두고 학습을 진행했습니다.

## 테이블 설계 및 데이터 모델링

### 데이터 타입

적절한 데이터 타입을 사용해야 합니다.

- 숫자 데이터는 `NUMBER` 대신 `INTEGER` 혹은 `FLOAT`
- 문자열 데이터는 가변 길이 `VARCHAR` 설정
- `BOOLEAN` 타입의 적극적인 활용

위 내용은 기본적으로 메모리 공간을 절약하기 위한 전략으로, 전통적인 RDBMS와 성질을 공유합니다. 추가적인 부분이라고 하면, 가변 `VARCHAR` 만을 지원하여 최대 길이를 지정할 필요가 없어 편리하다는 점 정도가 있습니다.

### VARIANT

여기에 더해 Snowflake는 `VARIANT` 타입으로 JSON과 같은 반정형 데이터를 저장하여 보다 효율적으로 처리할 수 있습니다.

`VARIANT` 타입을 사용하면, JSON의 스키마를 미리 정의할 필요가 없기 때문에 스키마가 자주 변경되는 데이터를 처리할 때 유용합니다.

```sql
CREATE TABLE users (
    id INT PRIMARY KEY,
    json_data VARIANT
);
```

```sql
INSERT INTO users VALUES (1, PARSE_JSON('{"name": "Jaehee", "age": 31, "city": "Seoul"}'));

```

저장된 JSON 데이터의 필드에는 콜론(:) 연산자를 통해 직접 접근이 가능합니다..

위 INSERT문으로 저장된 JSON 형식의 사용자의 이름과 나이에 접근해보겠습니다.

```sql
SELECT json_data:name, json_data:age::INTEGER FROM users;
//'Jaehee', 31
// ::연산자를 사용하여 타입캐스팅 가능
```

전통적인 RDMBS에서는 JSON 내부 데이터를 추출하기 위해 `JSON_EXTRACT()`와 같은 함수를 사용해야 하며, 이는 문법을 복잡해지고 성능이 떨어지는 결과를 초래합니다. Snowflake는 JSON을 컬럼 단위로 저장하기 때문에 검색 속도가 빠르며, 자동으로 JSON을 최적화하여 성능을 향상시킵니다.

### 정규화 vs 비정규화

Snowflake는 OLAP 방식의 데이터 웨어하우스이기 때문에 OLTP 방식의 높은 정규화보다는 플랫 테이블이 성능 면에서 유리합니다.

<aside>
💡

**OLTP이란?**

- Online Transaction Processing의 약자로, 실시간 트랜잭션 처리에 중점을 두는 데이터 처리 유형
- 은행 거래, 주문 처리, 재고 관리 등 전통적인 CRUD연산에 사용되며, 우리가 익숙한 INSERT, UPDATE, DELETE등의 단순 쿼리를 빠르게 처리
- 정규화된 데이터베이스 구조를 사용하여 데이터 중복을 최소화
</aside>

<aside>
💡

**그렇다면 OLAP은?**

- Online Analytical Processing의 약자로, 복잡한 데이터 분석과 리포트 생성에 중점을 두는 데이터 처리 유형
- 대량의 데이터를 처리하며, 집계함수 등을 사용하여 데이터를 분석
- 비정규화된 데이터베이스 구조를 사용하여 쿼리 성능 최적화
</aside>

OLTP 시스템에서는 일반적으로 3NF(제3정규형) 이상의 정규화를 적용하여 데이터 중복을 최소화하고, JOIN 연산을 통해 데이터를 조회하는 방식이 많이 사용됩니다. 이는 데이터 일관성과 무결성을 유지하는 데 유리하지만, Snowflake와 같은 클라우드 기반 데이터 웨어하우스에서는 이러한 방식이 성능 저하를 일으킬 수 있습니다.

Snowflake는 스토리지와 컴퓨팅 리소스가 분리된 아키텍처를 채택하고 있습니다. 데이터는 클라우드 스토리지에 저장되며, 쿼리가 실행될 때 필요한 데이터만 컴퓨팅 노드로 로드됩니다. 정규화된 데이터는 여러 테이블에 분산되어 저장되기 때문에, JOIN을 수행할 때마다 관련된 여러 테이블에서 데이터를 읽어와야 하며, 이는 불필요한 네트워크 대역폭과 컴퓨팅 리소스의 추가적인 소모를 야기합니다.

> **_그러면 중복 저장 비용이 늘어나지 않나요?_**

늘어나는 것은 사실이지만, Snowflake는 저장 공간이 상대적으로 저렴한 편에 속하기 때문에 성능상의 이점을 버릴 이유가 되지 않습니다.

## 쿼리 성능 최적화

### SELECT

앞에 간략하게 설명하고 넘어갔는데, Snowflake는 데이터 스토리지와 컴퓨팅 리소스가 분리되어 있습니다. 쿼리를 실행하기 위해서는 필요한 데이터만 컴퓨팅 노드로 로드, 즉 ‘스캔’하여 실행하기 때문에 성능 최적화를 위해서는 당연하게도 쿼리의 최적화 또한 필요합니다.

특히나, Snowflake는 컬럼 단위 저장을 하기 때문에 컬럼을 선택적으로 읽어올 수 있습니다. 이는 한 행의 데이터를 한 번에 읽어와서 `SELECT *` 를 하더라도 추가적인 비용이 크게 증가하지 않는 전통 RDBMS와 대조적입니다.

따라서, `SELECT *` 를 하기보다는 `SELECT name, age FROM users` 와 같이 필요한 컬럼만 명시적으로 조회하여 스캔 범위를 줄여야 합니다.

### WHERE

SELECT와 비슷하게, WHERE를 사용하여 필터링하는 것 자체가 불필요한 데이터 스캔을 방지하기 때문에 성능을 향상시킵니다.

Snowflake는 데이터를 자동으로 마이크로 파티션 단위로 분할하여 저장 및 관리합니다. WHERE 조건을 통해 필터링을 하면 불필요한 파티션을 건너뛰어 조회 속도가 향상이 됩니다.

```sql
SELECT name, age FROM users WHERE city = 'seoul';
```

위와 같이 `city` 기준으로 필터링하면 Snowflake는 관련된 마이크로 파티션만 조회하며 필요한 부분만 스캐닝합니다.

<aside>
💡

**클러스터링 키**

- Snowflake는 자동으로 데이터를 마이크로 파티션 단위로 저장하지만, 어떤 컬럼을 기준으로 배치될 지 모름
- 따라서, 위 쿼리를 실행했을 때 `age` 기준으로 배치되면 조회 성능의 증가가 없거나 미미함
- 이러한 문제를 해결하기 위해 특정 컬럼을 기준으로 데이터를 정렬하는 것이 클러스터링 키

```sql
ALTER TABLE users CLUSTER BY (city);
```

</aside>

## 데이터 관리

### 리클러스터링

클러스터링 키를 설정을 하였더라도 시간이 지남에 따라 효과가 감소할 수 있습니다. 필요 시 수동으로 리클러스터링을 수행할 수 있습니다.

```sql
ALTER TABLE users RECLUSTER;
```

### TIME TRAVEL 관리

매체 API버전 업그레이드 시 데이터 정합성을 검증하기 위해 TIME TRAVEL기능을 사용합니다. 이외에도 실수에도 손쉽게 대응할 수 있게 해주는 것이 TIME TRAVEL입니다.
다만, 불필요하게 과거의 데이터가 유지될 경우 필요 이상의 스토리지 비용이 발생합니다.

```sql
ALTER TABLE users SET DATA_RETENTION_TIME_IN_DAYS = 30;
```

데이터의 변경이 잦거나, 보존 기간이 필요 이상으로 설정된 경우에는 위와 같이 `DATA_RETENTION_TIME_IN_DAYS` 를 통해 보존 기간을 설정할 수 있습니다.

## Warehouse 및 비용

### Warehouse 크기 조정

Warehouse란 Snowflake의 컴퓨팅 리소스 클러스터로, Snowflake의 모든 DML 연산에는 Warehouse가 필요합니다. 그렇기 때문에 Warehouse가클수록 쿼리를 실행하는 데 사용할 수 있는 컴퓨팅 리소스가 많아지며, 때로는 단순히 이 Warehouse의 크기를 늘리는 것이 쿼리 성능 향상을 위한 전략이 됩니다.

```sql
ALTER WAREHOUSE my_wh SET WAREHOUSE_SIZE = large;
```

이렇게 간단하게 성능을 향상시킬 수 있지만 비용에 유의해야 합니다. 추가 비용 대비 실행 시간 감소가 유의미하게 감소한다면 적절하게 사용할 수 있습니다. 예를 들어 비용이 두배인 큰 Warehouse에서 쿼리를 실행시킬 때 시간이 반절 걸린다면 총비용은 동일하게 유지되어 동일한 총비용으로 더 빠르게 목적을 달성할 수 있습니다.

![image.png](attachment:75a834e5-9213-4c57-9a89-032ccb0fb0ea:image.png)

### 멀티 클러스터 Warehouse

동시에 실행되는 쿼리가 많으면 여러 개의 Warehouse를 운영하여 성능을 향상시킬 수 있습니다. 기본적으로는 쿼리를 실행하기 위해 단일 Warehouse의 컴퓨팅 리소스 클러스터에서 자원을 할당합니다. 그러나, Warehouse에 제출된 모든 쿼리를 실행할 수 있는 리소스가 불충분한 경우, Snowflake에서는멀티 클러스터 Warehouse를 통해 추가 클러스터를 정적 또는 동적으로 할당할 수 있습니다.

```sql
ALTER WAREHOUSE my_wh SET MIN_CLUSTER_COUNT = 2, MAX_CLUSTER_COUNT = 4;
```

## 출처

---

https://docs.snowflake.com/ko/data-types

https://docs.snowflake.com/ko/user-guide/json-basics-tutorial-prerequisites

https://docs.snowflake.com/ko/guides-overview-queries

https://docs.snowflake.com/ko/user-guide/performance-query-warehouse-size

[https://survivinginthedata.tistory.com/entry/Snowflake는-무엇인가](https://survivinginthedata.tistory.com/entry/Snowflake%EB%8A%94-%EB%AC%B4%EC%97%87%EC%9D%B8%EA%B0%80)

https://f-lab.kr/insight/understanding-oltp-and-olap?gad_source=1&gclid=Cj0KCQiA4-y8BhC3ARIsAHmjC_E3uhggFfE9_7Zs6dqliWfF_xum91XNsUauyCR4T5r5l7Z0RWcRyfcaAi93EALw_wcB

https://www.udemy.com/course/snowflake-masterclass/
