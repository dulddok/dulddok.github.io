---
layout: default
title: 보안과 베스트 프랙티스
nav_order: 5
parent: Terraform Dulddok Lab
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

