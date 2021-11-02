---
title: "S3 vs DynamoDB"
tags: backend
---

![](/assets/images/19-09-16-s2-vs-dynamodb-2021-11-02-10-18-39.png)

# S3

- 구조화되지 않은 데이터를 위해 설계된 객체 저장소
- 처리량을 위해 설계되어 있어서 waiting time이 항상 예측가능 하거나 낮은 편은 아니다.
- 데이터웨어 하우스 시나리오에 더 유용하다. 구조화된 데이터를 쿼리할 수 있는 서비스가 있지만 DynamoDB에 비해 속도가 느리다.
- 웹서버 처럼 최종 사용자가 HTTP를 사용해 직접 개체에 액세스 할 수 있다

# DynamoDB

- 문서 데이터베이스. NoSQL
- 낮은 waiting time과 지속적인 사용 패턴을 위해 설계
- 항목의 수가 적은 경우 (항목이 4KB 미만) DynamoDB가 S3 보다 훨씬 빠르다
- 트래픽이 급증하면 요청이 잠시 제한될 수 있다
- DynamoDB는 항목의 내용을 이해하고 항목의 속성을 효율적으로 쿼리하기 위해 인덱스를 설정할 수 있다.
- DynamoDB 내의 데이터에 액세스하려면 IAM 인증을 받은 AWS SDK 가 필요하다.

<!--more-->

![](/assets/images/19-09-16-s2-vs-dynamodb-2021-11-02-10-18-31.png)
