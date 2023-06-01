
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
For the purpose of this assignment, I've used minikube to set up a Kubernetes cluster.

Both Jenkins and production pods must be able to communicate with outside of the cluster, therefore I've set up a service and ingress for both with an NGINX controller.

The cluster has two namespaces - `devops` where the pipeline is executed and `prod` where the production pods run.

### CI/CD Pipeline
The CI/CD pipeline is managed by Jenkins and executed using an agent, both working inside the K8s cluster.

Jenkins' configuration should be persistent and therefore use a persistent volume within the cluster.

### .NET Core sample application
I've used dotnet to build a simple landing page template as my sample app.

For the building and containerizing stage, Jenkins uses a multistage Dockerfile using Microsoft's .NET SDK image as the builder and Microsoft's ASP.NET image for the delivered image.

## Set-up process
Using minikube, I've initiated a cluster with the 2 namespaces mentioned above.

To start 

## Installation

Use the package manager [pip](https://pip.pypa.io/en/stable/) to install foobar.

```bash
ls if [[ -z $1 ]]; then
pip install foobar
```

## Usage

```python
import foobar
```