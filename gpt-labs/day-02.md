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
Mount into Pod (add to deployment.yaml):

```
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
```
4️⃣ Add Liveness & Readiness Probes
Edit Deployment:

```
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
```
Apply changes:
```
kubectl apply -f deployment.yaml
kubectl describe pod <pod-name> -n day2-lab
```
5️⃣ Expose with Service
```
kubectl expose deployment webapp \
  --type=NodePort \
  --port=80 \
  -n day2-lab
```
Check:
```
kubectl get svc -n day2-lab
```
Access app via <NodeIP>:<NodePort>.

6️⃣ Horizontal Pod Autoscaling (HPA)

Enable metrics-server first (if not installed).

Then:
```
kubectl autoscale deployment webapp \
  --cpu-percent=50 \
  --min=2 \
  --max=5 \
  -n day2-lab
```
Check HPA:
```
kubectl get hpa -n day2-lab
```



```
kubectl describe secret db-secret -n day2-lab
```



```
touch create_day2_project.sh
```

```
#!/bin/bash

# Define project root folder
PROJECT_DIR="day2-webapp"

# Create folder structure
mkdir -p $PROJECT_DIR

# Create empty yaml files
cat <<EOF > $PROJECT_DIR/namespace.yaml
# Namespace definition
apiVersion: v1
kind: Namespace
metadata:
  name: day2-prod
EOF

cat <<EOF > $PROJECT_DIR/configmap.yaml
# ConfigMap for app variables
apiVersion: v1
kind: ConfigMap
metadata:
  name: webapp-config
  namespace: day2-prod
data:
  APP_COLOR: "green"
  APP_MESSAGE: "Welcome to Student WebApp - Day2"
EOF

cat <<EOF > $PROJECT_DIR/secret.yaml
# Secret for DB credentials
apiVersion: v1
kind: Secret
metadata:
  name: db-secret
  namespace: day2-prod
type: Opaque
data:
  DB_USER: YWRtaW4=   # base64(admin)
  DB_PASS: UGFzc3cwcmQ=   # base64(Passw0rd)
EOF

cat <<EOF > $PROJECT_DIR/pvc.yaml
# Persistent Volume Claim
apiVersion: v1
kind: PersistentVolumeClaim
metadata:
  name: webapp-pvc
  namespace: day2-prod
spec:
  accessModes:
    - ReadWriteOnce
  resources:
    requests:
      storage: 1Gi
EOF

cat <<EOF > $PROJECT_DIR/webpage-configmap.yaml
# ConfigMap for custom index.html
apiVersion: v1
kind: ConfigMap
metadata:
  name: webapp-html
  namespace: day2-prod
data:
  index.html: |
    <!DOCTYPE html>
    <html>
    <head>
      <title>Day2 Student WebApp</title>
      <style>
        body {
          background-color: {{APP_COLOR}};
          font-family: Arial, sans-serif;
          color: white;
          text-align: center;
          margin-top: 20%;
        }
      </style>
    </head>
    <body>
      <h1>{{APP_MESSAGE}}</h1>
      <h3>Powered by Kubernetes Day2 Lab 🚀</h3>
    </body>
    </html>
EOF

cat <<EOF > $PROJECT_DIR/deployment.yaml
# Deployment with probes and init container
apiVersion: apps/v1
kind: Deployment
metadata:
  name: webapp
  namespace: day2-prod
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
        image: nginx:latest
        ports:
        - containerPort: 80
        volumeMounts:
        - name: html
          mountPath: /usr/share/nginx/html
        envFrom:
        - configMapRef:
            name: webapp-config
        - secretRef:
            name: db-secret
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

      initContainers:
      - name: init-webpage
        image: busybox
        command: ["/bin/sh", "-c"]
        args:
          - |
            cp /config/index.html /tmp/index.html && \\
            sed -i "s/{{APP_COLOR}}/\$APP_COLOR/g" /tmp/index.html && \\
            sed -i "s/{{APP_MESSAGE}}/\$APP_MESSAGE/g" /tmp/index.html && \\
            cp /tmp/index.html /usr/share/nginx/html/index.html
        envFrom:
        - configMapRef:
            name: webapp-config
        volumeMounts:
        - name: html
          mountPath: /usr/share/nginx/html
        - name: config-html
          mountPath: /config

      volumes:
      - name: html
        emptyDir: {}
      - name: config-html
        configMap:
          name: webapp-html
EOF

cat <<EOF > $PROJECT_DIR/service.yaml
# Service to expose app
apiVersion: v1
kind: Service
metadata:
  name: webapp-service
  namespace: day2-prod
spec:
  type: NodePort
  selector:
    app: webapp
  ports:
    - port: 80
      targetPort: 80
      nodePort: 30080
EOF

cat <<EOF > $PROJECT_DIR/hpa.yaml
# Horizontal Pod Autoscaler
apiVersion: autoscaling/v2
kind: HorizontalPodAutoscaler
metadata:
  name: webapp-hpa
  namespace: day2-prod
spec:
  scaleTargetRef:
    apiVersion: apps/v1
    kind: Deployment
    name: webapp
  minReplicas: 2
  maxReplicas: 5
  metrics:
  - type: Resource
    resource:
      name: cpu
      target:
        type: Utilization
        averageUtilization: 50
EOF

echo "✅ Project structure created in $PROJECT_DIR/"
```
```
chmod +x create_day2_project.sh
./create_day2_project.sh
```
touch deploy_all.sh
chmod +x deploy_all.sh
```
```
vi deploy_all.sh
```
```
#!/bin/bash

