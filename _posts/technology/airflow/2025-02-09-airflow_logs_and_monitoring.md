---
title: "Airflow 로그 관리 및 모니터링"
writer: James
date: 2025-02-09 01:00:00 +0900
categories: [technology, airflow]
tags: [technology, airflow, logs, monitoring]
pin: false
math: true
mermaid: true
image:
  path: https://2679069.fs1.hubspotusercontent-na1.net/hubfs/2679069/Copy%20of%20dimension%20site%20%20%2816%29.jpg
---

## 들어가며

최근 몇 주 동안 Airflow를 사용하며 가장 익숙해지고자 노력했던 부분이 로그와 에러 핸들링이었습니다. 특히, 이번 주에 Airflow DAG를 수정하는 작업을 하며  DAG와 Task의 상태를 모니터링하고, 발생하는 문제를 빠르게 파악하기 위해 로그를 적극적으로 활용했습니다. 따라서, 이번 TIL에서는 Airflow의 로그 관리와 외부 시스템과의 연동에 대해 자세히 살펴보겠습니다.

## Airflow 로그

Airflow는 Python의 표준 `logging` 모듈을 기반으로 로그를 관리하기 때문에 구성 측면에서 많은 유연성을 제공하며, 이를 사용하여 DAG 및 Task 실행 중 발생하는 다양한 이벤트를 기록합니다. 로그는 기본적으로 로컬 파일 시스템에 저장되며, 필요에 따라 외부 스토리지와 연동할 수도 있습니다.

로그를 통해 다음과 같은 목적을 달성할 수 있습니다:

- **DAG 및 Task의 실행 상태 추적**
- **오류 발생 시 원인 분석**
- **성능 최적화를 위한 실행 시간 분석**

### **로그 저장 위치 및 파일 구조**

Airflow의 기본 설정에서는 각 Task의 로그가 로컬 디렉토리에 저장되며 다음의 디렉터리에 생성됩니다:

```bash
{AIRFLOW_HOME}/logs/{dag_id}/{task_id}/{execution_date}/{try_number}.log
```

예를 들어, `test_dag`의 `task_1`이 2024년 2월 10일 10:00에 실행되었고 첫번째 시도였다면, 로그 파일의 경로는 다음과 같습니다:

```bash
$AIRFLOW_HOME/logs/example_dag/task_1/2024-02-10T10:00:00/1.log
```

### Airflow 로그 종류

Airflow는 네 가지의 주요 로그를 생성합니다:

- **Task 로그**
    
    Task 로그는 단일 태스크가 실행될 때의 출력을 보여줍니다. 
    
    - 파이썬 코드에서 `logging` 모듈을 사용하여 생성된 로그
    - 태스크가 출력하는 표준출력과 표준오류
    - 해당 태스크와 관련있는 Airflow 시스템 로그
- **Worker 로그**
    
    Airflow Worker의 활동을 보여줍니다. Airflow에서 각 워커는 고유한 로그파일을 생성합니다.
    
    - 워커 프로세스의 시작과 종료
    - 큐에서 실행시킬 태스크 받아오기
    - 태스크 시작 및 태스크 완료
    - 태스크 실행 중 발생한 에러와 exception
- **Scheduler 로그**
    
    Airflow의 스케쥴러는 태스크의 의존성을 모니터링합니다. 실행 준비된 태스크들을 큐에 넣고, 오래된 데이터를 정리합니다. 이렇게 때문에 스케쥴러 로그는 현재 DAG의 상태와 성능을 보여줍니다.
    
    - 태스크 의존성 파악 및 큐에 삽입
    - 워커 실행 결과에 따라 태스크 `SUCCESS` 또는 `FAILED` 로 표시
    - 오래된 DAG 실행 기록 및 태스크 기록 정리
- **Webserver 로그**
    
    Airflow의 웹서버는 Flask로 구현된 애플리케이션으로, Web UI를 사용자에게 제공합니다. 따라서, 웹서버 로그는 Airflow UI로의 요청에 대해 로깅합니다.
    
    - DAG, 태스크, 로그 조회 요청
    - 요청 처리 시간 등의 성능 메트릭

## Web UI에서 로그 시각화 및 활용

Airflow의 Web UI에서는 각 Task의 실행 로그를 쉽게 확인할 수 있습니다.

1. **Airflow Web UI에 접속** 
2. **DAG 목록에서 원하는 DAG 선택**
3. **Task를 클릭하여 실행 기록 확인**
4. **"Logs" 버튼을 클릭하여 로그 확인**

이를 통해 실시간으로 로그를 분석하고, Task 실패 원인을 파악할 수 있습니다.

## 로그 레벨 설정과 커스터마이징

