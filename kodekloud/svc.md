### Q1: How many Services exist on the system?
```
k get svc
```
### Q2: That is a default service created by Kubernetes at launch.
```
ok
```
### Q3: What is the type of the default kubernetes service?
```
ClusterIP
```
### Q4: What is the targetPort configured on the kubernetes service?
```
controlplane ~ ✖ kubectl describe svc kubernetes 
Name:              kubernetes
Namespace:         default
Labels:            component=apiserver
                   provider=kubernetes
Annotations:       <none>
Selector:          <none>
Type:              ClusterIP
IP Family Policy:  SingleStack
IP Families:       IPv4
IP:                10.43.0.1
IPs:               10.43.0.1
Port:              https  443/TCP
TargetPort:        6443/TCP
Endpoints:         10.244.229.57:6443
Session Affinity:  None
Events:            <none>
```
### Q5: How many labels are configured on the kubernetes service?
```
controlplane ~ ➜  kubectl describe svc kubernetes
Name:              kubernetes
Namespace:         default
Labels:            component=apiserver
                   provider=kubernetes
Annotations:       <none>
Selector:          <none>
Type:              ClusterIP
IP Family Policy:  SingleStack
IP Families:       IPv4
IP:                10.43.0.1
IPs:               10.43.0.1
Port:              https  443/TCP
TargetPort:        6443/TCP
Endpoints:         10.244.229.57:6443
Session Affinity:  None
Events:            <none>
```
### Q6: How many Endpoints are attached on the kubernetes service?
```
kubectl describe svc kubernetes
```
### Q7: How many Deployments exist on the system now?. In the current(default) namespace
```
 k get deploy
```
### Q8: What is the image used to create the pods in the deployment?
```
k describe deploy simple-webapp-deployment | grep Image
Image:        kodekloud/simple-webapp:red
```
### Q9: Are you able to accesss the Web App UI? Try to access the Web Application UI using the tab simple-webapp-ui above the terminal.
```
no
```
### Q10: Create a new service to access the web application using the service-definition-1.yaml file.
```
Name: webapp-service
Type: NodePort
targetPort: 8080
port: 8080
nodePort: 30080
selector:
  name: simple-webapp
```
```
apiVersion: v1
kind: Service
metadata:
  name: webapp-service
spec:
  type: NodePort
  selector:
    name: simple-webapp
  ports:
    - port: 8080
      targetPort: 8080
      nodePort: 30080
```
### Q11: Access the web application using the tab simple-webapp-ui above the terminal window.
```
yes
```
