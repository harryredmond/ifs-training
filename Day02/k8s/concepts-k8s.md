# Kubernetes concepts

- Kubernetes is a container management system

- It runs and manages containerized applications on a cluster

--

- What does that really mean?

---

## What can we do with Kubernetes?

- Let's imagine that we have a 3-tier e-commerce app:

  - web frontend

  - API backend

  - database (that we will keep out of Kubernetes for now)

- We have built images for our frontend and backend components

  (e.g. with Dockerfiles and `docker build`)

- We are running them successfully with a local environment

  (e.g. with Docker Compose)

- Let's see how we would deploy our app on Kubernetes!

---


## Basic things we can ask Kubernetes to do

--

- Start 5 containers using image `demo/backend:v1.0`

--

- Place an internal load balancer in front of these containers

--

- Start 10 containers using image `demo/frontend:v1.3`

--

- Place a public load balancer in front of these containers

--

- It's Black Friday (or Christmas), traffic spikes, grow our cluster and add containers

--

- New release! Replace my containers with the new image `demo/frontend:v1.4`

--

- Keep processing requests during the upgrade; update my containers one at a time

---

## Other things that Kubernetes can do for us

- Autoscaling

  (straightforward on CPU; more complex on other metrics)

- Resource management and scheduling

  (reserve CPU/RAM for containers; placement constraints)

- Advanced rollout patterns

  (blue/green deployment, canary deployment)

---

## More things that Kubernetes can do for us

- Batch jobs

- Fine-grained access control


- Stateful services


- Automating complex tasks with *operators*


---

class: pic

![that one is more like the real thing](images/cluster01.png)

---

class: pic

![that one is more like the real thing](images/cluster02.png)

---

class: pic

![that one is more like the real thing](images/cluster04.png)

---

class: pic

![that one is more like the real thing](images/cluster05.png)

---

class: pic

# Kubernetes Architecture

![that one is more like the real thing](images/k8s-arch2.png)


---

## Kubernetes Architecture: The Worker Nodes

- The nodes executing our containers run a collection of services:

  - a container Engine (typically Docker)

  - kubelet (the "node agent")

  - kube-proxy (a necessary but not sufficient network component)

- Nodes were formerly called "minions"

  (You might see that word in older articles or documentation)

---

## Kubernetes Architecture: The Control Plane


- The Kubernetes logic (its "brains") is a collection of services:

  - the API server (our point of entry to everything!)

  - core services like the scheduler and controller manager

  - `etcd` (a highly available key/value store; the "database" of Kubernetes)

- Together, these services form the control plane of our cluster

- The control plane is also called the "master"

---

class: extra-details

## How many nodes should a cluster have?

- There is no particular constraint

  (no need to have an odd number of nodes for quorum)

- A cluster can have zero node

  (but then it won't be able to start any pods)

- For testing and development, having a single node is fine

- For production, make sure that you have extra capacity

  (so that your workload still fits if you lose a node or a group of nodes)

- Kubernetes is tested with [up to 5000 nodes](https://kubernetes.io/docs/setup/best-practices/cluster-large/)

  (however, running a cluster of that size requires a lot of tuning)

---

class: extra-details

## Do we need to run Docker at all?

No!

--

- By default, Kubernetes uses the Docker Engine to run containers

- We can leverage other pluggable runtimes through the *Container Runtime Interface*

---


## Interacting with Kubernetes

- We will interact with our Kubernetes cluster through the Kubernetes API

- The Kubernetes API is (mostly) RESTful

- It allows us to create, read, update, delete *resources*

- A few common resource types are:

  - node (a machine — physical or virtual — in our cluster)

  - pod (group of containers running together on a node)

  - service (stable network endpoint to connect to one or multiple containers)


---

class: pic

# Pods and Containers 


![Pods](images/pods01.png)

---

class: pic

![Pods](images/pods02.png)

---

class: pic

![Pods](images/pods03.png)

---

class: pic

![Pods](images/pods04.png)

---

class: pic

![Pods](images/pods05.png)

---

