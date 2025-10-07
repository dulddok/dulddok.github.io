---
layout: default
title: 보안과 베스트 프랙티스
nav_order: 5
parent: Terraform
---

## 공개 저장소 원칙
- 커밋 제외: `*.tfvars`, `*.auto.tfvars`, `environments/**/backend.hcl`, `terraform.tfstate*`
- 커밋 포함: `*.example`만 커밋해 포맷과 키를 안내

## Backend/변수 분리
- backend.tf에는 타입만: `backend "s3" {}`
- 스택별 `backend.hcl`로 값 주입, 예제는 `backend.hcl.example`
- tfvars도 `*.example` 제공, 실값은 로컬/CI에서 주입

## 누출 대응
1) 즉시 로테이션(키/토큰/암호)
2) git-filter-repo/BFG로 과거 히스토리 제거 후 force-push
3) 포크/클론 보유자에게 공지

## 자동 스캐닝
- CI에 `gitleaks`/`trufflehog` 추가를 권장

## 상태 관리 보안
- 원격 상태(S3)는 서버사이드 암호화와 버저닝을 활성화합니다.
- DynamoDB 잠금 테이블을 사용하여 동시 변경을 방지합니다.
- 상태 접근은 전용 IAM 역할과 최소 권한 정책으로 제한합니다.

## 권한 분리 원칙
- 배포/검증/운영 역할을 분리하고 환경별 권한을 격리합니다.
- 모듈/환경에서 필요한 리소스에만 한정된 액세스를 부여합니다.

## 비밀/민감정보 처리
- `*.tfvars` 실값은 커밋하지 않습니다. 예시는 `*.example`로 제공하고 실값은 로컬/CI에서 주입합니다.
- 리포지토리 스캔을 주기적으로 수행합니다.

## 사고 대응 체크리스트
1) 자격 증명 즉시 로테이션
2) 과거 히스토리 제거(git-filter-repo/BFG) 및 강제 푸시
3) 누출 영향 범위 파악 및 통지

