---
layout: default
title: Pinpoint 설치 및 설정
parent: 설치 및 기본 설정
nav_order: 4
---

# Pinpoint 설치 및 설정

Pinpoint의 핵심 컴포넌트인 Collector와 Web UI를 설치하고 설정하는 방법을 안내합니다.

## Pinpoint 개요

Pinpoint는 Collector와 Web UI로 구성되며, 각각 데이터 수집과 시각화를 담당합니다.

### 구성 요소
- **Collector**: 에이전트로부터 데이터를 수집하여 HBase에 저장
- **Web UI**: 수집된 데이터를 시각화하고 분석 기능 제공
- **Query Server**: Web UI와 HBase 간의 데이터 조회 중계

## 시스템 요구사항

### 최소 요구사항
- **Java**: JDK 8 이상
- **메모리**: 4GB RAM (권장 8GB)
- **디스크**: 20GB 이상
- **네트워크**: 1Gbps 이상

### 권장 사항
- **CPU**: 4코어 이상
- **메모리**: 16GB RAM
- **디스크**: SSD
- **네트워크**: 10Gbps

## 설치 순서

### 1. Java 설치 확인
```bash
java -version
```

### 2. Pinpoint 다운로드
```bash
# Pinpoint 다운로드
wget https://github.com/pinpoint-apm/pinpoint/releases/download/v2.5.2/pinpoint-collector-2.5.2.war
wget https://github.com/pinpoint-apm/pinpoint/releases/download/v2.5.2/pinpoint-web-2.5.2.war

# 디렉터리 생성
sudo mkdir -p /opt/pinpoint
sudo mkdir -p /opt/pinpoint/collector
sudo mkdir -p /opt/pinpoint/web
```

### 3. Tomcat 설치
```bash
# Tomcat 다운로드
wget https://downloads.apache.org/tomcat/tomcat-9/v9.0.73/bin/apache-tomcat-9.0.73.tar.gz
tar -xzf apache-tomcat-9.0.73.tar.gz

# Collector용 Tomcat
sudo mv apache-tomcat-9.0.73 /opt/pinpoint/collector/tomcat
sudo cp pinpoint-collector-2.5.2.war /opt/pinpoint/collector/tomcat/webapps/ROOT.war

# Web용 Tomcat
sudo cp -r /opt/pinpoint/collector/tomcat /opt/pinpoint/web/tomcat
sudo cp pinpoint-web-2.5.2.war /opt/pinpoint/web/tomcat/webapps/ROOT.war
```

## 설정 파일

### Collector 설정 (pinpoint-collector.properties)
```properties
# HBase 설정
hbase.client.host=localhost
hbase.client.port=2181

# Collector 설정
collector.receiver.grpc.port=9991
collector.receiver.grpc.worker.threadSize=128
collector.receiver.grpc.worker.queueSize=5120

# UDP 설정
collector.receiver.udp.port=9994
collector.receiver.udp.worker.threadSize=128
collector.receiver.udp.worker.queueSize=5120

# TCP 설정
collector.receiver.tcp.port=9995
collector.receiver.tcp.worker.threadSize=128
collector.receiver.tcp.worker.queueSize=5120

# Span 설정
collector.receiver.span.port=9996
collector.receiver.span.worker.threadSize=128
collector.receiver.span.worker.queueSize=5120

# 성능 설정
collector.worker.threadSize=128
collector.worker.queueSize=5120
collector.worker.monitor.enable=true
```

### Web UI 설정 (pinpoint-web.properties)
```properties
# HBase 설정
hbase.client.host=localhost
hbase.client.port=2181

# Web UI 설정
web.server.port=8080
web.server.https.port=9991

# 성능 설정
web.server.worker.threadSize=128
web.server.worker.queueSize=5120

# 보안 설정
web.server.security.enable=false
web.server.security.username=admin
web.server.security.password=admin
```

