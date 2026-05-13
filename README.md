# AWS EKS Setup with Terraform

This is my Terraform setup for provisioning a production-ready EKS cluster on AWS. I built this based on what I actually use at work, cleaned up into a reusable structure.

## What this sets up

- VPC with public and private subnets across multiple availability zones
- EKS cluster with managed node groups
- IAM roles and policies for the cluster and worker nodes
- Helm installed and configured on the cluster
- ArgoCD deployed for GitOps-style application delivery
- Prometheus and Grafana for monitoring
- GitHub Actions pipeline to apply Terraform changes automatically

## Folder structure

```
aws-eks-terraform-setup/
├── terraform/
│   ├── main.tf
│   ├── variables.tf
│   ├── outputs.tf
│   └── modules/
│       ├── eks/
│       ├── vpc/
│       └── iam/
├── kubernetes/
│   ├── deployments/
│   ├── services/
│   └── monitoring/
└── .github/
    └── workflows/
        └── ci-cd.yml
```

## How to use it

```bash
# clone the repo
git clone https://github.com/harsha-cpu/aws-eks-terraform-setup.git
cd aws-eks-terraform-setup/terraform

# initialise terraform
terraform init

# check what will be created
terraform plan

# apply
terraform apply
```

Once the cluster is up, connect to it:

```bash
aws eks update-kubeconfig --region ap-south-1 --name my-eks-cluster

# verify nodes are ready
kubectl get nodes
```

## What I learned building this

Splitting the Terraform into modules (eks, vpc, iam) made it much easier to manage. Earlier I had everything in one main.tf which became hard to read and debug. Also learned to use remote state with S3 and DynamoDB for locking — without that, running terraform apply from two places can corrupt your state file.

Getting ArgoCD to sync automatically on Git push took some configuration around the repository credentials and webhook setup, but once it worked it made deployments much cleaner.

## Prerequisites

- AWS CLI configured with appropriate permissions
- Terraform 1.0+
- kubectl
- Helm 3+

---

— Harish
