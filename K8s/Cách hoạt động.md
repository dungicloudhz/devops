# I. Cách K8s hoạt động (logic chung)
Khi bạn chạy:
```bash
kubectl apply -f deployment.yaml
```
K8s sẽ đi qua 4 tầng logic:
1. **kubectl → API Server**. `File YAML được gửi đến **API Server**, nơi kiểm tra cú pháp, quyền hạn (RBAC), rồi ghi thông tin vào **etcd**`.
2. **API Server → Controller Manager**. `Controller Manager thấy trong etcd có "một Deployment mới", nhưng chưa có Pod nào tương ứng → nó ra lệnh cho Scheduler tạo Pod`.
3. **Schedule → Node (qua kubelet)**. `Scheduler chọn Node phù hợp (dựa CPU, RAM, label, taint, affinity...) và gửi yêu cầu đến **kubelet** trên Node đó`.
4. **Kubelet → Container Runtime (Docker/containerd)**. `Kubelet gọi runtime để pull image, tạo container, gắn volume, cấu hình network (thông qua kube-proxy), rồi báo lại trạng thái API Server`.

___
# II. Các thành phần cơ bản và cách chúng map với nhau
## 1. Pod - Đơn vị nhỏ nhất trong K8s
- **Chức năng**:
Pod chứa **1 hoặc nhiều container** (cùng chia sẻ network, volume).
- **Cấu hình YAML**:
```yaml
apiVersion: v1
kind: Pod
metadata:
    name: myapp-pod
    labels:
        app: myapp
spec:
    containers:
      - name: myapp
        image: myapp:1.0
        ports:
            - containerPort: 8080
```
- **Mapping logic**:
    - `metadata.labels` → dùng để **Service hoặc ReplicaSet** tìm đúng Pod (qua `selector.matchLabels`)
    - `spec.containers` → mô tả container (image, port, volumne,...)

## 2. ReplicaSet - Giữ đúng số lượng Pod
- **Chức năng**:
Đảm bảo **luôn có n bản sao Pod** đang chạy.
Nếu Pod bị crash hoặc delete → ReplicaSet tạo mới Pod khác.
- **Cấu hình YAML**:
```yaml
apiVersion: apps/v1
kind: ReplicaSet
metadata:
    name: myapp-rs
spec:
    replicas: 3
    selector:
        matchLabels:
            app: myapp
    template:  # Pod template
        metadata:
            labels:
                app: myapp
        spec:
            containers:
                - name: myapp
                image: myapp:1.0
                ports:
                    - containerPort: 8080
```
- **Mapping logic**:
    - `selector.matchLabels` ↔ `template.metadata.labels`
    → ReplicaSet "biết Pod nào là của mình" để quản lý.

## 3. Deployment - Quản lý vòng đời ReplicaSet
- **Chức năng**:
    - Quản lý **ReplicaSet** (và gián tiếp là Pod)
    - Hỗ trợ **rolling update, rollback, scale**
- **Cấu hình YAML**:
```yaml
apiVersion: apps/v1
kind: Deployment
metadata: 
    name: myapp-deployment
spec:
    replicas: 3
    selector:
        matchLabels:
            app: myapp
    template:
        metadata:
            labels:
                app: myapp
        spec:
            containers:
                - name: myapp
                image: myapp:1.0
```
- **Mapping logic**:
    - Deployment tạo và quản lý **ReplicaSet**
    - ReplicaSet tạo và quản lý **Pod**
    - Khi bạn `kubectl apply` thay ddoori `image: myapp:1.1`, Deployment tạo **ReplicaSet mới** → scale lên Pod mới → scale xuống Pod cũ → **zero-downtime update**

