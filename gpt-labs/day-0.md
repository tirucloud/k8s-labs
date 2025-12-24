🔹 LAB 0: Verify Cluster Access

kubectl version --short
kubectl get nodes


Expected:

STATUS: Ready

🔹 LAB 1: Kubernetes Architecture (Observation)

kubectl get nodes -o wide
kubectl get pods -n kube-system


Understand

Control Plane components

Worker nodes

kube-system namespace

🎯 Interview Tip

kube-system contains core Kubernetes components.

🔹 LAB 2: Your First Pod

Create Pod (Imperative)

kubectl run nginx-pod --image=nginx --port=80


Check

kubectl get pods
kubectl describe pod nginx-pod


Delete

kubectl delete pod nginx-pod


📌 Learning

Pod is the smallest unit in Kubernetes

Pod = one or more containers

🔹 LAB 3: Pod Using YAML (Very Important)

Create pod.yaml

apiVersion: v1
kind: Pod
metadata:
  name: mypod
spec:
  containers:
    - name: nginx
      image: nginx
      ports:
        - containerPort: 80


Apply

kubectl apply -f pod.yaml
kubectl get pods


🔹 LAB 4: Deployment (MOST IMPORTANT)

Create Deployment

kubectl create deployment nginx-deploy --image=nginx


Check

kubectl get deployments
kubectl get pods


Scale

kubectl scale deployment nginx-deploy --replicas=3
kubectl get pods


📌 Learning

Deployment manages replicas

Enables self-healing

🔹 LAB 5: Self-Healing Demo (WOW LAB)

Delete a Pod

kubectl delete pod <one-pod-name>


Observe

kubectl get pods


🎯 Result

Pod is recreated automatically

🎯 Interview Line

Deployments provide self-healing by recreating failed pods.

🔹 LAB 6: Expose App Using Service

Create Service

kubectl expose deployment nginx-deploy \
  --type=NodePort \
  --port=80


Check

kubectl get svc


Access Application

http://<NODE-IP>:<NODE-PORT>


📌 Learning

Service provides stable networking

Pod IPs are ephemeral

🔹 LAB 7: Namespaces

kubectl get namespaces
kubectl create namespace dev


Deploy into Namespace

kubectl apply -f pod.yaml -n dev
kubectl get pods -n dev


🔹 LAB 8: Logs & Exec (Debugging)

Logs

kubectl logs <pod-name>


Exec

kubectl exec -it <pod-name> -- /bin/bash


🔹 LAB 9: YAML Understanding (Exam Critical)

kubectl explain pod
kubectl explain deployment.spec


Understand

apiVersion

kind

metadata

spec

🔹 LAB 10: Cleanup

kubectl delete deployment nginx-deploy
kubectl delete svc nginx-deploy
kubectl delete namespace dev


🧠 DAY-1 CONCEPT SUMMARY

Concept

Status

Pod

✅

Deployment

✅

Service

✅

Namespace

✅

Scaling

✅

Self-Healing

✅

YAML

✅

kubectl

✅

🎯 Day-1 Interview One-Liners

What is a Pod?
Smallest deployable unit in Kubernetes.

Why Deployment?
For scaling and self-healing.

Why Service?
To expose pods with stable networking.

Why YAML?
Declarative and version-controlled configuration.
