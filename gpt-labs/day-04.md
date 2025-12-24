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
### 05-deployment.yaml
```yml
apiVersion: apps/v1
kind: Deployment
metadata:
  name: nginx-deployment
  namespace: dev-team
spec:
  replicas: 3
  selector:
    matchLabels:
      app: nginx-deploy
  template:
    metadata:
      labels:
        app: nginx-deploy
    spec:
      containers:
      - name: nginx
        image: nginx
        ports:
        - containerPort: 80
        resources:
          requests:
            cpu: "60m"
            memory: "68Mi"
          limits:
            cpu: "120m"
            memory: "130Mi"
```
### 06-services.yaml
```yml
# 1. ClusterIP: Internal Use Only
apiVersion: v1
kind: Service
metadata:
  name: nginx-internal
  namespace: dev-team
spec:
  type: ClusterIP
  selector:
    app: nginx-deploy
  ports:
  - port: 80
    targetPort: 80
---
# 2. NodePort: Exposed on every Node's IP at a specific port (30000-32767)
apiVersion: v1
kind: Service
metadata:
  name: nginx-nodeport
  namespace: dev-team
spec:
  type: NodePort
  selector:
    app: nginx-deploy
  ports:
  - port: 80
    targetPort: 80
    nodePort: 32000
---
# 3. LoadBalancer: External IP (Status will stay <pending> if not on Cloud/MetalLB)
apiVersion: v1
kind: Service
metadata:
  name: nginx-external
  namespace: dev-team
spec:
  type: LoadBalancer
  selector:
    app: nginx-deploy
  ports:
  - port: 80
    targetPort: 80
```
```bash
kubectl apply -f 05-deployment.yaml
kubectl get rs -n dev-team
kubectl get pods -n dev-team -l app=nginx-deploy
kubectl apply -f 06-services.yaml
kubectl get svc -n dev-team
```
