Mình đã chuẩn bị một **pipeline GitLab CI/CD toàn diện, linh hoạt** (đa ngôn ngữ, đa phương thức deploy, multi-env: dev/staging/prod, có/không build Docker image, Gitlab Pages, severless, rollback, manual approval cho prod...).

Mình tóm tắt cấu trúc rồi đính kèm `.gitlab-ci.yml` **mẫu** (có thể copy/paste vào repo và chỉnh biến CI theo môi trường bạn). Mình cũng kèm phần **giải thích** + **cách cấu hình biến bí mật (CI variables)** để bạn chạy pipeline ngay.

# Tổng quan (ý tưởng)
- Pipeline **tự động phát hiện** ngôn ngữ theo file trong repo (ví dụ `package.json`, `pom.xml`, `requirements.txt`, `go.mod`, `*.csproj`, `Dockerfile`, `public/` cho static site).
- Có các stage: `prepare`, `build`, `test`, `package`, `docker_build`, `docker_push`, `deploy_dev`, `deploy_staging`, `deploy_prod`, `pages`, `cleanup`.
- Bạn bật/tắt từng hành động **CI variables** (ví dụ `ENABLE_DOCKER=true`, `DEPLOY_TO_K8S=true`, ...). Mặc định pipeline sẽ chạy tất cả job phù hợp với repo.
- Deploy lên:
    - **Linux server** (SSH + rsync/scp)
    - **Docker registry** (build/push image)
    - **Kubernetes** (kubectl / Helm) - Cần biến `KUBE_CONFIG`
    - **GitLab Pages** (static site)
    - **Serverless / Cloud** (placeholder; dùng provider CLI: AWS/GCP/Azure - cần thêm secrets)
- **Rollback**: job deploy prod có option manual rollback (deploy previous image tag).
- Jobs **prod** mặc định `when: manual` để tránh deploy tự động nếu bạn muốn thủ công.

