# 1. Gitlab CI/CD là gì?
Gitlab CI/CD là công cụ tích hợp sẵn trong Gitlab giúp bạn:
- **CI (Continuous Integration):** Tự động build → test mỗi khi code thay đổi.
- **CD (Continuous Delivery/Deployment):** Tự động deploy lên môi trường dev/staging/production.

Gitlab CI/CD chạy bằng file cấu hình đặc biệt:
```bash
.gitlab-ci.yml
```
Gitlab Runner sẽ đọc file này và thực thi các job.

# 2. Kiến trúc Gitlab CI/CD
Bạn cần 3 thành phần:
**1. Gitlab repository**
Nơi chứa code + file `.gitlab-ci.yml`
**2. Gitlab Runner**
Runner là "máy chủ" thực thi job:
- Shared Runners (Gitlab cung cấp sẵn)
- Specific Runner (Tự cài Docker / máy ảo của bạn)

**3. `.gitlab-ci.yml`**
File này định nghĩa pipeline, gồm:
- stages
- jobs
- scripts
- rules
- artifacts
- environments

# 3. Cấu trúc cơ bản của `.gitlab-ci.yml`
Ví dụ đơn giản:
```yaml
stages:
    - build
    - test
    - deploy

build-job:
    stage: build
    script:
        - echo "Building project..."

test-job:
    stage: test
    script:
        - echo "Running tests..."
        - npm test

deploy-job:
    stage: deploy
    script:
        - echo "Deploying app..."
    only:
        - main
```
# 4. Chi tiết từng phần
## 4.1 Stages
Xác định pipeline gồm bao nhiêu bước:
```yaml
stages:
    - build
    - test
    - deploy
```
Job trong cùng 1 stage sẽ chạy **song song**.
Stage sau chạy khi stage trước hoàn thành.

## 4.2 Job
Job = 1 tác vụ trong CI/CD.
```yaml
build-job:
    stage: build
    script:
        - mvn package
```
Mỗi job gồm:
| Thuộc tính                  | Ý nghĩa                          |
| --------------------------- | -------------------------------- |
| `stage`                     | thuộc stage nào                  |
| `script`                    | lệnh cần chạy                    |
| `rules` / `only` / `except` | khi nào chạy                     |
| `image`                     | Docker image sử dụng             |
| `artifacts`                 | output chuyển sang job tiếp theo |

## 4.3 Sử dụng Docker Image
Rất quan trọng trong GitLab CI.
Ví dụ build bằng NodeJS:
```yaml
image: node:18

build:
    stage: build
    script:
        - npm install
        - npm run build
```
## 4.4 Artifacts (truyền file giữa các job)
```yaml
build:
    stage: build
    script:
        - npm run build
    artifacts:
        paths:
            - dist/
        expire_in: 1 week
```
Job test/deploy có thể lấy thư mục dist từ build job.

## 4.5 Cache (tăng tốc build)
```yaml
cache:
    paths:
        - node_modules/
```
Cache sẽ lưu các dependency để build nhanh hơn.

## 4.6 Rules (khi nào job được chạy)
Ví dụ chỉ chị job khi commit vào branch main:
```yaml
deploy:
    stage: deploy
    script:
        - sh deploy.sh
    rules:
        - if: '$CI_COMMIT_BRANCH == "main"'
```
Ví dụ chỉ chạy khi tạo merge request:
```yaml
test:
    stage: test
    script: npm test
    rules:
        - if: $CI_PIPELINE_SOURCE == "merge_request_event"
```
# 5. Triển khai CI/CD thực tế
Dưới đây là hướng dẫn CI/CD cho các hệ thống phổ biến.
## 5.1 CI/CD cho NodeJS
```yaml
image: node:18

stages:
    - install
    - test
    - build
    - deploy

install:
    stage: install
    script:
        - npm install
    cache:
        paths:
            - node_modules/

test:
    stage: test
    script:
        - npm test

build:
    stage: build
    script:
        - npm run build
    artifacts:
        path:
            - dish/

deploy:
    stage: deploy
    script:
        - scp -r dist/* user@server:/var/www/html
    only:
        - main
```

## 5.2 CI/CD cho Spring Boot (build JAR)
```yaml
image: maven:3.9.0-eclipse-temurin-17

variables:
    MAVEN_OPTS: "-Dmaven.repo.local=.m2/repository"

stages:
    - build
    - deploy

build:
    stage: build
    script:
        - mvn -B clean package
    artifacts:
        paths:
            - target/*.jar

deploy:
    stage: deploy
    script:
        - scp target/*.jar user@server:/opt/app/
        - ssh user@server "systemctl restart app"
    only:
        - main
```
## 5.3 CI/CD build Docker image + push to registry
```yaml
image: docker:latest

services:
    - docker:dind

stages:
    - build
    - push

variables:
    DOCKER_TLS_CERTDIR: ""

build-image:
    stage: build
    script:
        - docker build -t $CI_REGISTRY
```

## 5.4 Deploy lên Kubernetes (Helm)
```yaml
deploy:
    stage: deploy
    image: alpine/k8s:1.29.0
    script:
        - helm upgrade --install my-app ./helm-chart \
        -- set image.tag=$CII_COMMIT_SHA
    only:
        - main
```
# 6. Gitlab Runner - cài đặt local
Nếu bạn muốn tự cài runner:
**Cài đặt Gitlab Runner:**
```bash
sudo gitlab-runner register
```
Chọn:
- executor → docker
- image → alpine/ ubuntu ...

# 7. Debug job trong GitLab CI
Bật chế độ debug:
```yaml
variables:
    CI_DEBUG_TRACE: "true"
```
# 8. Template nâng cao - pipeline đa môi trường
```yaml
stages:
    - build
    - test
    - deploy_dev
    - deploy_prod

deploy_dev:
    stage: deploy_dev
    script:
        - ./deploy.sh dev
    only:
        - develop

deploy_prod:
    stage: deploy_prod
    script:
        - ./deploy.sh prod
    rules:
        - if: '$CI_COMMIT_BRANCH ==  "main"'
        when: menual
```
# 9. Muốn mình viết pipeline CI/CD mẫu theo project của bạn?
Bạn cho mình thông tin:
- App dùng là gì? (NodeJS/ Spring Boot/ Python/ Go/ React/ .NET ?)
- Deploy lên đâu? (Linux server, Docker, K8s, Gitlab Pages?)
- Có cần build Docker image không?
- Có environment dev / staging / prod không?
## 1. App