Dưới đây là **hướng dẫn Docker Compose nâng cao**, gồm 3 phần bạn yêu cầu:
**1. Docker Compose Profiles**
**2. Docker Compose Override Files**
**3. Docker Compose Networks (Tùy chỉnh nâng cao)**
Tất cả có ví dụ thực tế cho ứng dụng **Spring Boot + MySQL + Redis + Adminer.**

# 1. DOCKER COMPOSE PROFILES (BẬT/TẮT SERVICE THEO MÔI TRƯỜNG)
Compose Profiles cho phép bật/tắt từng service tùy môi trường:
**Ví dụ** `docker-compose.yml`
```yaml
version: "3.9"

services:
    app:
        build: .
        ports:
            - "8080:8080"
        depends_on:
            - db
            - redis
    
    db:
        image: mysql:8
        environment:
            MYSQL_ROOT_PASSWORD: root
            MYSQL_DATABASE: test

    redis:
        image: redis:latest

    adminer:
        image: adminer
        port:
            - "8090:8080"
        profiles: ["dev"]
```
**Giải thích**
- `adminer` chỉ chạy khi kích hoạt **profile = dev**
- Các service kkhacs chạy mặc định
**Chạy với một profile**
```bash
docker-compose --profile dev up -d
```
Kết quả:
- `app`, `db`, `redis`, `adminer` được chạy

**Chạy không profile**
```bash
docker-compose up -d
```
Kết quả:
- adminer KHÔNG chạy
- Chỉ chạy `app + db + redis`

**Chạy nhiều profile cùng lúc**
```bash
docker compose --profile dev --profile debug up -d
```
**Khi nào dùng profiles?**
- Dev mode: bật thêm Adminer, phpMyAdmin, Kafka UI
- Dubug mode: bật thêm APM, Jaeger
- Prod mode: tắt tất cả tool dev/debug

# 2. DOCKER COMPOSE OVERRIDE FILES
Compose hỗ trợ nhiều file cấu hình để override:
**File mặc định:**
- `docker-compose.yml`
- `docker-compose.override.yml` (tự động load)

**Ý nghĩa:**
- cấu hình cơ bản → `docker-compose.yml`
- cấu hình máy dev → `docker-compose.override.yml`

**Ví dụ** `docker-compose.yml`
```yaml
version: "3.9"
services:
    app:
        image: myapp:latest
        ports:
            - "8080:8080"
        environment:
            SPRING_PROFILES_ACTIVE: prod
```

**Ví dụ** `docker-compose.override.yaml`
Dành cho **developer**, load tự động:
```yaml
services:
    app:
        environment:
            SPRING_PROFILES_ACTIVE: dev
        volumes:
            - ./src:/app/src
        ports:
            - "8081:8080" # đổi port local
```
**Kết quả sau khi override**
- port đổi thành `8081`
- profile từ `prod → dev`
- mount code local để hot-reload

**Override thủ công bằng `-f`**
```bash
docker-compose -f docker-compose.yml -f docker-compose.dev.yml dev
```
Thứ tự: File đứng sau sẽ override file đứng trước.

**Khi nào dùng Override files?**
- Dùng một file riêng cho dev (không commit lên môi trường production)
- Dùng file riêng cho CI/CD
- Dùng file riêng khi chạy local hoặc test

# 3. DOCKER COMPOSE NETWORKS (NÂNG CAO)
Compose tự tạo 1 network mặc định, nhưng bạn có thể **chia network** để cô lập service.

**Ví dụ nâng cao với nhiều networks**
```yaml
version: "3.9"
services:
    app:
        build: .
        networks:
            - backend
            - frontend
        depends_on:
            - db
            - redis

    db:
        image: mysql:8
        networks:
            - backend
    
    redis:
        image: nginx
        networks:
            - frontend
        ports:
            - "80:80"

network:
    backend:
    frontend:
```
**Giải thích:**
**Network** `backend`
- app <-> db
- app <-> redis
- db và redis không thể gọi nginx

**Network** `frontend`
- app <-> nginx
- frontend UI giao tiếp app
- Không truy cập được vào db trực tiếp → rất an toàn

**Đây là mô hình microservice đúng chuẩn:**
```css
[Browser] → [NGINX] → [APP] → [DB/Redis]
```

🔥 **Kiến thức quan trọng về networks**
**3.1 Mỗi network là một "LAN ảo"**
- container gọi nhau bằng `service-name:port`
- không cần IP tĩnh

**3.2 Bridge network (mặc định)**
Dùng cho đa số trường hợp.

**3.3 Host network**
Dùng khi bạn muốn container dùng network host trực tiếp:
```makefile
network_node: host
```
⚠ Không dùng Windows/Mac → chỉ linux support tốt.

**3.4 Internal network (chỉ trong cluster)**
```yaml
networks:
    backend:
        internal: true
```
→ container trong backend không thể ra internet.
Dùng cho:
- database
- redis
- rabbitmq
- kafka

**Ví dụ tổng hợp đầy đủ & nâng cao**
```yaml
version: "3.9"

services:
  app:
    build: .
    networks:
      - frontend
      - backend
    depends_on:
      - db
      - redis

  db:
    image: mysql:8
    networks:
      - backend

  redis:
    image: redis
    networks:
      - backend

  adminer:
    image: adminer
    ports:
      - "8090:8080"
    profiles:
      - dev
    networks:
      - frontend

  nginx:
    image: nginx
    networks:
      - frontend
    ports:
      - "80:80"

networks:
  frontend:
  backend:
    internal: true
```
**Kết luận**
**Docker Compose nâng cao bao gồm:**
| Thành phần         | Mục đích                                                |
| ------------------ | ------------------------------------------------------- |
| **profiles**       | bật / tắt service tùy môi trường                        |
| **override files** | custom cấu hình dev/prod mà không sửa file chính        |
| **networks**       | chia mạng để bảo mật & tách logic giữa frontend/backend |
