---
layout: default
title: 실행과 자동화
nav_order: 4
parent: Terraform
---

## Makefile 사용
```bash
make plan-dev-networking
make deploy-dev-networking
make destroy-dev-networking

make plan-dev-compute
make deploy-dev-compute
make destroy-dev-compute
```

## 스크립트 개요

- scripts/init-backend.sh: 백엔드(S3/DDB) 수동 생성 가이드
- scripts/init-backend-auto.sh: 개발용 자동 생성(주의)
- scripts/deploy.sh: 환경/서비스별 plan/apply/destroy 래퍼
- scripts/validate.sh: 환경별 `terraform validate`

## Makefile 워크플로
반복적인 작업은 Make 타깃으로 제공됩니다.

```bash
# 계획
make plan-dev-networking
make plan-dev-compute

# 배포
make deploy-dev-networking
make deploy-dev-compute

# 삭제
make destroy-dev-networking
make destroy-dev-compute

# 전체 배포(순서)
make deploy-dev
```

## 표준 실행 순서
네트워킹 → 컴퓨트 → 데이터베이스/스토리지 순으로 실행합니다.

```bash
./scripts/deploy.sh dev networking plan
./scripts/deploy.sh dev networking apply
./scripts/deploy.sh dev compute plan
./scripts/deploy.sh dev compute apply
```

## 예시: deploy.sh 핵심 로직
```bash
#!/bin/bash
set -e

ENVIRONMENT=$1
SERVICE=$2
ACTION=$3

SERVICE_DIR="environments/$ENVIRONMENT/$SERVICE"
cd "$SERVICE_DIR"

terraform init
terraform validate

case $ACTION in
  plan) terraform plan -var-file="terraform.tfvars" ;;
  apply) terraform apply -var-file="terraform.tfvars" -auto-approve ;;
  destroy) terraform destroy -var-file="terraform.tfvars" -auto-approve ;;
esac
```

## 검증 및 포맷팅
```bash
# 환경별 검증
./scripts/validate.sh dev

# 포맷팅
make format
```

