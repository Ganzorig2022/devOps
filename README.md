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

### 3.1 Stateless vs Stateful applications

> Stateless applications do not save client data generated in one session for use in the next session with that client. e.g deployments, services, etc.
> Stateful applications save client data from one session for use in the next session with that client. e.g databases, etc. Each time a POD is terminated, a new POD is created and needs to pick up the saved state.

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
kubectl get services
kubectl describe <object>: detailed information about an object
```

Architecture
From Larger to Smaller:

-   Clusters and Control Planes
-   Nodes
-   Pods

Cheat Sheet:

-   Kubernetes **Cluster**: set of connected computers (Nodes) configured to run K8s
-   Kubernetes **Control Plane** : manages the Nodes in a Cluster
-   Kubernetes **Nodes**: AKA “worker machines”, running Linux and container engine
-   Kubernetes **Pods**: set of one or more containers, smallest deployable unit
-   Kubernetes **Services**: a resource for exposing network connectivity, required to connect to Pods from outside, and for communication between Pods

### 3.2 Scaling

```bash
kubectl get pods --namespace=monitoring
kubectl scale deployment <deployment_name> --replicas <number_of_replicas>
kubectl apply -f 01_first_manifest.yml # return "deployment.apps/nginx-deployment created"
kubectl scale deployment nginx-deployment --replicas 10 # scale UP to 10 replicas (10 pods)
kubectl get pods # check the number of pods
kubectl scale deployment nginx-deployment --replicas 3 # scale DOWN to 3 replicas (3 pods)
```

### 3.3 Kubernetes Storage (such as s3-service)

> Persistent Volumes (PVs). Maintained in parallel to Pods.
> PVs are mapped to Pods using Persistent Volume Claims (PVCs).
> A mapped PV allows data persistence when the Pod is stopped or deleted.

#### 3 only objects are needed to make storage work:

-   Storage Class (SC) - defines different types of storage (e.g SSD, HDD etc)
-   Persistent Volume (PV)
-   Persistent Volume Claim (PVC)

```bash
kubectl get sc # list all storage classes
kubectl get pv # list all persistent volumes
kubectl get pvc # list all persistent volumes claims
```

`Storage Class example`

```yaml
apiVersion: storage.k8s.io/v1
kind: StorageClass
metadata:
    name: standard # which storage class to use
provisioner: kubernetes.io/no-provisioner
reclaimPolicy: Delete
```

`PersistentVolume example`

```yaml
apiVersion: v1
kind: PersistentVolume
metadata:
    name: my-pv
spec:
    capacity:
        storage: 5Gi
    accessModes:
        - ReadWriteOnce
    storageClassName: standard # which storage class to use
    local:
        path: /data/s3_data
```

`PersistentVolumeClaim example`

```yaml
apiVersion: v1
kind: PersistentVolumeClaim
metadata:
    name: my-pvc
spec:
    accessModes:
        - ReadWriteOnce
    resources:
        requests:
            storage: 5Gi
    storageClassName: standard
```

`Pod Usage example`

```yaml
apiVersion: v1
kind: Pod
metadata:
    name: my-pod
spec:
    containers:
        - name: my-container
          image: nginx
          volumeMounts:
              - mountPath: /data
                name: my-storage
    volumes:
        - name: my-storage
          persistentVolumeClaim:
              claimName: my-pvc
```
