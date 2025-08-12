### Install azure cli
- https://learn.microsoft.com/en-us/cli/azure/install-azure-cli-windows?tabs=azure-cli
- az --version # to check cli version
- Install azure aks cli
- az aks install-cli # run from command prompt or powershell
- kubectl version
- Then onpen visual studio code and run the commands from powershell terminal

### Azure CLI command
- az login --tenant "6c497b5d-9c1b-4c0b-bb4a-58ec11153372"
- Get-AzContext
- az --version
- kubectl version

## AKS Introduction
- AKS is highly available, secure and fully Azure managed Kubernetes Service
- As on today available in 36 regions and growing. 
- When compared to other cloud providers, AKS is the one which is available in highest number of regions
- Will be able to run any type of workloads 
	- Windows based applications like .Net Apps 
	- Linux supported applications like Java
	- IOT device deployment and management on demand
	- Machine Learning Model training with AKS

## AKS Architecture - Master
<img width="477" height="556" alt="image" src="https://github.com/user-attachments/assets/3656cba7-e285-400f-ae8b-5222960aa435" />

### kube-apiserver
- It acts as front end for the Kubernetes control plane. It exposes the Kubernetes API
- It is the central management point for the Kubernetes cluster. It exposes the Kubernetes API, allowing users and other components to interact with the cluster.
### etcd
- Consistent and highly-available key value store used as Kubernetes’ backing store for all cluster data.
- It stores all the masters and worker node information. 
### kube-scheduler
- Responsible for scheduling pods (the smallest deployable units in Kubernetes) onto the available nodes in the cluster based on resource requirements
- It watches for newly created Pods with no assigned node, and selects a node for them to run on
### kube-controller-manager
Controllers are responsible for noticing and responding when nodes, containers or endpoints go down. They make decisions to bring up new containers in such cases. 
- Node Controller:
  - Responsible for noticing and responding when nodes go down. 
- Replication Controller:
   - Responsible for maintaining the correct number of pods for every replication controller object in the system.
- Endpoints Controller:
  - Populates the Endpoints object (that is, joins Services & Pods)
- Service Account & Token Controller:
  -  Creates default accounts and API Access for new namespaces.
### cloud-controller-manager
- A Kubernetes control plane component that embeds cloud-specific control logic. 
- It only runs controllers that are specific to your cloud provider. 
- On-Premise Kubernetes clusters will not have this component. 
- Node controller:
  - For checking the cloud provider to determine if a node has been deleted in the cloud after it stops responding
- Route controller:
  - For setting up routes in the underlying cloud infrastructure
- Service controller:
  - For creating, updating and deleting cloud provider load balancer

## AKS Architecture - Worker Node
<img width="477" height="255" alt="image" src="https://github.com/user-attachments/assets/133f937c-b474-45ed-9ab1-b1d32b393cb0" />

### Container Runtime
- Container Runtime is the underlying software where we run all these Kubernetes components. 
- We are using Docker, but we have other runtime options like rkt, container-d etc.
### Kubelet
- Kubelet is the agent that runs on every node in the cluster
- This agent is responsible for making sure that containers are running in a Pod on a node.
### Kube-Proxy
- It is a network proxy that runs on each node in your cluster.
- It maintains network rules on nodes
- In short, these network rules allow network communication to your Pods from network sessions inside or outside of your cluster.

```
## Create one aks cluster using azure portal
az aks get-credentials --resource-group kubernetes-poc --name aks-poc-ne001
kubectl get nodes -o wide 

# List Namespaces
kubectl get namespaces
kubectl get ns

# List Pods from all namespaces
kubectl get pods --all-namespaces

# List all k8s objects from Cluster Control plane
kubectl get all --all-namespaces

	#Create a folder kube-manifest
	#Create 2 files – deployment.yml and loadbalancer.yml


# Deploy Application
kubectl apply -f kube-manifests/

# Verify Pods
kubectl get pods

# Verify Deployment
kubectl get deployment

# Verify Service (Make a note of external ip)
kubectl get service

# Access Application
http://<External-IP-from-get-service-output>

# Delete Applications
kubectl delete -f kube-manifests/
```
