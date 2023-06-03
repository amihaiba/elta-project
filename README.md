
# Kubernetes, Jenkins, .NET Core Project
Author: Amihai Ben-Arush

## Assignment description
להלן פרטי המשימה:  
1. נא ליצור תהליך יצירה ולהקים באמצעותו שירות Jenkins בתור Pod בk8s, ב NS בשם devops ולחבר אותו לGIT.  
   • אין משמעות לסוג של תהליך היצירה, של k8s ושל GIT.  
2. נא ליצור תהליך מסוג Jenkins pipeline המשתמש בJenkins שנוצר,
אשר מקמפל בNS אחד אפליקציה מסוג Web, מבוססת  .netCore
ולאחר מכן פורס ומעלה אותה בתור אתר Web בNS השני.
יש לשמור את התהליכים והנגיש אותם דרך Github.

## Design  
### Git Repository  
The project and documentation are hosted on this Github repo for you to view and evaluate.  

### Kubernetes Cluster
For the purpose of this assignment, I've used minikube to set up a Kubernetes cluster with two namespaces - `devops` where the pipeline is executed and `prod` where the web app is deployed.  

### CI/CD Pipeline
The CI/CD pipeline is managed by Jenkins and executed using agent pods inside the K8s cluster.  
Jenkins' configuration is stored in a persistent volume.  

### .NET Core sample application
dotnet is used to build a simple landing page template as my sample app.  
For the building and containerizing stage, Jenkins uses a multistage Dockerfile using Microsoft's .NET SDK and ASP.NET images.  

## Detailed set-up process  
A step by step documentation can be found in the [step-by-step.md](https://github.com/amihaiba/elta-project/blob/main/step-by-step.md) file

## Further improvements  
NodePort is sufficient for the scope of this project as it is running on just one node but should be using an NGINX controller and ingress for both namespaces to allow better scalability and reliability.  