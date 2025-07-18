---
layout: default
title: DB 파티셔닝
parent: Zabbix 기술 문서
nav_order: 3
---

# DB 파티셔닝

## 파티셔닝의 필요성
- 데이터 증가에 따른 성능 저하
- 백업 및 복구 시간 단축
- 데이터 관리 효율성

## 구현 방법
### 테이블 파티셔닝
```sql
CREATE TABLE history (
    itemid bigint NOT NULL,
    clock integer NOT NULL,
    value numeric(16,4) NOT NULL,
    ns integer NOT NULL
) PARTITION BY RANGE (clock);
```

### 파티션 관리
- 자동 파티션 생성
- 오래된 파티션 삭제
- 파티션 유지보수