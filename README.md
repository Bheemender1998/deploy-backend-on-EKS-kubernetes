# Deploying a Containerized Backend App on AWS EKS


## Project Overview

This repository contains resources and configuration files for deploying a containerized Flask backend application on AWS Elastic Kubernetes Service (EKS). The project demonstrates a complete deployment workflow from development to production infrastructure.

## Features

- AWS EKS cluster setup and configuration
- Docker containerization of a Python Flask application
- Kubernetes manifests for deployments and services
- Security best practices implementation
- Monitoring and observability setup
- Cost optimization strategies

## Prerequisites

- AWS account with appropriate permissions
- AWS CLI configured with access credentials
- Docker installed
- kubectl installed
- eksctl installed
- Git installed

## Architecture

- **Amazon ECR** - Container registry for storing Docker images
- **Amazon EKS** - Managed Kubernetes service for container orchestration
- **Amazon EC2** - Compute instances for running containerized applications
- **Elastic Load Balancer** - For routing traffic to the backend services

## Setup and Deployment

### 1. Clone the Repository

```bash
git clone https://github.com/yourusername/aws-eks-deployment.git
cd aws-eks-deployment
```

### 2. Set Up EKS Cluster

```bash
eksctl create cluster \
  --name backend-cluster \
  --region us-east-1 \
  --nodegroup-name linux-nodes \
  --node-type t2.micro \
  --nodes 2 \
  --nodes-min 1 \
  --nodes-max 3 \
  --managed
```

### 3. Build and Push Docker Image

```bash
# Build the Docker image
docker build -t flask-backend:latest .

# Create ECR repository
aws ecr create-repository --repository-name flask-backend

# Authenticate to ECR
aws ecr get-login-password --region us-east-1 | docker login --username AWS --password-stdin $(aws sts get-caller-identity --query Account --output text).dkr.ecr.us-east-1.amazonaws.com

# Tag and push image
AWS_ACCOUNT_ID=$(aws sts get-caller-identity --query Account --output text)
docker tag flask-backend:latest ${AWS_ACCOUNT_ID}.dkr.ecr.us-east-1.amazonaws.com/flask-backend:latest
docker push ${AWS_ACCOUNT_ID}.dkr.ecr.us-east-1.amazonaws.com/flask-backend:latest
```

### 4. Deploy to Kubernetes

```bash
# Apply Kubernetes manifests
kubectl apply -f kubernetes/namespace.yaml
kubectl apply -f kubernetes/deployment.yaml
kubectl apply -f kubernetes/service.yaml
```

### 5. Verify Deployment

```bash
# Check deployment status
kubectl get deployments -n backend

# Check pods
kubectl get pods -n backend

# Get service endpoint
kubectl get svc -n backend
```

## Repository Structure

```
.
├── Dockerfile                 # Docker configuration for Flask app
├── app/                       # Flask application code
├── kubernetes/                # Kubernetes manifests
│   ├── deployment.yaml        # Deployment configuration
│   ├── namespace.yaml         # Namespace configuration
│   ├── network-policy.yaml    # Network policies
│   └── service.yaml           # Service configuration
├── scripts/                   # Helper scripts for setup and deployment
└── docs/                      # Extended documentation and guides
```

## Troubleshooting

### ImagePullBackOff Error
- Verify image URL in deployment manifest
- Check ECR permissions
- Create and use ECR pull secrets

### CrashLoopBackOff Error
- Check pod logs: `kubectl logs -n backend <pod-name>`
- Verify application configuration
- Test container locally

### Service Not Accessible
- Verify service has an external IP
- Check endpoint creation
- Confirm security group rules

## Best Practices

### Security
- Use IAM roles for service accounts
- Implement network policies
- Store sensitive data in Secrets
- Regularly update container images

### Performance
- Set resource requests and limits
- Implement horizontal pod autoscaling
- Configure liveness and readiness probes

### Cost Optimization
- Use spot instances for non-critical workloads
- Implement cluster autoscaling
- Clean up unused resources

## Monitoring and Logging

- **CloudWatch**: AWS native monitoring
- **Prometheus and Grafana**: Open-source monitoring
- **Fluent Bit**: Log aggregation

## Roadmap

- **CI/CD Integration**: Implementation of CI/CD pipelines using GitHub Actions or AWS CodePipeline (coming soon)
- Expanded monitoring with custom dashboards
- Automated scaling based on custom metrics
- Multi-environment deployment (dev, staging, prod)

## Further Reading

- [Official EKS Documentation](https://docs.aws.amazon.com/eks/latest/userguide/what-is-eks.html)
- [Kubernetes Documentation](https://kubernetes.io/docs/home/)
- [Docker Documentation](https://docs.docker.com/)

## License

This project is licensed under the MIT License - see the [LICENSE](LICENSE) file for details.

## Contributing

Contributions are welcome! Please feel free to submit a Pull Request.

## Contact

Bheemender Gurram - [gurram.bheemender@gmail.com](mailto:gurram.bheemender@gmail.com)

Project Link: 

---

⭐️ If you find this project helpful, please give it a star on GitHub! ⭐️
