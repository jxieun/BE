# 🎯 Supabase PostgreSQL 설정 가이드

Supabase는 **영구 무료**로 PostgreSQL 데이터베이스를 제공합니다 (500MB).

---

## 1️⃣ Supabase 계정 생성 및 프로젝트 생성

### 1-1. 계정 생성
1. https://supabase.com 접속
2. **"Start your project"** 클릭
3. GitHub 계정으로 로그인 (권장)

### 1-2. 새 프로젝트 생성
1. 대시보드에서 **"New Project"** 클릭
2. 프로젝트 정보 입력:
   - **Name**: `robo-advisor-db` (원하는 이름)
   - **Database Password**: 강력한 비밀번호 생성 (저장 필수!) wjsqhdwnsvmfhwprxm
   - **Region**: `Northeast Asia (Seoul)` 또는 `Southeast Asia (Singapore)`
   - **Pricing Plan**: **Free** 선택
3. **"Create new project"** 클릭
4. 프로젝트 생성까지 약 2분 대기

---

## 2️⃣ 데이터베이스 연결 정보 확인

### 2-1. Connection String 가져오기
1. Supabase 대시보드 → **Settings** (왼쪽 하단 톱니바퀴)
2. **Database** 메뉴 클릭
3. **Connection string** 섹션에서 **URI** 복사

**형식**:
```
postgresql://postgres.[PROJECT-REF]:[YOUR-PASSWORD]@aws-0-ap-northeast-1.pooler.supabase.com:6543/postgres
```

postgresql://postgres:[wjsqhdwnsvmfhwprxm]@db.pfmrpgoqwweadaytpfoz.supabase.co:5432/postgres

postgresql://postgres.pfmrpgoqwweadaytpfoz:[wjsqhdwnsvmfhwprxm]@aws-1-ap-northeast-2.pooler.supabase.com:5432/postgres

### 2-2. JDBC URL 변환

Spring Boot에서 사용할 JDBC URL 형식으로 변환:

**원본 (Supabase URI)**:
```
postgresql://postgres.abcdefgh:[PASSWORD]@aws-0-ap-northeast-1.pooler.supabase.com:6543/postgres
```

**변환 (JDBC URL)**:
```
jdbc:postgresql://aws-0-ap-northeast-1.pooler.supabase.com:6543/postgres?user=postgres.abcdefgh&password=[PASSWORD]
```

또는 개별 설정:
```properties
spring.datasource.url=jdbc:postgresql://aws-0-ap-northeast-1.pooler.supabase.com:6543/postgres
spring.datasource.username=postgres.abcdefgh
spring.datasource.password=[YOUR-PASSWORD]
```

---

## 3️⃣ Render 환경 변수 설정

Render 대시보드 → **jeonbongjun-backend** → **Environment**:

### 추가할 환경 변수

| Key | Value | 예시 |
|-----|-------|------|
| `DATABASE_URL` | JDBC URL | `jdbc:postgresql://aws-0-ap-northeast-1.pooler.supabase.com:6543/postgres` |
| `DB_USERNAME` | Supabase Username | `postgres.abcdefgh` |
| `DB_PASSWORD` | 생성한 비밀번호 | `your-strong-password` |

---

## 4️⃣ 로컬 개발 환경 설정

### 4-1. `.env` 파일 생성 (BE 폴더)

```bash
cd /Users/rose/Downloads/mini3/BE
cat > .env << EOF
DATABASE_URL=jdbc:postgresql://aws-0-ap-northeast-1.pooler.supabase.com:6543/postgres
DB_USERNAME=postgres.abcdefgh
DB_PASSWORD=your-strong-password
EOF
```

### 4-2. 로컬 테스트

```bash
./mvnw spring-boot:run
```

**성공 메시지**:
```
HikariPool-1 - Starting...
HikariPool-1 - Start completed.
```

---

## 5️⃣ Supabase Dashboard에서 테이블 확인

1. Supabase 대시보드 → **Table Editor**
2. Spring Boot 첫 실행 후 JPA가 자동으로 테이블 생성
3. `users`, `stocks`, `portfolios` 등 테이블 확인

---

## ✅ 검증

### Backend 로그 확인
```bash
# Render 로그에서 확인
2026-01-23 15:30:00 INFO  HikariPool-1 - Starting...
2026-01-23 15:30:01 INFO  HikariDataSource - Start completed.
```

### Supabase 대시보드 확인
- **Database** → **Activity**: 연결 활동 확인
- **Table Editor**: 생성된 테이블 확인

---

## 🎁 Supabase 무료 플랜 제한

- ✅ **500MB** 데이터베이스 스토리지
- ✅ **무제한** API 요청
- ✅ **2GB** egress (충분함)
- ✅ **50,000** 월간 활성 사용자
- ✅ **영구 무료** (시간 제한 없음)

---

## 🔧 문제 해결

### 연결 실패: "Connection refused"
- Supabase 프로젝트가 일시 중지되었을 수 있음
- 대시보드에서 프로젝트 "Resume" 클릭

### 비밀번호 분실
- Supabase 대시보드 → Settings → Database
- **"Reset database password"** 클릭
- 새 비밀번호 설정 후 환경 변수 업데이트

### SSL 오류
JDBC URL에 SSL 파라미터 추가:
```
jdbc:postgresql://...?sslmode=require
```
