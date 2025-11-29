### Q1: How many PODs exist on the system? In the current(default) namespace.
```k get pods```
### Q2: How many ReplicaSets exist on the system?. In the current(default) namespace.
```kubectl get rs```
### Q3: How about now? How many ReplicaSets do you see?. We just made a few changes!
```kubectl get rs```
### Q4: How many PODs are DESIRED in the new-replica-set?
```
controlplane ~ ➜  k  get rs
NAME              DESIRED   CURRENT   READY   AGE
new-replica-set   4         4         0       23s
```
### Q5: What is the image used to create the pods in the new-replica-set?
```
kubectl describe rs new-replica-set | grep Image
```
### Q6: How many PODs are READY in the new-replica-set?
```
controlplane ~ ➜  k  get rs
NAME              DESIRED   CURRENT   READY   AGE
new-replica-set   4         4         0       23s
```
### Q7: Why do you think the PODs are not ready?

### Q10: Why are there still 4 PODs, even after you deleted one?
### Q11: Create a ReplicaSet using the replicaset-definition-1.yaml file located at /root. There is an issue with the file, so try to fix it.
```
apiVersion: apps/v1
kind: ReplicaSet
metadata:
  name: replicaset-1
spec:
  replicas: 3
  selector:
    matchLabels:
      tier: frontend
  template:
    metadata:
      labels:
        tier: frontend     # ✅ matches selector
    spec:
      containers:
      - name: nginx
        image: nginx
```
### Q12: Fix the issue in the replicaset-definition-2.yaml file and create a ReplicaSet using it. This file is located at /root/.
```
apiVersion: apps/v1
kind: ReplicaSet
metadata:
  name: replicaset-2
spec:
  replicas: 2
  selector:
    matchLabels:
      tier: frontend
  template:
    metadata:
      labels:
        tier: frontend
    spec:
      containers:
      - name: nginx
        image: nginx
```
### Q13: Delete the two newly created ReplicaSets - replicaset-1 and replicaset-2.
```
kubectl delete rs replicaset-1 replicaset-2

```
### Q14:Fix the original replica set new-replica-set to use the correct busybox image. Either delete and recreate the ReplicaSet or update the existing ReplicaSet and then delete all PODs, so new ones with the correct image will be created.
If you opt to delete the ReplicaSet and recreate it, please refer to the file named new-replica-set.yaml, which is saved in the /root/ directory for your convenience and fix it.
```
kubectl edit rs new-replica-set
edit Image: busybox
kubectl delete pod --all
```
### Q15: Scale the ReplicaSet to 5 PODs. Use kubectl scale command or edit the replicaset using kubectl edit replicaset.
```
kubectl scale rs new-replica-set --replicas=5
```
### Q16: Now scale the ReplicaSet down to 2 PODs. Use the kubectl scale command or edit the replicaset using kubectl edit replicaset.
```
kubectl scale rs new-replica-set --replicas=2
```



