# Setting up Kubernetes

- Kubernetes is made of many components that require careful configuration

- Secure operation typically requires TLS certificates and a local CA

  (certificate authority)

- Setting up everything manually is possible, but rarely done

  (except for learning purposes)

- Let's do a quick overview of available options!

---

## Local development

- Examples: Docker Desktop, k3d, KinD, MicroK8s, Minikube

  (some of these also support clusters with multiple nodes)

---

## Managed clusters

- Many cloud providers and hosting providers offer "managed Kubernetes"

- The deployment and maintenance of the cluster is entirely managed by the provider

  (ideally, clusters can be spun up automatically through an API, CLI, or web interface)

- Given the complexity of Kubernetes, this approach is *strongly recommended*

  (at least for your first production clusters)

- After working for a while with Kubernetes, you will be better equipped to decide:

  - whether to operate it yourself or use a managed offering

  - which offering or which distribution works best for you and your needs

---

class: pic

![managed](images/managedhosts.png)

---

## Kubernetes distributions and installers

- If you want to run Kubernetes yourselves, there are many options

  (free, commercial, proprietary, open source ...)

- Some of them are installers, while some are complete platforms

- Some of them leverage other well-known deployment tools

  (like Puppet, Terraform ...)

- A good starting point to explore these options is this [guide](https://v1-16.docs.kubernetes.io/docs/setup/#production-environment)

  (it defines categories like "managed", "turnkey" ...)

---

## kubeadm

- kubeadm is a tool part of Kubernetes to facilitate cluster setup

- Many other installers and distributions use it (but not all of them)

- It can also be used by itself

- Excellent starting point to install Kubernetes on your own machines

  (virtual, physical, it doesn't matter)

---

## Manual setup

- The resources below are mainly for educational purposes!

- [Kubernetes The Hard Way](https://github.com/kelseyhightower/kubernetes-the-hard-way) by Kelsey Hightower

  - step by step guide to install Kubernetes on Google Cloud

  - covers certificates, high availability ...

  - *“Kubernetes The Hard Way is optimized for learning, which means taking the long route to ensure you understand each task required to bootstrap a Kubernetes cluster.”*

- [Deep Dive into Kubernetes Internals for Builders and Operators](https://www.youtube.com/watch?v=3KtEAa7_duA)

  - conference presentation showing step-by-step control plane setup

  - emphasis on simplicity, not on security and availability

---

## [Minikube](https://minikube.sigs.k8s.io/docs/)

- Supports many [drivers](https://minikube.sigs.k8s.io/docs/drivers/)

  (HyperKit, Hyper-V, KVM, VirtualBox, but also Docker and many others)

- Can deploy a single cluster; recent versions can deploy multiple nodes

- Great option if you want a "Kubernetes first" experience

---

## Deploying a managed cluster

*"The easiest way to install Kubernetes is to get someone
else to do it for you."

- Let's see a few options to install managed clusters!

- This is not an exhaustive list

  (the goal is to show the actual steps to get started)

- The list is sorted alphabetically

- All the options mentioned here require an account
with a cloud provider

- ... And a credit card

---

## AKS (initial setup)

- Install the Azure CLI

- Login:
  ```bash
  az login
  ```

- Select a [region](https://azure.microsoft.com/en-us/global-infrastructure/services/?products=kubernetes-service&regions=all
)

- Create a "resource group":
  ```bash
  az group create --name my-aks-group --location westeurope
  ```

---
## AKS (create cluster)

- Create the cluster:
  ```bash
  az aks create --resource-group my-aks-group --name my-aks-cluster
  ```

- Wait about 5-10 minutes

- Add credentials to `kubeconfig`:
  ```bash
  az aks get-credentials --resource-group my-aks-group --name my-aks-cluster
  ```

---

## AKS (cleanup)

- Delete the cluster:
  ```bash
  az aks delete --resource-group my-aks-group --name my-aks-cluster
  ```

- Delete the resource group:
  ```bash
  az group delete --resource-group my-aks-group
  ```

- Note: delete actions can take a while too!

  (5-10 minutes as well)

---

## Amazon EKS (the old way)

- [Read the doc](https://docs.aws.amazon.com/eks/latest/userguide/getting-started-console.html)

- Create service roles, VPCs, and a bunch of other oddities

- Try to figure out why it doesn't work

- Start over, following an [official AWS blog post](https://aws.amazon.com/blogs/aws/amazon-eks-now-generally-available/)

- Try to find the missing Cloud Formation template

---

## Amazon EKS (the new way)

- Install `eksctl`

- Set the usual environment variables

  ([AWS_DEFAULT_REGION](https://docs.aws.amazon.com/general/latest/gr/rande.html#eks_region), AWS_ACCESS_KEY, AWS_SECRET_ACCESS_KEY)

- Create the cluster:
  ```bash
  eksctl create cluster
  ```

- Cluster can take a long time to be ready (15-20 minutes is typical)

---

## Amazon EKS (cleanup)

- Delete the cluster:
  ```bash
  eksctl delete cluster <clustername>
  ```

- If you need to find the name of the cluster:
  ```bash
  eksctl get clusters
  ```

.footnote[Note: the AWS documentation has been updated and now includes [eksctl instructions](https://docs.aws.amazon.com/eks/latest/userguide/getting-started-eksctl.html).]

---

## Amazon EKS (notes)

- Convenient if you *have to* use AWS

- Needs extra steps to be truly production-ready

- Versions tend to be outdated

- The only officially supported pod network is the [Amazon VPC CNI plugin](https://docs.aws.amazon.com/eks/latest/userguide/pod-networking.html)

  - integrates tightly with security groups and VPC networking

  - not suitable for high density clusters (with many small pods on big nodes)

  - other plugins [should still work](https://docs.aws.amazon.com/eks/latest/userguide/alternate-cni-plugins.html) but will require extra work

---

## GKE (initial setup)

- Install `gcloud`

- Login:
  ```bash
  gcloud auth init
  ```

- Create a "project":
  ```bash
  gcloud projects create my-gke-project
  gcloud config set project my-gke-project
  ```

- Pick a [region](https://cloud.google.com/compute/docs/regions-zones/)

  (example: `europe-west1`, `us-west1`, ...)

---

## GKE (create cluster)

- Create the cluster:
  ```bash
  gcloud container clusters create my-gke-cluster --region us-west1 --num-nodes=2
  ```

  (without `--num-nodes` you might exhaust your IP address quota!)

- The first time you try to create a cluster in a given project, you get an error

  - you need to enable the Kubernetes Engine API
  - the error message gives you a link
  - follow the link and enable the API (and billing)
    <br/>(it's just a couple of clicks and it's instantaneous)

- Clutser should be ready in a couple of minutes

---

## GKE (cleanup)

- List clusters (if you forgot its name):
  ```bash
  gcloud container clusters list
  ```

- Delete the cluster:
  ```bash
  gcloud container clusters delete my-gke-cluster --region us-west1
  ```

- Delete the project (optional):
  ```bash
  gcloud projects delete my-gke-project
  ```

---

## GKE (notes)

- Well-rounded product overall

  (it used to be one of the best managed Kubernetes offerings available;
  now that many other providers entered the game, that title is debatable)

- The cluster comes with many add-ons

- Versions lag a bit:

  - latest minor version (e.g. 1.18) tends to be unsupported
 
  - previous minor version (e.g. 1.17) supported through alpha channel

  - previous versions (e.g. 1.14-1.16) supported
