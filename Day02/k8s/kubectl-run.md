# Deployments

- Remember, we can't create containers directly

- The desired state is described in a Deployment YAML file

- A deployment creates Replicaset which creates Pods

- The Deployment is a high level controller

---

## Pod

- Can have one or multiple containers

- Runs on a single node

  (Pod cannot "straddle" multiple nodes)

- Pods cannot be moved

  (e.g. in case of node outage)

- Pods cannot be scaled

  (except by manually creating more Pods)

---

class: extra-details

## Pod details

- A Pod is not a process; it's an environment for containers

  - it cannot be "restarted"

  - it cannot "crash"

- The containers in a Pod can crash

- They may or may not get restarted

  (depending on Pod's restart policy)

- If all containers exit successfully, the Pod ends in "Succeeded" phase

- If some containers fail and don't get restarted, the Pod ends in "Failed" phase

---

## Replica Set

- Set of identical (replicated) Pods

- Defined by a pod template + number of desired replicas


---

## `kubectl run` 

- From Kubernetes 1.18, `kubectl run` creates a Pod

  - other kinds of resources can still be created with `kubectl create`

---

## Creating a Deployment 

- Let's destroy that `pingpong` app that we created

- Then we will use `kubectl create deployment` to re-create it

---

class: extra-details

## In Kubernetes 1.19

- Since Kubernetes 1.19, we can specify the command to run

- The command must be passed after two dashes:
  ```bash
  kubectl create deployment pingpong --image=alpine -- ping 127.1
  ```

---

## Viewing container output

- Let's use the `kubectl logs` command

- We will pass either a *pod name*, or a *type/name*


---

## Streaming logs in real time

- Just like `docker logs`, `kubectl logs` supports convenient options:

  - `-f`/`--follow` to stream logs in real time (à la `tail -f`)

  - `--tail` to indicate how many lines you want to see (from the end)

  - `--since` to get logs only after a given timestamp

---

## Scaling our application

- We can create additional copies of our container (I mean, our pod) with `kubectl scale`

.exercise[

- Scale our `pingpong` deployment:
  ```bash
  kubectl scale deploy/pingpong --replicas 3
  ```

- Note that this command does exactly the same thing:
  ```bash
  kubectl scale deployment pingpong --replicas 3
  ```

- Check that we now have multiple pods:
  ```bash
  kubectl get pods
  ```

]

---

class: extra-details

## Scaling a Replica Set

- What if we scale the Replica Set instead of the Deployment?

- The Deployment would notice it right away and scale back to the initial level

- The Replica Set makes sure that we have the right numbers of Pods

- The Deployment makes sure that the Replica Set has the right size

  (conceptually, it delegates the management of the Pods to the Replica Set)

- This might seem weird (why this extra layer?) but will soon make sense

  (when we will look at how rolling updates work!)

---

## Lifecycle

- What happens when a Pod gets created?

--

- Pod phases:

--

  - Pending

--

  - Running

--

  - Succeeded

--

  - Failed

--

  - Unknown

--

  - CrashLoopBackOff

---