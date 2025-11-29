# DAY-1
### Q1: How many pods exist on the system? In the current(default) namespace.
```
kubectl get pods
```
### Q2: Create a new pod using the nginx image. (Image name: nginx)
```
kubectl run nginx-pod --image=nginx
pod/nginx-pod created

kubectl get pods | grep nginx
NAME            READY   STATUS    RESTARTS   AGE
nginx-pod       1/1     Running   0          16s
```
```
🔍 Difference
Command	Purpose	When to use
kubectl run	Quickly create a pod (imperative command)	                                                          Testing, simple pods
kubectl create	Create resources from files or specific types like deployment, configmap, etc.	                Production objects, structured YAML
```
### Q3: How many pods are created now?
### Note: We have created a few more pods. So please check again in the current(default) namespace.
```
k get pods -n default
NAME            READY   STATUS    RESTARTS   AGE
newpods-r5tdw   1/1     Running   0          12m
newpods-l5pwx   1/1     Running   0          12m
newpods-86mln   1/1     Running   0          12m
nginx-pod       1/1     Running   0          11m
```
### Q4: Which image is specified for the pods whose names begin with the newpods- prefix? You must look at one of the new pods in detail to figure this out.
```
kubectl describe pod newpods-nglk8 | grep image
kubectl get pods | grep ^newpods- | awk '{print $1}' | xargs -I {} kubectl get pod {} -o jsonpath='{.metadata.name} {.spec.containers[*].image}{"\n"}'
kubectl get pod newpods-r5tdw -o jsonpath='{.spec.containers[*].image}'
```
### Q5: Which nodes are these pods placed on? You must look at all the pods in detail to figure this out.
```
kubectl get pods -o wide | grep ^newpods- 
newpods-86mln   1/1     Running   1 (6m48s ago)   23m   10.42.0.10   controlplane   <none>           <none>
newpods-r5tdw   1/1     Running   1 (6m48s ago)   23m   10.42.0.9    controlplane   <none>           <none>
newpods-l5pwx   1/1     Running   1 (6m48s ago)   23m   10.42.0.11   controlplane   <none>           <none>
```
### Q6: We just created a new pod named webapp. How many containers are part of the webapp pod? Note: Ignore the state of the pod for now.
```
kubectl get pod webapp -o jsonpath='{.spec.containers[*].name}' | wc -w
```
### Q7: What images are used in the new webapp pod? You must look at all the pods in detail to figure this out.
```
kubectl get pod webapp -o jsonpath='{.spec.containers[*].image}'
```
### Q8: What is the state of the container agentx in the pod webapp? Wait for it to finish the ContainerCreating state
```
kubectl describe pod webapp | grep -A5 "agentx"
```
### Q9: Why do you think the container agentx in pod webapp is in error? Try to figure it out from the events section of the pod.




