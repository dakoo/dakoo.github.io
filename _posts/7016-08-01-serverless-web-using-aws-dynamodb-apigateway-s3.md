---
layout: post
title: AWS DynamoDB, API Gateway, S3를 이용한 Serverless Web Server 구축   
description: AWS DynamoDB, API Gateway, S3를 이용한 Serverless Web Server 구축 
modified: 2018-08-01
tags: [aws]
comments: true
image:
  feature: abstract-17.png
---
퀵 메모장 기능을 지원하는 Serverless Web Server를 간단히 구축해 보자. 

## 0. 기능 요구사항

기능 요구사항은 다음과 같다. 

1. 메모 추가 (내용)
2. 메모 삭제 
3. 메모 업데이트 
4. 메모 리스트 보기(최신순)

## 1. Architecture

```
( Webbrowser ) --------- (S3) - Static contents(image, html, js, css 파일들)
    |
    |--------------------(API Gateway) -------- (DynamoDB) - Dynamic contents
```

## 2. Dynamic Contents 처리

### 2.1 DynamoDB

### 2.2 API Gateway

### 2.3 Test

### 3. Static Contents 처리

### 3.1 Web page 

### 3.2 Local data를 이용한 테스트

## 4. 테스트
