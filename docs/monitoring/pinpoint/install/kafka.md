---
layout: default
title: Kafka 설치 및 설정
parent: 설치 및 기본 설정
nav_order: 2
---

# Kafka 설치 및 설정

대용량 데이터 처리 시 Pinpoint와 함께 사용하는 Kafka를 설치하고 설정하는 방법을 안내합니다.

## Kafka 개요

Kafka는 고성능 분산 메시징 시스템으로, Pinpoint에서 대용량 트래픽 처리 시 사용됩니다.

### 주요 특징
- **고성능**: 초당 수백만 메시지 처리
- **확장성**: 수평적 확장으로 용량 증가
- **내구성**: 디스크 기반 메시지 저장
- **실시간 처리**: 실시간 스트림 처리 지원

## 시스템 요구사항

### 최소 요구사항
- **Java**: JDK 8 이상
- **메모리**: 4GB RAM (권장 8GB)
- **디스크**: 50GB 이상 (SSD 권장)
- **네트워크**: 1Gbps 이상

### 권장 사항
- **CPU**: 4코어 이상
- **메모리**: 16GB RAM
- **디스크**: NVMe SSD
- **네트워크**: 10Gbps

## 설치 순서

### 1. Java 설치 확인
```bash
java -version
```

### 2. ZooKeeper 설치 (HBase와 공유 가능)
```bash
# ZooKeeper 다운로드
wget https://downloads.apache.org/zookeeper/zookeeper-3.7.1/apache-zookeeper-3.7.1-bin.tar.gz
tar -xzf apache-zookeeper-3.7.1-bin.tar.gz
sudo mv apache-zookeeper-3.7.1 /opt/zookeeper
```

### 3. Kafka 설치
```bash
# Kafka 다운로드
wget https://downloads.apache.org/kafka/2.13-3.4.0/kafka_2.13-3.4.0.tgz
tar -xzf kafka_2.13-3.4.0.tgz
sudo mv kafka_2.13-3.4.0 /opt/kafka
```

## 설정 파일

### server.properties
```properties
# Broker ID
broker.id=0

# 네트워크 설정
listeners=PLAINTEXT://localhost:9092
advertised.listeners=PLAINTEXT://localhost:9092

# 로그 디렉터리
log.dirs=/opt/kafka/logs

# ZooKeeper 연결
zookeeper.connect=localhost:2181

# 기본 설정
num.partitions=3
default.replication.factor=1
min.insync.replicas=1

# 메모리 설정
log.retention.hours=168
log.segment.bytes=1073741824
log.retention.check.interval.ms=300000
```

### zookeeper.properties
```properties
# 데이터 디렉터리
dataDir=/opt/zookeeper/data

# 클라이언트 포트
clientPort=2181

# 기본 설정
tickTime=2000
initLimit=10
syncLimit=5
```

## 시작 및 중지

### 서비스 시작
```bash
# ZooKeeper 시작
/opt/kafka/bin/zookeeper-server-start.sh -daemon /opt/kafka/config/zookeeper.properties

# Kafka 시작
/opt/kafka/bin/kafka-server-start.sh -daemon /opt/kafka/config/server.properties
```

### 서비스 중지
```bash
# Kafka 중지
/opt/kafka/bin/kafka-server-stop.sh

# ZooKeeper 중지
/opt/kafka/bin/zookeeper-server-stop.sh
```

## 토픽 생성

Pinpoint용 토픽을 생성합니다:

```bash
# 토픽 생성
/opt/kafka/bin/kafka-topics.sh --create \
  --bootstrap-server localhost:9092 \
  --replication-factor 1 \
  --partitions 3 \
  --topic pinpoint-collector

# 토픽 목록 확인
/opt/kafka/bin/kafka-topics.sh --list \
  --bootstrap-server localhost:9092
```

## 성능 튜닝

### 메모리 설정
```properties
# 힙 메모리 설정
KAFKA_HEAP_OPTS="-Xms4g -Xmx8g"

# 페이지 캐시 설정
log.flush.interval.messages=10000
log.flush.interval.ms=1000
```

### 네트워크 설정
```properties
# 소켓 버퍼 크기
socket.send.buffer.bytes=102400
socket.receive.buffer.bytes=102400
socket.request.max.bytes=104857600
```

## 모니터링

### 토픽 상태 확인
```bash
# 토픽 상세 정보
/opt/kafka/bin/kafka-topics.sh --describe \
  --bootstrap-server localhost:9092 \
  --topic pinpoint-collector

# 컨슈머 그룹 확인
/opt/kafka/bin/kafka-consumer-groups.sh --list \
  --bootstrap-server localhost:9092
```

### 주요 메트릭
- **Messages/sec**: 초당 메시지 처리량
- **Bytes/sec**: 초당 바이트 처리량
- **Consumer lag**: 컨슈머 지연 시간
- **Partition count**: 파티션 개수

## Pinpoint 연동

### Collector 설정
```properties
# Kafka 사용 설정
kafka.bootstrap.servers=localhost:9092
kafka.topic=pinpoint-collector
kafka.compression.type=snappy
```

### 성능 최적화
- **배치 크기**: 적절한 배치 크기 설정
- **압축**: Snappy 압축 사용
- **파티션**: 파티션 수 조정

## 문제 해결

### 일반적인 문제
- **메모리 부족**: JVM 힙 크기 조정
- **디스크 공간 부족**: 로그 보관 정책 설정
- **네트워크 지연**: 네트워크 설정 최적화

### 로그 확인
```bash
# Kafka 로그
tail -f /opt/kafka/logs/server.log

# ZooKeeper 로그
tail -f /opt/zookeeper/logs/zookeeper.log
```

---

Kafka가 성공적으로 설치되면 Pinpoint Collector에서 대용량 데이터를 효율적으로 처리할 수 있습니다. 