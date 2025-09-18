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

> Each config file has 3 main parts:

1. **metadata** - name, labels, etc
2. **spec** - specification of the desired state of the object
3. **status** - automatically filled by k8s, current state of the object

### 3.1 Stateless vs Stateful applications

> Stateless applications do not save client data generated in one session for use in the next session with that client. e.g deployments, services, etc.
> Stateful applications save client data from one session for use in the next session with that client. e.g databases, etc. Each time a POD is terminated, a new POD is created and needs to pick up the saved state.

### Kubernetes Architecture

Kubernetes interacts with Docker to schedule and maintain containers.

-   Pods - smallest, deployable unit
-   Nodes - smallest hardware unit. Nodes have pods. Is a group of one or more pods.
-   Control Plane - AKA brain of a Kubernetes cluster, manages nodes and pods.

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

```bash
kubectl version # check the client version
kubectl create -f <manifest.yaml> # create new objects, with -f for "filename"
kubectl apply -f <file_name>.yml # Applying configuration. Create deployment if it doesn't exist, update it if it does.
kubectl delete -f <file_name>.yml # delete objects defined in a file
kubectl get <object>: overview about objects deployed on Kubernetes
kubectl get pods
kubectl get nodes # kubectl get no
kubectl get services # kubectl get svc
kubectl get namespace # kubectl get ns
kubectl get ingress -n <namespace_name> # get ingress in a specific namespace
kubectl get all -n <namespace_name> # get all objects in a specific namespace
kubectl describe <object>: detailed information about an object
```

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

-   Storage Class (SC) - First: defines different types of storage (e.g SSD, HDD etc)
-   Persistent Volume Claim (PVC) - Second: creates a claim to a specific storage class
-   Persistent Volume (PV) - Third: it will be created automatically when a PVC is created

```bash
kubectl get sc # list all storage classes
kubectl get pv # list all persistent volumes
kubectl get pvc # list all persistent volumes claims
kubectl apply -f 01_pods.yml -f 02_pvc.yml # create pods and pvc
kubectl describe pvc | grep "Used By" <pod_name> # check which pod is using the PVC
kubectl get pvc -n datacamp # check pvc in a specific namespace
```

`Storage Class example:`

```yaml
apiVersion: storage.k8s.io/v1
kind: StorageClass
metadata:
    name: standard # which storage class to use
provisioner: kubernetes.io/no-provisioner
reclaimPolicy: Delete
```

`PersistentVolume example:`

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

`PersistentVolumeClaim example:`

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

`Pod Usage example:`

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

### 3.4 Networking and Load Balancing

> Load balancers distribute incoming network load over Pods evenly.
> Ingress objects are used to route HTTP and HTTPS requests (traffic) from outside the cluster to services in the cluster.
> Ingress rules define which requests are served by which service.
> Typically, used in combination with load balancing.

`Creating a Service`

1. Declare the Pods to be attached to your Service. Don't forget to define appropiriate labels to be attached to the Pods.
2. Deploy the Pods.
3. Declare the Service, specifying the selector to match the labels of the Pods.
4. Deploy the Service.

```bash
cat *.yml
kubectl apply -f 01_pods.yml -f 02.service.yml
kubectl describe service <service_name> # get detailed information about a service
kubectl get deployments # get all deployments
kubectl scale deployment <deployment_name> --replicas 5 # scale up to 5 replicas
kubectl describe service <service_name> # check the endpoints of the service (should be 5)
```

#### Creating Application

> Configure → Deploy → Expose
> Configure (secret, config)

`1.`. Create a secret file yaml
`2.`. Create a configmap file yaml (ConfigMaps hold non-sensitive config (e.g., env vars, config files) for injection into pods.)
`3.`. Create a deployment file yaml
`4.`. Create a service file yaml (nodePort, clusterIp, loadBalancer (traefik))
`5.`. Create an ingress file yaml (routing rules, domain, tls)

```bash

```

### 3.5 Namespaces

