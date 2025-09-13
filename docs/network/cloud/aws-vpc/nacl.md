---
title: NACL (Network Access Control Lists)
parent: AWS VPC
nav_order: 5
---

# NACL (Network Access Control Lists)

AWS VPC에서 서브넷 레벨의 방화벽 역할을 하는 NACL에 대해 설명합니다.

## NACL이란?

Network Access Control Lists(NACL)는 VPC의 서브넷에 대한 인바운드 및 아웃바운드 트래픽을 제어하는 선택적 보안 계층입니다. Security Groups와 달리 서브넷 레벨에서 동작합니다.

## Security Groups vs NACL

| 구분 | Security Groups | NACL |
|------|----------------|------|
| 레벨 | 인스턴스 레벨 | 서브넷 레벨 |
| 규칙 | 허용만 가능 | 허용/거부 모두 가능 |
| 상태 | 상태 기반 | 무상태 |
| 평가 | 모든 규칙 평가 | 순서대로 평가 |
| 기본값 | 모든 아웃바운드 허용 | 모든 트래픽 거부 |

## NACL 규칙 구조

### 규칙 번호
- 1-32766 범위
- 낮은 번호부터 평가
- 100 단위로 간격 두는 것이 권장

### 규칙 구성 요소
| 필드 | 설명 | 예시 |
|------|------|------|
| Rule # | 규칙 번호 (우선순위) | 100, 200, 300 |
| Type | 프로토콜 유형 | SSH, HTTP, Custom TCP |
| Protocol | 프로토콜 | TCP, UDP, ICMP, All |
| Port Range | 포트 범위 | 22, 80, 1024-65535 |
| Source | 소스 IP | 0.0.0.0/0, 10.0.0.0/16 |
| Allow/Deny | 허용/거부 | ALLOW, DENY |

## 기본 NACL vs 사용자 정의 NACL

### 기본 NACL
- VPC 생성 시 자동 생성
- 모든 트래픽 허용 (인바운드/아웃바운드)
- 규칙 수정 불가

### 사용자 정의 NACL
- 사용자가 생성
- 모든 트래픽 거부 (기본값)
- 규칙 추가/수정 가능

## 실무 구성 예시

### 웹 서버 서브넷 NACL
```
인바운드 규칙:
100 - HTTP (80) - 0.0.0.0/0 - ALLOW
200 - HTTPS (443) - 0.0.0.0/0 - ALLOW
300 - SSH (22) - 10.0.0.0/16 - ALLOW
* - All Traffic - 0.0.0.0/0 - DENY

아웃바운드 규칙:
100 - All Traffic - 0.0.0.0/0 - ALLOW
```

### 데이터베이스 서브넷 NACL
```
인바운드 규칙:
100 - MySQL (3306) - 10.0.1.0/24 - ALLOW
200 - SSH (22) - 10.0.0.0/16 - ALLOW
* - All Traffic - 0.0.0.0/0 - DENY

아웃바운드 규칙:
100 - All Traffic - 0.0.0.0/0 - ALLOW
```

## 모범 사례

### 1. 규칙 번호 관리
- 100 단위로 간격 유지
- 향후 규칙 추가를 고려한 번호 체계

### 2. 명시적 거부 규칙
- 마지막에 모든 트래픽 거부 규칙 추가
- 예상치 못한 접근 차단

### 3. 서브넷별 분리
- 역할별로 서브넷 분리
- 각 서브넷에 적절한 NACL 적용

### 4. 정기적 검토
- 사용하지 않는 규칙 제거
- 접근 패턴 분석

## 문제 해결

### 연결 실패 시 확인 순서
1. Security Groups 규칙 확인
2. NACL 인바운드 규칙 확인
3. NACL 아웃바운드 규칙 확인
4. 라우팅 테이블 확인

{: .note }
NACL은 Security Groups와 함께 사용하여 다층 보안을 구현할 수 있지만, 복잡성을 증가시킬 수 있으므로 신중한 설계가 필요합니다. 