Airflow에서는 로그 레벨을 설정하여 로그의 상세도를 조절할 수 있습니다. `airflow.cfg` 파일에서 `logging_level` 설정을 통해 로그 레벨을 지정할 수 있습니다.

Airflow에서는 로그 레벨을 다음 중 하나로 설정할 수 있습니다:

- `DEBUG`: 상세한 디버깅 정보를 출력
- `INFO`: 일반적인 실행 정보를 출력 (기본값)
    
    ![image.png](attachment:2ff0b22c-6a9e-48eb-a552-4d1c3b9df820:image.png)
    
- `WARNING`: 경고 메시지 출력
- `ERROR`: 에러 발생 시 출력
    
    ![image.png](attachment:f1627c0a-dbc1-48f7-b67f-880046df13f4:image.png)
    
- `CRITICAL`: 심각한 오류 발생 시 출력

Airflow에 입문하는 입장에서, 이 부분이 가장 중요했습니다. 위의 로그 시각화 부분과 조합하여 

`빨간 태스크 클릭` → `logs` →  `ERROR 확인` 

정도로 90% 이상의 문제를 수월하게 해결할 수 있었습니다. 

## 외부 로깅 시스템과의 연동

Airflow의 로그는 외부 로깅 시스템과 연동할 수 있습니다. Airflow의 best practice에 따르면 프로덕션 환경에서는 로그를 원격 스토리지에 저장하는 것이 권장되는데,  Airflow는 AWS S3, Google Cloud Storage, Azure Blob Storage와 같은 클라우드 스토리지에 로그를 로그를 저장할 수 있는 핸들러가 제공됩니다. 

## 로그 관리 및 모니터링

### Web UI 자체 제공 모니터링 도구

Airflow는 기본적으로 몇 가지 모니터링 도구를 제공합니다. 

- **Task Duration Analysis**
    - 각 Task의 실행 시간을 분석하여 DAG내에서의 병목 구간 파악
    - 특정 Task의 실행 시간이 점점 길어지는지 확인하여 성능 최적화를 수행
- **Gantt Chart**
    - DAG의 실행 흐름을 시각적으로 표현
    - 여러 Task의 실행 순서를 확인하고, 병렬 처리 여부를 쉽게 파악
- **Code View**
    - DAG의 파이썬 코드를 Web UI에서 확인하는 기능

### ELK

대규모 운영 환경에서는 ELK스택을 사용하여 로그 분석과 시각화가 가능합니다. 

<aside>
💡

### **왜 ELK와 연동해야 할까?**

- **중앙 집중형 로깅**: 여러 서버와 Airflow 인스턴스에서 생성된 로그를 한 곳에서 관리할 수 있습니다.
- **실시간 로그 모니터링**: 다양한 소스에서 오는 로그를 실시간으로 확인할 수 있습니다.
- **로그의 안전한 보관**: Airflow의 로그를 외부 스토리지에 안전하게 저장하여 데이터 손실을 방지할 수 있습니다.
- **머신러닝 활용**: 이상 탐지 기능을 적용하여 비정상적인 패턴을 감지할 수 있습니다.
</aside>

- **설정 방법**
    1. **FluentD 설정**: Airflow의 로그를 수집할 수 있도록 FluentD를 설정합니다.
    2. **Elasticsearch에 로그 저장**: FluentD를 통해 수집된 로그를 Elasticsearch에 저장합니다.
    3. **Kibana 대시보드 구성**: Elasticsearch에 저장된 로그를 Kibana에서 시각화하여 분석합니다.

### Prometheus & Grafana

Prometheus를 사용하여 Grafana 대시보드를 구성하면 Airflow의 상태를 보다 정밀하게 모니터링할 수 있습니다.

- **설정 방법**
    1. **Prometheus Exporter 설정**: Airflow에서 노출하는 메트릭을 Prometheus가 수집할 수 있도록 설정합니다.
    2. **Grafana 대시보드 구성**: Prometheus 데이터를 시각적으로 표현하기 위해 Grafana에서 대시보드를 생성합니다.
    3. **경고(Alert) 설정**: 특정 오류나 성능 저하가 발생할 경우 이메일 또는 Slack으로 알림을 보낼 수 있습니다.

## 출처

https://airflow.apache.org/docs/apache-airflow/stable/administration-and-deployment/logging-monitoring/index.html

https://airflow.apache.org/docs/apache-airflow/stable/logging-monitoring/logging-tasks.html

https://airflow.apache.org/docs/apache-airflow/stable/logging-monitoring/logging-monitoring-architecture.html

https://www.astronomer.io/docs/airflow/logging

https://stackabuse.com/handling-task-failures-in-airflow-a-practical-guide

https://expertbeacon.com/a-comprehensive-guide-to-logging-in-apache-airflow

https://github.com/astronomer/airflow-guides/blob/main/guides/error-notifications-in-airflow.md