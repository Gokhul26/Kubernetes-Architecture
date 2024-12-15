# Kubernetes-Architecture

![k8s archi](https://github.com/user-attachments/assets/75a57a6a-a1e2-4414-a2e9-1387ab6b0d3a)



# Project Title

1. Kubernetes (K8s) is a open-source powerful container orchestration platform that automates the deployment, scaling, and management of containerized applications. Kubernetes is written in Go Lang.


Node:  
A Node is a physical or virtual machine in the Kubernetes cluster that runs the necessary services to execute the application workloads. Each node in the cluster is managed by the control plane and contains the necessary components to run pods and support their lifecycle.

Pod:  
A Pod is the smallest deployable unit in Kubernetes. It represents a single instance of a running process in your cluster.  

Each pod encapsulates:  
One or more containers (e.g., Docker containers).  
Storage resources (e.g., persistent volumes).  
A unique network IP address.  
Specifications for how to run the containers (e.g., environment variables, ports).

### **Key Differences:**

| **Aspect**             | **Node**                                        | **Pod**                                       |
|------------------------|-------------------------------------------------|-----------------------------------------------|
| **Definition**          | A physical or virtual machine in the cluster.   | The smallest deployable unit containing one or more containers. |
| **Role**                | Provides the infrastructure for running pods.   | Encapsulates the application containers running on the node. |
| **Components**          | Includes the Kubelet, Kube-proxy, and container runtime. | Contains application containers and shared resources (network, storage). |
| **Resource Management** | Manages CPU, memory, storage, and other hardware resources. | Uses resources from the node to run containers. |
| **Lifecycle**           | A node remains in the cluster until it is removed. | Pods are short-lived, typically managed by controllers like Deployments or ReplicaSets. |
| **Communication**       | Nodes communicate with the control plane and other nodes. | Pods communicate within the node and can expose services to other pods or nodes. |
| **Scaling**             | Nodes scale by adding or removing physical/virtual machines. | Pods scale by increasing the number of replicas in a deployment or replica set. |

---


Cluster:  
A cluster is a collection of nodes (physical or virtual machines) that work together to run and manage containerized applications.A Kubernetes cluster consists of at least one main (control) plane, and one or more worker machines, called nodes. Both the control planes and node instances can be physical devices, virtual machines, or instances in the cloud.

Kubernetes architecture comprises two major layers:

Control Plane: Responsible for managing the cluster and making global decisions.
Worker Nodes: Execute containerized applications.


2. Control Plane
The control plane is the brain of the Kubernetes cluster and manages the entire system. It runs on one or more master nodes.

Key Components of the Control Plane:

    1. API Server (kube-apiserver):
        ◦ The entry point for all Kubernetes API requests. 
        ◦ Acts as the interface for users, external systems, and the control plane components. 
        ◦ Handles authentication, validation, and updates to the cluster's state (stored in etcd). 
        ◦ It validates and processes the requests made via API.
    2. Etcd:
        ◦ A distributed key-value store used for storing the cluster's state and configuration data. 
        ◦ Ensures consistency across the cluster. 
        ◦ Examples of stored data: Pod specifications, secrets, and service configurations. 
    3. Controller Manager (kube-controller-manager):
        ◦ It is controller of controllers.It validates and processes the requests made via API.
        ◦ Runs various controllers to maintain the desired state of the cluster. 
        ◦ Examples of controllers: 
            ▪ Node Controller: Monitors the health of worker nodes. 
            ▪ Replication Controller: Ensures the desired number of pod replicas are running. 
            ▪ Endpoint Controller: Manages service endpoints. 
            ▪ Job Controller: Manages batch jobs. 
            • ReplicaSet: Ensures a defined number of pods are running. 
            • Deployment: Manages ReplicaSets and supports features like rolling updates.             
            • DaemonSet: Ensures one copy of a pod runs on each node. 
            • CronJob: Runs jobs on a scheduled basis. 
    4. Scheduler (kube-scheduler):
        ◦ It schedules pods to worker nodes.
        ◦ It watches api-server for newly created Pods with no assigned node, and selects a healthy node for them to run on.
        ◦ If there are no suitable nodes, the pods are put in a pending state until such a healthy node appears.
        ◦ It watches API Server for new work tasks. 
    5. Cloud Controller Manager:
        ◦ Integrates Kubernetes with cloud provider APIs (e.g., AWS, GCP, Azure). 
        ◦ Manages tasks like load balancers, storage, and cloud-specific networking. 
        ◦ The cloud-controller-manager only runs controllers that are specific to your cloud provider
        ◦ Cloud controller lets you link your cluster into cloud provider’s API, and separates out the components that interact with that cloud platform from components that only interact with your cluster


3. Worker Nodes
Worker nodes are where the actual application workloads run. Each node has the following components:
Key Components of a Worker Node:

    1. Kubelet:
        ◦ The primary agent running on each worker node. 
        ◦ Communicates with the control plane to ensure the specified pods are running. 
        ◦ Monitors pod health and reports the node's status back to the control plane. 
    2. Container Runtime:
        ◦ The container runtime is the software that is responsible for running containers (in Pods).
        ◦ To run the containers, each worker node has a container runtime engine.
        ◦ It pulls images from a container image registry and starts and stops containers.
        ◦ Common runtimes: Docker, containerd, CRI-O. 
    3. Kube-Proxy:
        ◦ Manages networking on each node. 
        ◦ Implements rules to handle traffic routing, forwarding, and load balancing for Kubernetes services. 
        ◦ It ensure each Pod gets unique IP address.It makes possible that all containers in a pod share a single IP.
    4. Pods:
        ◦ The smallest deployable unit in Kubernetes. 
        ◦ A pod contains one or more tightly coupled containers, sharing storage, network, and configuration. 

6. Workflow:
 How Kubernetes Works

    1. User Interaction:
        ◦ A user interacts with Kubernetes using the CLI (kubectl) or a web UI (Dashboard) to submit API requests. 
    2. API Server:
        ◦ Validates the request and updates the cluster state in etcd. 
    3. Scheduler:
        ◦ Assigns pods to worker nodes based on resource requirements. 
    4. Kubelet:
        ◦ Receives instructions from the API server and ensures the containers in the pod are running. 
    5. Networking:
        ◦ Kube-proxy handles communication between services, pods, and external clients. 

---

### **Components of Control Plane and Their Communication with Data Plane**

| **Control Plane Component**  | **Interacts with Data Plane Component(s)**  | **Purpose**                                                                                                                                      |
|-------------------------------|---------------------------------------------|--------------------------------------------------------------------------------------------------------------------------------------------------|
| **API Server**               | - **Kubelet** on worker nodes, Pods, Node components (kube-proxy) | - Serves as the central communication hub. Kubelet communicates with the API server to report node status and receive pod specifications. Pods and services communicate with the API server for resource management. |
| **Scheduler**                | - **Nodes**                                 | - Selects the most appropriate node for deploying a pod by evaluating resource availability and constraints. Sends scheduling decisions to the API server. |
| **Controller Manager**       | - **Pods**, **Nodes**, and other resources via API Server | - Ensures the desired state of resources like ReplicaSets, Endpoints, or Service accounts by managing their lifecycle. Communicates with the data plane indirectly via the API Server. |
| **etcd**                     | - None directly                             | - Stores cluster state and configuration, indirectly affecting data plane operations. |
| **Cloud Controller Manager** | - Cloud-based node VMs and load balancers   | - Integrates with cloud provider APIs for managing infrastructure resources such as nodes, storage, and networking. |

---

### **Data Plane Components and Their Interaction with Control Plane**

| **Data Plane Component** | **Communicates with Control Plane Component(s)** | **Purpose**                                                                                                                   |
|---------------------------|-------------------------------------------------|-----------------------------------------------------------------------------------------------------------------------------|
| **Kubelet**              | - **API Server**                                 | - Fetches pod specifications. Reports node status and health. Watches for events and ensures pods are running as expected. |
| **kube-proxy**           | - **API Server**                                 | - Watches the API server for updates to services and endpoints. Configures network rules to route traffic to appropriate pods. |
| **Pods/Containers**      | - **API Server** (via kubelet)                   | - Receives configuration data (e.g., environment variables) via the kubelet. Communicates indirectly with other pods via services. |
| **Nodes**                | - **Scheduler**                                 | - Receives new pod placement decisions via the API Server. Reports resource availability through the kubelet. |

---



---


### **Example Workflow:**

1. The **Kubernetes Scheduler** places a **pod** on an appropriate **node** based on available resources.
2. The **Kubelet** on the **node** manages the lifecycle of the pod, including starting the containers within the pod.
3. The **pod**'s containers communicate with each other over localhost, sharing network and storage resources.
4. If a pod fails, the **control plane** ensures that the pod is rescheduled to another **node**.

---
