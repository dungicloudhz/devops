# 1. Pod (yếu tố cơ bản nhất)
**Khi dùng**: test nhanh, debug, demo. Không dùng cho production trừ khi bạn cần Pod "độc lập".
```yaml
apiVersion: v1
kind: Pod
metadata:
    name: demo-pod
    labels:
        app: demo
spec:
    containers:
        - name: demo
        image: nginx:1.23
        ports:
            - containerPort: 80
        env:
            - name: GREETING
            value: "hello"
        resources:
            requests:
                cpu: "100m"
                memory: "128Mi"
            limits:
                cpu: "500m"
                memory: "256Mi"
        livenessProbe:
            httpGet:
                path: /
                port: 80
            initialDelaySeconds: 30
            periodSeconds: 10
    restartPolicy: Always
```
**Giải thích chính**:
- `apiVersion`, `kind` - Loại object.
- `metadata.name` - tên duy nhất trong namespace.
- `labels` - nhãn dùng để Service/Selector tìm Pod.
- `spec.containers[]` - mô tả container(s) trong Pod.
- `ports.containerPort` - cổng container lắng nghe.
- `env` - biến môi trường (có thể dùng `valueFrom` để lấy từ ConfigMap/Secret).
- `resources.request/limits` - yêu cầu & giới hạn tài nguyên. Requests dùng Scheduler; Litmits dùng runtime để ép.
- `livenessProbe`/ `readinessProbe` - probe để K8s biết container sống/ đã sẵn sàng. Quan trọng để tránh traffic tới Pod chưa sẵn sàng.
- `restartPolicy` - `Always`/ `OnFailure` / `Never` (Pod đơn lẻ).

**Mẹo**: Tránh dùng Pod trực tiếp cho app production - nếu Pod chết, không có controller tạo lại.

# 2. Deployment (thường dùng cho app stateless)
**Khi dùng**: production cho web/API - hỗ trợ rollout/rollback/scale.
```yaml
apiVersion: apps/v1
kind: Deployment
metadata:
    name: web-deployment
    labels: 
        app: web
spec:
    replicas: 3
    selector:
        matchLabels:
            app: web
    strategy:
        type: RollingUpdate
        rollingUpdate:
            maxSurge: 1
            maxUnavailable: 0
    template:
        metadata:
            labels:
                app: web
        spec:
            containers:
                - name: web
                image: nginx:1.23
                ports:
                    - containerPort: 80
                readinessProbe:
                    httpGet:
                        path: /
                        port: 80
                    initialDelaySeconds: 5
                    periodSeconds: 5
```
**Giải thích chính**:
- `spec.replicas` - số bản sao mong muốn.
- `spec.selector.matchLabels` - selector cho ReplicaSet → nhất quán với `template.metadata.labels`.
- `spec.template` - Pod template; Deployment tạo ReplicaSet dựa trên template.
- `strategy` - cách rollout (rollingUpdate mặc định)
    - `maxSurge` - số Pod mới có thể tạo hơn `replicas`.
    - `maxUnavailable` - số Pod cũ có thể ngừng trong update.
- `readinessProbe` - nếu false, Pod không nhận traffic từ Service.

**Gotcha**: `selector` phải khớp với labels trong template; nếu không, Deployment không quản lý Pod.

# 3. ReplicaSet
**Khi dùng**: hiếm khi viết trực tiếp (Deployment tự tạo). Nếu muốn đảm bảo số bản sao nhưng không cần rollout logic để có thể viết ReplicaSet.
```yaml
apiVersion: apps/v1
kind: ReplicaSet
metadata:
    name: demo-rs
spec:
    replicas: 2
    selector:
        matchLabels:
            app: demo
    template:
        metadata:
            labels:
                app: demo
        spec:
            containers:
                - name: demo
                image: nginx
```
**Giải thích**: giống Deployment nhưng không có chiến lượng rollout/rollback.