# `.gitlab-cii.yml` mẫu (toàn diện)
Lưu ý: file dưới đây là template **mạnh**, có nhiều job; gỡ bỏ những job bạn không cần hoặc dùng biến để tắt. Đọc phần `CI VARIABLES` bên dưới trước khi chạy.
```yaml
# .gitlab-ci.yml - All-in-one template

stages:
    - prepare
    - build
    - test
    - package
    - docker_build
    - docker_push
    - deploy_staging
    - deploy_prod
    - pages
    - cleanup

# ---------- GLOBAL DEFAULTS ----------
variables:
    GIT_DEPTH: "3"
    MAVEN_OPTS: "-Dmaven.repo.local=.m2/repository"
    DOCKER_TLS_CERDIR: "" # for docker:dind
    # Feature toggles (set to "true"/"false" in CI/CD > Variables)
    ENABLE_DOCKER: "true"
    ENABLE_K8S: "false"
    ENABLE_SSH_DEPLOY: "true"
    ENABLE_PAGES: "false"
    IMAGE_TAG: "$CI_COMMIT_SHORT_SHA"

# Caching common directories
cache:
    key: "$CI_COMMIT_REF_SLUG"
    paths:
        - .m2/repository
        - node_modules/
        - .venv/

# ---------- PREPARE ----------
.detect-files:
    stage: prepare
    image: alpine:latest
    script:
        - echo "Detecting project files..."
        - '[ -f package.json ] && echo "NODE" || true'
        - '[ -f pom.xml ] && echo "MAVEN" || true'
        - '[ -f requirements.txt ] && echo "PYTHON" || true'
        - '[ -f go.mod ] && echo "GO" || true'
        - 'ls *.csproj >/dev/null 2>&1 && echo "DOTNET" || true'
        - '[ -f Dockerfile ] && echo "DOCKERFILE" || true'
    artifacts:
        paths:
            - . # keep repo for subsequent jobs
        expire_in: 1 hour
    rules:
        - when: always

# ---------- BUILD JOBS (per language) ----------
# NodeJS / React
build_node:
    stage: build
    image: node:18
    script:
        - if [ -f package.json ]; then npm ci; npm run build || true; fi
    cache:
        paths: [node_modules/]
    rules:
        - exists:
            - package.json

# Java (Maven) - Spring Boot
build_maven:
    stage: build
    image: maven:3.9-jdk-17
    script:
        - if [ -f pom.xml ]; then mvn -B -DskipTests clean package;fi
    artifacts:
        paths:
            - target/*.jar
    rules:
        - exists:
            - pom.xml
            
# Python
build_python:
    stage: build
    image: python:3.11
    script:
        - if [ -f requirements.txt ]; then python -m venv; source .venv/bin/activate; pip install -r requirements.txt; fi
        - if [ -f setup.py ]; then python setup.py sdist bdist_wheel;fi
    artifacts:
        paths:
            - dist/
    rules:
        - exists:
            - requirements.txt

# Go
build_go:
    stage: build
    image: golang:1.21
    script:
        - if [ -f go.mod ]; then go mod download; go build -o bin/app ./...;fi
    artifacts:
        paths:
            - bin/
    rules:
        - exists:
            - go.mod

# .NET
build_dotnet:
    stage: build
    image: mcr.microsoft.com/dotnet/sdk:7.0
    script:
        - |
            if ls *.csproj 1> /dev/null 2>&1; then
            dotnet restore
            dotnet publish -c Release -o out
            fi
    artifacts:
        paths:
            - out/
    rules:
        - exists:
            - "*.csproj"

# ---------- TEST (example generic) ----------
test_all:
    stage: test
    image: alpine:latest
    script:
        - echo "Run tests for each detected project"
        - if [ -f package.json ]; then npm test || true;fi
        - if [ -f pom.xml ]; then mvn test || true; fi
        - if [ -f requirements.txt]; then pytest || true; fi
    rules:
        - when: always

# ---------- PACKAGE (prepare artifacts) ----------
package_generic:
    stage: package
    script:
        - echo "Packaging artifacts for deploy"
    artifacts:
        paths:
            - dist/
            - target/
            - out/
            - bin/
    rules:
        - when: on_success

# ---------- DOCKER BUILD ----------
docker_build:
    stage: docker_build
    image: docker:24
    services:
        - docker:dind
    before_script:
        - docker info
    script:
        - |
            if ["$ENABLE_DOCKER" = "true"] && [ -f Dockerfile ]; then
                docker build --pull -t "$CI_REGISTRY_IMAGE:$IMAGE_TAG" .
            else
                echo "Skipping docker_build"
            fi
    rules:
        - exists:
            - Dockerfile

docker_push:
    stage: docker_push
    image: docker:24
    services:
        - docker:dind
    script:
        - |
            if ["$ENABLE_DOCKER" = "true"] && [ -f Dockerfile ]; then
                echo "$CI_REGISTRY_PASSWORD" | docker login -u "$CI_REGISTRY_USER" --password-stdin "$CI_REGISTRY"
                docker push "$CI_REGISTRY_IMAGE:$IMAGE_TAG"
                docker tag "$CI_REGISTRY_IMAGE:$IMAGE_TAG" "$CI_REGISTRY_IMAGE:latest" || true
                docker push "$CI_REGISTRY_IMAGE:latest" || true
            else
                echo "Skipping docker_push"
            fi
    rules:
        - exists:
            - Dockerfile
    dependencies:
        - docker_build

# ---------- DEPLOY TO LINUX SERVERS (SSH) ----------
.before_ssh:
    before_script:
        - 'which ssh-agent || (apt-get update -y && apt-get install -y openssh-client rsync)'
        - eval $(ssh-agent -s)
        - echo "$SSH_PRIVATE_KEY" | tr -d '\r' || ssh-add -
        - mkdir -p ~/.ssh
        - echo -e "Host *\n\tStrictHostKeyChecking no\n" > ~/.ssh/config
    rules:
        - if: '$ENABLE_SSH_DEPLOY == "true"'
          when: always
        - when: never

deploy_dev_ssh:
    stage: deploy_dev
    extends: .before_ssh
    image: alpine:latest
    script:
        - |
           if [ "$ENABLE_SSH_DEPLOY" = "true" ]; then
              rsync -avz --delete ./ $SSH_USER@DEV_SERVER:$DEPLOY_PATH
           else
              echo "SSH deploy disabled"
           fi
    environment:
        name: dev
        url: http://$DEV_SERVER
    rules:
        - if: '$CI_COMMIT_BRANCH ==  "develop" || $CI_COMMIT_BRANCH =~ /^feature\//'

deploy_staging_ssh:
    stage: deploy_staging
    extends: .before_ssh
    image: alpine:latest
    script:
        - |
           rsync -avz --delete ./ $SSH_USER@STAGING_SERVER:$DEPLOY_PATH
    environment:
        name: staging
        url: http://$STAGING_SERVER
    rules:
        - if: '$CI_COMMIT_BRANCH == "staging" || $CI_PIPELINE_SOURCE == "merge_request_event"'

deploy_prod_ssh:
    stage: deploy_prod
    extends: .before_ssh
    image: alpine:latest
    script:
        - |
           rsync -avz --delete ./ $SSH_USER&PROD_SERVER:$DEPLOY_PATH
    environment:
        name: production
        url: http://$PROD_SERVER
    when: manual
    rules:
        - if: '$CI_COMMIT_BRANCH == "main"'

# ---------- KUBERNETES (Helm / kubectl) ----------
k8s_deploy:
    stage: deploy_prod
    image: lachanevenson/k8s-kubectl:latest
    script:
        - |
            if [ "$ENABLE_K8S" = "true"]; then
                echo "$KUBE_CONFIG" | base64 -d > kubeconfig
                export KUBECONFIG=$CI_PROJECT_DIR/kubeconfig
                # Example helm upgrade
                helm upgrade --install my-app ./helm-chart --set image.tag=$IMAGE_TAG --namespace ${K8S_NAMESPACE:-defualt}
            else
                echo "K8S deploy disabled"
            fi
    environment:
        name: production
    when: manual
    rules:
        - if: '$CI_COMMIT_BRANCH == "main"'

# ---------- GITLAB PAGES (static websites) ----------
pages:
    stage: pages
    image: node:18
    script:
        - if [ -d public ]; then echo "publishing Pages"; else mkdir -p public && echo "Empty" > public/index.html; fi
    artifacts:
        paths:
            - public
    rules:
        - if: '$ENABLE_PAGES == "true"'

# ---------- SERVERLESS (placeholder) ----------
serverless_deploy:
    stage: deploy_prod
    image: node:18
    script:
        - |
            if [ "$ENABLE_SERVERLESS" = "true" ]; then
                # Example: AWS serverless deploy (user must provide AWS creds in CI variables)
                npm install -g serverless
                serverless deploy --stage $CI_ENVIRONMENT_NAME
            else
                echo "Serverless disabled"
            fi
    when: manual
    rules:
        - if: '$ENABLE_SERVERLESS == "true"'    

# ---------- ROLLBACK EXAMPLE (manual) ----------
rollback_prod:
    stage: deploy_prod
    image: docker:24
    services:
        - docker:dind
    script:
        - |
            if [ "$ENABLE_DOCKER" = "true" ]; then
                echo "Manual rollback: choose tag via CI variable ROLLBACK_TAG:
                # Implementation depends on your infra: e.g., helm set image.tag
                helm upgrade --install my-app ./helm-chart --set image.tag=$ROLLBACK_TAG --namespace ${K8S_NAMESPACE:-default}
            else
                echo "Rollback for non-docker apps: implement your steps"
            fi
    when: manual
    rules:
        - if: '$CI_COMMIT_BRANCH == "main"'

# ---------- CLEANUP ----------
cleanup:
    stage: cleanup
    image: alpine:latest
    script: 
        - echo "Cleanup temporary files"
    rules:
        - when: always
```
# Giải thích và hướng dẫn cấu hình nhanh
## 1. Cách pipeline tự động chạy đúng job
- Các job `rules`/`exists` sẽ chạy khi file tương ứng tồn tại trong repo (ví dụ `pom.xml` → chạy build Maven).
- Bạn có thể tắt/bật tính năng qua CI Variables trong Gitlab UI (Project → Settings → CI/CD → Variables).

