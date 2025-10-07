---
layout: default
title: 아키텍처
nav_order: 3
parent: Terraform
---

## 개요
VPC, Subnet, SG, ALB, EC2, S3, IAM을 모듈화하여 환경별로 조합합니다.

## 다이어그램(개념)
```mermaid
flowchart TB
  subgraph VPC
    PUB1[PUB Subnet A]
    PUB2[PUB Subnet C]
    PRI1[PRI Subnet A]
    PRI2[PRI Subnet C]
  end

  ALB[ALB]
  ASG[EC2 Auto Scaling]

  ALB -->|target group| ASG
  PUB1 --> ALB
  PUB2 --> ALB
  PRI1 --> ASG
  PRI2 --> ASG
```

## 모듈 구성 개요
프로젝트는 서비스별로 모듈화되어 있으며 환경(`dev/staging/prod`)에서 조합합니다.

```text
modules/
  vpc/              # VPC, 서브넷, 라우트, IGW/NAT
  security-groups/  # 공통 SG 정책
  alb/              # ALB 및 Target Group/Listener
  ec2/              # ASG/Launch Template
  rds/              # RDS 인스턴스/파라미터
  s3/               # S3 버킷 및 정책
  iam/              # 역할/정책/프로필
```

환경별 디렉터리는 서비스 단위로 모듈을 호출합니다.

```text
environments/{dev,staging,prod}/
  networking/  # VPC, SG, ALB 등 네트워크 계층
  compute/     # EC2/ASG 등 컴퓨트 계층
  database/    # RDS 등 데이터 계층
  storage/     # S3 등 스토리지 계층
```

## 구성 원칙
- 모듈은 단일 책임을 지향하고 입출력 변수를 명확히 정의합니다.
- 교차 의존성은 `terraform_remote_state` 또는 명시적 출력값을 통해 연결합니다.
- 환경별 차이는 `terraform.tfvars`로 주입하고, 공통값은 모듈 변수의 기본값으로 유지합니다.
- 변경의 파급을 최소화하기 위해 상위(네트워킹) → 하위(컴퓨트/데이터) 순으로 배포합니다.