# 4. Service (3 loại phổ biến)
- **ClusterIP (mặc định)**: chỉ trong cluster.
- **NodePort**: mở 1 port trên mỗi node.
- **LoadBalancer**: yêu cầu cloud provider -> cấp public LB.
```yaml
apiVersion: v1
kind: Service
metadata: 
    name: web-svc
spec:
    type: ClusterIP
    selector:
        app: web
    ports:
        - port: 80 # port Service
        targetPort: 80 # port container
        protocol: TCP
```
**Giải thích**:
- `selector` - tìm Pod có label tương ứng.
- `port` - cổng ngoài mà Service expose.
- `targetPort` - cổng nội bộ container.
- `type` - xác định phạm vi truy cập.

**NodePort ví dụ**:
```yaml
spec:
    type: NodePort
    ports:
        - port: 80
        targetPort: 80
        nodePort: 30080 # port trên node (30000-32767)
```
**Mẹo**: Tránh dùng NodePort cho production trực tiếp - dùng Ingress hoặc cloud LoadBalancer.

# 5. Ingress (quản lý HTTP/HTTPS)
**Yêu cầu**: có Ingress Controller (nginx-ingress, Traefik, etx.).
```yaml
apiVersion: networking.k8s.io/v1
kind: Ingress
metadata: 
    name: web-ingress
    annotations:
        kubernetes.io/ingress.class: nginx
spec:
    rules:
        - host: example.local
        http:
            paths:
                - path: /
                pathType: Prefix
                backend:
                    service: web-svc
                    port:
                        number: 80
```
**Giải thích**:
- `rules.host` - domain lọc request.
- `paths` → `backend.service.name` trỏ tới Service.
- `annotations` - cấu hình controller-specific (SSL, rewrite, auth).

**Mẹo**: Để HTTPS, kết hợp Secret chứa TLS cert và `tls` block trong Ingress.

# 6. ConfigMap & Secret (cấu hình)
**ConfigMap (plain text)**:
```yaml
apiVersion: v1
kind: ConfigMap
metadata:
    name: app-config
data:
    APP_MODE: "production"
    PAGE_SIZE: "20"
```
**Secret (endcoded base64 hoặc stringData)**:
```yaml
apiVersion: v1
kind: Secret
metadata:
    name: db-secret
type: Opaque
stringData:
    DB_USER: admin
    DB_PASSWORD: mypassword
```
**Sử dụng trong Pod:**
- **Env từ ConfigMap/Secret**
```yaml
env:
  - name: DB_USER
    valueFrom:
      secretKeyRef:
        name: db-secret
        key: DB_USER
```
- **Mount như file** (thường cho config file):
```yaml
volumeMounts:
  - name: config
    mountPath: /app/config
volumes:
  - name: config
    configMap:
      name: app-config
```

**Gotcha**: Secret không phải hoàn toàn an toàn (mã hóa khi at-rest nếu etcd được cấu hình). Tránh put extremely sensitive info mà không mã hóa etcd.

# 7. PersistentVolume (PV) & PersistentVolumeClaim (PVC)
**PVC (yêu cầu storage)**:
```yaml
apiVersion: v1
kind: PersistentVolumeClain
metadata:
    name: data-pvc
spec:
    accessModes:
        - ReadWriteOnce
    resources:
        requests:
            storage: 5Gi
    storageClassName: standard
```
**Sử dụng trong Pod**:
```yaml
volumes:
    - name: data
    persistentVolumeClaim:
        claimName: data-pvc
volumeMounts:
    - name: data
    mountPath: /var/lib/data
```
**Giải thích:**
- `accessModes`:
    - `ReadWriteOnce` - 1 node có thể read/write.
    - `ReadOnlyMany` - nhiều node read.
    - `ReadWriteMany` - nhiều node read/write (cần storage hỗ trợ).
- `storageClassName` - chỉ rõ provisioner (dynamic prvisioning).
- PV do admin cung cấp hoặc tự động tạo qua StorageClass.

**Mẹo**: Với DB production dùng PVC + StatefulSet (Xem bên dưới).
# 8. StatefulSet (ứng dụng có trạng thái: DB, Kafka)