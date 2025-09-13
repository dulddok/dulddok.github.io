---
title: DNS 이해 및 문제 해결
parent: network 에 대한 정리
nav_order: 5
---

# DNS 이해 및 문제 해결

도메인 이름을 IP 주소로 변환하는 DNS의 원리와, 문제 발생 시 해결 방법을 다룹니다.

## DNS의 동작 원리
- 사용자가 도메인 입력 → DNS 질의 → IP 주소 반환
- 루트, TLD, 권한 있는 네임서버 순으로 질의

## 문제 해결
- nslookup, dig 등 도구 활용
- DNS 캐시 삭제, 네임서버 변경

{: .note }
DNS 문제는 인터넷 접속 장애의 주요 원인 중 하나입니다.
