### What Kubernetes Will Do for You

> Kubernetes is managing your containers for you.
> Kubernetes is not a cloud service, it is a container orchestration tool.
> It is a collection of open-source tools.
> Create objects (e.g Pods) and manage them (e.g. scale, update, monitor, etc.)
> Monitor Pods and re-create them, scale Pods etc.
> Kubernetes utilizes the provided resources to apply your config / goals.

### What You Need To Do / Setup

1. Install **kubectl** - command line tool to interact with k8s cluster - `kubectl`
2. Setup a k8s **cluster** - can be local (minikube, kind) or cloud (GKE, EKS, AKS) - `cluster`
3. Configure kubectl to interact with your cluster - `kubeconfig`
4. Create a **deployment** configuration file (YAML) - `Node, Pod, Deployment, ReplicaSet, Namespace`
5. **Apply** the config file to create resourcces in the cluster - `kubectl apply -f <file-name>.yaml`
6. Verify the resources are created and running - `get, describe, logs`
7. Expose your application to be acessible from outside the cluster - `service`
8. Monitor and manage your application - `dashboard`
9. Scale your application as needed - `horizontal pod autoscaler`
10. Update your application with zero downtime - `rolling updates`
11. Clean up resources when no longer needed - `delete resources`
12. Set up persistent storage if your application needs to store data. - `PV, PVC and Storage Classes`

-   Create the Cluster and the Node instances (Worker + Master)
-   Setup API server, kubelet, kube-proxy and other k8s services/software on the nodes
-   Create other (cloud) provider resoures that might be needed (e.g. Load Balancer, VPC, FileSystems, etc.)

```bash
# 1. install minikube
# 2. install OrbStack (Docker Desktop alternative for Mac)
minikube start --driver=docker # start minikube with docker driver
minikube status # check minikube status
minikube dashboard # open k8s dashboard
minikube stop # stop minikube
minikube delete # delete minikube cluster
```

### Imperative vs Declarative

-   Imperative - you tell k8s what to do step by step using commands (e.g. `kubectl create deployment`, `kubectl scale deployment`, etc.)
-   Declarative - you define the desired state of your application using configuration files (YAML) and apply them using `kubectl apply -f <file-name>.yaml`

`1.` Imperative Approach

```bash
docker tag <image_id> <repository>:<tag> # tag an image
docker push <repository>:<tag> # push an image to repository
kubectl create deployment <deployment_name> --image=<repository>:<tag> # create a deployment
kubectl get deployments
```

`2.` Declarative Approach

```bash
kubectl apply -f <file_name>.yaml
```
