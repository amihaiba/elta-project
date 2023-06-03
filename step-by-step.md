# Kubernetes configuration:
#### Start minikube  
`minikube start`  
#### Add namespaces  
`kubectl create namespace devops`  
`kubectl create namespace prod`  
  
#### Set `devops` as the default namespace  
`kubectl config set-context --currnet --namespace=devops`  
  
#### Create an authorized `jenkins` user in kubernetes  
Generate a key:  
`openssl genrsa -out jenkins.key 2048`  
Generate certificate signing request:  
`openssl req -new -key jenkins.key -out jenkins.csr -subj "/CN=jenkins/O=devops/O=minikube.com"`  
Sign certificate by the minikube certificate authority:  
`openssl x509 -req -CA ~/.minikube/ca.crt -CAkey ~/.minikube/ca.key -CAcreateserial -days 730 -in jenkins.csr -out jenkins.crt`
Add `jenkins` user with certificate:  
`kubectl config set-credentials jenkins --client-certificate=jenkins.crt --client-key=jenkins.key`  
`kubectl config set-context jenkins --cluster=minikube --user=jenkins --namespace=devops`  
Give permissiion to `jenkins` user  
`kubectl apply -f kubernetes/jenkins-user.yaml`  
Switch context to use the new jenkins context:  
`kubectl config use-context jenkins jenkins`  

#### Start jenkins deployment  
Create service account:  
`kubectl apply -f kubernetes/jenkins-sa.yaml`  
Create deployment, services, pv, pvc  
`kubectl apply -f kubernetes/jenkins-depl.yaml`  

#### Configure Jenkins  
Install the following plugins:  
    • Pipeline  
    • Workspace Cleanup  
    • Git  
    • Kubernetes  
  
Add Github and Docker-hub credentials  
