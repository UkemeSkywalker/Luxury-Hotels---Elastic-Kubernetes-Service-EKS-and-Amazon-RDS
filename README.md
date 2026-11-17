
# Luxury Hotels — AWS EKS and RDS Infrastructure

A hands-on cloud infrastructure project demonstrating how a containerised application environment can be deployed on AWS using Amazon Elastic Kubernetes Service (EKS), Amazon RDS and Terraform.

The project explores the infrastructure components involved in moving an application environment towards AWS-managed compute, networking and database services.

## Architecture Overview

The infrastructure is organised into three main areas:

### 1. Networking

The Terraform configuration provisions an Amazon VPC with:

- CIDR range `10.0.0.0/16`.
- Three Availability Zones.
- Three private subnets.
- Three public subnets.
- NAT Gateway configuration.
- DNS hostnames enabled.
- Kubernetes subnet tags for load balancer integration.

### 2. Container Orchestration — Amazon EKS

The EKS configuration uses the `terraform-aws-modules/eks/aws` Terraform module.

The current configuration includes:

- EKS cluster running Kubernetes 1.22.
- Two managed node groups.
- EC2 `t3.small` instances.
- Configurable minimum, maximum and desired node group sizes.
- Private subnet placement for EKS worker nodes.
- Separate security groups for the node groups.

Current node group configuration:

| Node group | Instance type | Minimum | Desired | Maximum |
|---|---|---:|---:|---:|
| node-group-1 | t3.small | 1 | 2 | 3 |
| node-group-2 | t3.small | 1 | 1 | 2 |

### 3. Database — Amazon RDS

The project also provisions an Amazon RDS MySQL database.

Current configuration:

- Database engine: MySQL 5.7.
- Instance class: `db.t3.micro`.
- Allocated storage: 10 GB.
- Database credentials supplied through a Terraform variable.
- Database subnet group and security group configuration.

## Technology Stack

| Layer | Technology |
|---|---|
| Cloud provider | AWS |
| Infrastructure as Code | Terraform |
| Container orchestration | Amazon EKS |
| Compute | Amazon EC2 |
| Database | Amazon RDS |
| Networking | Amazon VPC |
| Container platform | Docker |
| Kubernetes configuration | Kubernetes manifests |

## Repository Structure

```text
.
├── luxxy-app/
│   └── Application source
│
├── luxxy-kubernetes/
│   └── Kubernetes deployment configuration
│
└── luxxy-terraform-aws/
    ├── eks-cluster.tf
    ├── rds.tf
    ├── security-groups.tf
    ├── vpc.tf
    └── variables.tf
```

## Prerequisites

- An AWS account.
- AWS CLI.
- Terraform.
- kubectl.
- Docker, if building the application container.
- Appropriate AWS IAM permissions.

Use an approved AWS authentication mechanism such as IAM Identity Center or an assumed IAM role.

## Deployment Overview

The infrastructure and application configuration are separated into distinct directories.

A typical deployment workflow is:

1. Configure AWS credentials.
2. Initialise the Terraform configuration.
3. Review and validate the infrastructure plan.
4. Provision the VPC, EKS cluster and database.
5. Configure kubectl to connect to the EKS cluster.
6. Apply the Kubernetes manifests.
7. Verify application and database connectivity.

Terraform commands:

```bash
terraform init
terraform fmt -recursive
terraform validate
terraform plan
terraform apply
```

Configure kubectl after the cluster is provisioned:

```bash
aws eks update-kubeconfig \
  --region us-east-1 \
  --name <cluster-name>
```

The actual cluster name is generated using a random suffix in the Terraform configuration.

## Security Considerations

This repository is a learning and infrastructure demonstration, not a production-ready deployment.

Before using it in a real environment, address the following:

- Restrict database ingress to trusted application security groups rather than the public internet.
- Place RDS in private subnets and disable public accessibility.
- Use a dedicated database user instead of the root account.
- Store database credentials in AWS Secrets Manager or another approved secrets manager.
- Review EKS control-plane access and node security group rules.
- Upgrade the legacy Kubernetes and MySQL versions.
- Review Terraform module and provider version constraints.
- Configure appropriate backups, encryption and deletion protection.

## Potential Improvements

- Upgrade EKS to a currently supported Kubernetes version.
- Introduce private database networking.
- Implement secure secret management.
- Add Terraform remote state and locking.
- Add CI/CD automation for infrastructure and application deployments.
- Introduce monitoring, logging and alerting.
- Add deployment verification and operational runbooks.

## License

See the repository for licensing information.
