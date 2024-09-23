---
title: "corou : 배포"
writer: James
date: 2024-09-23 11:00:00 +0900
categories: [devlog, corou]
tags: [devlog, corou, side project]
pin: false
math: true
mermaid: true
image:
  path: https://blog.kakaocdn.net/dn/I0fI1/btqCUbSwdwr/WeSNgsCc4KjVyX35SUiSH1/img.png
---

## 배포  

백엔드 서버만 EC2에 배포하고, 이를 로컬 데베로 연결할 수는 없을까 생각을 해봤다. 전에 RDS에 배포한 데이터베이스는 과금이 되어 삭제했기 때문에 가급적이면 이를 피하고 싶었기 때문이다.  

다만, 프로젝트 막바지이기도 하고, 소액의 과금 정도는 배포 수업료라고 생각하면 괜찮아서 그냥 DB와 백엔드 서버 모두 AWS에 배포하는 방향으로 선택했다.  

## RDS  

RDS 배포는 저번에 설명했지만, 이번에 할 때 다시 헷갈려서 오히려 다시 복습 겸 해본 게 다행이다 싶었다.  

![db](/images/images/2024-09-23-23-51-02.png)  

최선의 방법은 아닐 수도 있지만, 내가 했던 절차를 설명하자면 다음과 같다:  
 
![데이터베이스](/images/images/2024-09-23-23-52-10.png)

RDS 대시보드에 진입하여 DB인스턴스를 선택하면 위와 같은 화면이 나온다. 여기에서 데이터베이스 생성을 선택해준 후, 생성 도우미를 따라간다.  

![2](/images/images/2024-09-23-23-53-45.png)

표준 생성을 선택한 후 사용하고자 하는 DB 엔진을 선택한다. (나의 경우에는 MySQL을 사용했다)  

![3](/images/images/2024-09-23-23-55-01.png)  

템플릿 선택지에서 '프리 티어'를 선택하고, 설정란에서는 인스턴스 식별자, 사용자 이름, 마스터 암호를 입력한다.  

이 부분에서 입력한 값들이 곧 데이터베이스 연결할 때 쓰이기 때문에 당연히 기억해둬야 한다.  

![4](/images/images/2024-09-23-23-56-35.png)  

인스턴스는 t3.micro로 선택한다. t4로 선택하여 만들었을 땐 제대로 연결이 안되는 문제가 있었는데, 보안 그룹 문제였을 수도 있지만 일단 t3로 하고 나서 나는 제대로 작동했다.  

스토리지는 SSD(gp2)로 선택했다.  

![5](/images/images/2024-09-23-23-59-08.png)

위와 같이 선택했다. VPC 보안 그룹은 보안 그룹 중 인바운드를 모두 허용한 것을 선택했다 (0.0.0.0/0). 해당 부분에서 조금 내리면 '추가 구성' 이라는 드롭다운이 있는데 여기에 포트를 설정할 수 있다. 기본적으로 3306으로 되어있으며, 웬만하면 변경할 필요 없다.  

이렇게 RDS 인스턴스를 생성하고 나면 실제로 데이터베이스를 만들어줘야 한다. SQL Workbench등의 GUI를 쓰는 것이 편할 수도 있겠지만, 나는 단순하게 터미널로 mysql이 있는 폴더를 찾아 실행해주었다.  

![6](/images/images/2024-09-24-00-11-26.png)  

생성한 DB 인스턴스를 선택하고 엔드포인트를 복사해준다.  

![mysql](/images/images/2024-09-24-00-13-14.png)

mysql이 있는 디렉토리의 \bin 폴더에서 `mysql -h <AWS RDS endpoint> -P 3306 -u <사용자이름> -p`를 치고 엔터를 눌러주고 비밀번호를 입력하면 연결이 된다.  

이후는 [AWS RDS 데이터베이스 초기 설정](https://jaenam615.github.io/posts/corou-awsrds-testing/)을 따라하면 된다.  

## EC2  

![1](/images/images/2024-09-24-00-03-34.png)  

EC2는 Ubuntu, 그리고 AMI중 프리 티어를 지원하는 서버로 선택하면 된다.  

![2](/images/images/2024-09-24-00-04-16.png)

인스턴스 유형도 마찬가지로 프리 티어로 사용 가능한 t2.micro를 선택해주고, 키페어는 기존에 만들어둔 키페어가 있으면 이를 사용하면 된다. 기존에 만들어둔 키페어가 없다면 새로 생성하자.  

![3](/images/images/2024-09-24-00-06-19.png)  

위에 데이터베이스에도 같이 사용했던 launch-wizard-2를 사용했다. 해당 보안 그룹에 모든 인바운드를 허용하도록 설정했기 때문에 개발용으로 적합하다. 별도로 이렇게 설정해둔 보안 그룹이 없다면 미리 만들어두면 편하다.  

이렇게 설정해두고 인스턴스 시작을 누르면 EC2 인스턴스가 생성된다. 생성된 인스턴스에 백엔드 서버를 올릴려면 SSH를 통해 EC2인스턴스로 로그인을 해야한다. git bash에 pemkey, 즉 키페어가 있는 디렉토리에서 `ssh -i ssh -i "<키.pem>" ubuntu@<ec2 퍼블릭 IP>` 를 치면 연결이 된다. git clone을 통해 레포지토리를 클론해주도록 하자.  

뒤이어서 ubuntu@ 경로에 node를 설치해주어야 한다. (다른 언어를 사용중이면 해당 언어를 설치하면 된다.)  

```bash
sudo apt-get update
sudo apt-get install -y build-essential
sudo apt-get install curl
curl -sL https://deb.nodesource.com/setup_18.x | sudo -E bash --
sudo apt-get install -y nodejs
```

packages.json이 있는 폴더로 내비게이트한 후 `npm i`로 의존성을 설치해주면 준비가 다 됐다.  

![4](/images/images/2024-09-24-00-19-23.png)
*실행해주면 된다*  

![5](/images/images/2024-09-24-00-22-31.png)
![6](/images/images/2024-09-24-00-22-40.png)  
*로컬에서 클라이언트를 켜서 보낸 요청들이 잘 들어오는 것을 확인할 수가 있다.*  

---

