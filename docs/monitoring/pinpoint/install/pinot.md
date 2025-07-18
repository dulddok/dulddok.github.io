---
layout: default
title: Pinot 설치 및 설정
parent: 설치 및 기본 설정
nav_order: 3
---

# Pinot 설치 및 설정

실시간 분석을 위한 Pinot를 설치하고 Pinpoint와 연동하는 방법을 안내합니다.

## Pinot 개요

Pinot는 LinkedIn에서 개발한 실시간 분산 OLAP 데이터베이스로, Pinpoint에서 실시간 분석 기능을 제공합니다.

### 주요 특징
- **실시간 분석**: 실시간 데이터 쿼리 및 분석
- **고성능**: 밀리초 단위 응답 시간
- **확장성**: 수평적 확장으로 성능 향상
- **SQL 지원**: 표준 SQL 쿼리 지원

## 시스템 요구사항

### 최소 요구사항
- **Java**: JDK 11 이상
- **메모리**: 8GB RAM (권장 16GB)
- **디스크**: 100GB 이상 (SSD 권장)
- **네트워크**: 1Gbps 이상

### 권장 사항
- **CPU**: 8코어 이상
- **메모리**: 32GB RAM
- **디스크**: NVMe SSD
- **네트워크**: 10Gbps

## 설치 순서

### 1. Java 11 설치
```bash
# Ubuntu/Debian
sudo apt update
sudo apt install openjdk-11-jdk

# CentOS/RHEL
sudo yum install java-11-openjdk-devel
```

### 2. ZooKeeper 설치 (HBase/Kafka와 공유 가능)
```bash
# ZooKeeper 다운로드
wget https://downloads.apache.org/zookeeper/zookeeper-3.7.1/apache-zookeeper-3.7.1-bin.tar.gz
tar -xzf apache-zookeeper-3.7.1-bin.tar.gz
sudo mv apache-zookeeper-3.7.1 /opt/zookeeper
```

### 3. Pinot 설치
```bash
# Pinot 다운로드
wget https://downloads.apache.org/pinot/apache-pinot-1.0.0/apache-pinot-1.0.0-bin.tar.gz
tar -xzf apache-pinot-1.0.0-bin.tar.gz
sudo mv apache-pinot-1.0.0 /opt/pinot
```

## 설정 파일

### pinot-controller.conf
```yaml
controller:
  dataDir: /opt/pinot/data
  zkStr: localhost:2181
  clusterName: PinpointCluster
  port: 9000
  adminPort: 9001
```

### pinot-broker.conf
```yaml
broker:
  dataDir: /opt/pinot/data
  zkStr: localhost:2181
  clusterName: PinpointCluster
  port: 8099
```

### pinot-server.conf
```yaml
server:
  dataDir: /opt/pinot/data
  zkStr: localhost:2181
  clusterName: PinpointCluster
  port: 8098
  adminPort: 8097
```

## 시작 및 중지

### 서비스 시작
```bash
# ZooKeeper 시작
/opt/zookeeper/bin/zkServer.sh start

# Pinot Controller 시작
/opt/pinot/bin/pinot-admin.sh StartController -configFileName /opt/pinot/config/pinot-controller.conf

# Pinot Broker 시작
/opt/pinot/bin/pinot-admin.sh StartBroker -configFileName /opt/pinot/config/pinot-broker.conf

# Pinot Server 시작
/opt/pinot/bin/pinot-admin.sh StartServer -configFileName /opt/pinot/config/pinot-server.conf
```

### 서비스 중지
```bash
# Pinot 서비스 중지
/opt/pinot/bin/pinot-admin.sh StopServer
/opt/pinot/bin/pinot-admin.sh StopBroker
/opt/pinot/bin/pinot-admin.sh StopController

# ZooKeeper 중지
/opt/zookeeper/bin/zkServer.sh stop
```

## 테이블 생성

Pinpoint용 테이블을 생성합니다:

```bash
# 테이블 스키마 생성
/opt/pinot/bin/pinot-admin.sh AddTable \
  -tableConfigFile /opt/pinot/config/pinpoint-table-config.json \
  -schemaFile /opt/pinot/config/pinpoint-table-schema.json \
  -exec
```

### 테이블 스키마 예시
```json
{
  "schemaName": "pinpoint_traces",
  "dimensionFieldSpecs": [
    {"name": "applicationName", "dataType": "STRING"},
    {"name": "agentId", "dataType": "STRING"},
    {"name": "traceId", "dataType": "STRING"}
  ],
  "metricFieldSpecs": [
    {"name": "responseTime", "dataType": "LONG"},
    {"name": "errorCount", "dataType": "INT"}
  ],
  "timeFieldSpec": {
    "incomingGranularitySpec": {
      "name": "timestamp",
      "dataType": "LONG",
      "timeType": "MILLISECONDS"
    }
  }
}
```

## 성능 튜닝

### 메모리 설정
```bash
# JVM 힙 크기 설정
PINOT_CONTROLLER_OPTS="-Xms4g -Xmx8g"
PINOT_BROKER_OPTS="-Xms2g -Xmx4g"
PINOT_SERVER_OPTS="-Xms8g -Xmx16g"
```

### 인덱스 설정
```yaml
# 인덱스 설정
indexingConfig:
  loadMode: HEAP
  invertedIndexColumns:
    - applicationName
    - agentId
  rangeIndexColumns:
    - responseTime
```

## 모니터링

### Web UI 접속
- **Pinot Controller**: http://localhost:9000
- **Pinot Query Console**: http://localhost:9000/query

### 주요 메트릭
- **Query QPS**: 초당 쿼리 처리량
- **Query Latency**: 쿼리 응답 시간
- **Segment Count**: 세그먼트 개수
- **Memory Usage**: 메모리 사용량

## Pinpoint 연동

### Collector 설정
```properties
# Pinot 사용 설정
pinot.controller.host=localhost
pinot.controller.port=9000
pinot.broker.host=localhost
pinot.broker.port=8099
```

### 데이터 전송
```java
// Pinot 클라이언트 설정
PinotClient pinotClient = new PinotClient("localhost:8099");

// 데이터 전송
pinotClient.sendRecord(tableName, record);
```

## 문제 해결

### 일반적인 문제
- **메모리 부족**: JVM 힙 크기 조정
- **디스크 공간 부족**: 세그먼트 정리
- **쿼리 성능 저하**: 인덱스 최적화

### 로그 확인
```bash
# Pinot 로그
tail -f /opt/pinot/logs/pinot-controller.log
tail -f /opt/pinot/logs/pinot-broker.log
tail -f /opt/pinot/logs/pinot-server.log
```

---

Pinot가 성공적으로 설치되면 Pinpoint에서 실시간 분석 기능을 활용할 수 있습니다. 