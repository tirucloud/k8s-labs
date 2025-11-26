### Q2: Create a new pod using the nginx image. (Image name: nginx)

```
controlplane ~ ➜  kubectl run nginx-pod --image=nginx
pod/nginx-pod created

controlplane ~ ➜  kubectl get pods | grep nginx
NAME            READY   STATUS    RESTARTS   AGE
nginx-pod       1/1     Running   0          16s

```
### Q3: How many pods are created now?
### Note: We have created a few more pods. So please check again in the current(default) namespace.
```
controlplane ~ ➜  k get pods -n default
NAME            READY   STATUS    RESTARTS   AGE
newpods-r5tdw   1/1     Running   0          12m
newpods-l5pwx   1/1     Running   0          12m
newpods-86mln   1/1     Running   0          12m
nginx-pod       1/1     Running   0          11m

```
### A: 4

### Q4: Which image is specified for the pods whose names begin with the newpods- prefix? You must look at one of the new pods in detail to figure this out.
```
kubectl get pods | grep ^newpods- | awk '{print $1}' | xargs -I {} kubectl get pod {} -o jsonpath='{.metadata.name} {.spec.containers[*].image}{"\n"}'
kubectl get pod newpods-r5tdw -o jsonpath='{.spec.containers[*].image}'
```

### Q5: Which nodes are these pods placed on? You must look at all the pods in detail to figure this out.
```
controlplane ~ ➜  kubectl get pods -o wide | grep ^newpods- 
newpods-86mln   1/1     Running   1 (6m48s ago)   23m   10.42.0.10   controlplane   <none>           <none>
newpods-r5tdw   1/1     Running   1 (6m48s ago)   23m   10.42.0.9    controlplane   <none>           <none>
newpods-l5pwx   1/1     Running   1 (6m48s ago)   23m   10.42.0.11   controlplane   <none>           <none>
```







