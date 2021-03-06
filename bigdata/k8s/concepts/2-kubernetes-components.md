# Kubernetes Components

## Master Components
Master components provide the cluster's control page. Master components make global decisions about the cluster(for example, scheduling), and detecting and responding to cluster events(starting up a new pod when a replication controller's `replicas` field is unsatisfied). 
Master components can be run on any machine in the cluster. However, for simplicity, set up scripts typically start all master components on the same machine, and do not run user containers on this machine. 

### kube-apiserver
Component on the master that exposes the Kubernetes API. It is the front-end for the kubernetes control pane. 
It is designed to scale horizontally - that is, it scales by deploying more instances. See [Building High-Availability Clusters](https://kubernetes.io/docs/admin/high-availability/).

### etcd
Consistent and highly-available key-value store used as Kubernetes' backing store for all cluster data. 

### kube-scheduler
Component on the master that watches newly created pods that have no node assigned, and selects a node for them to run on.

Factors taken into account for scheduling decisions include individual and collective resource requirements, hardware/software/policy constraints, affinity and anti-affinity specifications, data locality, inter-workload interface and deadlines.

### kube-controller-manager
Component on the master that runs [controllers](https://kubernetes.io/docs/reference/command-line-tools-reference/kube-controller-manager/).
Logically, each controller is a separate process, but to reduce complexity, they are all compiled into a single binary and run in a single process. 
These controllers include:
+ Node Controller: Responsible for noticing and responding when nodes go down. 
+ Replication Controller: Responsible for maintaining the correct number of pods for every replication controller object in the system. 
+ Endpoints Controller: Populates the Endpoints object(that is, joins Services & Pods).
+ Service Account & Token Controllers: Create default accounts and API access tokens for new namespaces.
### cloud-controller-manager
[cloud-controller-manager](https://kubernetes.io/docs/tasks/administer-cluster/running-cloud-controller/) runs controllers that interact with the underlying cloud providers. 
cloud-controller-manager runs cloud-provider-specific controller loops only. You must disable these controller loops in the kube-controller-manager. You can disable the controller loops by setting the **--cloud-provider** flag to **external** when starting the kube-controller-manager.
The following controllers have cloud provider dependencies:
+ Node Controller: For checking the cloud provider to determine if a node has been deleted in the cloud after it stops responding
+ Route Controller: For setting up routes in the underlying cloud infrastructure
+ Service Controller: For creating, updating and deleting cloud provider load balancers
+ Volume Controller: For creating, attaching, and mounting volumes, and interacting with the cloud provider to orchestrate volumes

----------
Need to know how to configure cloud-controller-manager, and how cloud-controller-manager works with Kubernetes.
----------

## Node Components
Node components run on every node, maintaining running pods and providing the Kubernetes runtime environment.
### kubelet
An agent that runs on each node in the cluster. It makes sure that contains are running in a pod. The kubelet doesn't manage containers which were not created by Kubernetes.

### kube-proxy
[kube-proxy](https://kubernetes.io/docs/admin/kube-proxy/) enables the Kubernetes service abstraction by maintaining network rules on the host and performing connection forwarding. 
As I know, this is built atop iptables. 

### Container Runtime
The container runtime is the software that is responsible for running containers. Kubernetes supports several runtimes: [Docker](http://www.docker.com/), [rkt](https://coreos.com/rkt/), [runc](https://github.com/opencontainers/runc) and any OCI [runtime-spce](https://github.com/opencontainers/runtime-spec) implementation. 

P.s. Maybe I should add some notes on LXC(Linux Container).













## Addons
