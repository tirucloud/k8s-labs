# 🔹 DAY2
**Objective: Practice intermediate Kubernetes concepts: ConfigMaps, Secrets, Probes, Scaling, and Namespaces.**

1️⃣ Create a Namespace

Namespaces help organize resources.

```
kubectl create namespace day2-lab
```
namespace/day2-lab created
```
kubectl get ns
```
NAME              STATUS   AGE
day2-lab          Active   0s
default           Active   3m55s
kube-node-lease   Active   3m55s
kube-public       Active   3m55s
kube-system       Active   3m55s


2️⃣ Deploy an App with ConfigMap

Create a ConfigMap file:

```
# configmap.yaml
apiVersion: v1
kind: ConfigMap
metadata:
  name: app-config
  namespace: day2-lab
data:
  APP_COLOR: "blue"
  APP_MESSAGE: "Hello from Day2 Lab"
```
```
kubectl apply -f configmap.yaml
```
2. Use it inside a Deployment:
```
# deployment.yaml
apiVersion: apps/v1
kind: Deployment
metadata:
  name: webapp
  namespace: day2-lab
spec:
  replicas: 2
  selector:
    matchLabels:
      app: webapp
  template:
    metadata:
      labels:
        app: webapp
    spec:
      containers:
      - name: webapp
        image: nginx
        ports:
        - containerPort: 80
        envFrom:
        - configMapRef:
            name: app-config
```
```
kubectl apply -f deployment.yaml
kubectl get pods -n day2-lab
```
3️⃣ Create a Secret
```
kubectl create secret generic db-secret \
  --from-literal=DB_USER=admin \
  --from-literal=DB_PASS=Passw0rd \
  -n day2-lab

```
Verify:
```
kubectl get secrets -n day2-lab
kubectl describe secret db-secret -n day2-lab
```
