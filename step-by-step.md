# Step by step documentation
## Prerequisites
• Git  
• Docker  
• Minikube  
## Kubernetes configuration:
#### Start minikube
```bash
minikube start
minikube node add --worker
```
#### Add namespaces
```bash
kubectl create namespace devops
kubectl create namespace prod
```
#### Set `devops` as the default namespace
```bash
kubectl config set-context --currnet --namespace=devops
```
#### Create an authorized `jenkins` user in kubernetes
Generate a key:
```bash
openssl genrsa -out jenkins.key 2048
```
Generate certificate signing request:
```bash
openssl req -new -key jenkins.key -out jenkins.csr -subj "/CN=jenkins/O=devops/O=minikube.com"
```
Sign certificate by the minikube certificate authority:
```bash
openssl x509 -req -CA ~/.minikube/ca.crt -CAkey ~/.minikube/ca.key -CAcreateserial -days 730 -in jenkins.csr -out jenkins.crt
```
Add `jenkins` user with certificate:
```bash
kubectl config set-credentials jenkins --client-certificate=jenkins.crt --client-key=jenkins.key
kubectl config set-context jenkins --cluster=minikube --user=jenkins --namespace=devops
```
Give permissiion to `jenkins` user
```bash
kubectl apply -f kubernetes/jenkins-user.yaml
```
Switch context to use the new jenkins context:
```bash
kubectl config use-context jenkins jenkins
```

#### Start jenkins deployment
Create service account:
```bash
kubectl apply -f kubernetes/jenkins-sa.yaml
```
Create deployment, services, pv, pvc
```bash
kubectl apply -f kubernetes/jenkins-depl.yaml
```

## Jenkins configuration  
#### Install the following plugins:  
• Pipeline  
• Workspace Cleanup  
• Git  
• Kubernetes  
  
Add Github and Docker-hub credentials  
