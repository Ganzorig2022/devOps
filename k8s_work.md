### What Kubernetes Will Do for You

> Kubernetes is managing your containers for you.
> Kubernetes is not a cloud service, it is a container orchestration tool.
> It is a collection of open-source tools.

### What You Need To Do / Setup

1. Install **kubectl** - command line tool to interact with k8s cluster
2. Setup a k8s **cluster** - can be local (minikube, kind) or cloud (GKE, EKS, AKS)
3. Configure kubectl to interact with your cluster - **kubeconfig**
4. Create a **deployment** configuration file (YAML) - **manifests, config files**
5. **Apply** the config file to create resourcces in the cluster - `kubectl apply -f <file-name>.yaml`
6. Verify the resources are created and running - `get, describe, logs`
7. Expose your application to be acessible from outside the cluster - **service**
8. Monitor and manage your application - **dashboard**
9. Scale your application as needed - **horizontal pod autoscaler**
10. Update your application with zero downtime - **rolling updates**
11. Clean up resources when no longer needed - **delete** resources
12. Set up persistent storage if your application needs to store data. - **PV, PVC and Storage Classes**