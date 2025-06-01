---
layout: default
title: Item 설명 및 설정
parent: Zabbix 기술 문서
nav_order: 2
---

# Item 설명 및 설정

## Item이란?
Item은 Zabbix에서 모니터링하는 데이터의 기본 단위입니다.

## Item 유형
- Zabbix Agent
- SNMP
- IPMI
- JMX
- Database Monitor

## Item 설정 예시
### 기본 설정
```yaml
name: CPU Utilization
type: Zabbix agent
key: system.cpu.util
```

### 고급 설정
```yaml
name: Memory Usage
type: Zabbix agent
key: vm.memory.size[pused]
delay: 1m
history: 7d
trends: 365d
```

## 모범 사례
- Item 명명 규칙
- 수집 주기 설정
- 데이터 보관 기간