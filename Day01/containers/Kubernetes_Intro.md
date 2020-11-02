# Introduction to Kubernetes

In this chapter, we will:

* Explain what is orchestration and why we would need it.

* Present (from a high-level perspective) some orchestrators.

* Introduce Kubernetes

---

class: pic

## What's orchestration?

![Joana Carneiro (orchestra conductor)](images/conductor.jpg)

---

## What's orchestration?

According to Wikipedia:

*Orchestration describes the __automated__ arrangement,
coordination, and management of complex computer systems,
middleware, and services.*

--

*[...] orchestration is often discussed in the context of 
__service-oriented architecture__, __virtualization__, provisioning, 
Converged Infrastructure and __dynamic datacenter__ topics.*

--

What does that really mean?

---

## Example 1: dynamic cloud instances

--

- Q: do we always use 100% of our servers?

--

- A: obviously not!

.center[![Daily variations of traffic](images/traffic-graph.png)]

---

## Example 1: dynamic cloud instances

- Every night, scale down
  
  (by shutting down extraneous replicated instances)

- Every morning, scale up
  
  (by deploying new copies)

- "Pay for what you use"
  
  (i.e. save big $$$ here)

---

## Example 1: dynamic cloud instances

How do we implement this?

- Crontab
  
- Autoscaling (save even bigger $$$)

That's *relatively* easy.

Now, how are things for our IAAS provider?

---

## Example 2: dynamic datacenter

- Q: what's the #1 cost in a datacenter?

--

- A: electricity!

--

- Q: what uses electricity?

--

- A: servers, obviously

- A: ... and associated cooling

--

- Q: do we always use 100% of our servers?

--

- A: obviously not!

---

## Exercise 1

- You have:

  - 5 hypervisors (physical machines)

- Each server has:

  - 16 GB RAM, 8 cores, 1 TB disk

- Each week, your team requests:

  - one VM with X RAM, Y CPU, Z disk

Scheduling = deciding which hypervisor to use for each VM.

Difficulty: easy!

---
## Exercise 2

- You have:

  - 1000+ hypervisors (and counting!)

- Each server has different resources:

  - 8-500 GB of RAM, 4-64 cores, 1-100 TB disk

- Multiple times a day, a different team asks for:

  - up to 50 VMs with different characteristics

Scheduling = deciding which hypervisor to use for each VM.

Difficulty: ???
---

## Exercise 3

- You have machines (physical and/or virtual)

- You have containers

- You are trying to put the containers on the machines

- Sounds familiar?

---
## Some orchestrators

We are going to present briefly a few orchestrators.

There is no "absolute best" orchestrator.

It depends on:

- your applications,

- your requirements,

- your pre-existing skills...

---

## Nomad

- Open Source project by Hashicorp.

- Arbitrary scheduler (not just for containers).

- Great if you want to schedule mixed workloads.

  (VMs, containers, processes...)

- Less integration with the rest of the container ecosystem.

---

## Swarm

- Tightly integrated with the Docker Engine.

- Extremely simple to deploy and setup, even in multi-manager (HA) mode.

- Secure by default.

- Strongly opinionated:

  - smaller set of features,

  - easier to operate.

---

## Kubernetes

- Open Source project initiated by Google.

- Contributions from many other actors.

- *De facto* standard for container orchestration.

- Many deployment options; some of them very complex.

- Reputation: steep learning curve.

- Reality:

  - true, if we try to understand *everything*;

  - false, if we focus on what matters.

---
# Summary

- [Where containers come from?](https://blog.aquasec.com/a-brief-history-of-containers-from-1970s-chroot-to-docker-2016)
- [How to operate and manage containers using Docker?](https://docs.docker.com/samples/)
- [How to build images using Dockerfile?](https://docs.docker.com/develop/develop-images/dockerfile_best-practices/)
- [How containers networking work?](https://docs.docker.com/network/)
- [How to run multi-container applications using Docker Compose?](https://docs.docker.com/compose/)
- [What is Kubernetes?](https://kubernetes.io/docs/concepts/overview/what-is-kubernetes/)