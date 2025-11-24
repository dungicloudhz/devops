Dưới đây là **toàn bộ các câu lệnh Docker CLI quan trọng**, chia theo từng nhóm, kèm ví dụ thực tế, giải thích chi tiết và tip để nhớ lâu.
Đây là tài liệu full, dùng được từ level fresher → senior DevOps.

# I. CÁC LỆNH LÀM VIỆC VỚI CONTAINER
## 1. Xem danh sách container
```bash
docker ps
docker ps -a         # gồm cả container đã stop
docker ps -q         # chỉ lấy ID
```
## 2. Tạo & chạy container
```bash
docker run nginx
docker run -d nginx              # chạy nền
docker run -it ubuntu bash       # tương tác shell
docker run --name web nginx      # đặt tên
docker run -p 8080:80 nginx      # map port 8080 -> 80
docker run -v data:/var/lib/mysql mysql   # mount volume
```
Các option quan trọng:
- `-d:` chạy nền (detached)
- `-p:` map port
- `-v:` gắn volume
- `--name:` đặt tên container
- `-e:` truyền biến môi trường

## 3. Dừng container
```bash
docker stop <id|name>
```
Kill ngay lập tức:
```bash
docker kill <id>
```

## 4. Xóa container
```bash
docker rm <id>
docker rm -f <id>        # xóa kể cả đang chạy
docker rm $(docker ps -aq)   # xóa tất cả
```

## 5. Khởi động lại container
```bash
docker restart <id>
```

## 6. Liên kết terminal vào container
```bash
docker exec -it <id> sh
docker exec -it <id> bash
```
Nếu container không có shell:
```bash
docker exec -it <id> /bin/sh
```

## 7. Xem log container
```bash
docker logs <id>
docker logs -f <id>       # xem realtime (tail -f)
docker logs --tail 100 <id>
```
## 8. Inspect (xem thông tin chi tiết)
```bash
docker inspect <id>
```
Tìm IP:
```bash
docker inspect -f '{{ .NetworkSettings.IPAddress }}' <id>
```
## 9. Copy file từ/to container
```bash
docker cp <container>:/app/log.txt ./log.txt
docker cp ./config.json <container>:/app/
```
## 10. Kiểm tra tài nguyên container (monitoring)
```bash
docker stats
```

# II. CÁC LỆNH LÀM VIỆC VỚI IMAGE
## 1. Xem danh sách image
```bash
docker images
```
## 2. Build image từ Dockerfile
```bash
docker build -t myapp .
docker build -t myapp:1.0 -f Dockerfile.prod .
```
## 3. Tag image
```bash
docker tag myapp myrepo/myapp:latest
docker tag myapp myrepo/myapp:1.0.0
```
## 4. Xóa image
```bash
docker rmi <id>
docker rmi -f <id>   # ép xóa
```
## 5. Pull image từ Docker Hub
```bash
docker pull nginx
docker pull nginx:latest
```
## 6. Push image lên registry
```bash
docker push myrepo/myapp:latest
```
## 7. Xem lịch sử layer của image
```bash
docker history <image>
```
Rất hữu ích để tối ưu Dockerfile.

# III. CÁC LỆNH VỀ VOLUME
## 1. Xem volume
```bash
docker volume ls
```
## 2. Tạo volume
```bash
docker volume create data
```
## 3. Xóa volume
```bash
docker volume rm data
```
## 4. Inspect volume
```bash
docker volume inspect data
```
# IV. CÁC LỆNH VỀ NETWORK
## 1. Xem network
```bash
docker network ls
```
## 2. Tạo network
```bash
docker network create backend
```
## 3. Xóa network
```bash
docker network rm backend
```
## 4. Gắn container vào network
```bash
docker network connect backend mycontainer
```
## 5. Inspect network
```bash
docker network inspect backend
```
# V. DỌN DẸP (CLEANUP)
## 1. Xóa container + image không dùng
```bash
docker system prune
```
Xóa tất cả (rất mạnh!):
```bash
docker system prune -a
```
## 2. Xóa volume không dùng
```bash
docker volume prune
```
# VI. DOCKER COMPOSE CLI
## 1. Chạy docker-compose
```bash
docker-compose up -d
```
## 2. Dừng toàn bộ
```bash
docker-compose down
```
Xóa kèm volume:
```bash
docker-compose down -v
```
## 3. Build image trong compose
```bash
docker-compose build
```
## 4. Xem log
```bash
docker-compose logs -f
```
## 5. Restart service trong compose
```bash
docker-compose restart app
```
# BONUS: CÁC LỆNH CỰC HỮU ÍCH TRONG THỰC TẾ
## Kiểm tra port container đang mở
```bash
docker port <id>
```
## Dọn disk space của Docker
```bash
docker system df
```
## Export/import container
```bash
docker export <id> -o file.tar
docker import file.tar
```
## Tạm dừng / resume container
```bash
docker pause <id>
docker unpause <id>
```
# TÀI LIỆU TÓM TẮT (PARETO 80/20)
80% các lệnh dùng nhiều nhất là:
```bash
docker ps
docker ps -a
docker run
docker stop
docker rm
docker logs
docker exec -it
docker build
docker pull
docker push
docker images
docker-compose up
docker-compose down
```