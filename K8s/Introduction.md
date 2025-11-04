# I. Kubernetes là gì?
**Kubernetes (K8s) là một hệ thống mã nguồn mở** giúp **tự động hóa việc triển khai (deploy), mở rộng (scale), và quản lý** các ứng dụng trong **container** (thường là Docker).
Nói ngắn gọn:
> Docker giúp đóng gói và chạy 1 container, còn Kubernetes giúp quản lý hàng trăm container đang chạy trên nhiều máy chủ (cluster).

# II. Kiến thức tổng thể của Kubernetes
Kubernetes được chia làm 2 phần chính:
## 1. Control Plane (Máy chủ quản lý)
Chịu trách nhiệm điều phối toàn hệ thống.
- **API Server** - Cổng giao tiếp trung tâm của K8s (nơi `kubectl` hoặc ứng dụng gọi API).
- **Scheduler** - Quyết định Pod nào sẽ chạy ở Node nào.
- **Controller Manager** - Theo dõi trạng thái cluster và thực hiện hành động (ví dụ: tự tạo lại pod bị lỗi).
- **etcd** - Database dạng key-value lưu trữ cấu hình và trạng thái của toàn cluster.

## 2. Worker Nodes (Máy chủ thực thi)
Là nơi **container thật sự chạy**.
- **kubectl** - Agent chạy trên mỗi node, chịu trách nhiệm khởi chạy container.
- **kube-proxy** - Quản lý networking, load balancing giữa các Pod.
- **Container Runtime** - Thường là Docker, containerds, hoặc CRI-O.

# III. Các khái niệm cơ bản trong Kubernetes
| Thành phần             | Mô tả ngắn gọn                                             | Vai trò                        |
| ---------------------- | ---------------------------------------------------------- | ------------------------------ |
|**Pod**|Nhỏ nhất trong K8s - chứa 1 hoặc nhiều container cùng chạy|Đơn vị triển khai cơ bản|
|**Deployment**|Mô tả cách tạo, cập nhật, và scale Pod|Đảm bảo số lượng Pod mogn muốn|
|**Service**|Tạo địa chỉ mạng ổn định để truy cập Pod|Load balancing & networking|
|**ReplicaSet**|Quản lý số lượng bản sao của Pod|Tự tạo thêm/bớt Pod|
|**Namespace**|Không gian tên, dùng để tách môi trường|Giúp tổ chức project|
|**ConfigMap / Secret**|Lưu cấu hình và thông tin nhạy cảm|Tách config khỏi code|
|**Ingress**|Quản lý truy cập HTTP/HTTPS từ bên ngoài|Giống như reserve proxy|
|**Volumne**|Gán ổ đĩa, lưu dữ liệu cho Pod|Giữ data khi Pod bị xóa|


# IV. Quy trình làm việc cơ bản với Kubernetes
## 1. Viết file YAML mô tả resource (Deployment, Service, ...)
Ví dụ: `nginx-deployment.yaml`
```yaml
apiVersion: apps/v1
kind: Deployment
metadata:
    name: nginx-deployment
spec:
    replicas: 2
    selector:
        matchLabels:
            app: nginx
    template:
        metadata:
            labels:
                app: nginx
        spec:
            containers:
                - name: nginx
                  image: nginx:latest
                  ports:
                    - containerPort: 80
```
## 2. Triển khai lên cluster
```bash
kubectl apply -f nginx-deployment.yaml
```
## 3. Kiểm tra Pod:
```bash
kubectl get pods
```
## 4. Expose Service để truy cập từ ngoài:
```bash
kubectl expose deployment nginx-deployment --type=LoadBalancer --port=80
```

# V. Cách học và thực hành K8s hiệu quả
**Bước 1: Cài môi trường học**
- **Cách 1**: Cài **Minikube** (một cluster 1 node trên máy local)
- **Cách 2**: Dùng **Docker Desktop** (tích hợp sẵn Kubernetes)
- **Cách 3**: Dùng cloud như GKE (Google), EKS (AWS), AKS (Azure)

**Bước 2: Làm quan với `kubectl`**
Một số lệnh cơ bản:
```bash
kubectl get pods
kubectl get services
kubectl describe pod <tên>
kubectl logs <tên-pod>
kubectl delete pod <tên>
```

**Bước 3: Học quản lý cấu hình YAML**
Tập viết file Deployment, Service, ConfigMap, Secret, v.v.

**Bước 4: Thực hành thực tế**
Ví dụ: triển khai ứng dụng **Spring Boot** + **PostgreSQL** lên K8s:
- 1 Deployment cho app
- 1 Deployment cho database
- 2 Service nội bộ + 1 Ingress public

# VI. Các chủ để nâng cao (khi bạn vũng cơ bản)
| Chủ đề                               | Ý nghĩa                                                        |
| ------------------------------------ | -------------------------------------------------------------- |
| **Helm**                             | “Package manager” của K8s – giúp quản lý chart (template YAML) |
| **Kustomize**                        | Cách custom YAML theo môi trường (dev/staging/prod)            |
| **Horizontal Pod Autoscaler (HPA)**  | Tự scale pod khi CPU/RAM cao                                   |
| **StatefulSet**                      | Dùng cho app có trạng thái (VD: database)                      |
| **DaemonSet**                        | Triển khai Pod trên mọi Node                                   |
| **Persistent Volume / PVC**          | Lưu trữ dữ liệu bền vững                                       |
| **RBAC**                             | Phân quyền truy cập                                            |
| **Monitoring (Prometheus, Grafana)** | Giám sát cluster                                               |
| **Service Mesh (Istio, Linkerd)**    | Quản lý giao tiếp giữa service                                 |

# VII. Lộ trình học gợi ý (4 tuần)
| Tuần | Chủ đề                               | Mục tiêu                                   |
| ---- | ------------------------------------ | ------------------------------------------ |
| 1    | Docker, Container, kiến trúc K8s     | Hiểu cơ chế chạy container & K8s tổng quan |
| 2    | Pod, Deployment, Service, YAML       | Deploy app đầu tiên lên K8s                |
| 3    | ConfigMap, Secret, Volume, Namespace | Làm app thực tế với database               |
| 4    | Helm, HPA, Monitoring, Ingress       | Triển khai app hoàn chỉnh + scale tự động  |

