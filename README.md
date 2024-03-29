# Learner Management System

Welcome to our learner management system repository! This collaborative effort by our team aims to deploy a learner management system to the AWS cloud. This repository houses code, documentation, and guides for a smooth deployment process. By harnessing AWS capabilities, our aim was to create a highly efficient and scalable system.
<br> <br>

View all the tools and languages that were used at the bottom of the page!

<br>

## Table of Contents

1. [Introduction](#introduction)
2. [Prerequisites](#prerequisites)
3. [Getting Started](#getting-started)
    - [Clone the Repository](#clone-the-repository)
    - [Setting up AWS](#setting-up-aws)
4. [Usage](#usage)
    - [Application Setup](#application-setup)
    - [Accessing the Learner UI](#accessing-the-learner-ui)
5. [System Infrastructure](#system-infrastructure)
6. [Languages and Tools](#languages-and-tools)
7. [Contributing](#contributing)
9. [License](#license)

## Introduction

Our learner management system allows users to access a Northcoders UI, sign up as a new user, or sign in as a pre-existing user. This README provides comprehensive guidance on setting up and running the system.

The following image summarises the infrastructure, environment and CI/CD pipeline:
<img width="6688" alt="project (6)" src="https://github.com/1point21/nc-ce-final-project-env/assets/137282472/b2be9e3e-8d23-448c-8d14-1fe9dfd5983b">


## Prerequisites

Before you start, ensure you have the following prerequisites:

- [AWS Account](https://aws.amazon.com/)
- [Node.js](https://nodejs.org/) installed
- [Java](https://www.java.com/) installed
- [Docker](https://www.docker.com/) installed
- [kubectl](https://kubernetes.io/docs/tasks/tools/install-kubectl/) installed

## Getting Started

### Clone the Repository

```bash
git clone https://github.com/your-username/learner-management-system.git
cd learner-management-system
```

### Setting up AWS

Ensure you have an AWS account and configure your credentials.

```bash
aws configure
```

## Aplication Setup:
In order to create and deploy the infrastructure, simply follow the instructions below and run the provided commands:
<br> <br>

Firstly:
<br>
Install ArgoCd and complete the required setup, here is how:
<br> <br>

We will first need to create a namespace for argo.
<br>
Run the following command:
<br>

```bash
kubectl create namespace argocd
```
<br> <br>

After creating the namespace, you can install ArgoCD with the following command:
<br>

```bash
kubectl apply -n argocd -f https://raw.githubusercontent.com/argoproj/argo-cd/stable/manifests/install.yaml
```
<br> <br>

Congratulations, you have successfully installed ArgoCD, Now access the interface with the following two commands:
<br>

```bash
kubectl port-forward svc/argocd-server -n argocd 8083:443
```
<br>

```bash
kubectl -n argocd get secret argocd-initial-admin-secret -o jsonpath="{.data.password}" | base64 -d
```

<br> <br>
The first command will port-forward the application to your localhost, this gives you access to the Argo UI via the internet
<br> <br>
The second command will generate you a password, copy this password as you will need to use it later on.
<br><br>

Access the ArgoCD Interface throught the following link:
<br>
http://localhost:8083
<br><br>

Once you use the link above to access argo, you may run into an error message saying:
<br>
'Your connection is not private'
<br><br>
To access Argo, simply select the advanced button, and the select the following link:
<br>
"Proceed to localhost (unsafe)"
<br><br>
You should now be able to see the Argo login UI, <br>

You will be asked for a user name and password, use the following:
<br>

```bash
Username = admin
Password = (The output provided when entering the provided command to generate the password)
```
<br>

At this point you should have successfully conncted to the ArgoCD interface!


<br> <br>
```bash
pulumi init
```
<br>

```bash
pulumi up
```
<br> 

### Setup Ingress Controller with nginx

Install an ingress controller from nginx to forward the frontend and backend services we will setup later with argo

```
kubectl apply -f https://raw.githubusercontent.com/kubernetes/ingress-nginx/main/deploy/static/provider/cloud/deploy.yaml
```

### K8s secrets

Add the url, username and password using k8s from your terminal (making sure you are in the correct K8s context, and the same namespace of your environment)
```
kubectl create secret generic mysecrets --from-literal=url=jdbc:postgresql://REPLACE-DB-URL-HERE.com:5432/testdb2 --from-literal=username=username --from-literal=password=password
```

### Setup environment with Argo

Once you have logged in to argo, set up the environment from the following repository:
https://github.com/shenuka-jayasinghe/ce-team-project-k8s

When you configure the new application on argo, select 'learners-helm' as the directory and select the configuration options that suits your environment.
For example, host will be 'localhost' for the local machine, and load-balancer DNS for the cloud.

![image](https://github.com/1point21/nc-ce-final-project-env/assets/137282472/ecbec2dc-d1c4-4787-ad86-1c0f807cb6a1)


#### Optional: Developer environment

If you want to setup a developer environment as well simply start a new application on argo from the same repo and directory above but change the 'type' of both services from 'ClusterIP' to 'LoadBalancer'. Although Argo might show there is an error with the environment, it is only because 


### Setting up Jenkins

A DinD (Docker in Docker) will automatically run in the cluster like in the above image. You need to shell in to the container and run the following command:
```
/# ./jenkins.sh
```
If your cluster is on the cloud, run ```kubectl get services``` to find the DNS that Jenkins is running on, it will be on port 8084.
If you are on the local machine, Jenkins will run on http://localhost:8084

Once Jenkins is installed and you have signed-up, you need to:
1. Add Github credentials
2. Add Dockerhub crendentials (IMPORTANT: ID of dockerhub credentials has to be 'dockerhub')
3. Add the backend and frontend repos to your pipeline and build


### Accessing the Learner UI

Open your browser and navigate to [http://localhost](http://localhost) to access the Learner UI.
<br> <br> OR <br> <br>
Open your browser and navigate to the created ingress DNS to access the Learner UI.

<br> 

## System Infrastructure
Our infrastructure was created using the popular IaC tool Pulumi. <br>
Through the use of Pulumi, We successfully created an infrastructure which consists of the following:
<br> <br>

|  IaC Networking | IaC Security |
| ------------- | --------------- |
| Ec2 Provisioning | SSH Security Group |
| 3 Public Subnets | Ingress Security Groups |
| 3 Private Subnets | Egress Security Groups |
| Internet Gateway | HTTP Security Groups |
| Public Route Table | Security Group Rules |


<br>

## Languages and Tools

Throught the creation on this project, many tools and languages were used. However, luckly for you we have completed the hard lifting. Therefore, you will not need to worry about having to use all the tools and languages that we did, in order to get this system up and running. 
<br> <br>
Here is a list of the tools and languages that were used:
<br> <br>

| Tool/Language  | Used For |
| ------------- | ------------- |
| Amazon AWS | Cloud Computing Provider |
| Pulumi  | Infrastructure as Code (IaC)  |
| Argo CD  | Workflow Automation |
| Jenkins | Continuous Integration / Continuous Deployment Pipeline |
| Circle CI  | Continuous Integration / Continuous Deployment Backup Pipeline|
| Docker | Containerization |
| Kubernetes | Container Orchestration Platform |
| Java | Object-Oriented Programming Language |
| Helm | Package Manager |
| Prometheus | Metrics Scraping |
| Grafana  | Metrics Visualization |
| PostgreSQL | Database  |
| React | Frontend JavaScript Library |
| HTML | Hyper Text Markup Language |
| Javascript | Object Oriented Programming Language |
| GitHub | Source Management Control |

<br> <br>
<p align="left"> <a href="https://aws.amazon.com" target="_blank" rel="noreferrer"> <img src="https://raw.githubusercontent.com/devicons/devicon/master/icons/amazonwebservices/amazonwebservices-original-wordmark.svg" alt="aws" width="40" height="40"/> </a> <a href="https://circleci.com" target="_blank" rel="noreferrer"> <img src="https://www.vectorlogo.zone/logos/circleci/circleci-icon.svg" alt="circleci" width="40" height="40"/> </a> <a href="https://www.docker.com/" target="_blank" rel="noreferrer"> <img src="https://raw.githubusercontent.com/devicons/devicon/master/icons/docker/docker-original-wordmark.svg" alt="docker" width="40" height="40"/> </a> <a href="https://grafana.com" target="_blank" rel="noreferrer"> <img src="https://www.vectorlogo.zone/logos/grafana/grafana-icon.svg" alt="grafana" width="40" height="40"/> </a> <a href="https://www.java.com" target="_blank" rel="noreferrer"> <img src="https://raw.githubusercontent.com/devicons/devicon/master/icons/java/java-original.svg" alt="java" width="40" height="40"/> </a> <a href="https://www.jenkins.io" target="_blank" rel="noreferrer"> <img src="https://www.vectorlogo.zone/logos/jenkins/jenkins-icon.svg" alt="jenkins" width="40" height="40"/> </a> <a href="https://kubernetes.io" target="_blank" rel="noreferrer"> <img src="https://www.vectorlogo.zone/logos/kubernetes/kubernetes-icon.svg" alt="kubernetes" width="40" height="40"/> </a> <a href="https://www.nginx.com" target="_blank" rel="noreferrer"> <img src="https://raw.githubusercontent.com/devicons/devicon/master/icons/nginx/nginx-original.svg" alt="nginx" width="40" height="40"/> </a> <a href="https://www.postgresql.org" target="_blank" rel="noreferrer"> <img src="https://raw.githubusercontent.com/devicons/devicon/master/icons/postgresql/postgresql-original-wordmark.svg" alt="postgresql" width="40" height="40"/> </a> <a href="https://reactjs.org/" target="_blank" rel="noreferrer"> <img src="https://raw.githubusercontent.com/devicons/devicon/master/icons/react/react-original-wordmark.svg" alt="react" width="40" height="40"/> </a> <a href="https://spring.io/" target="_blank" rel="noreferrer"> <img src="https://www.vectorlogo.zone/logos/springio/springio-icon.svg" alt="spring" width="40" height="40"/> </a> <a href="https://www.typescriptlang.org/" target="_blank" rel="noreferrer"> <img src="https://raw.githubusercontent.com/devicons/devicon/master/icons/typescript/typescript-original.svg" alt="typescript" width="40" height="40"/> </a> </p>

<br> <br> 

## Contributing
Feel free to modify any parts of this template according to the specific details of your project.

<br> <br>

## License

This project is licensed under the MIT License. 
<br> <br>
[![License: MIT](https://img.shields.io/badge/License-MIT-yellow.svg)](https://opensource.org/licenses/MIT)






# Learner Management System

Welcome to our learner management system repository! This collaborative effort by our team aims to deploy a learner management system to the AWS cloud. This repository houses code, documentation, and guides for a smooth deployment process. By harnessing AWS capabilities, our aim was to create a highly efficient and scalable system.
<br> <br>

View all the tools and languages that were used at the bottom of the page!

<br>

## Table of Contents

1. [Introduction](#introduction)
2. [Prerequisites](#prerequisites)
3. [Getting Started](#getting-started)
    - [Clone the Repository](#clone-the-repository)
    - [Setting up AWS](#setting-up-aws)
4. [Usage](#usage)
    - [Application Setup](#application-setup)
    - [Accessing the Learner UI](#accessing-the-learner-ui)
5. [System Infrastructure](#system-infrastructure)
6. [Languages and Tools](#languages-and-tools)
7. [Contributing](#contributing)
9. [License](#license)

## Introduction

Our learner management system allows users to access a Northcoders UI, sign up as a new user, or sign in as a pre-existing user. This README provides comprehensive guidance on setting up and running the system.

The following image summarises the infrastructure, environment and CI/CD pipeline:
<img width="6688" alt="project (6)" src="https://github.com/1point21/nc-ce-final-project-env/assets/137282472/b2be9e3e-8d23-448c-8d14-1fe9dfd5983b">


## Prerequisites

Before you start, ensure you have the following prerequisites:

- [AWS Account](https://aws.amazon.com/)
- [Node.js](https://nodejs.org/) installed
- [Java](https://www.java.com/) installed
- [Docker](https://www.docker.com/) installed
- [kubectl](https://kubernetes.io/docs/tasks/tools/install-kubectl/) installed

## Getting Started

### Clone the Repository

```bash
git clone https://github.com/your-username/learner-management-system.git
cd learner-management-system
```

### Setting up AWS

Ensure you have an AWS account and configure your credentials.

```bash
aws configure
```

## Aplication Setup:
In order to create and deploy the infrastructure, simply follow the instructions below and run the provided commands:
<br> <br>

Firstly:
<br>
Install ArgoCd and complete the required setup, here is how:
<br> <br>

We will first need to create a namespace for argo.
<br>
Run the following command:
<br>

```bash
kubectl create namespace argocd
```
<br> <br>

After creating the namespace, you can install ArgoCD with the following command:
<br>

```bash
kubectl apply -n argocd -f https://raw.githubusercontent.com/argoproj/argo-cd/stable/manifests/install.yaml
```
<br> <br>

Congratulations, you have successfully installed ArgoCD, Now access the interface with the following two commands:
<br>

```bash
kubectl port-forward svc/argocd-server -n argocd 8083:443
```
<br>

```bash
kubectl -n argocd get secret argocd-initial-admin-secret -o jsonpath="{.data.password}" | base64 -d
```

<br> <br>
The first command will port-forward the application to your localhost, this gives you access to the Argo UI via the internet
<br> <br>
The second command will generate you a password, copy this password as you will need to use it later on.
<br><br>

Access the ArgoCD Interface throught the following link:
<br>
http://localhost:8083
<br><br>

Once you use the link above to access argo, you may run into an error message saying:
<br>
'Your connection is not private'
<br><br>
To access Argo, simply select the advanced button, and the select the following link:
<br>
"Proceed to localhost (unsafe)"
<br><br>
You should now be able to see the Argo login UI, <br>

You will be asked for a user name and password, use the following:
<br>

```bash
Username = admin
Password = (The output provided when entering the provided command to generate the password)
```
<br>

At this point you should have successfully conncted to the ArgoCD interface!


### Setup Ingress Controller with nginx

Install an ingress controller from nginx to forward the frontend and backend services we will setup later with argo

```
kubectl apply -f https://raw.githubusercontent.com/kubernetes/ingress-nginx/main/deploy/static/provider/cloud/deploy.yaml
```

### K8s secrets

Add the url, username and password using k8s from your terminal (making sure you are in the correct K8s context, and the same namespace of your environment)
```
kubectl create secret generic mysecrets --from-literal=url=jdbc:postgresql://REPLACE-DB-URL-HERE.com:5432/testdb2 --from-literal=username=username --from-literal=password=password
```

### Setup environment with Argo

Once you have logged in to argo, set up the environment from the following repository:
https://github.com/shenuka-jayasinghe/ce-team-project-k8s

When you configure the new application on argo, select 'learners-helm' as the directory and select the configuration options that suits your environment.
For example, host will be 'localhost' for the local machine, and load-balancer DNS for the cloud.

![image](https://github.com/1point21/nc-ce-final-project-env/assets/137282472/ecbec2dc-d1c4-4787-ad86-1c0f807cb6a1)


#### Optional: Developer environment

If you want to setup a developer environment as well simply start a new application on argo from the same repo and directory above but change the 'type' of both services from 'ClusterIP' to 'LoadBalancer'. Although Argo might show there is an error with the environment, it is only because 


### Setting up Jenkins

A DinD (Docker in Docker) will automatically run in the cluster like in the above image. You need to shell in to the container and run the following command:
```
/# ./jenkins.sh
```
If your cluster is on the cloud, run ```kubectl get services``` to find the DNS that Jenkins is running on, it will be on port 8084.
If you are on the local machine, Jenkins will run on http://localhost:8084

Once Jenkins is installed and you have signed-up, you need to:
1. Add Github credentials
2. Add Dockerhub crendentials (IMPORTANT: ID of dockerhub credentials has to be 'dockerhub')
3. Add the backend and frontend repos to your pipeline and build


### Accessing the Learner UI

Open your browser and navigate to [http://localhost](http://localhost) to access the Learner UI.
<br> <br> OR <br> <br>
Open your browser and navigate to the created ingress DNS to access the Learner UI.

<br> 

## System Infrastructure
Our infrastructure was created using the popular IaC tool Pulumi. <br>
Through the use of Pulumi, We successfully created an infrastructure which consists of the following:
<br> <br>

|  IaC Networking | IaC Security |
| ------------- | --------------- |
| Ec2 Provisioning | SSH Security Group |
| 3 Public Subnets | Ingress Security Groups |
| 3 Private Subnets | Egress Security Groups |
| Internet Gateway | HTTP Security Groups |
| Public Route Table | Security Group Rules |


<br>

## Languages and Tools

Throught the creation on this project, many tools and languages were used. However, luckly for you we have completed the hard lifting. Therefore, you will not need to worry about having to use all the tools and languages that we did, in order to get this system up and running. 
<br> <br>
Here is a list of the tools and languages that were used:
<br> <br>

| Tool/Language  | Used For |
| ------------- | ------------- |
| Amazon AWS | Cloud Computing Provider |
| Pulumi  | Infrastructure as Code (IaC)  |
| Argo CD  | Workflow Automation |
| Jenkins | Continuous Integration / Continuous Deployment Pipeline |
| Circle CI  | Continuous Integration / Continuous Deployment Backup Pipeline|
| Docker | Containerization |
| Kubernetes | Container Orchestration Platform |
| Java | Object-Oriented Programming Language |
| Helm | Package Manager |
| Prometheus | Metrics Scraping |
| Grafana  | Metrics Visualization |
| PostgreSQL | Database  |
| React | Frontend JavaScript Library |
| HTML | Hyper Text Markup Language |
| Javascript | Object Oriented Programming Language |
| GitHub | Source Management Control |

<br> <br>
<p align="left"> <a href="https://aws.amazon.com" target="_blank" rel="noreferrer"> <img src="https://raw.githubusercontent.com/devicons/devicon/master/icons/amazonwebservices/amazonwebservices-original-wordmark.svg" alt="aws" width="40" height="40"/> </a> <a href="https://circleci.com" target="_blank" rel="noreferrer"> <img src="https://www.vectorlogo.zone/logos/circleci/circleci-icon.svg" alt="circleci" width="40" height="40"/> </a> <a href="https://www.docker.com/" target="_blank" rel="noreferrer"> <img src="https://raw.githubusercontent.com/devicons/devicon/master/icons/docker/docker-original-wordmark.svg" alt="docker" width="40" height="40"/> </a> <a href="https://grafana.com" target="_blank" rel="noreferrer"> <img src="https://www.vectorlogo.zone/logos/grafana/grafana-icon.svg" alt="grafana" width="40" height="40"/> </a> <a href="https://www.java.com" target="_blank" rel="noreferrer"> <img src="https://raw.githubusercontent.com/devicons/devicon/master/icons/java/java-original.svg" alt="java" width="40" height="40"/> </a> <a href="https://www.jenkins.io" target="_blank" rel="noreferrer"> <img src="https://www.vectorlogo.zone/logos/jenkins/jenkins-icon.svg" alt="jenkins" width="40" height="40"/> </a> <a href="https://kubernetes.io" target="_blank" rel="noreferrer"> <img src="https://www.vectorlogo.zone/logos/kubernetes/kubernetes-icon.svg" alt="kubernetes" width="40" height="40"/> </a> <a href="https://www.nginx.com" target="_blank" rel="noreferrer"> <img src="https://raw.githubusercontent.com/devicons/devicon/master/icons/nginx/nginx-original.svg" alt="nginx" width="40" height="40"/> </a> <a href="https://www.postgresql.org" target="_blank" rel="noreferrer"> <img src="https://raw.githubusercontent.com/devicons/devicon/master/icons/postgresql/postgresql-original-wordmark.svg" alt="postgresql" width="40" height="40"/> </a> <a href="https://reactjs.org/" target="_blank" rel="noreferrer"> <img src="https://raw.githubusercontent.com/devicons/devicon/master/icons/react/react-original-wordmark.svg" alt="react" width="40" height="40"/> </a> <a href="https://spring.io/" target="_blank" rel="noreferrer"> <img src="https://www.vectorlogo.zone/logos/springio/springio-icon.svg" alt="spring" width="40" height="40"/> </a> <a href="https://www.typescriptlang.org/" target="_blank" rel="noreferrer"> <img src="https://raw.githubusercontent.com/devicons/devicon/master/icons/typescript/typescript-original.svg" alt="typescript" width="40" height="40"/> </a> </p>

<br> <br> 

## Contributing
Feel free to modify any parts of this template according to the specific details of your project.

<br> <br>

## License

This project is licensed under the MIT License. 
<br> <br>
[![License: MIT](https://img.shields.io/badge/License-MIT-yellow.svg)](https://opensource.org/licenses/MIT)






