# 🎯 Upstash Redis 설정 가이드

Upstash는 **영구 무료**로 Redis를 제공합니다 (10,000 commands/day).

---

## 1️⃣ Upstash 계정 생성

1. https://upstash.com 접속
2. **"Get Started"** 클릭
3. GitHub 계정으로 로그인 (권장)

---

## 2️⃣ Redis 데이터베이스 생성

### 2-1. 새 데이터베이스 생성
1. Upstash Console → **Redis** → **"Create Database"**
2. 데이터베이스 정보 입력:
   - **Name**: `robo-advisor-redis`
   - **Region**: `Asia Pacific (Seoul)` 또는 가장 가까운 지역
   - **Type**: **Regional** (무료)
   - **Eviction**: **noeviction** (권장)
3. **"Create"** 클릭

### 2-2. 연결 정보 확인
생성 완료 후 자동으로 **Details** 페이지로 이동

---

## 3️⃣ Redis URL 가져오기

### 3-1. Connection String 복사

**Details** 페이지에서:
- **Endpoint**: `https://...upstash.io`
- **Port**: `6379` 또는 `6380`
- **Password**: 자동 생성됨

### 3-2. Spring Boot 형식 URL

Upstash Console → **Details** → **Spring Boot** 탭:

```
rediss://default:[PASSWORD]@musical-mollusk-12345.upstash.io:6379
```
redis-cli --tls -u redis://default:AcokAAIncDFjM2NkZGMwM2Y3YTE0NmM1OTMxOTFjZTYwY2NhZjU1MnAxNTE3NDg@social-halibut-51748.upstash.io:6379

또는 **Redis CLI** 탭에서 개별 정보 확인:
```properties
REDIS_HOST=musical-mollusk-12345.upstash.io
REDIS_PORT=6379
REDIS_PASSWORD=your-password-here
```

---

## 4️⃣ Render 환경 변수 설정

Render 대시보드 → **jeonbongjun-backend** → **Environment**:

### 추가할 환경 변수

| Key | Value | 예시 |
|-----|-------|------|
| `REDIS_URL` | Upstash Redis URL | `rediss://default:[PASSWORD]@musical-mollusk-12345.upstash.io:6379` |

---

## 5️⃣ 로컬 개발 환경 설정

### 5-1. `.env` 파일에 추가

```bash
cd /Users/rose/Downloads/mini3/BE
echo "REDIS_URL=rediss://default:[PASSWORD]@musical-mollusk-12345.upstash.io:6379" >> .env
```

### 5-2. 로컬 테스트

```bash
./mvnw spring-boot:run
```

**성공 메시지**:
```
2026-01-23 15:35:00 INFO  LettuceConnectionFactory - Lettuce initialized
```

---

## 6️⃣ Redis 동작 확인

### 6-1. Upstash Console에서 확인
1. Upstash Console → **Data Browser**
2. Spring Session이 자동으로 생성한 키 확인:
   - `spring:session:*`

### 6-2. 테스트 명령어 (선택사항)

Upstash Console → **CLI** 탭:
```redis
KEYS *
# 저장된 키 목록 확인

GET spring:session:sessions:[session-id]
# 세션 데이터 확인
```

pcsk_59Sxkd_Etsrs4NZVQoLZpoLg3XVcD44FUMHBLuwsNMaxGQwzNeD15SqX5vm2UxojssUkkG
---

## ✅ 검증

### Backend 로그 확인
```bash
# Render 로그에서 확인
2026-01-23 15:35:00 INFO  LettuceConnectionFactory - Lettuce initialized
```

### Upstash Console 확인
- **Data Browser**: 세션 키 확인
- **Metrics**: 요청 수 모니터링

---

## 🎁 Upstash 무료 플랜 제한

- ✅ **10,000** commands/day (충분함)
- ✅ **256MB** 최대 데이터 크기
- ✅ **10KB** 최대 데이터 사이즈/요청
- ✅ **무제한** concurrent connections
- ✅ **영구 무료** (시간 제한 없음)

**예상 사용량**: 세션 관리용으로는 하루 1,000~2,000 commands면 충분합니다.

---

## 🔧 문제 해결

### 연결 실패: "Connection refused"
**원인**: 잘못된 URL 또는 방화벽 차단

**해결**:
1. Upstash Console에서 URL 다시 확인
2. `rediss://` (SSL) 사용 확인
3. 비밀번호에 특수문자가 있다면 URL 인코딩

### SSL 오류
**해결**: Spring Boot에 SSL 설정 추가
```properties
spring.data.redis.ssl=true
spring.data.redis.ssl.enabled=true
```

### 명령어 제한 초과
**확인**: Upstash Console → **Metrics**에서 일일 사용량 확인

**해결**:
- 세션 TTL 줄이기
- 불필요한 세션 데이터 정리

---

## 💡 성능 최적화

### Session TTL 설정
```properties
# application.properties에 추가
spring.session.timeout=1800s  # 30분
```

### 연결 풀 설정
```properties
spring.data.redis.lettuce.pool.max-active=8
spring.data.redis.lettuce.pool.max-idle=8
spring.data.redis.lettuce.pool.min-idle=2
```
