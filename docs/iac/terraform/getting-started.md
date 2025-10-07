---
layout: default
title: 시작하기
nav_order: 2
---

## 사전 준비
- Terraform >= 1.0
- AWS 자격 증명 설정(`aws configure`)

## 디렉터리 구조
```text
environments/{dev,staging,prod}/
  networking/ compute/ ...
modules/{vpc,security-groups,alb,ec2,rds,s3,iam}/
scripts/
```

## 백엔드/변수 준비
```bash
# backend
cp environments/dev/networking/backend.hcl.example environments/dev/networking/backend.hcl

# tfvars (자동로드)
cp environments/dev/networking/terraform.tfvars.example environments/dev/networking/terraform.tfvars
```

## 초기화/실행
```bash
terraform -chdir=environments/dev/networking init -backend-config=backend.hcl
terraform -chdir=environments/dev/networking plan
terraform -chdir=environments/dev/networking apply -auto-approve
```

## Compute 실행 예
```bash
cp environments/dev/compute/backend.hcl.example environments/dev/compute/backend.hcl
cp environments/dev/compute/terraform.tfvars.example environments/dev/compute/terraform.tfvars

terraform -chdir=environments/dev/compute init -backend-config=backend.hcl
terraform -chdir=environments/dev/compute plan
terraform -chdir=environments/dev/compute apply -auto-approve
```

