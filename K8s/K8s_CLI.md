**BẢNG TỔNG HỢP LỆNH KUBECTL**

# 1. KIỂM TRA THÔNG TIN
| Nhóm     | Lệnh                              | Ý nghĩa                |
| -------- | --------------------------------- | ---------------------- |
| Get      | `kubectl get pods`                | Danh sách Pod          |
|          | `kubectl get pods -A`             | Tất cả namespace       |
|          | `kubectl get deploy`              | Danh sách Deployment   |
|          | `kubectl get svc`                 | Danh sách Service      |
|          | `kubectl get ns`                  | Namespace              |
| Describe | `kubectl describe pod <name>`     | Xem chi tiết Pod       |
| Explain  | `kubectl explain deployment.spec` | Giải thích trường YAML |
| Logs     | `kubectl logs <pod>`              | Xem log                |
|          | `kubectl logs <pod> -f`           | Theo dõi log liên tục  |

# 2. TƯƠNG TÁC / DEBUG
| Lệnh                                          | Ý nghĩa               |
| --------------------------------------------- | --------------------- |
| `kubectl exec -it <pod> -- bash`              | Vào shell của Pod     |
| `kubectl exec -it <pod> -c <container> -- sh` | Exec container cụ thể |
| `kubectl port-forward <pod> 8080:80`          | Port forward          |
| `kubectl cp local.txt <pod>:/tmp`             | Copy file vào Pod     |
| `kubectl cp <pod>:/tmp/log.txt .`             | Copy từ Pod ra        |

# 3. TẠO / ÁP DỤNG / XÓA RESOURCE
| Lệnh                                            | Ý nghĩa                |
| ----------------------------------------------- | ---------------------- |
| `kubectl create deployment myapp --image=nginx` | Tạo Deployment         |
| `kubectl create ns dev`                         | Tạo namespace          |
| `kubectl apply -f file.yaml`                    | Apply YAML             |
| `kubectl apply -f ./k8s/`                       | Apply nhiều file       |
| `kubectl delete pod <name>`                     | Xóa pod                |
| `kubectl delete -f file.yaml`                   | Xóa resource bằng YAML |

# 4. SCALE – ROLLOUT – UPDATE
| Lệnh                                              | Ý nghĩa            |
| ------------------------------------------------- | ------------------ |
| `kubectl scale deploy myapp --replicas=3`         | Scale Deployment   |
| `kubectl rollout status deploy/myapp`             | Trạng thái rollout |
| `kubectl rollout history deploy/myapp`            | Lịch sử deploy     |
| `kubectl rollout undo deploy/myapp`               | Rollback           |
| `kubectl set image deploy/myapp nginx=nginx:1.21` | Update image       |

# 5. EDIT – PATCH
| Lệnh                                                      | Ý nghĩa        |
| --------------------------------------------------------- | -------------- |
| `kubectl edit deploy myapp`                               | Edit trực tiếp |
| `kubectl patch deploy myapp -p '{"spec":{"replicas":4}}'` | Patch nhanh    |

# 6. CONFIGMAP / SECRET
| Lệnh                                                                 | Ý nghĩa       |
| -------------------------------------------------------------------- | ------------- |
| `kubectl create configmap mycm --from-literal=key=value`             | Tạo ConfigMap |
| `kubectl create configmap mycm --from-file=config.json`              | CM từ file    |
| `kubectl get cm mycm -o yaml`                                        | Xem YAML      |
| `kubectl create secret generic mysecret --from-literal=password=123` | Tạo Secret    |
| `kubectl get secret mysecret -o yaml`                                | Xem secret    |

# 7. NAMESPACE
| Lệnh                                                       | Ý nghĩa                |
| ---------------------------------------------------------- | ---------------------- |
| `kubectl get ns`                                           | Danh sách namespace    |
| `kubectl create ns backend`                                | Tạo namespace          |
| `kubectl delete ns backend`                                | Xóa namespace          |
| `kubectl config set-context --current --namespace=backend` | Đặt namespace mặc định |

# 8. NODE & CLUSTER MANAGEMENT
| Lệnh                                               | Ý nghĩa                        |
| -------------------------------------------------- | ------------------------------ |
| `kubectl get nodes`                                | Danh sách node                 |
| `kubectl describe node <node>`                     | Thông tin node                 |
| `kubectl cordon <node>`                            | Khóa node (không nhận pod mới) |
| `kubectl drain <node> --ignore-daemonsets --force` | Di chuyển pod khỏi node        |
| `kubectl uncordon <node>`                          | Mở khóa node                   |

# 9. JOB / CRONJOB
| Lệnh                                                       | Ý nghĩa          |
| ---------------------------------------------------------- | ---------------- |
| `kubectl get job`                                          | Xem Job          |
| `kubectl get cronjob`                                      | Xem CronJob      |
| `kubectl describe cronjob <name>`                          | Chi tiết         |
| `kubectl delete job <name>`                                | Xóa Job          |
| `kubectl create job myjob --image=busybox -- echo "hello"` | Tạo Job đơn giản |

# 10. LABEL / ANNOTATION
| Lệnh                                      | Ý nghĩa        |
| ----------------------------------------- | -------------- |
| `kubectl label pod <pod> env=prod`        | Gắn label      |
| `kubectl get pod --show-labels`           | Xem label      |
| `kubectl annotate pod <pod> owner="danh"` | Gắn annotation |

# 11. CONTEXT / CONFIG
| Lệnh                                                   | Ý nghĩa           |
| ------------------------------------------------------ | ----------------- |
| `kubectl config view`                                  | Xem config        |
| `kubectl config get-contexts`                          | Danh sách context |
| `kubectl config use-context <context>`                 | Chuyển context    |
| `kubectl config set-context --current --namespace=dev` | Set NS mặc định   |

# 12. API RESOURCE
| Lệnh                    | Ý nghĩa                   |
| ----------------------- | ------------------------- |
| `kubectl api-resources` | Liệt kê resource types    |
| `kubectl api-versions`  | API version trong cluster |

# 13. TẠO YAML MẪU (generator)
| Lệnh                                                                      | Ý nghĩa              |
| ------------------------------------------------------------------------- | -------------------- |
| `kubectl create deploy myapp --image=nginx --dry-run=client -o yaml`      | Xuất YAML Deployment |
| `kubectl create svc clusterip mysvc --tcp=80:80 --dry-run=client -o yaml` | Xuất YAML Service    |

# 14. SHORTCUTS
| Resource    | Shortcut |
| ----------- | -------- |
| pod         | `po`     |
| deployment  | `deploy` |
| service     | `svc`    |
| namespace   | `ns`     |
| configmap   | `cm`     |
| secret      | `sec`    |
| daemonset   | `ds`     |
| statefulset | `sts`    |
| ingress     | `ing`    |

