---
layout: default
title: URI 모니터링 설정
parent: 핀포인트 APM 모니터링
nav_order: 3
---

# URI 모니터링 설정

Pinpoint에서 웹 요청(URI)을 추적하고 모니터링하는 방법을 안내합니다.

## URI 모니터링 개요

URI 모니터링은 웹 애플리케이션의 엔드포인트별 성능을 추적하는 핵심 기능입니다.

### 주요 기능
- **요청 추적**: 각 URI별 요청 처리 과정 추적
- **성능 분석**: 응답 시간, 처리량, 에러율 분석
- **의존성 분석**: 데이터베이스, 외부 API 호출 관계 분석
- **실시간 모니터링**: 실시간 요청 처리 상태 모니터링

## 웹 애플리케이션 설정

### Spring Boot 설정
```java
// Spring Boot 애플리케이션에 Pinpoint Agent 설정
@SpringBootApplication
public class WebApplication {
    public static void main(String[] args) {
        // JVM 옵션으로 Pinpoint Agent 설정
        // -javaagent:/path/to/pinpoint-bootstrap-2.5.2.jar
        // -Dpinpoint.applicationName=MyWebApp
        // -Dpinpoint.agentId=web-server-01
        SpringApplication.run(WebApplication.class, args);
    }
}
```

### JVM 옵션 설정
```bash
# Pinpoint Agent JVM 옵션
-javaagent:/opt/pinpoint-agent/pinpoint-bootstrap-2.5.2.jar
-Dpinpoint.applicationName=MyWebApp
-Dpinpoint.agentId=web-server-01
-Dpinpoint.collector.ip=localhost
-Dpinpoint.collector.tcp.port=9994
-Dpinpoint.collector.udp.port=9995
-Dpinpoint.collector.span.port=9996
```

## 컨트롤러 설정

### 기본 컨트롤러 모니터링
```java
@RestController
@RequestMapping("/api")
public class UserController {
    
    @GetMapping("/users")
    @Trace  // Pinpoint 추적 활성화
    public ResponseEntity<List<User>> getUsers() {
        // 비즈니스 로직
        return ResponseEntity.ok(userService.getUsers());
    }
    
    @PostMapping("/users")
    @Trace
    public ResponseEntity<User> createUser(@RequestBody User user) {
        // 비즈니스 로직
        return ResponseEntity.ok(userService.createUser(user));
    }
}
```

### 고급 추적 설정
```java
@RestController
public class AdvancedController {
    
    @GetMapping("/complex-operation")
    @Trace
    public ResponseEntity<Result> complexOperation() {
        // 메서드 시작 시간 기록
        long startTime = System.currentTimeMillis();
        
        try {
            // 1단계: 데이터 검증
            validateData();
            
            // 2단계: 비즈니스 로직 처리
            Result result = processBusinessLogic();
            
            // 3단계: 결과 저장
            saveResult(result);
            
            return ResponseEntity.ok(result);
            
        } catch (Exception e) {
            // 에러 발생 시 Pinpoint에 에러 정보 전송
            throw e;
        } finally {
            // 메서드 실행 시간 기록
            long executionTime = System.currentTimeMillis() - startTime;
            // Pinpoint에 실행 시간 전송
        }
    }
}
```

## 필터 설정

### HTTP 요청/응답 필터
```java
@Component
public class PinpointFilter implements Filter {
    
    @Override
    public void doFilter(ServletRequest request, ServletResponse response, 
                        FilterChain chain) throws IOException, ServletException {
        
        HttpServletRequest httpRequest = (HttpServletRequest) request;
        HttpServletResponse httpResponse = (HttpServletResponse) response;
        
        // 요청 시작 시간 기록
        long startTime = System.currentTimeMillis();
        
        try {
            // 요청 처리
            chain.doFilter(request, response);
            
        } catch (Exception e) {
            // 에러 처리
            throw e;
        } finally {
            // 응답 시간 계산 및 기록
            long responseTime = System.currentTimeMillis() - startTime;
            recordResponseTime(httpRequest.getRequestURI(), responseTime, 
                             httpResponse.getStatus());
        }
    }
    
    private void recordResponseTime(String uri, long responseTime, int status) {
        // Pinpoint에 응답 시간 정보 전송
    }
}
```

## 데이터베이스 연동 모니터링

### JPA/Hibernate 모니터링
```java
@Repository
public class UserRepository {
    
    @PersistenceContext
    private EntityManager entityManager;
    
    @Trace
    public List<User> findUsersByStatus(String status) {
        // SQL 쿼리 실행 시간이 자동으로 추적됨
        return entityManager.createQuery(
            "SELECT u FROM User u WHERE u.status = :status", User.class)
            .setParameter("status", status)
            .getResultList();
    }
}
```