> Namespaces > Secret > ConfigMap > Deployment > Service > Ingress
> Organize resources (clusters) in namespaces (like folders on a computer)
> Each namespaces must have its own set of resources (pods, services, deployments, configmaps, secrets, etc)

`1.` Default - for objects with no namespace
`2.` kube-system - for objects created by the system
`3`. kube-public - for objects that should be visible and readable by all users (including those not authenticated). This namespace is mostly reserved for cluster usage, in case that some resources should be visible and readable publicly throughout the whole cluster.

##### Access service in another namespace?

-   <service_name>.<namespace> (e.g customer-service.card-system)

```bash
kubectl get namespaces # list all namespaces
kubectl apply -f <file_name>.yml --namespace <namespace_name> # create objects in a specific namespace
kubectl get pods --namespace <namespace_name> # list all pods in a specific namespace
kubectl create namespace <namespace_name> # or create a new namespance
kubectl config set-contect --current --namespace=<namespace_name> # change the active namespace
kubectl get all --namespace <namespace_name> # get all objects in a specific namespace
```

### 3.6 Service

`Why we need a Service?`

-   Stable IP address and DNS to access a set of Pods
-   Load balancing
-   Loose coupling
-   Within & outside the cluster

`Different types of Services`

-   ClusterIP - default (e.g Pod to Pod communication)
-   NodePort - exposes the service on each Node's IP at a static port (e.g external access to a service)
-   Load Balancer

> Pods are mortal, Services are immortal. Pods are identified via selectors ("selector")
> Each pod has a Service, but a Service can have multiple Pods.

```yaml
apiVersion: v1
kind: Service
metadata:
    name: <service_name>
spec:
    selector:
        app: <pod_label>
```

```yaml
labels:
    app: <pod_label>
```

`Service Endpoints`

> K8s creates Endpoint object
> Same name as the Service
> Keeps track of the which Pods are members/endpoints of the Service

> Service port is different from Pod port
> targetPort must match containerPort where the pod is listening and the service is forwarding the traffic to

`Here is the scenario:`

`1.` Client sends request to `NodeIP:NodePort` (externally, e.g from web browser, etc) - NodePort, typically 30000–32767

`2.` Node forwards the request to `ClusterIP:ServicePort` (internally. spec.ports.port)

`3.` Service forwards the request to `PodIP:targetPort` (internally. spec.ports.targetPort)

`4.` Pod receives the request on `containerPort` (internally. spec.containers[].ports.containerPort)

### 3.7 Ingress

> Ingress is an API object that manages external access to the services in a cluster, typically HTTP.

```bash
kubectl apply -f <ingress_name>.yml
kubectl get ingress -n <namespace_name>
sudo nano /etc/hosts # add the domain name and IP address of the minikube. e.g 192.168.1.10 myapp.example.com
minikube tunnel # start the tunnel to access ingress from outside the cluster

```

`Ingress service example:`

```yaml
apiVersion: networking.k8s.io/v1
kind: Ingress
metadata:
    name: <ingress_name>
    annotations:
spec:
    rules:
        - host: dev-vendor.qpay.mn
          http:
              paths:
                  - path: /v2
                    pathType: ImplementationSpecific
                    backend:
                        service:
                            name: qpay-vendor-service
                            port:
                                name: http
status:
    loadBalancer: {}
```
### 3.7 Deploying images in K8s from private Docker repository

> (Namespace > Secret > ConfigMap > Deployment > Service > Ingress)
> A Docker repository is where container images are stored, like Docker Hub (public or private)

`Types of Docker repositories:`

-   DockerHub - public and private repos
-   AWS ECR - private repos
-   GCR - private repos (Google Cloud)
-   Self-hosted registry - private repos

`1. Creating docker registry file yaml:`

