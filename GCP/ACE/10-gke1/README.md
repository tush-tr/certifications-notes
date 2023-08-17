- [Google Kubernetes Engine Introduction](#google-kubernetes-engine-introduction)
  - [Kubernetes](#kubernetes)
  - [What is GKE?](#what-is-gke)
- [Creating GKE Cluster](#creating-gke-cluster)
  - [Create k8s cluster with the default node pool](#create-k8s-cluster-with-the-default-node-pool)
    - [Standard Cluster](#standard-cluster)
    - [Autopilot Cluster](#autopilot-cluster)
    - [Step 1. Create a k8s cluster with default node pool](#step-1-create-a-k8s-cluster-with-default-node-pool)
- [Create Deployment and a Service](#create-deployment-and-a-service)
  - [Step 2. Login to cloud shell](#step-2-login-to-cloud-shell)
  - [Step 3. Connect with the cluster](#step-3-connect-with-the-cluster)
  - [Step 4. Deploy microservices to k8s](#step-4-deploy-microservices-to-k8s)
- [Explore GKE Console](#explore-gke-console)
  - [Node pool](#node-pool)
  - [Workloads](#workloads)
  - [Services and Ingress](#services-and-ingress)
- [Scaling Deployments and Resizing node pools](#scaling-deployments-and-resizing-node-pools)
  - [Step 5. Increase number of instances of your microservice](#step-5-increase-number-of-instances-of-your-microservice)
  - [Step 6. Increate number of nodes in k8s cluster](#step-6-increate-number-of-nodes-in-k8s-cluster)
- [Autoscaling, Configmaps and Secrets](#autoscaling-configmaps-and-secrets)
  - [Step 7. Setup autoscaling for our microservice](#step-7-setup-autoscaling-for-our-microservice)
    - [Horizontal POD autoscaling](#horizontal-pod-autoscaling)
  - [Step 8. Setup Autoscaling for Cluster](#step-8-setup-autoscaling-for-cluster)
  - [Step 9. Add application configuration to microservice](#step-9-add-application-configuration-to-microservice)
    - [ConfigMaps](#configmaps)
  - [Step 10. Add password configuration for microservices](#step-10-add-password-configuration-for-microservices)
    - [Secrets](#secrets)
- [Kubernetes Deployment via YAML configuration](#kubernetes-deployment-via-yaml-configuration)
  - [Deployment.yaml file](#deploymentyaml-file)
  - [Service.yaml file](#serviceyaml-file)
  - [Applying yaml files](#applying-yaml-files)
- [GPU nodes, Deleting K8s resources](#gpu-nodes-deleting-k8s-resources)
  - [Step 11. Deploy a new microservice which needs nodes with a GPU attached.](#step-11-deploy-a-new-microservice-which-needs-nodes-with-a-gpu-attached)
  - [Step 12. Delete the microservices](#step-12-delete-the-microservices)
  - [Step 13. Delete the cluster](#step-13-delete-the-cluster)
- [Understanding Kubernetes (GKE) Cluster](#understanding-kubernetes-gke-cluster)
  - [Cluster?](#cluster)
  - [Master Node(Control Plane) Components](#master-nodecontrol-plane-components)
    - [1. API Server](#1-api-server)
    - [2. Scheduler](#2-scheduler)
    - [3. Control Manager](#3-control-manager)
    - [4. ETCD](#4-etcd)
  - [Worker Node Components](#worker-node-components)
    - [Kubelet](#kubelet)
  - [GKE Cluster Types](#gke-cluster-types)
    - [Zonal Cluster](#zonal-cluster)
    - [Regional Cluster](#regional-cluster)
    - [Private Cluster](#private-cluster)
    - [Alpha Cluster](#alpha-cluster)
- [Pods in Kubernetes?](#pods-in-kubernetes)
- [Deployments and Replicasets?](#deployments-and-replicasets)
- [Services in Kubernetes?](#services-in-kubernetes)
    - [ClusterIP](#clusterip)
    - [Load Balancer](#load-balancer)
    - [Node Port](#node-port)
- [Google Container Registry(GCR)](#google-container-registrygcr)
- [Important points](#important-points)
- [Scenarios in GKE](#scenarios-in-gke)
    - [You want to keep your costs low and optimize your GKE implementation](#you-want-to-keep-your-costs-low-and-optimize-your-gke-implementation)
    - [You want an efficient, completely auto scaling solution](#you-want-an-efficient-completely-auto-scaling-solution)
    - [You want to execute untrusted third-party code in k8s cluster](#you-want-to-execute-untrusted-third-party-code-in-k8s-cluster)
    - [You want to enable only internal communication between your microservice deployments in a kubernetes cluster](#you-want-to-enable-only-internal-communication-between-your-microservice-deployments-in-a-kubernetes-cluster)
    - [My pod stays pending](#my-pod-stays-pending)
    - [My pod stays waiting](#my-pod-stays-waiting)
- [GKE - Cluster management using gcloud cli](#gke---cluster-management-using-gcloud-cli)
    - [Create cluster](#create-cluster)
    - [Resize cluster](#resize-cluster)
    - [Autoscale cluster](#autoscale-cluster)
    - [Delete cluster](#delete-cluster)
    - [Adding node pool](#adding-node-pool)
    - [List images](#list-images)
    - [Rollback deployment](#rollback-deployment)

# Google Kubernetes Engine Introduction
## Kubernetes
- Most popular open source container orchestration solution.
- Provides cluster management (including upgrades)
  - Each cluster can have different types of virtual machines.
- Provides all important container orchestration features:
  - Autoscaling
  - Service discovery
  - Load Balancer
  - Self healing
  - Zero downtime deployments

## What is GKE?
- Managed kubernetes service
- Minimize operations with auto-repair (repair failed nodes) and auto-upgrade (use latest version of k8s always) features.
- Provides Pod and cluster Autoscaling
- Enable Cloud Logging and Cloud Monitoring with simple configuration
- Uses container-optimized OS, a hardened OS built by Google
- Provides support for Persistent disks and Local SSD.


# Creating GKE Cluster
## Create k8s cluster with the default node pool
- Enable Kubernetes Engine API
- ```gcloud container clusters create``` or use cloud console
- Choose standard cluster option.
### Standard Cluster
- You can manage cluster configuration.
- Node configuration flexibility.
- Pay per node.
### Autopilot Cluster
- New mode Operation of GKE
- Reduce your operational costs in running kubernetes clusters.
- Provides a hands-off experience.
- Pay per pod.

### Step 1. Create a k8s cluster with default node pool
- ```gcloud container clusters create```
- Using console
  - Select zonal or regional cluster
# Create Deployment and a Service
## Step 2. Login to cloud shell
```sh
gcloud auth login
```
## Step 3. Connect with the cluster
```sh
gcloud container clusters get-credentials my-cluster --zone us-central1-c --project my-gcp-project-id
```
- Now we can run kubectl commands on the connected cluster.
## Step 4. Deploy microservices to k8s
- Deploy a deployment of hello-world
  ```sh
  kubectl create deployment hello-world-rest-api --image=in28min/hello-world-rest-api:0.0.1.RELEASE
  ```
- Check deployment
  ```sh
  kubectl get deploy
  ```
- Deploy a service for the deployment
  ```sh
  kubectl expose deployment hello-world-rest-api --type=LoadBalancer --port=8080
  ```
- Check services
  ```sh
  kubectl get svc
  ```
# Explore GKE Console
- We can see master and worker nodes
## Node pool
- Multiple nodes are a part of a node pool.
- We can create multiple node pools in a k8s cluster.
- By default k8s cluster in gcp will create 1 node pool of 3 nodes in it.

## Workloads
- In workloads section in GKE console we an see what is running inside our cluster
  - Pods
  - Deployments
- We can see logs of our deployed objects.
## Services and Ingress
- We can see what services and ingress is deployed.

# Scaling Deployments and Resizing node pools
## Step 5. Increase number of instances of your microservice
```sh
kubectl scale deployment hello-world-rest-api --replicas=2
```

## Step 6. Increate number of nodes in k8s cluster
```sh
gcloud container clusters resize my-cluster --node-pool my-node-pool --num-nodes=5 --zone=us-central1-c
```

# Autoscaling, Configmaps and Secrets

## Step 7. Setup autoscaling for our microservice
### Horizontal POD autoscaling
```sh
kubectl autoscale deployment hello-world-rest-api --max=10 --cpu-percent=70
```
- Also known as HPA- Horizontal POD autoscaling 
- Get HPA
  ```sh
  kubectl get hpa
  ```
## Step 8. Setup Autoscaling for Cluster
```sh
gcloud container clusters update cluster-name --enable-autoscaling --min-nodes=1 --max-nodes=10
```

## Step 9. Add application configuration to microservice
### ConfigMaps
- Create a configmap
  ```sh
  kubectl create configmap todo-web-application-config --from-literal=RDS_DB_NAME=todos
  ```
- Get configmaps
  ```sh
  kubectl get configmap
  kubectl get configmap my-config-map
  ```
## Step 10. Add password configuration for microservices
### Secrets
- Create a k8s secret
  ```sh
  kubectl create secret generic todo-app-secrets-1 --from-literal=RDS_PASSWORD=dummypass
  ```
# Kubernetes Deployment via YAML configuration
## Deployment.yaml file
```yaml
apiVersion: apps/v1
kind: Deployment
metadata:
  name: myapp
  labels:
    app: myapp
spec:
  selector:
    matchLabels:
      app: myapp
  template:
    metadata:
      labels:
        app: myapp
    spec:
      containers:
      - name: myapp
        image: kubekode/react-todo-list-app
        ports:
          - containerPort: 80
```

## Service.yaml file
```yaml
apiVersion: v1
kind: Service
metadata:
  name: myapp
  labels:
    app: myapp 
spec:
  selector:
    app: myapp
  ports:
  - port: 80
    targetPort: 80
  sessionAffinity: None
  type: LoadBalancer
```

## Applying yaml files 
```sh
kubectl apply -f deployment.yaml
kubectl apply -f service.yaml
```

# GPU nodes, Deleting K8s resources
## Step 11. Deploy a new microservice which needs nodes with a GPU attached.
- Attach a new node pool with GPU instances to your cluster
  - ```gcloud container node-pools create POOL_NAME --cluster CLUSTER_NAME```
  - ```gcloud container node-pools list --cluster CLUSTER_NAME```
- Deploy a new microservice to the new pool by setting up nodeSelector in the deployment.yaml
    ```yaml
    nodeSelector: cloud.google.com/gke-nodepool:POOL_NAME
    ```
## Step 12. Delete the microservices
- Delete service - ```kubectl delete service```
- Delete Deploment ```kubectl delete deployment```
## Step 13. Delete the cluster
- ```gcloud container clusters delete```

# Understanding Kubernetes (GKE) Cluster
## Cluster?
- Group of Compute Engine Instances
  - Master Node(s) - Manage the cluster
  - Worker node(s) - Run the workloads(pods)
## Master Node(Control Plane) Components
### 1. API Server
- Handles all the communication for a k8s cluster (from nodes and outside)
### 2. Scheduler
- Decides placement of pods.
### 3. Control Manager
- Manages deployments & Replicasets
### 4. ETCD
- Distributed database storing the cluster state.
## Worker Node Components
- Runs your pods
### Kubelet
- Manages communication with master node(s).

## GKE Cluster Types
### Zonal Cluster
- Single zone
  - Single control place.
  - Nodes running in the same zone.
- Multi-zonal
  - Single control plane
  - Nodes are running in multiple zones
### Regional Cluster
- Replicas of control plane runs in multiple zones of the given region. 
- Nodes also run in the same zones where control plane runs.
### Private Cluster
- VPC Native cluster.
- Nodes only have internal IP Addresses.
### Alpha Cluster
- Clusters with alpha APIs
- Early feature APIs.
- Used to test new k8s features.

# Pods in Kubernetes?
- Smallest deployable unit in Kubernetes.
- A Pod container one or more containers
- Each Pod is assigned an ephemeral IP address.
- All containers in a pod share:
  - Network
  - Storage
  - IP Address
  - Ports
  - Volumes(Shared persistent disk)
- Getting all pods
  - ```kubectl get pods```
- POD statuses:
  - Running
  - Pending
  - Succeeded
  - Failed
  - Unknown

# Deployments and Replicasets?
- A Deployment is created for each microservice:
  - ```kubectl create deployment m1 --image=m1:v1```
  - Deployment represents a microservice(with all its releases)
  - Deployment manages new releases ensuring zero downtime.
- Replicaset ensures that a specific number of Pods are running for a specific microservice version.
  - ```kubectl get replicasets```
  - ```kubectl scale deployment m2 --replicas=2```
  - Even if one of the pods is killed, replicaset will launch a new  one.
- Deploy v2 of microservice - Creates a new replicaset
  - ```kubectl set image deployment m1 m1=m1:v2```
  - v2 replicaset is created
  - Deployment updates v1 replicaset and v2 replicaset based on the release strategies.

# Services in Kubernetes?
- Each pod has its own IP Address
  - How do you ensure that external users are not impacted when:
    - A pod fails and is replaced by replica set
    - A new release happens and all existing pods of old release by ones of new release
- Create service
  - ```kubectl expose deployment name --type=LoadBalancer --port=80```
### ClusterIP
- Exposes service on a cluster internal IP.
  - When you want microservices only to available inside a cluster(internal communication)
### Load Balancer
- Exposes service externally using a cloud provider's load balancer.
  - When you want to create an individual load balancer for each microservice.
### Node Port
- Exposes service on each node's IP at a static port(the NodePort)
  - You don't want to create an external Load Balancer for each microservice(You can create one ingress component to load balance multiple microservices)
- Expose all the services as nodeports
- Use ingress to route the one load balancer service nodeport services at different routes.

# Google Container Registry(GCR)
- Fully-managed container registry provided by GCP.
- (Alternative) Docker hub
- Can be integrated to CI/CD tools to publish images to registry(Cloud build)
- You can secure your container images. Analyze for vulnerabilities and enforce deployment policies.
- Naming: ```HOSTNAME/PROJECT_ID/IMAGE:TAG```
  - ```gcr.io/projectname/helloworld:1```

# Important points
- Replicate master nodes across multiple zones for high availability.
- (REMEMBER): Some CPU on the nodes is reserved by Control plane:
  - 1st core -6%, 2nd core - 1%, 3rd/4th - 0.5, Rest - 0.25
- Creating Docker images for your microservices(Dockerfile)
  - Build image: ```docker built -t image_name .```
- Kubernetes supports ```stateful``` deployments like Kafka, redis, Zookeeper.
  - ### StatefulSet
    - Set of Pods with unique, persistent identities and stable hostnames
- How do we run services on nodes for log collection or monitoring?
  - ### DaemonSet
    - One pod on every node(For background services)
- (Enabled by default) Integrates with Cloud Monitoring and Cloud Logging.
    - Cloud Logging system and application logs can be exported to BigQuery or Pub/Sub.


# Scenarios in GKE
### You want to keep your costs low and optimize your GKE implementation
- Consider Preemptible VMs, Appropriate region, commited-use discounts
- E2 machine types are cheaper than N1.
- Choose right environment to fit your workload type(Use multiple nodepools if needed)
### You want an efficient, completely auto scaling solution
- Configure Horizontal POD Auto scaler for deployments
- Cluster Autoscaler for node pools
### You want to execute untrusted third-party code in k8s cluster
- Create separate node-pool
- Create a new node pool with GKE Sandbox.
- Deploy untrusted code to sandbox node pool
### You want to enable only internal communication between your microservice deployments in a kubernetes cluster
- Create service of type ClusterIP
### My pod stays pending
- Most probably pod can't be schedule onto node(insufficient resources)
### My pod stays waiting
- Most probably failure to pull the image
- Not having enough access to pull the image from Container registry.


# GKE - Cluster management using gcloud cli
### Create cluster
```sh
gcloud container clusters create my-cluster --zone us-central1-a --node-locations us-central1-c,us-central1-b
```
### Resize cluster
```sh
gcloud container clusters resize my-cluster --node-pool my-node-pool --num-nodes=10
```
### Autoscale cluster
```sh
gcloud container clusters update cluster-name --enable-autoscaling --min-nodes=1 --max-nodes=10
```
### Delete cluster
```sh
gcloud container clusters delete my-cluster
```
### Adding node pool
```sh
gcloud container node-pools create new-node-pool-name --cluster my-cluster
```
### List images
```sh
gcloud container images list
```
### Rollback deployment
```sh
kubectl rollout undo deployment hello-world --to-revision=1
```

---