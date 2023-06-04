# Step by step documentation
## Prerequisites
• Git  
• Docker  
• Minikube  
• Github repository set up
• Dockerhub repository set up
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
Give permission to `jenkins` user and create a service account for the jenkins deployment
```bash
kubectl apply -f kubernetes/jenkins-sa.yaml
```
Switch context to use the new jenkins context:
```bash
kubectl config use-context jenkins
```



#### Deploy Jenkins
Create service account if it wasn't created earlier:
```bash
kubectl apply -f kubernetes/jenkins-sa.yaml
```
Create persistent volume resources:
```bash
kubectl apply -f kubernetes/jenkins-vol.yaml
```
Create deployment and service
```bash
kubectl apply -f kubernetes/jenkins-depl.yaml
```

Deploy sample app to `prod` namespace:
```bash
kubectl apply -f kubernetes/eltamvc-depl.yaml
```

## Jenkins configuration  
#### Install the following plugins:  
• Kubernetes  
  
#### Add Github and Docker-hub credentials  
Add Github's username and access-key  
Add Dockerhub's username and access-key  

#### Create a pipeline  
Using Jenkins' pipeline tool  
The pipeline perform the following stages:  
Git checkout - pull source files from the Github repo.  
Build - builds the application using a multistage Dockerfile

## Application
#### Create an application template using dotnet:
```bash
dotnet new mvc -o eltaMVC --no-https
```
#### Check the app is working and website is available:
```bash
dotnet run --urls=http://localhost:5000
```
Enter the url and check the website is a available.  
#### Create and run app inside a container, and push to docker registry:
```bash
docker build -t amihaiba/eltamvc:0.1.0 .
docker run -ti -p 5000:5000 --name dotnet-test amihaiba/eltamvc:0.1.0
docker push amihaiba/eltamvc:0.1.0
```
