---
title: "파일 디스크립터"
writer: James
date: 2024-03-14 20:33:00 +0900
categories: [cs, os]
tags: [cs, os]
pin: false
math: true
mermaid: true
image:
  path:
---

## 파일 디스크립터

파일 디스크립터란 프로세스가 열려있는 파일을 식별하기 위해 사용하는 비부호 정수값이다.

OPEN, PIPE, CREATE 등의 시스템 콜은 고유한 파일 디스크립터를 생성하며, 이렇게 생성된 파일 디스크립터는 파일 디스크립터 테이블에 관리된다.  
파일 디스크립터 테이블은 프로세스별로 있으며, 파일 디스크립터는 이 테이블에서 인덱스 역할을 한다.
