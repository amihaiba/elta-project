# Step by step documentation
## Prerequisites
• Git  
• Docker  
• Dotnet SDK
• Minikube  
• Github repository set up
• Dockerhub repository set up

## Application
#### Create an application template using dotnet:
```bash
dotnet new mvc -o eltaMVC --no-https
```
#### Check the app is working and website is available:
```bash
dotnet run --urls=http://localhost:5000
```
Enter the url and check that the website is a available.  
#### Create and run app inside a container, and push to docker registry:
```bash
docker build -t amihaiba/eltamvc:0.1.0 .
docker run -ti -p 5000:5000 --name dotnet-test amihaiba/eltamvc:0.1.0
```
Check if the app is available and working, if so, push to repo for further testing
```bash
docker push amihaiba/eltamvc:0.1.0
```

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
Give permission to `jenkins` user
```bash
kubectl apply -f kubernetes/jenkins-sa.yaml
```
Switch context to use the new jenkins context:
```bash
kubectl config use-context jenkins
```
#### Set `devops` as the default namespace
```bash
kubectl config set-context --currnet --namespace=devops
```

#### Deploy Jenkins
Install jenkins helm chart:
```bash
helm install jenkins jenkins/
```

## Jenkins configuration  
#### Make sure the following plugins are installed:  
• Pipeline
• Git
• Workspace cleanup
• Kubernetes  
  
#### Add Github and Docker-hub credentials  
Add Github's username and access-key  
Add Dockerhub's username and access-key  

#### Create a pipeline (Jenkinsfile)  
Using Jenkins' pipeline tool  
The pipeline perform the following stages:  
Git checkout - pull source files from the Github repo.  
Build - builds the application using a multistage Dockerfile  

In order for jenkins to perform tasks on agent-pods, I created 2 agent-pod yaml files:  
`build-agent.yaml` which uses a docker dind image  
`deploy-agent.yaml` which uses a kubectl + helm image  

