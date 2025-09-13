---
layout: default
title: Item 설명 및 설정
parent: Zabbix 모니터링 시스템
nav_order: 3
---

# Item 설명 및 설정

## Item이란?
Item은 Zabbix에서 모니터링하는 데이터의 기본 단위입니다.

## Zabbix에서 Item의 위치
✅ 기본 개념: Zabbix에서 Item의 위치
Zabbix 구조:


Item:
모니터링하려는 데이터 수집 단위 (예: CPU 사용률, 메모리 사용량, 포트 상태 등)

Item은 항상 특정 호스트(또는 템플릿)에 속해 있어야 합니다.
즉, Item 단독으로는 존재하지 않으며, 반드시 어떤 호스트에 "속해서" 정의됩니다.


## Item은 호스트에 종속된다

Zabbix에서 Item은 **항상 특정 호스트(또는 템플릿)**에 **종속**됩니다.  
즉, **Item은 단독으로 존재할 수 없고**, 반드시 **어떤 호스트에 속해서 정의**되어야 합니다.

```text
[Host/Template] → [Item] → [Trigger] → [Action]
````

### 예시

#### 1. 직접 호스트에 설정한 경우

```txt
Host: webserver-01
Item: system.cpu.util[,user]
```

#### 2. 템플릿을 통해 설정한 경우

```txt
Template: Template OS Linux
→ 연결된 모든 호스트가 동일한 Item 구성을 가짐
```

---

## Item의 주요 속성

| 속성               | 설명                                        |
| ---------------- | ----------------------------------------- |
| Key              | 수집할 데이터의 종류 (예: `system.cpu.util[,user]`) |
| Type             | 수집 방식 (Agent, SNMP, Zabbix Trapper 등)     |
| Update interval  | 데이터 수집 주기                                 |
| History / Trends | 데이터 보존 설정                                 |
| Applications     | Item들을 논리적으로 묶는 그룹                        |

---

![Zabbix Item 구조](/assets/images/monitoring/zabbix/item/zabbix-item-structure.png)

## Item 수집 방식 예시

* Zabbix Agent를 통한 로컬 시스템 데이터 수집
* SNMP를 통한 네트워크 장비 상태 수집
* HTTP/JSON API를 이용한 외부 시스템 데이터 수집
* 사용자 스크립트를 실행하는 External check

---

## 템플릿과 함께 쓰면 좋은 이유

* 여러 호스트에 동일한 Item 정의 재사용 가능
* 관리 일관성 확보
* 중앙 집중형 설정 유지 가능

---

## 요약

* Item은 데이터를 수집하는 **기본 단위**
* 반드시 **호스트 또는 템플릿에 종속**
* 다양한 수집 방식과 설정 옵션을 지원
* 템플릿을 활용하면 유지보수가 쉬워짐

---

## 같이 보기

* [Zabbix 공식 문서 - Items](https://www.zabbix.com/documentation/current/en/manual/config/items)
* [템플릿 vs 호스트 개별 설정 차이점](#)

```

---

원하는 포맷(Jekyll, Hugo, Astro 등)이나 폴더 구조가 있다면 거기에 맞춰서 더 구체적으로 수정도 가능해요.  
또한, 이어서 `Trigger`, `Template`, `Application`, `Graph` 등도 비슷하게 정리해드릴 수 있어요.
```


## Item 유형
- Zabbix Agent
- SNMP
- IPMI
- JMX
- Database Monitor

![Item 유형별 아이콘](/assets/images/monitoring/zabbix/item/item-types.png)

## Item 설정 예시
### 기본 설정
```yaml
name: CPU Utilization
type: Zabbix agent
key: system.cpu.util
```

![기본 Item 설정 화면](/assets/images/monitoring/zabbix/item/basic-item-config.png)

### 고급 설정
```yaml
name: Memory Usage
type: Zabbix agent
key: vm.memory.size[pused]
delay: 1m
history: 7d
trends: 365d
```

![고급 Item 설정 화면](/assets/images/monitoring/zabbix/item/advanced-item-config.png)

## 모범 사례
- Item 명명 규칙
- 수집 주기 설정
- 데이터 보관 기간

![Item 모범 사례 다이어그램](/assets/images/monitoring/zabbix/item/item-best-practices.png)