```yaml
kind: Secret
apiVersion: v1
metadata:
    name: registrypullsecret
    namespace: general
    uid: e7c906fc-17c2-4a84-b963-2be400b618af
    resourceVersion: '1561411'
    creationTimestamp: '2025-03-13T09:35:36Z'
    managedFields:
        - manager: dashboard
          operation: Update
          apiVersion: v1
          time: '2025-03-13T09:35:36Z'
          fieldsType: FieldsV1
          fieldsV1:
              f:data:
                  .: {}
                  f:.dockerconfigjson: {}
              f:type: {}
data:
    .dockerconfigjson: >-
        eyJhdXRocyI6eyJodHRwczovL2dpdC5xcGF5Lm1uOjUwMDUiOnsidXNlcm5hbWUiOiJnb21ib2RvcmoiLCJwYXNzd29yZCI6IjVGWU5XNDNacU5tRkxBRFUyWmpfIiwiYXV0aCI6IloyOXRZbTlrYjNKcU9qVkdXVTVYTkROYWNVNXRSa3hCUkZVeVdtcGYifX19
type: kubernetes.io/dockerconfigjson
```

`2. Create Deployment with Private Image using the secret:`

```yaml
apiVersion: apps/v1
kind: Deployment
metadata:
    name: qpay-sms-admin-service
    namespace: qpay-sms
spec:
    replicas: 2
    selector:
        matchLabels:
            app: qpay-sms-admin-service
    template:
        metadata:
            labels:
                app: qpay-sms-admin-service
        spec:
            containers:
                - name: qpay-sms-admin-service
                  image: git.qpay.mn:5005/merchant/sms-admin-service:dev # Private image
                  ports:
                      - name: server-port
                        containerPort: 3000
                        protocol: TCP
                  imagePullPolicy: Always
            restartPolicy: Always
            imagePullSecrets:
                - name: registrypullsecret # References the Secret
```

<!-- All commands -->

```bash
kubectl apply -f namespace.yaml
kubectl apply -f registrypullsecret.yaml
kubectl apply -f deployment.yaml
kubectl apply -f service.yaml
kubectl apply -f ingress.yaml  # Optional
```

```bash
cd /app/repos/sms-admin-service && git rev-parse --abbrev-ref HEAD && git reset --hard origin/$(git rev-parse --abbrev-ref HEAD) && git pull
cd /app/repos/sms-admin-service && docker build --network=host --add-host=git.qpay.mn:192.168.100.47 -t git.qpay.mn:5005/merchant/sms-admin-service:dev -f deployment/Dockerfile .
docker push git.qpay.mn:5005/merchant/sms-admin-service:dev
kubectl --namespace qpay-sms rollout restart deployment qpay-sms-admin-service
```

### 3.8 Securing cluster with RBAC (Role-Based Access Control)

> Link ServiceAccount to Role or ClusterRole via RoleBinding or ClusterRoleBinding (order matters!!!)
> For RBAC, a ServiceAccount acts like a bridge between resources (e.g Pod) and a Role or ClusterRole.

#### Visualizing RBAC

`Analogy: Imagine a building (Kubernetes cluster):`

```text
- Rooms: Namespaces (e.g., my-app).
- Role: A key for one room, allowing specific actions (e.g., “view pods”).
- ClusterRole: A master key for all rooms.
- RoleBinding: Giving the room key to a person (ServiceAccount or user).
- ClusterRoleBinding: Giving the master key to an admin.
```

`1.` Role

> With Role, you can define namespaced permissions. (entire namespace, typically for developers)
> Bound to (be certain) a specific namespace.
> What resources in that namespace you can access and what actions you can perform on those resources.

`Role configuration example:`

```yaml
apiVersion: rbac.authorization.k8s.io/v1
kind: Role
metadata:
    namespace: dev
    name: pod-reader
rules:
    - apiGroups: [''] # '' indicates the core API group
      resources: ['pods'] # specify the resource you want to access (e.g pods, services, deployments, etc)
      verbs: ['get', 'watch', 'list'] # specify the actions you can perform on the resource (e.g get, watch, list, create, update, delete)
      resourceNames: ['pod_name'] # optional: specify the names of the resources you want to access
```

`2.` ClusterRole

> With ClusterRole, you can define cluster-wide permissions. (entire cluster, typically for admins)
> Not bound to a specific namespace.