PROJECT_DIR="day2-webapp"

echo "🚀 Deploying Day2 WebApp to Kubernetes..."

# Apply namespace first
kubectl apply -f $PROJECT_DIR/namespace.yaml

# Apply core configs
kubectl apply -f $PROJECT_DIR/configmap.yaml
kubectl apply -f $PROJECT_DIR/secret.yaml

# Apply storage
kubectl apply -f $PROJECT_DIR/pvc.yaml

# Apply webpage configmap
kubectl apply -f $PROJECT_DIR/webpage-configmap.yaml

# Apply Deployment
kubectl apply -f $PROJECT_DIR/deployment.yaml

# Apply Service
kubectl apply -f $PROJECT_DIR/service.yaml

# Apply HPA
kubectl apply -f $PROJECT_DIR/hpa.yaml

echo "✅ Deployment complete!"
echo "👉 Run: kubectl get all -n day2-prod"
echo "👉 Access app at: http://<NodeIP>:30080"
```

```
./deploy_all.sh
```
clean
```
#!/bin/bash

NAMESPACE="day2-prod"

echo "🗑️ Cleaning up Day2 WebApp resources from Kubernetes..."

# Delete the whole namespace (removes everything inside)
kubectl delete namespace $NAMESPACE --ignore-not-found

# Optionally delete local folder if you want to clean project files too
# Uncomment if needed
# rm -rf day2-webapp

echo "✅ Cleanup complete!"
```
Usage

Save this as cleanup.sh in the same folder as your other scripts.

Make it executable:
```
chmod +x cleanup.sh
```

Run:
```
./cleanup.sh
```
1️⃣ Check All Resources

Run:

kubectl get all -n day2-prod


You should see:

Deployment (webapp)

Pods (webapp-xxxxx)

Service (webapp-service)

HPA (webapp-hpa)

2️⃣ Verify Pods are Running
kubectl get pods -n day2-prod


Expected output:

NAME                         READY   STATUS    RESTARTS   AGE
webapp-5d7d4f7b7b-xxxxx     1/1     Running   0          1m
webapp-5d7d4f7b7b-yyyyy     1/1     Running   0          1m


If STATUS ≠ Running, check logs:

kubectl logs <pod-name> -n day2-prod

3️⃣ Verify Service & Port
kubectl get svc -n day2-prod


You’ll see something like:

NAME             TYPE       CLUSTER-IP      EXTERNAL-IP   PORT(S)        AGE
webapp-service   NodePort   10.103.209.12   <none>        80:30080/TCP   1m


30080 = NodePort assigned

80 = container port

4️⃣ Access Application

👉 On bare metal / VM cluster (kind, kubeadm, minikube with NodePort):

Get your Node IP:

kubectl get nodes -o wide


Pick the Internal IP of a worker node.
Then open in browser:

http://<NodeIP>:30080


👉 On Cloud cluster (EKS/GKE/AKS) with type: LoadBalancer:

Run:

kubectl get svc webapp-service -n day2-prod


Check EXTERNAL-IP column. Use that in browser:

http://<EXTERNAL-IP>

5️⃣ Verify ConfigMap Rendering

When you access the app, the page background should be green and the heading should say:

Welcome to Student WebApp - Day2


(from ConfigMap values).

⚡ Would you like me to also give you a curl test command you can run from inside the cluster (via kubectl exec) to confirm the app works even before checking from browser?

You said:
curl test command
ChatGPT said:

Good call ⚡ — before even opening a browser, you can test the app with curl directly.

1️⃣ From Your Local Machine (if NodePort available)
curl http://<NodeIP>:30080


👉 Replace <NodeIP> with the Internal/External IP of your worker node:

kubectl get nodes -o wide

2️⃣ From Inside the Cluster (safe test)

Run a temporary pod:

kubectl run curl-test --rm -it --image=busybox --namespace=day2-prod -- /bin/sh


Inside the pod shell, run:

wget -qO- http://webapp-service


OR if you want to test via ClusterIP:

wget -qO- http://webapp-service.day2-prod.svc.cluster.local


You should see the HTML output with:

<h1>Welcome to Student WebApp - Day2</h1>

3️⃣ Test Load Balancer (if using cloud)

If your service is LoadBalancer type:

kubectl get svc webapp-service -n day2-prod


Copy the EXTERNAL-IP and run:

curl http://<EXTERNAL-IP>
