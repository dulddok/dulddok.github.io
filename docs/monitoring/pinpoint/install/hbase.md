---
layout: default
title: HBase 설치 및 설정
parent: 설치 및 기본 설정
nav_order: 1
---

# HBase 설치 및 설정

Pinpoint의 데이터 저장소로 사용되는 HBase를 설치하고 설정하는 방법을 안내합니다.

## HBase 개요

HBase는 Hadoop 기반의 분산 NoSQL 데이터베이스로, Pinpoint의 모든 모니터링 데이터를 저장합니다.

### 주요 특징
- **대용량 데이터 처리**: 수백 TB급 데이터 처리 가능
- **실시간 읽기/쓰기**: 빠른 데이터 접근
- **수평적 확장**: 클러스터 확장으로 성능 향상
- **고가용성**: 장애 복구 및 백업 지원

## 시스템 요구사항

### 최소 요구사항
- **Java**: JDK 8 이상
- **메모리**: 8GB RAM (권장 16GB)
- **디스크**: 100GB 이상 (SSD 권장)
- **네트워크**: 1Gbps 이상

### 권장 사항
- **CPU**: 8코어 이상
- **메모리**: 32GB RAM
- **디스크**: NVMe SSD
- **네트워크**: 10Gbps

## 설치 순서

### 1. Java 설치
```bash
# Ubuntu/Debian
sudo apt update
sudo apt install openjdk-8-jdk

# CentOS/RHEL
sudo yum install java-1.8.0-openjdk-devel
```

### 2. ZooKeeper 설치
```bash
# ZooKeeper 다운로드
wget https://downloads.apache.org/zookeeper/zookeeper-3.7.1/apache-zookeeper-3.7.1-bin.tar.gz
tar -xzf apache-zookeeper-3.7.1-bin.tar.gz
sudo mv apache-zookeeper-3.7.1 /opt/zookeeper

# 설정 파일 생성
cp /opt/zookeeper/conf/zoo_sample.cfg /opt/zookeeper/conf/zoo.cfg
```

### 3. HBase 설치
```bash
# HBase 다운로드
wget https://downloads.apache.org/hbase/2.4.12/hbase-2.4.12-bin.tar.gz
tar -xzf hbase-2.4.12-bin.tar.gz
sudo mv hbase-2.4.12 /opt/hbase
```

## 설정 파일

### hbase-site.xml
```xml
<configuration>
  <property>
    <name>hbase.rootdir</name>
    <value>file:///opt/hbase/data</value>
  </property>
  <property>
    <name>hbase.zookeeper.property.dataDir</name>
    <value>/opt/zookeeper/data</value>
  </property>
  <property>
    <name>hbase.cluster.distributed</name>
    <value>false</value>
  </property>
</configuration>
```

### hbase-env.sh
```bash
export JAVA_HOME=/usr/lib/jvm/java-8-openjdk-amd64
export HBASE_MANAGES_ZK=false
```

## 시작 및 중지

### 서비스 시작
```bash
# ZooKeeper 시작
/opt/zookeeper/bin/zkServer.sh start

# HBase 시작
/opt/hbase/bin/start-hbase.sh
```

### 서비스 중지
```bash
# HBase 중지
/opt/hbase/bin/stop-hbase.sh

# ZooKeeper 중지
/opt/zookeeper/bin/zkServer.sh stop
```

## 테이블 생성

Pinpoint용 테이블을 생성합니다:

```bash
# HBase Shell 접속
/opt/hbase/bin/hbase shell

# Pinpoint 테이블 생성
create 'ApplicationTraceIndex', {NAME => 'I', VERSIONS => 1, TTL => 7776000}
create 'ApplicationIndex', {NAME => 'I', VERSIONS => 1, TTL => 7776000}
create 'HostApplicationMap_Ver2', {NAME => 'I', VERSIONS => 1, TTL => 7776000}
create 'AgentId', {NAME => 'I', VERSIONS => 1, TTL => 7776000}
create 'AgentEvent', {NAME => 'I', VERSIONS => 1, TTL => 7776000}
create 'AgentLifeCycle', {NAME => 'I', VERSIONS => 1, TTL => 7776000}
create 'AgentStatV2', {NAME => 'I', VERSIONS => 1, TTL => 7776000}
create 'ApiMetaData', {NAME => 'I', VERSIONS => 1, TTL => 7776000}
create 'ApplicationMapStatisticsCallee_Ver2', {NAME => 'I', VERSIONS => 1, TTL => 7776000}
create 'ApplicationMapStatisticsCaller_Ver2', {NAME => 'I', VERSIONS => 1, TTL => 7776000}
create 'ApplicationMapStatisticsSelf_Ver2', {NAME => 'I', VERSIONS => 1, TTL => 7776000}
create 'ApplicationStatAggre', {NAME => 'I', VERSIONS => 1, TTL => 7776000}
create 'ApplicationTraceIndex', {NAME => 'I', VERSIONS => 1, TTL => 7776000}
create 'SqlMetaData_Ver2', {NAME => 'I', VERSIONS => 1, TTL => 7776000}
create 'StringMetaData', {NAME => 'I', VERSIONS => 1, TTL => 7776000}
create 'TraceV2', {NAME => 'I', VERSIONS => 1, TTL => 7776000}
create 'TransactionId', {NAME => 'I', VERSIONS => 1, TTL => 7776000}
create 'User', {NAME => 'I', VERSIONS => 1, TTL => 7776000}
```

## 성능 튜닝

### 메모리 설정
```xml
<property>
  <name>hbase.regionserver.global.memstore.size</name>
  <value>0.4</value>
</property>
<property>
  <name>hbase.regionserver.global.memstore.size.lower.limit</name>
  <value>0.35</value>
</property>
```

### 압축 설정
```xml
<property>
  <name>hbase.regionserver.codecs</name>
  <value>snappy</value>
</property>
```

## 모니터링

### Web UI 접속
- **HBase Master**: http://localhost:16010
- **RegionServer**: http://localhost:16030

### 주요 메트릭
- **Region Count**: 리전 개수
- **Store Count**: 스토어 개수
- **Memstore Size**: 메모리 스토어 크기
- **Compaction Queue**: 압축 큐 크기

## 문제 해결

### 일반적인 문제
- **메모리 부족**: JVM 힙 크기 조정
- **디스크 공간 부족**: 데이터 정리 및 압축
- **네트워크 지연**: 네트워크 설정 최적화

---

HBase가 성공적으로 설치되면 Pinpoint Collector와 Web UI를 설치할 수 있습니다. 