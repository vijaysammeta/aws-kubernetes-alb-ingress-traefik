# Multi-Application Deployment on Kubernetes with ALB Ingress and Traefik

## Project Overview

This project demonstrates deploying four independent web applications on an AWS Kubernetes cluster using Docker, Kubernetes, kOps, AWS Application Load Balancer (ALB) Ingress, and Traefik.

Applications:
- Internet Banking
- Mobile Banking
- Insurance
- Loans

Each application is containerized using Docker and deployed on Kubernetes with three replicas.

The project explores two approaches to application routing:
1. AWS ALB Ingress Controller with path-based routing and HTTPS.
2. Traefik Ingress Controller with path-based routing and middleware.

## Technologies Used

- AWS EC2
- Amazon VPC
- Amazon S3
- IAM
- kOps
- Kubernetes
- Docker
- Docker Hub
- AWS Load Balancer Controller
- Application Load Balancer
- Traefik
- AWS Certificate Manager
- Amazon Route 53
- Apache HTTP Server

## Project Architecture

                    Users
                      |
                  Route 53
                      |
              Application Entry
                      |
          +-----------+-----------+
          |                       |
       AWS ALB                 Traefik
      Ingress                  Ingress
          |                       |
          +-----------+-----------+
                      |
              Path-Based Routing
                      |
       +--------------+--------------+-------------------+
       |              |              |                    |
      /ib            /mb        /insurance               /loans
       |              |              |                     |
  ib-service     mb-service   insurance-service          loans-service
       |              |              |                      |
  ib-deployment  mb-deployment insurance-deployment       loans-deployment

                     


*ALB Ingress and Traefik are separate routing implementations, not a single combined controller chain.*

## Application URLs

| Application | Path |
|---|---|
| Internet Banking | `/ib` |
| Mobile Banking | `/mb` |
| Insurance | `/insurance` |
| Loans | `/loans` |

ALB application domain:

`https://k8s.vijaysammeta.online`

## Implementation Steps

### 1. Create Kubernetes Cluster Using kOps

- Install AWS CLI, kubectl, and kOps.
- Configure AWS credentials.
- Create an S3 bucket for kOps state storage.
- Create the Kubernetes cluster in AWS Mumbai.
- Export cluster credentials and verify the nodes.

```bash
kubectl get nodes
```

### 2. Containerize Applications Using Docker

- Prepare the four application directories.
- Create a Dockerfile for each application using Apache HTTP Server.
- Build four separate Docker images.
- Push the images to Docker Hub.

```bash
docker images
```

### 3. Deploy Applications on Kubernetes

Create Deployments and Services for all four applications.

Each Deployment runs three replicas.

```bash
kubectl apply -f kubernetes/application-deploy-service.yml
```

Verify:

```bash
kubectl get deployments
kubectl get pods
kubectl get services
```

### 4. Configure AWS ALB Ingress

- Install AWS Load Balancer Controller.
- Configure the required IAM permissions.
- Configure an ACM certificate.
- Create path-based routing rules.
- Configure URL rewriting for each application.
- Configure Route 53 to point the domain to the ALB.

```bash
kubectl apply -f alb-ingress/ingress.yml
kubectl get ingress
kubectl describe ingress hdfc-ingress
```

### 5. Configure Traefik Ingress

- Install Traefik using Helm.
- Configure its Service.
- Create Middleware resources using StripPrefix.
- Create an IngressRoute for all four applications.
- Test routing through Traefik.

```bash
helm repo add traefik https://traefik.github.io/charts
helm repo update

helm install traefik traefik/traefik \
  -n traefik --create-namespace \
  -f traefik/traefik-values.yaml
```

Apply the routes:

```bash
kubectl apply -f traefik/traefik-app-routes.yaml
```

Verify:

```bash
kubectl get pods -n traefik
kubectl get svc -n traefik
kubectl get ingressroute
kubectl get middleware
```

### 6. Testing

Test the ALB application URLs:

```text
https://k8s.vijaysammeta.online/ib
https://k8s.vijaysammeta.online/mb
https://k8s.vijaysammeta.online/insurance
https://k8s.vijaysammeta.online/loans
```

Test each application through its corresponding route and verify that it displays the correct web page.

## Final Outcome

- Deployed four independent web applications on Kubernetes.
- Created Docker images and published them to Docker Hub.
- Configured Kubernetes Deployments and NodePort Services.
- Implemented path-based routing using AWS ALB Ingress.
- Configured HTTPS using ACM and DNS using Route 53.
- Installed and tested Traefik as an alternative ingress controller.
- Practiced URL rewriting and path-prefix handling.

## Author

**Project:** Multi-Application Kubernetes Deployment with ALB Ingress and Traefik

**Cloud Provider:** AWS

**Region:** ap-south-1 (Mumbai)
