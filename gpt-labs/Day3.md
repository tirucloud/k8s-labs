# 🚀 Kubernetes Day 2 Practical Lab

## 🔹 Objective

Practice intermediate Kubernetes concepts: **ConfigMaps, Secrets,
Probes, Scaling, and Namespaces**.

------------------------------------------------------------------------

## 1️⃣ Namespace

``` yaml
apiVersion: v1
kind: Namespace
metadata:
  name: day2-prod
```

------------------------------------------------------------------------

## 2️⃣ ConfigMap

``` yaml
apiVersion: v1
kind: ConfigMap
metadata:
  name: webapp-config
  namespace: day2-prod
data:
  APP_COLOR: "green"
  APP_MESSAGE: "Welcome to Student WebApp - Day2"
```

------------------------------------------------------------------------

## 3️⃣ Secret

``` yaml
apiVersion: v1
kind: Secret
metadata:
  name: db-secret
  namespace: day2-prod
type: Opaque
data:
  DB_USER: YWRtaW4=   # base64(admin)
  DB_PASS: UGFzc3cwcmQ=   # base64(Passw0rd)
```

------------------------------------------------------------------------

## 4️⃣ Persistent Storage (PVC)

``` yaml
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
```

------------------------------------------------------------------------

## 5️⃣ Web Page ConfigMap

``` yaml
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
```

------------------------------------------------------------------------

## 6️⃣ Deployment

``` yaml
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
            cp /config/index.html /tmp/index.html && \
            sed -i "s/{{APP_COLOR}}/$APP_COLOR/g" /tmp/index.html && \
            sed -i "s/{{APP_MESSAGE}}/$APP_MESSAGE/g" /tmp/index.html && \
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
```

------------------------------------------------------------------------

## 7️⃣ Service

``` yaml
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
```

------------------------------------------------------------------------

## 8️⃣ HPA

``` yaml
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
```

------------------------------------------------------------------------

## 9️⃣ Deployment Scripts

### create_day2_project.sh

Creates project folder structure and manifests.

### deploy_all.sh

Applies all manifests in order.

### cleanup.sh

Deletes namespace and all resources.

------------------------------------------------------------------------

## 🔎 Verification

``` bash
kubectl get all -n day2-prod
kubectl get pods -n day2-prod
kubectl get svc -n day2-prod
```

Access via:

    http://<NodeIP>:30080

Or with curl:

``` bash
curl http://<NodeIP>:30080
```

Test inside cluster:

``` bash
kubectl run curl-test --rm -it --image=busybox --namespace=day2-prod -- /bin/sh
wget -qO- http://webapp-service
```

------------------------------------------------------------------------

✅ End of Day2 Lab Guide
