## 🎬 DevSecOps Netflix Clone on Amazon EKS ##
This project demonstrates an enterprise-grade DevSecOps pipeline for deploying a highly scalable Netflix Clone (React-based) on Amazon EKS. It integrates security scanning, automated CI/CD, and GitOps practices to ensure a "Zero Trust" and "Always-Sync" deployment environment.


### 🏗 Project Architecture ###
#### CI (Continuous Integration): 
Triggered by GitHub commits; Jenkins runs code analysis, security scans, and builds Docker images.

#### CD (Continuous Deployment):
ArgoCD monitors the repository and synchronizes the desired state from Git to the EKS cluster.

#### Infrastructure: 
Managed via Amazon EKS, utilizing AWS Load Balancer Controller for external traffic.


### 🛠 Technologies & Terms ###
### Core Technologies ###

#### React:
The frontend library used to build the Netflix UI.

#### Jenkins:
The automation engine for the CI pipeline.

#### Docker: 
Used to containerize the application for environment consistency.

#### Kubernetes (EKS):
Orchestrates the containers, handling scaling and self-healing.

#### ArgoCD:
A GitOps tool that ensures the cluster matches the configuration in GitHub.

#### Helm: 
The package manager used to deploy the AWS Load Balancer Controller and Prometheus.

### SecOps & Monitoring ###
#### SonarQube:
Performs Static Application Security Testing (SAST) to find bugs and vulnerabilities in the code.

#### Trivy: 
Scans Docker images for OS-level vulnerabilities.

#### Prometheus: 
A time-series database that collects performance metrics from the cluster.

#### Grafana:
A visualization dashboard for Prometheus metrics.


### ☁️ AWS Services Used ###
#### EKS (Elastic Kubernetes Service): 
Managed Kubernetes control plane.

#### EC2:
Worker nodes for the cluster and the host for Jenkins/Monitoring.

#### IAM (Identity & Access Management): 
Utilized OIDC and IRSA (IAM Roles for Service Accounts) to provide the Load Balancer Controller with the least-privilege access.

#### VPC (Virtual Private Cloud): 
Custom networking with public/private subnets and Elastic IPs.

#### ELB (Elastic Load Balancing): 
Automatically provisioned via the AWS Load Balancer Controller.


### 🚀 How to Solve (Deployment Steps) ###
#### Step 1: Infrastructure Provisioning
Create the EKS cluster using eksctl:

Bash
```
eksctl create cluster --name netflix --region eu-north-1 --node-type t3.medium
```

#### Step 2: CI Pipeline (Jenkins)
Install Jenkins on an EC2 instance.
Configure a pipeline to run npm install, SonarQube analysis, and Trivy scans.
Build and push the Docker image to your registry.

#### Step 3: SecOps Configuration (IAM & OIDC)
Establish a trust relationship between AWS and EKS to allow the Load Balancer to function:
Create an OIDC Provider for the cluster.
Create an IAM policy with Load Balancer permissions.
Associate the IAM Role with the aws-load-balancer-controller service account.

#### Step 4: GitOps with ArgoCD
Install ArgoCD on EKS.
Connect your GitHub Repo.
Sync the k8s folder. ArgoCD will detect your manifests and deploy the netflix-service and netflix-deployment.

#### Step 5: External Access
Annotate the Kubernetes service to trigger the AWS Load Balancer:

YAML
annotations:
  service.beta.kubernetes.io/aws-load-balancer-type: "external"
  service.beta.kubernetes.io/aws-load-balancer-scheme: "internet-facing"


### 📊 Monitoring ###
Access the Grafana dashboard to monitor:

#### Pod Health: 
CPU and Memory usage per container.

#### Network Traffic:
Request latency through the Load Balancer.


### 📦 Large File Management (Git LFS) ###
This project uses Git LFS (Large File Storage) to manage binary assets like PDFs and heavy images. In our .gitattributes, we use the following configuration:

#### *.pdf filter=lfs diff=lfs merge=lfs -text

#### filter=lfs:
Replaces the heavy PDF with a tiny pointer file in the main repo.

#### diff=lfs:
Tells Git not to try reading the "text" inside a PDF when comparing versions.

#### merge=lfs: 
Uses LFS logic to handle conflicts if two people change the same PDF.

#### -text:
Prevents Git from modifying line endings, ensuring the PDF doesn't get corrupted.

