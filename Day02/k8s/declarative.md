## Declarative vs Imperative

- With Kubernetes, we cannot say: "run this container"

- All we can do is write a *spec* and push it to the API server

- The API server will validate that spec 

- Then it will store it in etcd

- A *controller* will "notice" that spec and act upon it

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



