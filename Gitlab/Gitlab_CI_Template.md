Dưới đây là **hướng dẫn chi tiêt - đầy đủ - có ví dụ thực tế** để bạn hiểu và dùng được **GitLab CI Templates** (CI/CD Template) trong Gitlab.

# 1. GitLab CI Template là gì?
Gitlab CI Template là **các file** `.yml` **dùng chung** mà Gitlab cung cấp sẵn hoặc bạn tự tạo để:
- Tái sử dụng job
- Chuẩn hóa pipeline
- Giảm trùng lặp cấu hình
- Tách CI/CD logic ra khỏi repo chính

Template thường được import bằng từ khóa:
```yaml
include:
    - template: ...
    - local: ...
    - project: ...
    - remote: ...
```
# 2. GitLab cung cấp các template mặc định
GitLab đã có sẵn hằng trăm mẫu CI/CD cho nhiều language:
| Loại     | Ví dụ                            |
| -------- | -------------------------------- |
| Build    | Node.js, Go, Python, Java, Maven |
| Deploy   | Kubernetes, Helm, SSH            |
| Security | SAST, DAST                       |
| Scan     | Secret scan, License scan        |

Bạn có thể xem tại:
**CI/CD** → **Editor** → **Choose a template**

# 3. Cách sử dụng Template có sẵn của GitLab
Ví dụ bạn muốn dùng template NodeJS:
```yaml
include:
    - template: Node.gitlab-ci.yml
```
Gitlab sẽ tự động thêm job:
- install
- test
- build

# 4. Các dạng include template
## 4.1 Include template từ Gitlab official
```yaml
include:
    - template: Docker.gitlab-ci.yml
```
Danh sách template:
**.gitlab/ci/templates/** của GitLab

## 4.2 Include template từ repo của bạn
```yaml
include:
    - local: .gitlab-ci/common.yaml
```

## 4.3 Include template từ project khác
```yaml
include:
    - project: my-group/devops
      file: /template/build-maven.yml
```

## 4.4 Include từ URL ngoài
```yaml
include:
    remote: https://example.com/ci/java-template.yml
```

# 5. Tạo CI Template riêng để dùng chung cho nhiều repo
**Ví dụ bạn có repo devops-templates lưu các file:**
```yaml
/templates/
  ├── maven.yml
  ├── node.yml
  ├── ssh-deploy.yml
  └── k8s-deploy.yml
```

**Ví dụ file ssh-deploy.yml:**
```yaml
.deploy_ssh_template:
  stage: deploy
  before_script:
    - chmod 600 $SSH_PRIVATE_KEY
  script:
    - ssh -o StrictHostKeyChecking=no $SERVER_USER@$SERVER_HOST "cd /app && ./deploy.sh"
```

**Trong repo khác bạn có:**
```yaml
include:
    - project: devops/templates
      file: /templates/ssh-deploy.yml

deploy_prod:
    extends: .deploy_ssh_template
    variables:
        SERVER_HOST: "10.0.0.10"
        SERVER_USER: "ubuntu"
```

# 6. Sử dụng `extends:` để tái sử dụng job template
Template định nghĩa job ẩn (bắt đầu bằng dấu chấm):
```yaml
.build_template:
    stage: build
    script:
        - npm install
        - npm run build
```
Repo khác dùng lại:
```yaml
build-job:
    extends: .build-template
```

# 7. Ví dụ full GitLab CI sử dụng Template
**repo A (devops/template-repo)**
File: `/templates/node-build.yml`
```yaml
.node_build_template:
    stage: build
    image: node:18
    script:
        - npm install --prefer-offline
        - npm run build
    artifacts:
        paths: [dist]
```

File: `/template/ssh-deploy.yml`
```yaml
.ssh_deploy_template:
    stage: deploy
    script:
        - scp -r dist/* $SERVER_USER@SERVER_IP:/var/www/app
        - ssh $SERVER_USER@SERVER_IP "systemctl restart app"
```

**repo B (project thực tế)**
`.gitlab-ci.yml`:
```yaml
include:
    - project: devops/template-repo
      file:
        - /template/node-build.yml
        - /template/ssh-deploy.yml

stages:
    - build
    - deploy

build:
    extends: .node_build_template

deploy_prod:
    extends: .ssh_deploy_template
    only:
        - main
```

# 8. GitLab Build-in Template bạn nên biết
| Template                                  | Ý nghĩa                |
| ----------------------------------------- | ---------------------- |
| `Security/SAST.gitlab-ci.yml`             | Quét security          |
| `Security/Secret-Detection.gitlab-ci.yml` | Phát hiện leak secrets |
| `Terraform/Base.gitlab-ci.yml`            | Template IaC           |
| `Docker.gitlab-ci.yml`                    | Build docker tự động   |

Ví dụ bật SAST:
```yaml
include:
  - template: Security/SAST.gitlab-ci.yml
```
File template KHÔNG chứa secret → bạn đưa vào GitLab CI/CD variable:
- `SERVER_IP`
- `SSH_PRIVATE_KEY`
- `REGISTRY_TOKEN`

# 10. Tổ chức template cho doanh nghiệp (Best Practices)
```yaml
root-group/
  devops-templates/
    templates/
      build/
        node.yml
        maven.yml
      deploy/
        ssh.yml
        docker.yml
        k8s.yml
      utilities/
        notify.yml
        cache.yml
```
Và mỗi project chỉ cần 5 dòng:
```yaml
include:
  - project: root-group/devops-templates
    file:
      - /templates/build/node.yml
      - /templates/deploy/ssh.yml
```