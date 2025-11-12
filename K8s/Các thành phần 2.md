# 1. Helm - package manager cho K8s
**Mục đích**
Helm giúp **đóng gói, tái sử dụng và version hóa cấu hình Kubernetes** dưới dạng chart. Chart chứa template YAML (với Go templating), values.yaml dùng để inject biến môi trường khác nhau.
**Cài & khái quát**
- Cài đặt trên máy dev: `brew install helm` hoặc download binary.
- Chart gồm thư mục:
```arduino
mychart/
  Chart.yaml        # metadata (name, version)
  values.yaml       # giá trị mặc định
  templates/
    deployment.yaml
    service.yaml
    _helpers.tpl
  charts/            # subcharts
```
**Ví dụ đơn giản**
`Chart.yaml`
```yaml
apiVersion: v2
name: myapp
version: 0.1.0
```
`values.yaml`
```yaml
replicaCount: 2
image:
    repository: myapp
    tag: "1.0.0"
service:
    type: ClusterIP
    port: 80
```
`templates/deployment.yaml` (trích)
```yaml
apiVersion: apps/v1
kind: Deployment
metadata:
  name: {{ include "myapp.fullname" . }}
spec:
  replicas: {{ .Values.replicaCount }}
  template:
    spec:
      containers:
        - name: {{ .Chart.Name }}
          image: "{{ .Values.image.repository }}:{{ .Values.image.tag }}"
```
**Lệnh thường dùng**
- Install: `helm install myrelease ./mychart -n prod`
- Upgrade: `helm upgrade myrelease ./mychart -f prod-values.yaml`
- Rollback: `helm rollback myrelease 1`
- Template render: `helm template ./mychart -f values.yaml`

**Mapping với K8s:**
Helm render templates → tạo YAML → gửi tơi API Server (tương tự `kubectl apply`) → nhưng Helm quản lý release/version, rollback, hooks.

**Best practices / lưu ý**
- Tách biến vào `values.yaml` cho mỗi environment(dev/staging/prod).
- Dùng `helm lint` kiểm tra.
- Tránh chứa secret plain text trong `values.yaml`; dùng Helm Secrets hoặc external secret manager.
- Sử dụng subcharts để tái sử dụng (DB, ingress).

# 2. Kustomize - custom YAML theo environment
**Mục đích**
Kustomize cho phép **overlay** (patch) YAML gốc mà không copy-paste, phù hợp để biến đổi config theo môi trường (thay image tag, replica, configMap...)

Cài & khái quát
- Kustomize tích hợp trong `kubectl` (>=1.14): `kubectl apply -k ./overlays/prod`
- Cấu trúc:
```csharp
base/
  deployment.yaml
  service.yaml
overlays/
  staging/
    kustomization.yaml
  prod/
    kustomization.yaml
```
**Ví dụ kustomization**
```yaml
resources:
  - ../../base
patchesStrategicMerge:
  - deployment-patch.yaml
images:
  - name: myapp
    newTag: "1.0.1"
configMapGenerator:
  - name: app-config
    literals:
      - MODE=production
```
`deployment-patch.yaml` (chỉ patch fields cần thay đổi)
```yaml
apiVersion: apps/v1
kind: Deployment
metadata:
  name: myapp-deployment
spec:
  replicas: 4
```
**Lưu ý**:
- Kustomize tốt để maintain 1 base + nhiều overlays; tránh duplicate YAML
- Kustomize không có templating logic mạnh như Helm (ít khả năng đọng), nhưng an toàn hơn cho cấu hình.

# 3. Horizontal Pod Autoscaler (HPA)
**Mục đích**
Tự động **thay đổi số** `replicas` của Deployment/ReplicaSet dựa trên metrics (CPU, memory, custom metrics).
**Yêu cầu**
- `metrics-server` hoặc Prometheus adapter để cung cấp metric cho HPA

**Ví dụ (CPU-based)**
```yaml
apiVersion: autoscaling/v2
kind: HorizontalPodAutoscaler
metadata:
  name: web-hpa
spec:
  scaleTargetRef:
    apiVersion: apps/v1
    kind: Deployment
    name: web-deployment
  minReplicas: 2
  maxReplicas: 10
  metrics:
    - type: Resource
      resource:
        name: cpu
        target:
          type: Utilization
          averageUtilization: 60
```
**Lệnh**
- Tạo: `kubectl apply -f hpa.yaml`
- Kiểm tra: `kubectl get hpa`

