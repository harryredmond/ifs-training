# Configmaps | Secrets

- Some applications need to be configured 

- There are many ways for our code to pick up configuration:

  - command-line arguments

  - environment variables

  - configuration files

  - configuration servers 

- How can we do these things with containers and Kubernetes?

---

## Passing configuration to containers

- There are many ways to pass configuration to code running in a container:

  - command-line arguments

  - environment variables

  - injecting configuration files



---

## Environment variables, pros and cons

- Works great when the running program expects these variables

- Works great for optional parameters with reasonable defaults

---

## Injecting configuration files

- Sometimes, there is no way around it: we need to inject a full config file

- Kubernetes provides a mechanism for that purpose: `configmaps`

- A configmap is a Kubernetes resource that exists in a namespace

- Conceptually, it's a key/value map

  (values are arbitrary strings)

- We can think about them in two different ways:

  - as holding entire configuration file(s)

  - as holding individual configuration parameters

---

## Configmaps storing entire files

- In this case, each key/value pair corresponds to a configuration file

- Key = name of the file

- Value = content of the file

---

## Configmaps storing individual parameters

- In this case, each key/value pair corresponds to a parameter

- Key = name of the parameter

- Value = value of the parameter


---

## Exposing configmaps to containers

- Configmaps can be exposed as plain files in the filesystem of a container


- Configmaps can be exposed as environment variables in the container


---

## Passwords, tokens, sensitive information

- For sensitive information, there is another special resource: *Secrets*

- Secrets and Configmaps work almost the same way

---
## Differences between configmaps and secrets
 
- Secrets are base64-encoded when shown with `kubectl get secrets -o yaml`

  - keep in mind that this is just *encoding*, not *encryption*

  - it is very easy to extract and decode secrets