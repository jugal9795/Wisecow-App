# Containerisation and Deployment of Wisecow Application on Kubernetes

## Overview
This project demonstrates how to containerize and deploy the Wisecow application on a Kubernetes environment using AWS services. It includes setting up continuous integration and deployment (CI/CD) with GitHub Actions and securing the application with TLS.

## Prerequisites
1. AWS Account: You need an AWS account with permissions to create and manage ECR repositories, EKS clusters, IAM roles, and other resources.
2. Docker: Installed on your local machine to build Docker images.
3. kubectl: Kubernetes command-line tool installed on your local machine.
4. aws-cli: AWS command-line interface installed and configured on your local machine.

## Steps
### 1. Set Up Your AWS Environment

#### 1. Create an ECR Repository:

* Go to the Amazon ECR console.
* Click on Create repository.
* Enter a name for your repository (e.g., wisecow).
* Click Create repository.

#### 2. Create an EKS Cluster:

* Go to the Amazon EKS console.
* Click Add cluster and then Create.
* Enter a name for your cluster (e.g., wisecow-cluster).
* Configure your cluster settings and create the cluster.

#### 3. Set Up IAM Roles:

#### * EKS Cluster Role:
* Go to the IAM console.
* Click on Roles and then Create role.
* Choose EKS and attach the AmazonEKSClusterPolicy policy.
* Name the role (e.g., eks-cluster-role) and create it.
  
#### * EKS Node Group Role:
* Create another role for EKS node groups.
* Choose EKS and then EKS - Nodegroup.
* Attach the following policies:
  * 'AmazonEKSWorkerNodePolicy'
  * 'AmazonEC2ContainerRegistryReadOnly'
  * 'AmazonEKS_CNI_Policy'
* Name the role (e.g., eks-nodegroup-role) and create it.

#### 4. Update kubectl Configuration:

* Install aws-cli and kubectl if not already installed.
* Update your kubeconfig file to use your EKS cluster:

```
aws eks --region <your-region> update-kubeconfig --name <your-cluster-name>
```


### 2. Set Up GitHub Actions

#### 1.Create GitHub Secrets:

* Go to your GitHub repository.
* Navigate to Settings -> Secrets and variables -> Actions.
* Add the following secrets:
  * 'AWS_ACCESS_KEY_ID': Your AWS access key ID.
  * 'AWS_SECRET_ACCESS_KEY': Your AWS secret access key.


#### 2.Add the GitHub Actions Workflow:

* Create a GitHub Actions workflow file in your repository at '.github/workflows/ci-cd.yml'.

### 3. Deploy TLS Certificates

#### 1.Request a Certificate:

* Go to the AWS Certificate Manager (ACM) console.
* Request a public certificate for your domain.
* Validate the domain ownership as required.

#### 2.Configure Ingress with TLS:

* Create or update your Kubernetes Ingress resource to use the TLS certificate. Example configuration:

```
apiVersion: networking.k8s.io/v1
kind: Ingress
metadata:
  name: wisecow-ingress
  annotations:
    nginx.ingress.kubernetes.io/rewrite-target: /
spec:
  tls:
  - hosts:
    - wisecow.example.com
    secretName: wisecow-tls
  rules:
  - host: wisecow.example.com
    http:
      paths:
      - path: /
        pathType: Prefix
        backend:
          service:
            name: wisecow-service
            port:
              number: 80
```

## Summary
1. Set Up AWS Resources: Create an ECR repository and an EKS cluster. Configure IAM roles.
2. Update kubectl Configuration: Ensure kubectl can connect to your EKS cluster.
3. Configure GitHub Actions: Add AWS credentials as GitHub secrets and set up the CI/CD workflow.
4. Deploy TLS Certificates: (Optional) Set up TLS using AWS ACM and configure Kubernetes Ingress.

Following these steps will enable you to containerize, deploy, and manage the Wisecow application on AWS using Kubernetes, with CI/CD automation and optional TLS security.