**Tips**
- Thiết lập `requests` cho container đẻ HPA tính toán chính xác (HPA dựa trên usage/requests).
- Dùng `v2` API nếu cần custom metrics hoặc multuple metrics.
- Watch out for rapid scale-up/scale-down → configure stabilizationWindow / behaviour in v2.

# 4. StatefulSet
**Mục đích**
Triển khai các app **cần identity cố định & storage riêng** cho mỗi replica (DB, Kafka, Zookeeper).
**Đặc điểm khác với Deployment**
- Pod có tên cố định (`name-0`, `name-1`).
- Mỗi Pod có PVC riêng (qua `volumeClaimTemplates`).
- Orderly deployment & termination (giảm thiểu mất dữ liệu).
**Ví dụ**
```yaml
apiVersion: apps/v1
kind: StatefulSet
metadata:
  name: pg
spec:
  serviceName: "pg"
  replicas: 3
  selector:
    matchLabels:
      app: pg
  template:
    metadata:
      labels:
        app: pg
    spec:
      containers:
        - name: postgres
        image: postgre:14
        ports: [{containerPort: 5432}]
        volumeMounts:
          - name: data
          mountPath: /var/lib/postgresql/data
  volumeClaimTemplates:
    - metadata:
      name: data
    spec:
      accessModes: ["ReadWriteOnce"]
      resources:
        requests:
          storage: 10Gi
```
**Lưu ý**
- Dùng headless Service (`spec.clusterIP: None`) để pod có DNS stable.
- Backup/restore PVC cần plan kỹ.
- Updates (rolling) mặc định theo ordinal, test trước khi update major versions.

# 5. DaemonSet
**Mục đích**
Đảm bảo **một pod chạy trên mỗi node** (hoặc subset theo nodeSelector) — dùng cho logging agent, monitoring node-exporter, CNI plugin.
**Ví dụ**
```yaml
apiVersion: apps/v1
kind: DaemonSet
metadata:
  name: node-exporter
spec:
  selector:
    matchLabels:
      app: node-exporter
  template:
    metadata:
      labels:
        app: node-exporter
    spec:
      containers:
        - name: node-exporter
          image: prom/node-exporter:latest
          ports:
            - containerPort: 9100
```
**Lưu ý**
- Có thể dùng `tolerations` để chạy trên tainted nodes.
- Tránh đặt workloads nặng trong DaemonSet (một node có thể có nhiều DaemonSet pods).
# PersistentVolume (PV) / PersistentVolumeClaim (PVC)
**Mục đích**
Cung cấp **storage bền vững** cho Pod; tách quyền quản trị storage (admin tạo PV) khỏi người dùng (PVC request).
**Flow**
1. Admin tạo PV (static) hoặc StorageClass cho dynamic provisioning.
2. Pod tạo PVC (claim).
3. PVC bind với PV phù hợp (or dynamically provisioned PV).
4. Pod mount PVC như volume.
**Ví dụ PVC**
```yaml
apiVersion: v1
kind: PersistentVolumeClaim
metadata:
  name: pg-data
spec:
  accessModes: ["ReadWriteOnce"]
  resources:
    requests:
      storage: 20Gi
  storageClassName: standard
```
**PV ví dụ (static)**
```yaml
apiVersion: v1
kind: PersistentVolume
metadata:
  name: pv0001
spec:
  capacity:
    storage: 20Gi
  accessModes: ["ReadWriteOnce"]
  hostPath:
    path: "/mnt/data"
```
**Lưu ý**
- Chọn `accessModes` phù hợp (`RWO`, `RWX` etc.) — nhiều storage provider không hỗ trợ `RWX`.
- StorageClass quan trọng cho cloud (gcp/aws/azure).
- Với DB production, PV + StatefulSet thường dùng.

