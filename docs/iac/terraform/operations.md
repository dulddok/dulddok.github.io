---
layout: default
title: 실행과 자동화
nav_order: 4
parent: Terraform
---

## Makefile 사용
```bash
# stg, prod 환경 수행 시 dev 부분 변경 
make plan-dev-networking
make deploy-dev-networking
make destroy-dev-networking

make plan-dev-compute
make deploy-dev-compute
make destroy-dev-compute
```
### 사용 결과 예시 

**아래는 주요 Make 명령 실행 목록입니다.**
```bash
  clean           .terraform 디렉터리 정리
  deploy-dev-compute dev 환경 compute 배포
  deploy-dev-networking dev 환경 networking 배포
  deploy-dev      dev 환경 전체 배포
  deploy-prod-compute prod 환경 compute 배포
  deploy-prod-networking prod 환경 networking 배포
  deploy-prod     prod 환경 전체 배포
  deploy-staging-compute staging 환경 compute 배포
  deploy-staging-networking staging 환경 networking 배포
  deploy-staging  staging 환경 전체 배포
  destroy-dev-compute dev 환경 compute 삭제
  destroy-dev-networking dev 환경 networking 삭제
  destroy-prod-compute prod 환경 compute 삭제
  destroy-prod-networking prod 환경 networking 삭제
  destroy-staging-compute staging 환경 compute 삭제
  destroy-staging-networking staging 환경 networking 삭제
  dev-setup       dev 환경 전체 설정
  format          모든 Terraform 파일 포맷팅
  help            도움말 표시
  init-backend    S3 백엔드 초기화
  plan-dev-compute dev 환경 compute 계획
  plan-dev-networking dev 환경 networking 계획
  plan-prod-compute prod 환경 compute 계획
  plan-prod-networking prod 환경 networking 계획
  plan-staging-compute staging 환경 compute 계획
  plan-staging-networking staging 환경 networking 계획
  prod-setup      prod 환경 전체 설정
  staging-setup   staging 환경 전체 설정
  validate-dev    dev 환경 검증
  validate-prod   prod 환경 검증
  validate-staging staging 환경 검증
  validate        모든 환경 및 서비스 검증

## 스크립트 개요

- scripts/init-backend.sh: 백엔드(S3/DDB) 수동 생성 가이드
- scripts/init-backend-auto.sh: 개발용 자동 생성(주의)
- scripts/deploy.sh: 환경/서비스별 plan/apply/destroy 래퍼
- scripts/validate.sh: 환경별 `terraform validate`

## Makefile 워크플로
반복적인 작업은 Make 타깃으로 제공됩니다.

```bash
# 계획 (dev 환경 외 stg, prod 도 동일방식으로 수행 - make *-{stg|prod}-*)
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
# apply, destroy case 는 실행 시 plan 내용 먼저 수행 이후, 사용자 입력 필요 (수행 여부 yes/no)
case $ACTION in
  plan) terraform plan -var-file="terraform.tfvars" ;;
  apply) terraform apply -var-file="terraform.tfvars" -auto-approve ;;
  destroy) terraform destroy -var-file="terraform.tfvars" -auto-approve ;;
esac
```
### make 실행 예시
  ![make apply-dev-networking예시](/assets/images/iac/terraform/make-apply-dev.png)
  ![make apply-dev-networking예시](/assets/images/iac/terraform/make-destory-net.png)
  > 실제 배포/삭제는 plan -> 사용자 입력 (yes/no) -> auto-approve apply/destory 순으로 진행됩니다. 

## 검증 및 포맷팅
```bash
# 환경별 검증
./scripts/validate.sh dev

# 포맷팅
make format
```
