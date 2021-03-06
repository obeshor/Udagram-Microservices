# Udagram-Microservices
This is basically a web application built on ionic and node.js. There are two microservices implemented in this application

### Prerequisites

- Install [docker](https://docs.docker.com/docker-for-windows/install/)
- Install [kubectl](https://docs.aws.amazon.com/eks/latest/userguide/install-kubectl.html)
- Install [aws-cli](https://docs.amazonaws.cn/en_us/cli/latest/userguide/install-windows.html)
- Install [eksctl & aws-iam-authenticator](https://docs.aws.amazon.com/eks/latest/userguide/getting-started-eksctl.html)

### Setup enviroment

#### Setup docker enviroment
- Switch the folder: `cd udacity-c3-deployment/docker`
- Build the images: `docker-compose -f docker-compose-build.yaml  build --parallel`
- Run the containers: `docker-compose up`
- Push the images: `docker-compose -f docker-compose-build.yaml push`

#### Docker hub images
- [obeshor/udacity-restapi-feed](https://hub.docker.com/repository/docker/obeshor/udacity-restapi-feed)
- [obeshor/udacity-restapi-user](https://hub.docker.com/repository/docker/obeshor/udacity-restapi-user)
- [obeshor/udacity-restapi-frontend](https://hub.docker.com/repository/docker/obeshor/udacity-restapi-frontend)
- [obeshor/reverseproxy](https://hub.docker.com/repository/docker/obeshor/reverseproxy)

### Kubernetes cluster on AWS 

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
- You can also use the website [Base64 Encode and Decode](https://www.base64encode.org/) to encode your variables.
- Add these values in the appropriate places in `env-secret.yaml`, `aws-secret.yaml`, and `env-configmap.yaml` 

#### Setup Kubernetes Environment
Note that you need to apply the configurations in the `/deployments/k8s/` directory 
- Load secret files: 
    - `Note: The port forwarding must be done in separate terminals, otherwise, it won't work.`
	- `kubectl apply -f aws-secret.yaml`
	- `kubectl apply -f env-secret.yaml`
	- `kubectl apply -f env-configmap.yaml`
- Apply all other yaml files:
    - `Remember to run first frontend, feed and user, before running reverseproxy`
    - `kubectl apply -f .`

#### Check pods status
-   `kubectl get all : to see pods, services, deployments and replicasets `

#### Connect the services with port forwarding
- Use port forwarding to the frontend and reverse proxy services, note that : The port forwarding must be done in separate terminals, otherwise, it won't work.
	- `kubectl port-forward service/frontend 8100:8100`
	- `kubectl port-forward service/reverseproxy 8080:8080`

### CD/CI 

The CI tool used here is Travis CI
- Sign up for [TravisCI](https://travis-ci.com/) and connect your Github repository to TravisCI
- Add `.travis.yml` file to the git repository
A new commit to this repository will create an automatic build.
