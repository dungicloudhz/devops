# 1. Docker Compose
**Docker Compose là gì?**
Là công cụ giúp bạn **định nghĩa và chạy nhiều container** trong cùng một file `docker-compose.yml`.
Thay vì chạy thủ công:
```bash
docker run mysql ...
docker run backend ...
docker run frontend ...
```
Bạn chỉ cần:
```bash
docker-compose up -d
```
___
**Cấu trúc của docker-compose.yml**
```yaml
version: "3.9"

services:
    app:
        build: .
        ports:
            - "8080:8080"
        environment:
            SPRING_DATASOURCE_URL: jdbc:mysql://db:3306/test
        depends_on:
            - db
    
    db:
        image: mysql:8
        environment:
            MYSQL_ROOT_PASSWORD: root
            MYSQL_DATABASE: test
        volumes:
            - db_data:/var/lib/mysql

volumes:
    db_data:
```
**Giải thích:**
`services`
Mỗi service là một container.
**Kết nối giữa các service**
Docker Compose tự tạo **một network riêng**, các service có thể gọi nhau như:
```bash
jdbc:mysql://db:3306/test
```
`depends_on`
Đảm bảo app chạy sau db.
`volumes`
Dữ liệu database không mất khi container restart.

# 2. Tối ưu kích thước Docker Image
## 2.1 Dùng Alpine image (nhẹ nhất)
Ví dụ Java:
```dockerfile
FROM eclipse-temurin:17-jre-alpine
```
Giảm từ 300MB → 70MB.
**Lưu ý:** Alpine gặp vấn đề với `glibc` cho một số app Java → hãy thử trước,

## 2.2 Multi-stage build (bắt buộc)
Giảm size 2-10 lần so với build trực tiếp.
```dockerfile
FROM maven:3.9-eclipse-temurin-17 AS builld
WORKDIR /app
COPY . .
RUN mvn -q package -DskipTests

FROM eclipse-temurin:17-jre-alpine
WORKDIR /app
COPY --from=build /app/target/*.jar app.jar
ENTRYPOINT ["java", "-jar", "app.jar"]
```

## 2.3 Chỉ COPY file cần thiết
Thay vì:
```dockerfile
COPY . .
```
Hãy dùng `.dockerignore`:
```yaml
target/
.git/
*.md
```

## 2.4 Giảm số layer RUN
Không nên:
```dockerfile
RUN apt-get update
RUN apt-get install -y git
```
Tối ưu:
```dockerfile
RUN apt-get update && \
    apt-get install -y git && \
    rm -rf /var/lib/apt/lists/*
```
## 2.5 Không chạy ứng dụng bằng root
Tạo user:
```dockerfile
RUN addgroup -S app && adduser -S app -G app
USER app
```

# 3. Giải thích từng layer trong Docker Image
Docker image được build theo dạng **layered filesystem**, mỗi lệnh trong Dockerfile tạo **một layer mới**.
Ví dụ:
```dockerfile
FROM node:20
WORKDIR /app
COPY package.json .
RUN npm install
COPY . .
CMD ["npm", "start"]
```

| Lệnh              | Layer tạo ra    | Ghi chú                      |
| ----------------- | --------------- | ---------------------------- |
| FROM              | Layer base      | Nặng nhất                    |
| WORKDIR           | Layer metadata  | gần như không tốn dung lượng |
| COPY package.json | Layer chứa file |                              |
| RUN npm install   | Layer lớn       | chứa node_modules            |
| COPY . .          | Layer chứa code |                              |
| CMD               | Không tạo layer | chỉ metadata                 |

**Cơ chế overlay**
Khi container chạy, Docker ghép các layer theo dạng:
```pgsql
read-only layer (image)
+ read-write layer (container)
--------------------------------
Thành filesystem cuối cùng
```
Nếu bạn sửa file trong container:
- Docker không sửa trực tiếp file trong layer image
- Mà copy file vào **overlay (copy-on-write)** và sửa ở đó

**Vì sao layer quan trọng?**
- Layer giúp **cache** build → build lại siêu nhanh
- Layer giúp **tối ưu dung lượng**
- Layer giúp **chia sẻ giữa nhiều container**

Nếu bạn chỉ thay đổi 1 file trong code:
- Docker chỉ rebuild từ bước `COPY . .` trở xuống
- Các bước trước dùng lại cache

# 4. Docker hoạt động bên trong - kiến trúc
Docker sử dụng các công nghệ Linux;
## 4.1 Namespaces:
Tách biệt process:
- PID namespace → container có PID riền
- NET namespace → mỗi container có network riêng
- MNT namespace → filesystem riêng
- IPC namespace → signal riêng
- UTS namespace → hostname riêng

→ Container giống như "máy ảo mini", nhưng **thực chất là process trên host.**

## 4.2 Cgroups
Giới hạn tài nguyên:
- CPU
- RAM
- IO

Ví dụ:
```bash
docker run --memory="512m" --cpus="1.5"
```

## 4.3 UnionFS (layered filesystem)
Cơ chế để xây dựng image và container layer-by-layer.
Docker engine dùng:
- overlay2
- AUFS
- btrfs
- zfs (ít)

## 4.4 Docker Deamon & Client
Khi chạy:
```bash
docker run nginx
```
Flow:
1. CLI lệnh gửi tới daemon (`dockerd`)
2. Daemon kiểm tra image → kéo từ registry nếu cần
3. Daemon tạo container từ image
4. Daemon tạo network, mount volume
5. Container chạy thành process chính (PID 1)

## 4.5 Network của Docker
Docker có 3 loại network mặc định:
| Network | Mô tả                       |
| ------- | --------------------------- |
| bridge  | mặc định cho container      |
| host    | dùng network host trực tiếp |
| none    | không có network            |

Docker compose tạo riêng **mạng ảo** cho các service → service gọi nhau bằng **tên service**.

# Tóm tắt nhanh
**Docker Compose**
Dùng để chạy nhiều container bằng 1 file cấu hình.
**Tối ưu image**
Dùng Alpine, multi-stage build, .dockerignore, gom RUN lại.
**Layer trong image**
Mỗi lệnh tạo một layer → tận dụng cache để build nhanh và giảm size.
**Docker hoạt động bên trong**
Dùng namespaces, cgroups, unionFS → container chỉ là process tách biệt trên host.