# 7. RBAC (Role-Based Access Control)
**Mục đích**
Quyền kiểm soát ai (User/ServiceAccount) có thể làm gì (verbs) trên resources (pods, deployments, ...).
**Thành phần**
- `Role` (namespace-scoped) / `ClusterRole` (cluster-scoped)
- `RoleBinding` / `ClusterRoleBinding`
- `ServiceAccount` (dùng cho Pod)
**Ví dụ: cho phép một service account đọc pods**
`Role`
```yaml
apiVersion: rbac.authorization.k8s.io/v1
kind: Role
metadata:
  name: pod-reader
rules:
  - apiGroups: [""]
    resources: ["pods"]
    verbs: ["get","watch","list"]
```
`RoleBinding`
```yaml
apiVersion: rbac.authorization.k8s.io/v1
kind: RoleBinding
metadata:
  name: read-pods-binding
subjects:
  - kind: ServiceAccount
    name: my-sa
roleRef:
  kind: Role
  name: pod-reader
  apiGroup: rbac.authorization.k8s.io
```
**Lưu ý bảo mật**
- Principle of least privilege — chỉ cấp cần thiết.
- Audit logs + review thường xuyên.
- ServiceAccount tokens: rotate and limit scope.

# 8. Monitoring (Prometheus + Grafana)
**Mục đích**
Giám sát cluster và ứng dụng: metrics, alerting, dashboards.
**Thành phần cơ bản (Prometheus stack)**
- `Prometheus`: scrape metrics endpoints (cAdvisor, kube-state-metrics, node-exporter, app /metrics)
- `node-exporter`: metrics host-level
- `kube-state-metrics`: K8s state metrics (deployments, nodes, pods status)
- `Alertmanager`: quản lý alerts
- `Grafana`: dashboards & visualization

**Cài nhanh**
- Dùng Helm chart (kube-prometheus-stack):
```bash
helm repo add prometheus-community https://prometheus-community.github.io/helm-charts
helm repo update
helm install prometheus prometheus-community/kube-prometheus-stack -n monitoring --create-namespace
```
**App instrumentation**
- App expose /`metrics` (Prometheus format).
- Client libs: Java (micrometer/prometheus), Go (prometheus client), Python (prometheus_client).

**Best practices**
- Scrape target discovery: use ServiceMonitor CRDs (Prometheus Operator) để config scrape.
- Monitor both infra (node, kubelet) and app-level metrics (latency, error rate).
- Alerting rules for SLO breach, OOMKilled, pod restarts, node disk pressure.
- Use dashboards templates (Grafana community).

# 9. Service Mesh (Istio, Linkerd)
**Mục đích**
Cung cấp `observability`, `security`, `traffic control` giữa services: mTLS, retries, circuit breaking, traffic shifting (canary), metrics/tracing.
**Những gì mesh làm được**
- **mTLS** giữa services (mutual TLS).
- **Traffic control**: traffic splitting, A/B testing, canary.
- **Observability**: distributed tracing, metrics for service-to-service calls.
- **Security**: policy, authn/authz.

**So sánh ngắn**
- **Istio**: feature-rich, hơi nặng, nhiều capabilities (Envoy proxy), phù hợp enterprise.
- **Linkerd**: nhẹ hơn, dễ triển khai, focus trên simplicity và performance.

**Cài đặt cơ bản (concept)**
1. Install control plane (istioctl install / helm / operator).
2. Inject sidecar proxy vào namespace (automatic injection via label `istio-injection=enabled`).
3. Deploy services — mỗi Pod có sidecar proxy (Envoy).
4. Sử dụng CRDs (VirtualService, DestinationRule) để quản lý traffic.

**Ví dụ Istio (traffic split)**
`VirtualService`:
```yaml
apiVersion: networking.istio.io/v1beta1
kind: VirtualService
metadata:
  name: myapp
spec:
  hosts: ["myapp.example.com"]
  http:
    - route:
        - destination:
            host: myapp
            subset: v1
          weight: 90
        - destination:
            host: myapp
            subset: v2
          weight: 10
```
**Lưu ý / trade-offs**
- Overhead: sidecar consumes CPU/RAM.
- Complexity: learning curve, RBAC/policies thêm layer.
- Tracing/metrics: tích hợp với Prometheus/Grafana/Jaeger.