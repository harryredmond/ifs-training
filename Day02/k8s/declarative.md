## Declarative vs Imperative

- With Kubernetes, we cannot say: "run this container"

- All we can do is write a *spec* and push it to the API server

  (by creating a resource like e.g. a Pod or a Deployment)

- The API server will validate that spec (and reject it if it's invalid)

- Then it will store it in etcd

- A *controller* will "notice" that spec and act upon it

---

## Reconciling state

- Watch for the `spec` fields in the YAML files later!

- The *spec* describes *how we want the thing to be*

- Kubernetes will *reconcile* the current state with the spec
  <br/>(technically, this is done by a number of *controllers*)

- When we want to change some resource, we update the *spec*

- Kubernetes will then *converge* that resource

---

## Deploying with YAML

- So far, we created resources with the following commands:

  - `kubectl run`

  - `kubectl create deployment`

- We can also create resources directly with YAML manifests

---

## `kubectl apply` vs `create`

- `kubectl create -f whatever.yaml`

  - creates resources if they don't exist

  - if resources already exist, don't alter them
    <br/>(and display error message)

- `kubectl apply -f whatever.yaml`

  - creates resources if they don't exist

  - if resources already exist, update them
    <br/>(to match the definition provided by the YAML file)

  - stores the manifest as an *annotation* in the resource

---

## Creating multiple resources

- The manifest can contain multiple resources separated by `---`

```yaml
 kind: ...
 apiVersion: ...
 metadata: ...
   name: ...
 ...
 ---
 kind: ...
 apiVersion: ...
 metadata: ...
   name: ...
 ...
```
---

## Deleting resources

- We can also use a YAML file to *delete* resources

- `kubectl delete -f ...` will delete all the resources mentioned in a YAML file

  (useful to clean up everything that was created by `kubectl apply -f ...`)

- The definitions of the resources don't matter

  (just their `kind`, `apiVersion`, and `name`)

---

## Pruning resources

- We can also tell `kubectl` to remove old resources

- This is done with `kubectl apply -f ... --prune`

- It will remove resources that don't exist in the YAML file(s)

- But only if they were created with `kubectl apply` in the first place

  (technically, if they have an annotation `kubectl.kubernetes.io/last-applied-configuration`)

---

## YAML as source of truth

- Imagine the following workflow:

  - do not use `kubectl run`, `kubectl create deployment`, `kubectl expose` ...

  - define everything with YAML

  - `kubectl apply -f ... --prune --all` that YAML

  - keep that YAML under version control

  - enforce all changes to go through that YAML (e.g. with pull requests)

- Our version control system now has a full history of what we deploy

- Compares to "Infrastructure-as-Code", but for app deployments



