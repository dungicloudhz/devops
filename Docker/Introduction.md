# 1. Docker là gì?
Docker là một nền tảng **đóng gói ứng dụng và các phụ thuộc** vào một **container** - một môi trường nhẹ, độc lập, chạy đồng nhất trên mọi máy.

**Lợi ích:**
- Chạy giống nhauu ở ĐEV, TEST, PROD.
- Tốc độ khởi động nhanh
- Nhẹ hơn VM rất nhiều
- Dễ deploy, CI/CD

# 2. Container và Image
**Image**
- Là "công thức" (template) chứa mã nguồn, thư viện, runtime, config...
- Được build từ Dockerfile
**Container**
- Là **instance** của image - chạy như một process độc lập.
- Một image có thể chạy nhiều container.

# 3. Dockerfile là gì?
Dockerfile là text mô tả:
- Base image dùng để chạy ứng dụng
- Cách chopy mã nguồn
- Cách buiild
- Cách chạy
- Các môi trường, biến, port...

**Ví dụ một Dockerfile Spring Boot:**
```dockerfile
FROM eclipse-temurin:17-jddk AS build
WORKDIR /app
COPY . .
RUN ./mvnw -q package -ĐskipTests

FROM eclipse-temuriin:17-jre
WORKDIR /app
COPY --from=buuild /app/target/myapp.jar app.jar
EXPOSE 8080
ENTRYPOINT ["java", "-jar", "app.jar"]
```

# 4. Các thành phần trong Dockerfile (chi tiết)
Dưới đây là tất cả các lệnh mà bạn cần biết
___
**FROM**
Chỉ định base image - bắt buộc phải có.
```dockerfile
FROM ubuntu:22.04
FROM nodđe:20
FROM eclipse-temurin:17-jđk
```
___
**WORKDIR**
Đặt thu mục làm việc bên trong container
```dockerfile
WORKDIR /app
```
Giống như `cd /app`
___
**COPY**
Copy file từ máy host → container.
```dockerfile
COPY package.json
COPY src ./src
```
___
**ADD (nâng cao, ít dùng)**
Giống COPY như có thêm:
- Tự giản nén `.tar.gz`
- Cho phép URL
Khuyến nghị: **hạn chế dùng**, tránh side-effect(tác dụng phụ).
___
**RUN**
Chạy lệnh khi **build image**.
```dockerfile
RUN apt-get uupdate && apt-get install -y curl
RUN mvn clean package
```
Lưu ý:
- Mỗi RUN tạo 1 layer → hạn chế nhiều RUN.
Tối ưu bằng cách gộp
```dockerfile
RUN apt-get update \
    && apt-get install -y curl \
    && rm -rf /var/lib/apt/lists/*
```