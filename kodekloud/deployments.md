### Q1: How many PODs exist on the system? In the current(default) namespace.
```
controlplane ~ ➜  k get pod
No resources found in default namespace.
```
### Q2: How many ReplicaSets exist on the system? In the current(default) namespace.
```
controlplane ~ ➜  k get rs -n default 
No resources found in default namespace.
```
### Q3: How many Deployments exist on the system? In the current(default) namespace.
```
kubectl get deployments
```
### Q4: How many Deployments exist on the system now? We just created a Deployment! Check again!
```
controlplane ~ ➜  kubectl get deployments
NAME                  READY   UP-TO-DATE   AVAILABLE   AGE
frontend-deployment   0/4     4            0           35s
```
### Q5: How many ReplicaSets exist on the system now?
```
controlplane ~ ➜  k get rs
NAME                             DESIRED   CURRENT   READY   AGE
frontend-deployment-7b9984b987   4         4         0       91s
```
### Q6: How many PODs exist on the system now?
```
controlplane ~ ➜  k get po
NAME                                   READY   STATUS             RESTARTS   AGE
frontend-deployment-7b9984b987-krrk4   0/1     ImagePullBackOff   0          2m14s
frontend-deployment-7b9984b987-4h47d   0/1     ImagePullBackOff   0          2m14s
frontend-deployment-7b9984b987-6h7lx   0/1     ImagePullBackOff   0          2m14s
frontend-deployment-7b9984b987-v6lmc   0/1     ImagePullBackOff   0          2m14s
```
### Q7: Out of all the existing PODs, how many are ready?
```
controlplane ~ ➜  k get po
NAME                                   READY   STATUS             RESTARTS   AGE
frontend-deployment-7b9984b987-krrk4   0/1     ImagePullBackOff   0          2m14s
frontend-deployment-7b9984b987-4h47d   0/1     ImagePullBackOff   0          2m14s
frontend-deployment-7b9984b987-6h7lx   0/1     ImagePullBackOff   0          2m14s
frontend-deployment-7b9984b987-v6lmc   0/1     ImagePullBackOff   0          2m14s
```
### Q8: What is the image used to create the pods in the new deployment?
```
kubectl describe  deployment frontend-deployment | grep Image
```
### Q9: Why do you think the deployment is not ready?
```
image not exist
```
### Q10: Create a new Deployment using the deployment-definition-1.yaml file located at /root/. There is an issue with the file, so try to fix it. 
```
apiVersion: apps/v1
kind: Deployment
metadata:
  name: deployment-1
spec:
  replicas: 2
  selector:
    matchLabels:
      name: busybox-pod
  template:
    metadata:
      labels:
        name: busybox-pod
    spec:
      containers:
      - name: busybox-container
        image: busybox
        command:
        - sh
        - "-c"
        - echo Hello Kubernetes! && sleep 3600
```
### Q11: Create a new Deployment with the below attributes using your own deployment definition file.
```
Name: httpd-frontend;
Replicas: 3;
Image: httpd:2.4-alpine 
```
```
apiVersion: apps/v1
kind: Deployment
metadata:
  name: httpd-frontend
spec:
  replicas: 3
  selector:
    matchLabels:
      app: httpd-frontend
  template:
    metadata:
      labels:
        app: httpd-frontend
    spec:
      containers:
      - name: httpd-container
        image: httpd:2.4-alpine
        ports:
        - containerPort: 80
```

```
kubectl apply -f deployment.yaml
kubectl get deployments
kubectl get pods -o wide
kubectl describe deployment httpd-frontend
```
