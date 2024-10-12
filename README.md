# Coworking Analytics
Monitor and analyze user usage frequency, thereby developing appropriate business strategies.

## Resources used for this application
1. AWS CodeBuild - build Docker images remotely
2. AWS ECR - host Docker images
3. Kubernetes Environment with AWS EKS - run applications in k8s
4. AWS CloudWatch - monitor activity and logs in EKS
5. GitHub - pull and clone code

## Folder structure
```sh
|--analytics                # main application
|--db                       # db resource
|--deployment               # yaml file to deploy application and services
|--screenshot               # screenshot files of deployments and requirement from module 4
|--codebuild.yaml           # Buildspec file
|--Dockerfile
|--README.md
```
## Setup source
### 1. Create cluster
#### 1.1. create an eks cluster
```bash
eksctl create cluster --name my-cluster --region us-east-1 --nodegroup-name my-nodes --node-type t3.small --nodes 1 --nodes-min 1 --nodes-max 2
```
#### 1.2. Update the kubeconfig
```bash
aws eks --region us-east-1 update-kubeconfig --name my-cluster
```
### 2. Init env
Run these command at deployment folder
```bash
kubectl apply -f env-configmap.yaml
kubectl apply -f env-secret.yaml
```
### 3. Config database
Run these command at deployment folder
```bash
kubectl apply -f pvc.yaml
kubectl apply -f pv.yaml
kubectl apply -f postgresql-deployment.yaml
kubectl apply -f postgresql-service.yaml
```
### 4. Create data table and initialize data
#### 4.1. Connect to postgresql-service via Port forwarding
```bash
kubectl port-forward service/postgresql-service 5433:5432 &
```
#### 4.2. Init table and data
Run these command at db folder
```bash
export DB_PASSWORD=mypassword PGPASSWORD="$DB_PASSWORD" psql --host 127.0.0.1 -U myuser -d mydatabase -p 5433 < 1_create_tables.sql
export DB_PASSWORD=mypassword PGPASSWORD="$DB_PASSWORD" psql --host 127.0.0.1 -U myuser -d mydatabase -p 5433 < 2_seed_users.sql
export DB_PASSWORD=mypassword PGPASSWORD="$DB_PASSWORD" psql --host 127.0.0.1 -U myuser -d mydatabase -p 5433 < 3_seed_tokens.sql
```
### 5. Config codebuild to push source
### 6. Merge source or click build from code build to push image to ecr
### 7. Deploy application
Run these command at deployment folder
```bash
kubectl apply -f coworking-deployment.yaml
kubectl apply -f coworking-service.yaml
```