### MyBatis 모니터링
```java
@Mapper
public interface UserMapper {
    
    @Trace
    @Select("SELECT * FROM users WHERE status = #{status}")
    List<User> findUsersByStatus(@Param("status") String status);
}
```

## 외부 API 호출 모니터링

### RestTemplate 모니터링
```java
@Service
public class ExternalApiService {
    
    @Autowired
    private RestTemplate restTemplate;
    
    @Trace
    public UserData callExternalApi(String userId) {
        // 외부 API 호출 시간이 자동으로 추적됨
        return restTemplate.getForObject(
            "https://api.external.com/users/" + userId, 
            UserData.class);
    }
}
```

### WebClient 모니터링
```java
@Service
public class ReactiveApiService {
    
    @Autowired
    private WebClient webClient;
    
    @Trace
    public Mono<UserData> callReactiveApi(String userId) {
        // WebClient 호출도 자동으로 추적됨
        return webClient.get()
            .uri("/users/" + userId)
            .retrieve()
            .bodyToMono(UserData.class);
    }
}
```

## 성능 최적화

### 샘플링 설정
```properties
# URI별 샘플링 설정
profiler.uri.stat.enable=true
profiler.uri.stat.sample.interval=1000

# 특정 URI 제외
profiler.uri.exclude=/health,/metrics,/actuator/**

# 특정 URI만 포함
profiler.uri.include=/api/**,/web/**
```

### 버퍼 설정
```properties
# URI 데이터 버퍼 설정
profiler.uri.buffer.size=1024
profiler.uri.buffer.timeout=1000
```

## 대시보드 구성

### URI별 성능 대시보드
- **응답 시간**: URI별 평균/최대/최소 응답 시간
- **처리량**: URI별 초당 요청 처리량
- **에러율**: URI별 에러 발생률
- **호출 횟수**: URI별 총 호출 횟수

### 실시간 모니터링
- **현재 활성 요청**: 실시간 처리 중인 요청 수
- **응답 시간 분포**: 응답 시간 구간별 분포
- **에러 발생 현황**: 실시간 에러 발생 현황

### 트렌드 분석
- **시간대별 성능**: 시간대별 URI 성능 변화
- **요일별 성능**: 요일별 성능 패턴 분석
- **월별 성능**: 월별 성능 트렌드 분석

## 알림 설정

### URI별 알림 설정
```properties
# 특정 URI 응답 시간 알림
alert.uri.response.time./api/users.warning=1000
alert.uri.response.time./api/users.critical=3000

# 특정 URI 에러율 알림
alert.uri.error.rate./api/users.warning=0.05
alert.uri.error.rate./api/users.critical=0.1
```

### 알림 조건 설정
```properties
# 알림 지속 시간 설정
alert.uri.duration.warning=300
alert.uri.duration.critical=60

# 알림 복구 조건
alert.uri.recovery.threshold=0.8
```

## 문제 해결

### 일반적인 문제
- **URI 추적 안됨**: 필터 설정 확인
- **성능 영향**: 샘플링 비율 조정
- **메모리 사용량 증가**: 버퍼 크기 조정

### 디버깅
```properties
# URI 추적 디버그 모드
profiler.uri.debug.enable=true
profiler.uri.debug.log.level=DEBUG
```

### 로그 분석
```bash
# Pinpoint Agent 로그 확인
tail -f /opt/pinpoint-agent/logs/pinpoint-agent.log

# 애플리케이션 로그 확인
tail -f /path/to/application.log
```

## 모범 사례

### 1. URI 설계
- **RESTful 설계**: 표준 REST API 설계 원칙 준수
- **버전 관리**: API 버전 관리 (/api/v1/users)
- **일관된 네이밍**: 일관된 URI 네이밍 규칙 적용

### 2. 성능 최적화
- **캐싱 전략**: 적절한 캐싱 전략 적용
- **비동기 처리**: 장시간 작업은 비동기 처리
- **데이터베이스 최적화**: 쿼리 최적화 및 인덱스 설정

### 3. 모니터링 전략
- **핵심 URI 우선**: 비즈니스 핵심 URI 우선 모니터링
- **임계값 설정**: 적절한 임계값 설정
- **정기적 검토**: 정기적인 성능 검토 및 최적화

---

URI 모니터링이 성공적으로 설정되면 웹 애플리케이션의 성능을 세밀하게 분석할 수 있습니다. 