# Udagram-Microservices
Monolith to Microservices at Scale Project

### Prerequisites
- Install [docker](https://docs.docker.com/docker-for-windows/install/)
- Install [kubectl](https://docs.aws.amazon.com/eks/latest/userguide/install-kubectl.html)
- Install [aws-cli](https://docs.amazonaws.cn/en_us/cli/latest/userguide/install-windows.html)
- Install [eksctl & aws-iam-authenticator](https://docs.aws.amazon.com/eks/latest/userguide/getting-started-eksctl.html)

#### Setup docker enviroment
- Build the images: `docker-compose -f docker-compose-build.yaml  build --parallel`
- Run the containers: `docker-compose up`
- Push the images: `docker-compose -f docker-compose-build.yaml push`

#### Create a cluster with eksctl 
```
eksctl create cluster  --name YOUR_CLUSTER_NAME --version 1.14 --nodegroup-name standard-workers  --node-type t3.medium --nodes 3 --nodes-min 1 --nodes-max 4 --node-ami auto
```
#### Modify secrets and configmap .yaml files
- Encrypt the database username and password using base64 using the following commands
    - `echo POSTGRESS_PASSWORD | base64`
    - `echo POSTGRESS_USERNAME | base64`
- Encrypt the aws file using base64 using the following commands
    - `cat ~/.aws/credentials | base64` 
- Add these values in the appropriate places in `env-secret.yaml`, `aws-secret.yaml`, and `env-configmap.yaml` 
#### Setup Kubernetes Environment
Note that you need to apply the configurations in the `/deployments/k8s/` directory 
- Load secret files: 
    - `Note: The port forwarding must be done in separate terminals, otherwise, it won't work.`
	- `kubectl apply -f aws-secret.yaml`
	- `kubectl apply -f env-secret.yaml`
	- `kubectl apply -f env-configmap.yaml`
- Apply all other yaml files:
    - ` remember to run first frontend, feed and user, before running reverseproxy`
    - `kubectl apply -f .`

#### Check pods status
-   `kubectl get all`

#### Connect the services with port forwarding
- Use port forwarding to the frontend and reverse proxy services:
    - `Note: The port forwarding must be done in separate terminals, otherwise, it won't work.`
	- `kubectl port-forward service/frontend 8100:8100`
	- `kubectl port-forward service/reverseproxy 8080:8080`
