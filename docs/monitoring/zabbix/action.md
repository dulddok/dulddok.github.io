---
title: "Action 설정 및 관리"
parent: "Zabbix 모니터링 시스템"
nav_order: 4
description: "Zabbix Action 설정 방법과 알림 관리에 대한 가이드입니다."
---

# Zabbix Action 설정 및 관리

## 개요

Zabbix Action은 트리거가 발생했을 때 자동으로 실행되는 작업을 정의하는 핵심 기능입니다. 주로 알림 발송, 원격 명령 실행, 이벤트 처리 등에 사용됩니다.

## Action 구성 요소

### 1. 조건 (Conditions)
- 트리거 상태 (Problem, OK)
- 호스트 그룹
- 트리거 심각도
- 시간 조건
- 유지보수 모드 상태

### 2. 작업 (Operations)
- 알림 발송 (이메일, SMS, 웹훅 등)
- 원격 명령 실행
- 호스트 그룹 추가/제거
- 템플릿 연결/해제

## 기본 Action 설정 예시

### 이메일 알림 Action

```yaml
Action Name: "Email Notifications"
Conditions:
  - Trigger status = PROBLEM
  - Trigger severity >= Warning
  - Host group = Production Servers

Operations:
  - Send email to admin@company.com
  - Escalate after 10 minutes
  - Send to manager@company.com after 30 minutes
```

### 자동 복구 Action

```yaml
Action Name: "Auto Recovery"
Conditions:
  - Trigger status = PROBLEM
  - Trigger severity = High
  - Host group = Web Servers

Operations:
  - Execute remote command: "systemctl restart nginx"
  - Send notification to oncall@company.com
```

## Action 설정 모범 사례

### 1. 알림 그룹화
- 심각도별로 다른 Action 생성
- 업무 시간/비업무 시간 구분
- 담당자별 알림 분리

### 2. 에스컬레이션 설정
- 초기 알림 → 담당자
- 10분 후 → 팀 리드
- 30분 후 → 매니저
- 1시간 후 → 긴급 연락망

### 3. 중복 알림 방지
- 알림 간격 설정 (최소 5분)
- 유지보수 모드 중 알림 중단
- 해결된 문제에 대한 확인 알림

## 문제 해결

### 일반적인 문제들

1. **알림이 발송되지 않는 경우**
   - Action 조건 확인
   - 알림 수신자 설정 확인
   - 메일 서버 설정 확인

2. **중복 알림 발생**
   - 알림 간격 설정 확인
   - Action 조건 중복 확인

3. **원격 명령 실행 실패**
   - Zabbix Agent 설정 확인
   - 방화벽 설정 확인
   - 권한 설정 확인

## 모니터링 및 관리

### Action 로그 확인
- Administration → Audit → Action log
- 실패한 작업 확인
- 성공률 모니터링

### 정기적인 검토
- 사용하지 않는 Action 정리
- 알림 효과성 평가
- 설정 최적화

---

이 문서는 Zabbix Action 설정의 기본적인 내용을 다루고 있습니다. 더 자세한 설정이나 특정 시나리오에 대한 도움이 필요하시면 추가 문서를 참고하세요. 