### Q1: We have deployed a simple web application. Inspect the PODs and the Services. Wait for the application to fully deploy and view the application using the link called Webapp Portal above your terminal.
```
click on webapp portal
```
### Q2: What is the current color of the web application? Access the Webapp Portal.
```
blue
```
### Q3: Run the script named curl-test.sh to send multiple requests to test the web application. Take a note of the output. Execute the script at /root/curl-test.sh.

```
bash curl-test.sh
```
### Q4: Inspect the deployment in the default namespace and identify the number of PODs deployed by it
```
k get deploy -n default
```
### Q5: What container image is used to deploy the applications?
```
 k describe deploy frontend | grep Image
```
### Q6: Inspect the deployment and identify the current strategy
```
k describe deploy frontend | grep Strategy
```
### Q7: If you were to upgrade the application now what would happen?
```
pds are upgraded few at a time. Not all.
```
### Q8: Let us try that. Upgrade the application by setting the image on the deployment to kodekloud/webapp-color:v2. Do not delete and re-create the deployment. Only set the new image name for the existing deployment.
```
kubectl set image deployment/frontend simple-webapp=kodekloud/webapp-color:v2
kubectl rollout status deployment/frontend
kubectl describe deployment frontend | grep Image
```
### Q9: Run the script curl-test.sh again. Notice the requests now hit both the old and newer versions (If checked immediately). However none of them fail. Execute the script at /root/curl-test.sh.
```
bash curl-test.sh
```
### Q10: Up to how many PODs can be down for upgrade at a time. Consider the current strategy settings and number of PODs - 4
```
Given:

Replicas = 4

maxUnavailable = 25%

Update strategy = RollingUpdate

📌 Calculation

25% of 4 = 1

So up to 1 pod can be unavailable (down) during the rolling upgrade at any point.

✅ Answer

A maximum of 1 pod can be down at a time during the upgrade.
```
### Q11: Change the deployment strategy to Recreate. Delete and re-create the deployment if necessary. Only update the strategy type for the existing deployment.

```
Change the deployment strategy to Recreate

Delete and re-create the deployment if necessary. Only update the strategy type for the existing deployment.

Deployment Name: frontend

Deployment Image: kodekloud/webapp-color:v2

Strategy: Recreate

```
```
kubectl describe deployment frontend | grep Strategy
StrategyType:       Recreate
```
### Q12: Upgrade the application by setting the image on the deployment to kodekloud/webapp-color:v3. Do not delete and re-create the deployment. Only set the new image name for the existing deployment.
```
kubectl set image deployment/frontend simple-webapp=kodekloud/webapp-color:v3
kubectl rollout status deployment/frontend
```
### Q13: Run the script curl-test.sh again. Notice the failures. Wait for the new application to be ready. Notice that the requests now do not hit both the versions. Execute the script at /root/curl-test.sh.
```
bash curl-test.sh
```
