# 1. Virtualization and containerization

Use Cases of containerization

-   Microservice architecture
-   Container orchestration
-   Automation of pipelines

# 2. Container orchestration with Kubernetes (K8s)

-   Pods - smallest, deployable unit
-   Nodes - smallest hardware unit. Nodes have pods. Is a group of one or more pods.
-   Control Plane - AKA brain of a Kubernetes cluster, manages nodes and pods.

Docker Instructions

```yaml
FROM <name_of_image>
FROM pyhton:3.10

# COPY - copies files or directories from source to destination.
COPY <source> <destination>
# Copy files/folders to the main folder od the container
COPY . .
RUN - runs a command within a container
RUN <command>
RUN npm install

ENTRYPOINT - defines container’s default behavior
ENTRYPOINT  [“command”, “argument”]
ENTRYPOINT  [“python”, “hello_world.py”]
```

Docker build command

builds docker image from a dockerfile
dockerfile needs to be located in build’s context

```bash
docker build <context>
```

# 3. Kubernetes. Container orchestration with Kubernetes (K8s)
Kubernetes interacts with Docker to schedule and maintain containers.

-   Pods - smallest, deployable unit
-   Nodes - smallest hardware unit. Nodes have pods. Is a group of one or more pods.
-   Control Plane - AKA brain of a Kubernetes cluster, manages nodes and pods.

```bash
kubectl version # check the client version
kubectl create -f <manifest.yaml> # create new objects, with -f for "filename"
kubectl apply -f <file_name>.yml # Applying configuration. Create deployment if it doesn't exist, update it if it does.
kubectl get <object>: overview about objects deployed on Kubernetes
kubectl get pods
kubectl get nodes
kubectl describe <object>: detailed information about an object
```

Architecture
From Larger to Smaller:

-   Clusters and Control Planes
-   Nodes
-   Pods

Cheat Sheet:

-   Kubernetes Cluster: set of connected computers (Nodes) configured to run K8s
-   Kubernetes Control Plane: manages the Nodes in a Cluster
-   Kubernetes Nodes: AKA “worker machines”, running Linux and container engine
-   Kubernetes Pods: set of one or more containers, smallest deployable unit
-   Kubernetes Services: a resource for exposing network connectivity, required to connect to Pods from outside, and for communication between Pods
