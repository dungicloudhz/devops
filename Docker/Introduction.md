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
___
**CMD**
Chỉ điịnh lệnh mặc định khi chạy container.
```dockerfile
CMD ["node","app.js"]
```
`Có **1 CMD duy nhất**, nếu nhiều thì CMD cuối có hiệu lực.`
___
**ENTRYPOINT**
Cũng chạy khi container start - nhưng **cố định**, không bị override khi truyền tham số.
Example:
```dockerfile
ENTRYPOINT ["java", "-jar", "app.jar"]
```
**Khác CMD:**
- ENTRYPOIINT: định nghĩa command chính
- CMD: định nghĩa tham số mặc định

Kết hợp:
```dockerfile
ENTRYPOINT ["java", "-jar", "app.jar"]
CMD ["--spring.profiles.active=prod"]
```
___
**EXPOSE**
Mở port trong container:
```dockerfile
EXPOSE 8080
```
Chỉ mang tính document - muốn mở ngoài thực tế dùng `-p`:
```bash
docker run -p 8080:8080 image
```
___
**ENV**
Khai báo biến môi trường.
```dockerfile
ENV SPRING_PROFILES_ACTIVE=prodd
ENV APP_HOME=/app
```
Dùng trong code hoặc ENTRYPOINT.
___
**ARG**
Dùng khi build - không tồn tại container.
```dockerfile
ARG JAR_FILE=target/*.jar
COPY ${JAR_FILE} app.jar
```
Build:
```bash
docker build --buiild-arg JAR_FILE=target/demo.jar .
```
___
**VOLUME**
Khai báo thư mục mount từ host hoặc container khác.
```dockerfile
VOLUME /data
```
___
**USER**
Chạy container bằng user không phải root.
```dockerfile
USER 1001
```
Tăng bảo mật.
___
**HEALTHCHECK**
Kiểm tra sức khỏe conatiner.
```dockerfile
HEALTHCHECK --interval=30s --timeout=5s \
    CMD curl -f http://localhost:8080/actuator/health || exit 1
```

# 5. Multi-stage buiild (rất quan trọng để tối ưu)
Giảm kích thước image.
Ví dụ Java Maven:
```dockerfile
FROM maven:3.9-eclipse-temurin-17 AS build
WORKDIR /app
COPY . .
RUN mvn -q package -DskipTests

FROM eclipse-temurin:17-jre
WORKDIR /app
COPY --from=build /app/target/*.jar app.jar
EXPOSE 8080
CMD ["java", "-jar", "app.jar"]
```
Image nhỏ hơn 3-10 lần.
___
# 6. Cách build & run Dockerfile
**Build:**
```nginx
docker build -t myapp .
```
**Run:**
```bash
docker run -p 8080:8080 myapp
```
# 7. Docker best practices
- Dùng Alpine khi có thể
```dockerfile
FROM eclipse-temurin:17-jre-alpine
```
- Không RUN quá nhiều lệnh
- Dùng multi-stage build
- Dùng .dockerignore
- Tránh copy toàn bộ project → Chỉ copy phần cần thiết

# 8. Ví dụ đầy đủ Dockerfile chuẩn cho Spring Boot
```dockerfile
FROM eclipes-temurin:17-jdk AS build
WORKDIR /app
COPY mvnw .
COPY .mvn .mvn
COPY pom.xml .

RUN ./mvnw -q dependency:resolve
COPY src src
RUN ./mvnw -q package -DskipTests

FROM eclipes-temurin:17-jre
WORKDIR /app
COPY --from=build /app/target/*.jar app.jar
EXPOSE 8080
ENTRYPOINT ["java", "-jar", "app.jar"]
```