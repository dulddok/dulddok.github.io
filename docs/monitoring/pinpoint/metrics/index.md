---
layout: default
title: 시스템 메트릭 설정
parent: 핀포인트 APM 모니터링
nav_order: 2
---

# 시스템 메트릭 설정

Pinpoint에서 시스템 메트릭을 수집하고 모니터링하는 방법을 안내합니다.

## 시스템 메트릭 개요

시스템 메트릭은 애플리케이션의 성능과 상태를 파악하는 핵심 지표입니다.

### 주요 메트릭
- **JVM 메트릭**: 힙 메모리, GC, 스레드 정보
- **시스템 메트릭**: CPU, 메모리, 디스크, 네트워크
- **애플리케이션 메트릭**: 응답 시간, 처리량, 에러율

## JVM 메트릭 설정

### 힙 메모리 모니터링
```java
// JVM 옵션 설정
-XX:+PrintGCDetails
-XX:+PrintGCTimeStamps
-XX:+PrintGCDateStamps
-Xloggc:/path/to/gc.log
```

### GC 모니터링
```properties
# Pinpoint Agent 설정
profiler.jvm.memory.verbose=true
profiler.jvm.memory.verbose.verbose=true
profiler.jvm.memory.verbose.verbose.verbose=true
```

### 주요 JVM 메트릭
- **Heap Used**: 사용 중인 힙 메모리
- **Heap Max**: 최대 힙 메모리
- **GC Count**: 가비지 컬렉션 횟수
- **GC Time**: 가비지 컬렉션 시간
- **Thread Count**: 활성 스레드 수

## 시스템 리소스 모니터링

### CPU 모니터링
```properties
# CPU 사용률 모니터링
profiler.system.cpu.enable=true
profiler.system.cpu.sample.interval=1000
```

### 메모리 모니터링
```properties
# 시스템 메모리 모니터링
profiler.system.memory.enable=true
profiler.system.memory.sample.interval=1000
```

### 디스크 모니터링
```properties
# 디스크 I/O 모니터링
profiler.system.disk.enable=true
profiler.system.disk.sample.interval=5000
```

### 네트워크 모니터링
```properties
# 네트워크 I/O 모니터링
profiler.system.network.enable=true
profiler.system.network.sample.interval=1000
```

## 애플리케이션 메트릭 설정

### 응답 시간 모니터링
```java
// 애플리케이션 코드에서 응답 시간 측정
@Trace
public void processRequest() {
    long startTime = System.currentTimeMillis();
    try {
        // 비즈니스 로직
    } finally {
        long responseTime = System.currentTimeMillis() - startTime;
        // Pinpoint에 응답 시간 전송
    }
}
```

### 처리량 모니터링
```properties
# 처리량 모니터링 설정
profiler.application.throughput.enable=true
profiler.application.throughput.sample.interval=1000
```

### 에러율 모니터링
```java
// 예외 처리 및 에러율 모니터링
@Trace
public void processRequest() {
    try {
        // 비즈니스 로직
    } catch (Exception e) {
        // Pinpoint에 에러 정보 전송
        throw e;
    }
}
```

## 데이터베이스 메트릭 설정

### SQL 쿼리 모니터링
```properties
# SQL 쿼리 모니터링 설정
profiler.jdbc.enable=true
profiler.jdbc.sql.enable=true
profiler.jdbc.sql.max.length=1024
```

### 커넥션 풀 모니터링
```properties
# 커넥션 풀 모니터링 설정
profiler.jdbc.connection.pool.enable=true
profiler.jdbc.connection.pool.sample.interval=5000
```

### 주요 DB 메트릭
- **SQL 실행 시간**: 쿼리별 실행 시간
- **SQL 실행 횟수**: 쿼리별 실행 횟수
- **커넥션 풀 사용률**: 활성/대기 커넥션 수
- **데드락 발생**: 데드락 감지 및 알림

## 외부 API 메트릭 설정

### HTTP 클라이언트 모니터링
```properties
# HTTP 클라이언트 모니터링 설정
profiler.http.client.enable=true
profiler.http.client.sample.interval=1000
```

### 주요 API 메트릭
- **API 응답 시간**: 외부 API 호출 응답 시간
- **API 호출 횟수**: 외부 API 호출 횟수
- **API 에러율**: 외부 API 호출 에러율
- **타임아웃 발생**: API 호출 타임아웃 발생

## 알림 설정

### 임계값 설정
```properties
# 응답 시간 임계값
alert.response.time.warning=1000
alert.response.time.critical=3000

# 에러율 임계값
alert.error.rate.warning=0.05
alert.error.rate.critical=0.1

# 메모리 사용률 임계값
alert.memory.usage.warning=0.8
alert.memory.usage.critical=0.9
```

### 알림 채널 설정
```properties
# 이메일 알림
alert.email.enable=true
alert.email.recipients=admin@company.com

# Slack 알림
alert.slack.enable=true
alert.slack.webhook.url=https://hooks.slack.com/services/xxx

# SMS 알림
alert.sms.enable=true
alert.sms.recipients=+1234567890
```

## 대시보드 구성

### 실시간 대시보드
- **시스템 리소스**: CPU, 메모리, 디스크, 네트워크
- **JVM 상태**: 힙 메모리, GC, 스레드
- **애플리케이션 성능**: 응답 시간, 처리량, 에러율

### 트렌드 대시보드
- **시간별 성능**: 시간대별 성능 변화
- **일별 성능**: 일별 성능 통계
- **주별 성능**: 주별 성능 분석

### 알림 대시보드
- **활성 알림**: 현재 발생한 알림 목록
- **알림 히스토리**: 과거 알림 기록
- **알림 통계**: 알림 발생 패턴 분석

## 성능 최적화

### 샘플링 설정
```properties
# 샘플링 비율 설정
profiler.sampling.rate=1.0
profiler.sampling.new.throughput=0
profiler.sampling.continue.throughput=0
```

### 버퍼 설정
```properties
# 버퍼 크기 설정
profiler.buffer.size=1024
profiler.buffer.timeout=1000
```

### 배치 처리 설정
```properties
# 배치 크기 설정
profiler.batch.size=100
profiler.batch.timeout=1000
```

## 문제 해결

### 일반적인 문제
- **메트릭 수집 실패**: 네트워크 연결 확인
- **성능 영향**: 샘플링 비율 조정
- **메모리 사용량 증가**: 버퍼 크기 조정

### 디버깅
```properties
# 디버그 모드 활성화
profiler.debug.enable=true
profiler.debug.log.level=DEBUG
```

---

시스템 메트릭이 성공적으로 설정되면 애플리케이션의 성능을 실시간으로 모니터링할 수 있습니다. 