---
title: "Amazon ECS (와 Orchestration에 대하여)"
writer: James
date: 2025-01-09 01:00:00 +0900
categories: [technology, aws, amazon, ecs]
tags: [technology, aws, amazon, ecs, orchestration]
pin: false
math: true
mermaid: true
image:
  path: https://2679069.fs1.hubspotusercontent-na1.net/hubfs/2679069/Copy%20of%20dimension%20site%20%20%2816%29.jpg
---

## 들어가며

AB180에 입사 이후, 처음 주어진 태스크는 API 버전 업그레이드 입니다. 

작업하는 레포지토리에서 코드가 실제로 어떻게 배포되어있고, 작동하는지 감이 잡히지 않았습니다. 아키텍처를 보면 ECS에 배포되어 있는 것으로 나와있어 이에 대해 정리하면 도움이 될 것 같아 학습 주제로 선정했습니다. 

## Amazon ECS

Amazon ECS는 오케스트레이션 서비스로, AWS환경에서 컨테이너 애플리케이션을 쉽게 배포하고 관리할 수 있도록 도와줍니다. 

![image.png](https://prod-files-secure.s3.us-west-2.amazonaws.com/56af06bd-af23-41ed-9eae-5faeee5a75ac/8b03d630-e71d-45db-ab06-a29d56fb6cda/image.png)

- **Orchestration이란?**
    
    ![출처: 동아출판 프라임 영한사전 ](https://prod-files-secure.s3.us-west-2.amazonaws.com/56af06bd-af23-41ed-9eae-5faeee5a75ac/4404b3d1-75d8-4070-8ce0-0dddfdcd1e9f/image.png)
    
    출처: 동아출판 프라임 영한사전 
    
    ‘오케스트레이션’이란, 여러 [**작업, 시스템, 서비스]**를 자동화하고 조정하여 특정 목표를 달성하는 과정입니다.
    
    오케스트라에서 여러 악기가 조화를 이루어 음악을 만들어내는 것처럼, 다양한 구성 요소를 적절한 순서 + 방향으로 실행함으로 효율적으로 작동하는 것을 의미합니다. 
    
    단순 자동화와는 달리 작업들의 상호작용에 집중하며, 적절한 순서 및 적합한 조건 아래에 동작하도록 보장합니다.
    
- **그렇다면 Orchestrator는?**
    - 컨테이너의 생명 주기를 관리하는 일을 합니다.
    - 컨테이너를 생성할 자원 또는 서버를 찾는 일을 합니다.
    - 다수의 서버에 애플리케이션을 배포하고, 로드밸런싱도 담당합니다.
    - 오토스케일링을 통해 트래픽 규모에 따른 대응을 합니다.

ECS는 단순히 컨테이너 배포 방식을 관리하는 "두뇌" 역할을 합니다.
따라서 컨테이너를 배포하기 위해서는 여전히 물리 또는 가상 머신이 필요합니다.
ECS 자체는 서버나 컴퓨팅 자원을 가지고 있지 않으며, 컨테이너를 생성하거나 삭제하는 기능만 수행합니다. 

이 컨테이너들이 실행되려면 이를 실행할 수 있는 기반 인프라가 필요하며, 이 인프라가 바로 EC2 또는 Fargate가 되는 것입니다. 

## 주요 용어 & 개념

- Capacity
    - 컨테이너를 실행할 수 있는 컴퓨팅 자원을 의미합니다.
    - Capacity는 ECS 클러스터를 지원하는 인프라에서 제공되며, 대표적으로 EC2와 Fargate가 있습니다.
    - EC2 vs Fargate
        
        
        | **인프라** | EC2 | Fargate |
        | --- | --- | --- |
        | 관리 주체 | 사용자 | AWS |
        | CPU 및 메모리 할당 | EC2 인스턴스 유형과 해당되는 자원에 의존 | 사용자가 Task별 자원 정의, 
        AWS가 동적으로 할당 |
        | 유연성 | 인스턴스 유형 및 가격 모델에 대해 온전한 제어 가능  | Fargate의 사전 정의된 자원 할당 옵션으로 제한 |
        | 사용 용이성 | EC2 인스턴스의 지속적 관리 필요 | 관리할 인프라가 없음으로 간단한 설정 가능 |
        | 비용 모델 | 사용되지 않는 경우에도 인스턴스에 대한 비용 지불 | 작업이 실행되는 시간 및 리소스에 대해서만 비용 지불 |
        - EC2
            
            ![image.png](https://prod-files-secure.s3.us-west-2.amazonaws.com/56af06bd-af23-41ed-9eae-5faeee5a75ac/eed7e5a0-1ad3-46fa-8aee-3b47c56e37c9/image.png)
            
        - Fargate
            
            ![image.png](https://prod-files-secure.s3.us-west-2.amazonaws.com/56af06bd-af23-41ed-9eae-5faeee5a75ac/b0bfe801-d4ac-4193-af6a-a531572a4eb7/image.png)
            
- Controller
    - 컨테이너에서 실행되는 앱을 배포 및 관리합니다.
- Provisioning
    - 컨테이너를 실행하기 위해 자원을 할당하고 구성하는 과정입니다.
    - 클러스터 설정, 태스크 정의, 클러스터에서 실행중인 태스크 인스턴스의 수 관리 오토스케일링 등을 합니다.
- Task
    - Task Definition 파일은 컨테이너가 실행되어야 할 방식을 설명하는 일종의 청사진입니다.
    - 사용자는 도커파일을 작성하여 애플리케이션을 도커화합니다. 도커 이미지를 가지고 원격 레포지토리에 올리면, ECS에서 Task Definition 파일을 작성해야 합니다. 이 Task Definition 파일은 docker-compose 파일과 유사합니다.
    - 태스크는 이러한 Task Definition 파일을 인스턴스화 한 것입니다.
    - 도커 이미지가 인스턴스화 되어 컨테이너가 생성되는 것을 생각하면 쉽습니다.
    
    ![image.png](https://prod-files-secure.s3.us-west-2.amazonaws.com/56af06bd-af23-41ed-9eae-5faeee5a75ac/0b1c18a7-40e0-4337-b50b-14b6ee684484/image.png)
    
- Cluster
    - 클러스터란, ‘**여러 인스턴스들이 연결되어 하나의 시스템처럼 동작하는 인스턴스들의 집합**’을 의미합니다.
    - ECS 클러스터라 함은, 단순히 컨테이너가 실행될 리소스들의 집합입니다.
    
    ![image.png](https://prod-files-secure.s3.us-west-2.amazonaws.com/56af06bd-af23-41ed-9eae-5faeee5a75ac/8b44af42-d82c-46f6-86e9-bb5d2be368a5/image.png)
    

## 참고자료

https://docs.aws.amazon.com/ko_kr/AmazonECS/latest/developerguide/Welcome.html

https://docs.aws.amazon.com/AmazonECS/latest/developerguide/task_definitions.html

https://www.youtube.com/watch?v=esISkPlnxL0