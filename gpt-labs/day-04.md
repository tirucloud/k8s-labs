# NAMESPACES
### 01-namespace.yaml
```yml
apiVersion: v1
kind: Namespace
metadata:
  name: dev-team
```
### 02-resource-control.yaml
```yml
apiVersion: v1
kind: ResourceQuota
metadata: 
  name: dev-team-quota
  namespace: dev-team
spec:
  hard:
    requests.cpu: "1"
    requests.memory: "1Gi"
    pods: "15"
---
apiVersion: v1
kind: LimitRange
metadata:
  name: dev-team-limit-range
  namespace: dev-team
spec:
  limits:
  - type: Pod
    min: { cpu: 60m, memory: 68Mi }
    max: { cpu: 120m, memory: 130Mi }
```
### 03-security.yaml
```yml
apiVersion: networking.k8s.io/v1
kind: NetworkPolicy
metadata:
  name: allow-internal-dev
  namespace: dev-team
spec:
  podSelector:
    matchLabels:
      app: nginx-deploy
  policyTypes: ["Ingress"]
  ingress:
  - from:
    - podSelector:
        matchLabels:
          role: frontend
---
apiVersion: rbac.authorization.k8s.io/v1
kind: Role
metadata:
  name: pod-log-reader
  namespace: dev-team
rules:
- apiGroups: [""]
  resources: ["pods", "pods/log"]
  verbs: ["get", "list"]
---
apiVersion: rbac.authorization.k8s.io/v1
kind: RoleBinding
metadata: 
  name: pod-log-reader-binding
  namespace: dev-team
subjects:
- kind: User
  name: abc@gmail.com
  apiGroup: rbac.authorization.k8s.io
roleRef:
  kind: Role 
  name: pod-log-reader
  apiGroup: rbac.authorization.k8s.io
  ```
### 04-pod.yaml
```yml
  apiVersion: v1
kind: Pod
metadata:
  name: nginx-pod
  namespace: dev-team
  labels:
    app: nginx-deploy
spec:
  containers:
  - name: nginx
    image: nginx
    resources:
      requests: { cpu: "60m", memory: "68Mi" }
      limits: { cpu: "120m", memory: "130Mi" }
```
### order of execution
```bash
# 1. Create Namespace
kubectl apply -f 01-namespace.yaml

# 2. Set Resource Boundaries
kubectl apply -f 02-resource-control.yaml

# 3. Apply Security Rules
kubectl apply -f 03-security.yaml

# 4. Deploy the App
kubectl apply -f 04-pod.yaml
```
### test-pod.yml
```yml
apiVersion: v1
kind: Pod
metadata:
  name: test-fixed
  namespace: dev-team
spec:
  containers:
  - name: nginx
    image: nginx
    resources:
      requests:
        cpu: "70m"      # Higher than 60m min
        memory: "80Mi"  # Higher than 68Mi min
      limits:
        cpu: "100m"     # Lower than 120m max
        memory: "100Mi" # Lower than 130Mi max
```
```bash
kubectl apply -f test-pod.yaml
```
