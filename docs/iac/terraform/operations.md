---
layout: default
title: 실행과 자동화
nav_order: 4
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

