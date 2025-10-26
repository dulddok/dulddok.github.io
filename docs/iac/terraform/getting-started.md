---
layout: default
title: 시작하기
nav_order: 2
parent: Terraform
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

## 디렉터리 내 공통 요소
`.terraform/` 디렉터리는 **Terraform이 자동으로 생성하고 관리**하는 작업 디렉터리\
`.terraform.lock.hcl`은 **프로바이더와 모듈의 정확한 버전을 잠그는** 파일\
상태 파일은 **Terraform이 관리하는 리소스의 현재 상태**를 저장합니다.


## 백엔드/변수 준비
```bash
# (옵션) 개발/테스트용 자동 백엔드 생성
# region과 project 이름을 인자로 전달
./scripts/init-backend-auto.sh ap-northeast-2 dulddok-tfbackend

# init-backend-auto.sh 실행 후 출력된 리소스 이름으로 backend.tf 값을 갱신
# 예시)
#   bucket           = "terraform-state-${s3BucketName}"
#   dynamodb_table   = "terraform-locks-${dynamoDbnName}"

# backend (환경별 디렉터리에서 예제 파일 복사)
cp environments/dev/networking/backend.hcl.example environments/dev/networking/backend.hcl

# tfvars (자동로드될 변수 파일 예제 복사)
cp environments/dev/networking/terraform.tfvars.example environments/dev/networking/terraform.tfvars
```

자세한 예제와 스크립트는 저장소를 참고하세요: [dulddok/Terraform-Dulddok](https://github.com/dulddok/Terraform-Dulddok)

### 예시: backend.tf / backend.hcl
```terraform
# backend.tf (값은 backend.hcl로 주입)
terraform {
  backend "s3" {}
}
```

```terraform
# backend.hcl (예시)
bucket         = "<your-tfstate-bucket>"
key            = "dev/networking/terraform.tfstate"
region         = "ap-northeast-2"
encrypt        = true
dynamodb_table = "<your-lock-table>"
```

### 예시: provider.tf
```terraform
provider "aws" {
  region = var.aws_region

  default_tags {
    tags = {
      Environment = "dev"
      Project     = var.project_name
      Service     = "networking"
      ManagedBy   = "terraform"
    }
  }
}
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

## 문제 해결 가이드

### 일반적인 문제들

#### 1. .terraform 디렉터리 문제

```bash
# 문제: 프로바이더 버전 불일치
Error: Failed to query available provider packages

# 해결: 작업 디렉터리 재생성
rm -rf .terraform/
terraform init
```

#### 2. 상태 파일 문제

```bash
# 문제: 상태 파일 손상
Error: Failed to load state: state file is corrupted

# 해결: S3 versioning 내 확인
```

#### 3. 잠금 파일 문제

```bash
# 문제: 잠금 파일 버전 충돌
Error: Provider version constraints are changed

# 해결: 잠금 파일 업데이트
terraform init -upgrade
```

### 디버깅 명령어

```bash
# 상태 확인
terraform show
terraform state list

# 계획 상세 확인
terraform plan -detailed-exitcode

# 로그 레벨 설정
export TF_LOG=DEBUG
terraform plan
```

