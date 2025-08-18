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

# configmap.yaml
apiVersion: v1
kind: ConfigMap
metadata:
  name: app-config
  namespace: day2-lab
data:
  APP_COLOR: "blue"
  APP_MESSAGE: "Hello from Day2 Lab"

kubectl apply -f configmap.yaml


Use it inside a Deployment:

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

kubectl apply -f deployment.yaml
kubectl get pods -n day2-lab

3️⃣ Create a Secret
kubectl create secret generic db-secret \
  --from-literal=DB_USER=admin \
  --from-literal=DB_PASS=Passw0rd \
  -n day2-lab


Verify:

kubectl get secrets -n day2-lab
kubectl describe secret db-secret -n day2-lab


Mount into Pod (add to deployment.yaml):

env:
- name: DB_USER
  valueFrom:
    secretKeyRef:
      name: db-secret
      key: DB_USER
- name: DB_PASS
  valueFrom:
    secretKeyRef:
      name: db-secret
      key: DB_PASS

4️⃣ Add Liveness & Readiness Probes

Edit Deployment:

livenessProbe:
  httpGet:
    path: /
    port: 80
  initialDelaySeconds: 10
  periodSeconds: 5

readinessProbe:
  httpGet:
    path: /
    port: 80
  initialDelaySeconds: 5
  periodSeconds: 5


Apply changes:

kubectl apply -f deployment.yaml
kubectl describe pod <pod-name> -n day2-lab

5️⃣ Expose with Service
kubectl expose deployment webapp \
  --type=NodePort \
  --port=80 \
  -n day2-lab


Check:

kubectl get svc -n day2-lab


Access app via <NodeIP>:<NodePort>.

6️⃣ Horizontal Pod Autoscaling (HPA)

Enable metrics-server first (if not installed).

Then:

kubectl autoscale deployment webapp \
  --cpu-percent=50 \
  --min=2 \
  --max=5 \
  -n day2-lab


Check HPA:

kubectl get hpa -n day2-lab


Generate load (optional with busybox):

kubectl run -i --tty load-generator --image=busybox --restart=Never -- /bin/sh
# Inside pod
while true; do wget -q -O- http://webapp.day2-lab.svc.cluster.local; done

7️⃣ Clean Up
kubectl delete namespace day2-lab


✅ By the end of Day 2, you’ll know:

Namespaces

ConfigMaps & Secrets

Liveness & Readiness Probes

Service exposure

Horizontal Pod Autoscaling (HPA)
