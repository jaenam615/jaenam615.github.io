---
title: "Github Actions 환경변수와 시크릿"
writer: James
date: 2024-10-20 00:01:00 +0900
categories: [devlog, github actions]
tags: [devlog, github actions, ci, cd, ci/cd]
pin: false
math: true
mermaid: true
image:
  path: https://miro.medium.com/v2/resize:fit:875/0*em4noHtSUceQ-_ZA.png
---

## 환경변수  

때로는 로컬 개발 환경과 프로덕션 환경에서 다른 데이터베이스에 접근을 해야 하는 경우가 생기며, 키, 포팅이나 URL등도 환경에 따라 변동될 수 있다.  

Github을 사용할 때는 특히 이러한 환경변수가 포함된 .env 파일을 보안상의 이유로 직접 업로드하지 않는 것이 일반적이다. 그런데 Github Actions는 워크플로를 Github에서 제공하는 가상의 머신 위에서 테스트하고 빌드하는데 환경변수가 빠져있으면 제대로 된 테스트와 빌드가 불가능하다.  

이를 위해 민감하지 않은 정보는 Github Actions YAML파일에 직접 선언하여 사용한다. 

```yaml
  deploy:
    needs: test
    runs-on: ubuntu-latest
    steps:
      - name: Output information
        env:
          PORT: 3000
        run: |        
          echo "MONGODB_DB_NAME: $MONGODB_DB_NAME"
          echo "MONGODB_USERNAME: $MONGODB_USERNAME"
          echo "${{ env.PORT }}"
```

위 YAML파일에 명시된 것 처럼 `env:`에 명시할 수 있다.  

또는, Github에서 Setting에서 특정 환경을 설정할 수 있다.  

![env](/images/images/2024-10-20-23-17-24.png.png)

이렇게 환경을 만들어 두면 YAML파일에서 해당 환경을 불러올 수 있다.  

```yaml
jobs:
  test:
    environment: testing
    runs-on: ubuntu-latest
    env:
      MONGODB_CLUSTER_ADDRESS: cluster0.ntrwp.mongodb.net
      MONGODB_USERNAME: ${{ secrets.MONGODB_USERNAME }}
      MONGODB_PASSWORD: ${{ secrets.MONGODB_PASSWORD }}
      PORT: 8080
```

Secret도 비슷하게 할 수 있다. Secret은 민감한 정보를 안전하게 저장하고 사용할 수 있는 방식으로, API 키, 인증 토큰, 패스워드 등을 저장할 때 사용한다. 시크릿은 암호화되어 저장되고 GitHub의 워크플로우에서 사용되더라도 로그에 출력되지 않는다.  

![secret](/images/2024-10-20-23-20-13.png)  

이렇게 저장된 값은 생성자조차 값을 다시 볼 수 없으며, ${{secrets.변수이름}}의 형태로 접근 가능하다.

```yaml
name: CI Workflow

on: [push]

jobs:
  build:
    runs-on: ubuntu-latest
    steps:
      - name: Use secret in environment variable
        run: echo $MY_SECRET_VAR
        env:
          MY_SECRET_VAR: ${{ secrets.MY_SECRET }}
```