## 4. Service - Cổng truy cập cố định cho Pod
- **Chức năng**:
Cung cấp **địa chỉ IP ổn định, load balancing** cho nhóm Pod.
- **Cấu hình YAML**:
```yaml
apiVersion: v1
kind: Service
metadata:
    name: myapp-service
spec:
    selector:
        app: myapp # mapping đến label trong Pod
    ports:
        - protocol: TCP
        port: 80
        targetPort: 8080
    type: ClusterIP # hoặc NodePort, LoadBalancer
```
- **Mapping logic**:
    - `selector.app: myapp` → kết nối đến tất cả Pod có label `app=myapp`
    - `port` là cổng mà Service lắng nghe
    - `targetPort` là cổng trong container
        → Service = Load Balancer nội bộ cho Pod.

# 5. Ingress- Cổng HTTP/HTTPS cho bên ngoài truy cập
- **Chức năng**:
Điều hướng request từ bền ngoài (qua domain/path) → đến Service tương ứng.
- **Cấu hình YAML**:
```yaml
apiVersion: networking.k8s.io/v1
kind: Ingress
metadata:
    name: myapp-ingress
spec:
    rules:
        - host: myapp.example.com
        http:
            paths:
                - path: /
                pathType: Prefix
                backend: 
                    service:
                        name: myapp-service
                        port:
                            number: 80
```
- **Mapping logic**:
    - `backend.service.name` → trỏ đến **Service**
    - `Service` → trỏ đến **Pod** theo label
        => Chuỗi: **Ingress** → **Service** → **Pod**

# 6. ConfigMap & Secret - Cấu hình ứng dụng
- **ConfigMap (dữ liệu không nhạy cảm)**
```yaml
apiVersion: v1
kind: ConfigMap
metadata: 
    name: app-config
data:
    APP_MODE: "production"
    LOG_LEVEL: "debug"
```
- **Secret (dữ liệu nhạy cảm)**
```yaml
apiVersion: v1
kind: Secret
metadata:
    name: db-secret
type: Opaque
data:
    DB_USER: YWRtaW4=        # base64("admin")
    DB_PASS: cGFzc3dvcmQ=    # base64("password") 
```
- **Mapping vào Pod**:
```yaml
spec:
    containers:
        - name: myapp
        image: myapp:1.0
        env:
            - name: APP_MODE
            valueForm:
                configMapKeyRef:
                    name: app-config
                    key: APP_MODE
            - name: DB_USER
            valueFrom:
                secretKeyRef:
                    name: db-secret
                    key: DB_USER
```

# 7. Volume / PersistentVolumeClaim (PVC) - Lưu trữ dữu liệu 
- **PVC YAML**:
```yaml
apiVersion: v1
kind: PersistentVolumeClaim
metadata:
    name: myapp-pvc
spec:
    accessModes:
        - ReadWriteOnce
    resources:
        requests:
            storage: 1Gi
```
- **Mapping vào Pod**:
```yaml
spec:
    containers:
        - name: myapp
        image: myapp:1.0
        volumeMounts:
            - mountPath: "/data"
            name: app-storage
    volumes:
        - name: app-storage
        persistentVolumeClaim:
            claimName: myapp-pvc
```
→ Khi Pod bị xóa, dữ liệu vẫn còn trong PVC.

# 🔄 TỔNG KẾT QUAN HỆ GIỮA CÁC THÀNH PHẦN
```mathematica
Ingress 
   ↓
Service  ←→  Pod (có label "app=myapp")
                ↑
            ReplicaSet
                ↑
            Deployment
                ↑
          ConfigMap / Secret / Volume
```
# 🧠 MẸO HỌC NHỚ LOGIC
| Logic                         | Câu gợi nhớ                                                      |
| ----------------------------- | ---------------------------------------------------------------- |
| Deployment → ReplicaSet → Pod | “Deployment sinh con là ReplicaSet, ReplicaSet sinh cháu là Pod” |
| Service ↔ Pod                 | “Service là load balancer nội bộ cho Pod”                        |
| Ingress ↔ Service             | “Ingress là cổng công khai bên ngoài cho Service”                |
| ConfigMap / Secret            | “Não của Pod (nơi chứa biến môi trường)”                         |
| PVC                           | “Ổ cứng của Pod”                                                 |
