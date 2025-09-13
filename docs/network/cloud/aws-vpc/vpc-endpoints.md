---
title: VPC Endpoints
parent: AWS VPC
nav_order: 3
---

# VPC Endpoints

AWS 서비스에 프라이빗하게 접근할 수 있는 VPC Endpoints에 대해 설명합니다.

## VPC Endpoints란?

VPC Endpoints는 VPC 내의 리소스가 인터넷 게이트웨이나 NAT 게이트웨이를 거치지 않고 AWS 서비스에 프라이빗하게 접근할 수 있게 해주는 서비스입니다.

## Endpoint 유형

### 1. Gateway Endpoints
- **대상 서비스**: S3, DynamoDB
- **라우팅**: 라우팅 테이블에 엔트리 추가
- **비용**: 무료

### 2. Interface Endpoints
- **대상 서비스**: 대부분의 AWS 서비스 (EC2, SNS, SQS 등)
- **라우팅**: ENI(Elastic Network Interface) 사용
- **비용**: 시간당 요금 + 데이터 처리 비용

## 아키텍처 예시

```
프라이빗 서브넷
    │
    ├── EC2 인스턴스
    │       │
    │       ├── Gateway Endpoint → S3
    │       └── Interface Endpoint → SNS
    │
    └── 라우팅 테이블
```

## 주요 장점

- **보안 강화**: 인터넷을 거치지 않는 프라이빗 통신
- **성능 향상**: 직접 연결로 인한 지연 시간 감소
- **비용 절약**: NAT 게이트웨이 비용 절약
- **안정성**: 인터넷 연결에 의존하지 않음

## 설정 방법

### Gateway Endpoint 설정
1. VPC 콘솔에서 Endpoints 생성
2. 서비스 선택 (S3 또는 DynamoDB)
3. VPC 및 라우팅 테이블 선택
4. 정책 설정 (선택사항)

### Interface Endpoint 설정
1. VPC 콘솔에서 Endpoints 생성
2. 서비스 선택
3. VPC 및 서브넷 선택
4. 보안 그룹 설정
5. 정책 설정

## 보안 고려사항

- **Endpoint 정책**: 리소스별 접근 제어
- **보안 그룹**: Interface Endpoint에 대한 인바운드/아웃바운드 규칙
- **VPC Flow Logs**: 트래픽 모니터링

## 실무 활용 사례

- **데이터 레이크**: S3에 프라이빗 접근
- **메시징 시스템**: SNS/SQS 프라이빗 통신
- **모니터링**: CloudWatch 로그 프라이빗 전송

{: .note }
VPC Endpoints는 AWS 서비스 접근을 위한 안전하고 효율적인 방법을 제공하지만, 적절한 정책과 보안 설정이 필요합니다. 