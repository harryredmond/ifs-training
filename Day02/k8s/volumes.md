# Volumes

- Volumes are special directories that are mounted in containers

- Volumes can have many different purposes:

  - share files and directories between containers running on the same machine

  - share files and directories between containers and their host

---

class: pic

![pervolumes](images/pervolumes.png)

---

## Adding a volume

- Volumes are added in two places:

  - at the Pod level (to declare the volume)

  - at the container level (to mount the volume)

---

## Our basic Pod

```yaml
apiVersion: v1
kind: Pod
metadata:
  name: nginx-without-volume
spec:
  containers:
  - name: nginx
    image: nginx
```

---

## The Pod with a volume

```yaml
apiVersion: v1
kind: Pod
metadata:
  name: nginx-with-volume
spec:
  volumes:
  - name: www
  containers:
  - name: nginx
    image: nginx
    volumeMounts:
    - name: www
      mountPath: /usr/share/nginx/html/
```
---

## Sharing a volume between two containers

.small[
```yaml
apiVersion: v1
kind: Pod
metadata:
  name: nginx-with-git
spec:
  volumes:
  - name: www
  containers:
  - name: nginx
    image: nginx
    volumeMounts:
    - name: www
      mountPath: /usr/share/nginx/html/
  - name: git
    image: alpine
    command: [ "sh", "-c", "echo "Hello" > /www/index.html" ]
    volumeMounts:
    - name: www
      mountPath: /www/
  restartPolicy: OnFailure
```
]

---

## Volume lifecycle

- The lifecycle of a volume is linked to the pod's lifecycle

- This means that a volume is created when the pod is created

- This is mostly relevant for `emptyDir` volumes

  (other volumes, like remote storage, are not "created" but rather "attached" )

- A volume survives across container restarts

- A volume is destroyed (or, for remote storage, detached) when the pod is destroyed

---

## Persistent Volumes Claims

- Our Pods can use a special volume type: a *Persistent Volume Claim*

- A Persistent Volume Claim (PVC) is also a Kubernetes resource

  (visible with `kubectl get persistentvolumeclaims` or `kubectl get pvc`)

- A PVC is not a volume; it is a *request for a volume*

- It should indicate at least:

  - the size of the volume (e.g. "5 GiB")

  - the access mode (e.g. "read-write by a single pod")

---

## What's in a PVC?

- A PVC contains at least:

  - a list of *access modes* (ReadWriteOnce, ReadOnlyMany, ReadWriteMany)

  - a size (interpreted as the minimal storage space needed)

- It can also contain optional elements:

  - a selector (to restrict which actual volumes it can use)

  - a *storage class* (used by dynamic provisioning, more on that later)

---

## What does a PVC look like?

Here is a manifest for a basic PVC:

```yaml
kind: PersistentVolumeClaim
apiVersion: v1
metadata:
   name: my-claim
spec:
   accessModes:
     - ReadWriteOnce
   resources:
     requests:
       storage: 1Gi
```
---

## Using a Persistent Volume Claim

Here is a Pod definition like the ones shown earlier, but using a PVC:

```yaml
apiVersion: v1
kind: Pod
metadata:
  name: pod-using-a-claim
spec:
  containers:
  - image: ...
    name: container-using-a-claim
    volumeMounts:
    - mountPath: /my-vol
      name: my-volume
  volumes:
  - name: my-volume
    persistentVolumeClaim:
      claimName: my-claim
```