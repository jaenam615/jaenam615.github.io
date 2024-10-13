---
title: "Github Actions 기본"
writer: James
date: 2024-10-12 00:01:00 +0900
categories: [devlog, github actions]
tags: [devlog, github actions, ci, cd, ci/cd]
pin: false
math: true
mermaid: true
image:
  path: https://miro.medium.com/v2/resize:fit:875/0*em4noHtSUceQ-_ZA.png
---

## CI/CD

CI/CD란 Continuous Integration과 Continuous Deployment의 약자이며, 이는 지속적 통합과 지속적 배포를 의미한다.  

지속적 통합은 개발자가 코드를 병합할 때 자동으로 빌드와 테스트 등을 실행하도록 하여 코드의 품질을 보장하는 기술이다. 이러한 테스트나 빌드를 자동화함으로 작은 변경사항을 빠르게 검증할 수 있으며, 덕분에 보다 빈번한 업데이트가 가능하게 된다. 

> CI 핵심 개념
>> 빈번한 코드 통합
>> 자동화된 빌드와 테스트 
>> 즉각적인 피드백  

지속적 배포는 CI이후 코드가 코드가 CI의 파이프라인을 통화하면 자동으로 프로덕션에 배포한다. 다만, CD가 Continuous Delivery를 의미하는 경우도 있는데, 이 경우에는 배포 가능한 상태를 유지하는 것이며 자동으로 배포하지는 않는다.  

CI/CD 파이프라인이란 코드가 푸쉬된 이후 자동화된 빌드, 테스트, 배포 과정을 말한다.  

## 주요 컴포넌트

![gha](/images/2024-10-13-20-49-12.png)

### Workflow

Github Actions는 'event'가 일어나야 작동한다. 여기서 event라 함은 메인 브랜치로의 머지, 특정 브랜치에서의 커밋, 이슈 생성 등이 있다. 이러한 event가 발생하면 무언가를 수행하도록 하는 것이 Github Action이며, 여기서의 수행되는 것을 'workflow'라고 할 수 있다.  

Workflow란 하나 이상의 'job(작업)'으로 이루어진 자동화된 작업 흐름이며, 이는 .github/workflows/ 폴더에 정의된다.  

![folder](/images/2024-10-13-20-30-23.png)
*YAML파일로 정의되는 workflow*

Workflow YAML파일은 들여쓰기로 계층 구조를 표현하며, 이 계층 구조는 workflow의 각 부분이 어떻게 구성되고 연결되는지 표현한다.  

Workflow파일은 다음과 같은 기본 구성 요소로 이루어진다:  

- name: Workflow의 이름
- on: 트리거라고도 하며, 어떤 이벤트에 해당 workflow를 실행할지 정의
- jobs: 실행될 작업의 정의

![workflow](/images/2024-10-13-20-29-55.png)

### Job(작업)

작업은 workflow 내에서 독립적으로 실행되는 작업 단위이다. 하나의 workflow는 여러 개의 작업으로 이루어질 수 있으며 각 작업은 별도의 환경에서 이루어진다.  

작업은 병렬적 또는 순차적으로 실행될 수 있으며, 이와 상관 없이 독립적으로 실행된다. 기본적으로는 병렬로 실행되며, 이 기능은 서로 의존성이 없는 작업들에 대해 빌드 및 테스트 시간을 크게 줄일 수 있다.  

![parallel](/images/2024-10-13-20-39-22.png)

특정 작업이 다른 작업의 결과에 의존하는 경우, 이를 정의하여 순차적으로 작업을 실행할 수 있다.  

아래와 같이 `needs` 키워드를 사용하여 의존하는 작업의 이름을 넣으면 된다.  

![needs](/images/2024-10-13-20-41-38.png)

![series](/images/2024-10-13-20-40-52.png)

작업은 다음의 구성 요소를 갖는다:  

- runs-on : 작업이 실행될 러너 환경을 정의. `ubuntu-latest` 등이 있다. 
- steps: 각 작업 안에서 실행될 단계의 목록
- needs: 명시된 작업이 완료된 후에 순차 실행하도록 설정
- if: 조건을 설정하여 특정 상황에서만 작업이 실행되도록 설정

### Step

Step은 작업 내에서 실행되는 기본 단위이다. 각 step은 특정 작업을 수행하며, 명령어를 사용하거나 Action을 호출함으로 다양한 자동화 작업을 수행한다. 

- name: Step의 이름을 정의
- run: 키워드를 사용하여 직접 명령어를 실행
- uses: 미리 만들어진 자동화 작업을 구성하고 재사용
- with: uses에 사용되는 Action에 인자를 전달
- env: 환경변수 설정 