### JVM 설정

#### Collector JVM 옵션
```bash
JAVA_OPTS="-Xms4g -Xmx8g -XX:+UseG1GC -XX:+UseStringDeduplication"
```

#### Web UI JVM 옵션
```bash
JAVA_OPTS="-Xms2g -Xmx4g -XX:+UseG1GC -XX:+UseStringDeduplication"
```

## 시작 및 중지

### 서비스 시작
```bash
# Collector 시작
/opt/pinpoint/collector/tomcat/bin/startup.sh

# Web UI 시작
/opt/pinpoint/web/tomcat/bin/startup.sh
```

### 서비스 중지
```bash
# Web UI 중지
/opt/pinpoint/web/tomcat/bin/shutdown.sh

# Collector 중지
/opt/pinpoint/collector/tomcat/bin/shutdown.sh
```

## 포트 설정

### 필수 포트
- **9994**: Collector UDP (Span 데이터)
- **9995**: Collector TCP (Stat 데이터)
- **9996**: Collector TCP (Span 데이터)
- **8080**: Web UI HTTP
- **9991**: Web UI HTTPS

### 방화벽 설정
```bash
# Collector 포트 허용
sudo ufw allow 9994/udp
sudo ufw allow 9995/tcp
sudo ufw allow 9996/tcp

# Web UI 포트 허용
sudo ufw allow 8080/tcp
sudo ufw allow 9991/tcp
```

## 성능 튜닝

### Collector 성능 최적화
```properties
# 스레드 풀 크기 조정
collector.receiver.grpc.worker.threadSize=256
collector.receiver.udp.worker.threadSize=256
collector.receiver.tcp.worker.threadSize=256

# 큐 크기 조정
collector.receiver.grpc.worker.queueSize=10240
collector.receiver.udp.worker.queueSize=10240
collector.receiver.tcp.worker.queueSize=10240

# 배치 처리 설정
collector.worker.batchSize=100
collector.worker.batchTimeout=1000
```

### Web UI 성능 최적화
```properties
# 캐시 설정
web.cache.enable=true
web.cache.size=1000
web.cache.ttl=300

# 쿼리 타임아웃 설정
web.query.timeout=30000
web.query.maxRows=10000
```

## 모니터링

### Web UI 접속
- **HTTP**: http://localhost:8080
- **HTTPS**: https://localhost:9991

### 주요 메트릭
- **Collector QPS**: 초당 처리량
- **Collector Latency**: 처리 지연 시간
- **Web UI Response Time**: 응답 시간
- **Memory Usage**: 메모리 사용량

### 로그 확인
```bash
# Collector 로그
tail -f /opt/pinpoint/collector/tomcat/logs/catalina.out

# Web UI 로그
tail -f /opt/pinpoint/web/tomcat/logs/catalina.out
```

## 문제 해결

### 일반적인 문제
- **포트 충돌**: 사용 중인 포트 확인
- **메모리 부족**: JVM 힙 크기 조정
- **네트워크 연결 실패**: 방화벽 설정 확인

### 디버깅
```bash
# 프로세스 확인
ps aux | grep pinpoint

# 포트 사용 확인
netstat -tlnp | grep 9994
netstat -tlnp | grep 8080

# 로그 레벨 설정
JAVA_OPTS="$JAVA_OPTS -Dlog.level=DEBUG"
```

## 보안 설정

### SSL/TLS 설정
```properties
# Web UI HTTPS 설정
web.server.https.enable=true
web.server.https.port=9991
web.server.https.keyStore=/path/to/keystore.jks
web.server.https.keyStorePassword=password
```

### 인증 설정
```properties
# 기본 인증 설정
web.server.security.enable=true
web.server.security.username=admin
web.server.security.password=secure_password
```

---

Pinpoint가 성공적으로 설치되면 에이전트를 설정하여 애플리케이션 모니터링을 시작할 수 있습니다. 