## 2. Bắt buộc phải cấu hình CI/CD variables (ví dụ)
Trong **Settings > CI/CD > Variables** thêm:
- `CI_REGISTRY` - (nếu dùng GitLab registry thì có sẵn)
- `CI_REGISTRY_USER` - (thường $CI_REGISTRY_USER)
- `CI_REGISTRY_PASSWORD` - (token hoặc password)
- `ENABLE_DOCKER` - `true` / `false`
- `ENABLE_K8S` - `true` / `false`
- `KUBE_CONFIG`- base64 của kubeconfig (nếu deploy k8s)
- `SSH_PRIVATE_KEY` = private key (để ssh deploy)
- `SSH_USER`, `DEV_SERVER`, `STAGING_SERVER`, `PROD_SERVER`, `DEPLOY_PATH`
- `ENABLE_PAGES` = `true` nếu muốn Gitlab Pages
- `ENABLE_SEVERLESS`= `true` nếu dùng serverless (và set AWS/GCP creds) 
- `ROLLBACK_TAG` (dùng cho manual rollback)

Đặt `Protected` cho biến dùng trong `main`/`prod` nếu cần.

## 3. Runner
- Để build Docker images cần runner với Docker (executor `docker` + service `docker:dind`) hoặc `shell` runner có Docker