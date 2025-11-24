**4 Khái niệm cực kỳ quan trọng**

# 1. StatefulSet - Dành cho ứng dụng có trạng thái (DB, Kafka, Redis, Zookeeper,...)
**Mục đích**
- Dùng cho ứng dụng cần **stateful (trạng thái)**: mỗi Pod có **dữ liệu riêng, DNS ổn định, không bị mất dữ liệu** khi restart.
- Ví dụ: PostgreSQL, MongoDB, Kafka, RabbitMQ, Elasticsearch, Zookeeper...

**Đặc điểm khác Deployment**
| Đặc điểm         | Deployment             | StatefulSet                               |
| ---------------- | ---------------------- | ----------------------------------------- |
| Tên Pod          | Ngẫu nhiên (`web-xxx`) | Cố định (`pg-0`, `pg-1`, `pg-2`)          |
| Volume           | Chia sẻ hoặc không có  | Mỗi Pod có PVC riêng biệt                 |
| DNS              | Không ổn định          | Ổn định (`pg-0.pg`, `pg-1.pg`, …)         |
| Startup/Shutdown | Không theo thứ tự      | Theo thứ tự tăng/giảm (`pg-0` chạy trước) |

**Cấu trúc StatefulSet**
Cần ít nhất **2 YAML:**
**1. Headless Service** → để cấp DNS cố định cho Pod
**2. StatefulSet** → để tạo các Pod có volume riêng biệt

`service.yaml`
```yaml
apiVersion: v1
kind: Service
metadata:
  name: postgres
spec:
  clusterIP: None # Headless service
  selector:
    app: postgres
  ports:
    - port: 5432
    name: postgres
```

`statefulset.yaml`
```yaml
apiVersion: apps/v1
kind: StatefulSet
metadata:
  name: postgres
spec:
  serviceName: "postgres" # phải trùng với service ở trên
  replicas: 3
  selector:
    matchLabels:
      app: postgres
  template:
    metadata:
      labels:
        app: postgres
    spec:
      containers:
        - name: postgres
        image: postgres:14
        ports:
          - containerPort: 5432
        env:
          - name: POSTGRES_PASSWORD
          value: mysecret
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
          storage: 5Gi
```

**Cách hoạt động**
1. Tạo service headless → mỗi Pod có DNS như:
```pgsql
postgres-0.postgres.default.svc.cluster.local
postgres-1.postgres.default.svc.cluster.local
```
2. StatefulSet tạo Pod **theo thứ tự** (`pg-0`, rồi `pg-1`, rồi `pg-2`)
3. Mỗi Pod có PVC riêng:
```pgsql
data-postgres-0
data-postgres-1
data-postgres-2
```
4. Khi xóa Pod, PVC **không bị xóa** → dữ liệu vẫn giữ.