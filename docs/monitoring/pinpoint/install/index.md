---
layout: default
title: 설치 및 기본 설정
parent: 핀포인트 APM 모니터링
nav_order: 1
has_children: true
---

# Pinpoint 설치 및 기본 설정

Pinpoint APM을 설치하고 기본 환경을 구성하는 방법을 안내합니다.

## 시스템 요구사항

### 최소 요구사항
- **Java**: JDK 8 이상
- **메모리**: 4GB RAM (권장 8GB)
- **디스크**: 50GB 이상 (데이터 보관 기간에 따라 증가)
- **네트워크**: 1Gbps 이상

### 권장 사항
- **CPU**: 4코어 이상
- **메모리**: 16GB RAM
- **디스크**: SSD 권장
- **네트워크**: 10Gbps

## 설치 순서

### 1. HBase 설치
Pinpoint의 데이터 저장소로 HBase가 필요합니다.
- [HBase 설치 및 설정](./hbase.md)

### 2. Kafka 설치 (선택사항)
대용량 데이터 처리 시 Kafka를 사용할 수 있습니다.
- [Kafka 설치 및 설정](./kafka.md)

### 3. Pinot 설치 (선택사항)
실시간 분석을 위한 Pinot 설치
- [Pinot 설치 및 설정](./pinot.md)

### 4. Pinpoint 설치
핵심 Pinpoint 컴포넌트 설치
- [Pinpoint 설치 및 설정](./pinpoint.md)

## 아키텍처 구성

### 단일 서버 구성
```
[Pinpoint Agent] → [Collector] → [HBase]
                           ↓
                    [Web UI] ← [Query Server]
```

### 분산 구성 (권장)
```
[Agent 1] ┐
[Agent 2] ├→ [Collector Cluster] → [HBase Cluster]
[Agent 3] ┘                    ↓
                         [Web UI] ← [Query Server]
```

## 네트워크 포트

### 필수 포트
- **9994**: Collector (UDP)
- **9995**: Collector (TCP)
- **9996**: Collector (TCP)
- **16010**: HBase Master Web UI
- **16030**: HBase RegionServer Web UI
- **8080**: Pinpoint Web UI
- **9991**: Pinpoint Web UI (HTTPS)

### 선택 포트
- **9092**: Kafka Broker
- **2181**: ZooKeeper
- **9000**: Pinot Controller
- **8099**: Pinot Broker
- **8098**: Pinot Server

## 보안 설정

### 1. 방화벽 설정
```bash
# Collector 포트 허용
sudo ufw allow 9994/udp
sudo ufw allow 9995/tcp
sudo ufw allow 9996/tcp

# Web UI 포트 허용
sudo ufw allow 8080/tcp
```

### 2. SSL/TLS 설정
- Web UI HTTPS 설정
- Collector 간 통신 암호화
- Agent-Collector 통신 보안

### 3. 인증 설정
- Web UI 로그인 인증
- API 접근 제어
- 역할 기반 권한 관리

## 성능 튜닝

### 1. JVM 설정
```bash
# Collector JVM 옵션
JAVA_OPTS="-Xms4g -Xmx8g -XX:+UseG1GC"

# Web UI JVM 옵션
JAVA_OPTS="-Xms2g -Xmx4g -XX:+UseG1GC"
```

### 2. HBase 설정
- 메모리 설정 최적화
- WAL 설정 조정
- 압축 설정

### 3. 네트워크 설정
- TCP 버퍼 크기 조정
- 네트워크 타임아웃 설정
- 연결 풀 크기 조정

## 모니터링 설정

### 1. Pinpoint 자체 모니터링
- Collector 성능 모니터링
- HBase 성능 모니터링
- Web UI 응답 시간 모니터링

### 2. 알림 설정
- Collector 다운 알림
- HBase 장애 알림
- 디스크 공간 부족 알림

## 백업 및 복구

### 1. 데이터 백업
- HBase 데이터 백업
- 설정 파일 백업
- 로그 파일 백업

### 2. 재해 복구
- 백업에서 복구 절차
- 장애 시 대체 서버 구성
- 데이터 무결성 검증

## 다음 단계

설치가 완료되면 다음 단계를 진행하세요:

1. **[시스템 메트릭 설정](../metrics/)**: 기본 모니터링 구성
2. **[URI 모니터링 설정](../uri-monitoring/)**: 웹 요청 추적 설정
3. **[에이전트 설정](../agent/)**: 애플리케이션 에이전트 구성

## 문제 해결

### 일반적인 설치 문제
- **Java 버전 호환성**: JDK 8 이상 확인
- **포트 충돌**: 사용 중인 포트 확인
- **메모리 부족**: JVM 힙 크기 조정

### 네트워크 문제
- **방화벽 설정**: 포트 개방 확인
- **DNS 설정**: 호스트명 해석 확인
- **프록시 설정**: 프록시 환경에서의 설정

---

이 가이드를 따라 Pinpoint를 성공적으로 설치하고 기본 환경을 구성할 수 있습니다. 