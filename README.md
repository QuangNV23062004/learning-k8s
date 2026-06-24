# learning-k8s

Hands-on artifacts from a ~4-hour Kubernetes crash course (TechWorld with Nana,
*Kubernetes Tutorial for Beginners*). Each folder is a self-contained demo that
builds up the core building blocks of Kubernetes, from a single Pod to a
multi-component app exposed through an Ingress.

## Prerequisites

- A running cluster (`minikube`, `kind`, or Docker Desktop's K8s)
- `kubectl` configured against that cluster
- For the dashboard demo: the **nginx ingress controller** enabled
  (`minikube addons enable ingress`)

Apply any folder with `kubectl apply -f <folder>/`.

---

## `test-nginx/` — Pods, Deployments & Services

The first hands-on. A basic nginx Deployment exposed via a Service.

| File | What it does |
|------|--------------|
| `nginx-deployment.yaml` | Deployment managing nginx Pod replicas via a pod template |
| `nginx-service.yaml` | Service giving the Pods stable networking + load balancing |
| `nginx-deployment-result.yaml` | The full Deployment object as stored by the cluster (`kubectl get -o yaml`), handy for seeing the defaults K8s fills in |

**Concepts:** Pods as the smallest deployable unit · Deployments &
replica management · label selectors linking a Deployment to its Pods ·
Services as stable network endpoints.

---

## `test-mongodb/` — Secrets, ConfigMaps & multi-component apps

The centerpiece project: a MongoDB database with a Mongo Express web UI in
front of it.

### `mongodb/` — the database tier

| File | What it does |
|------|--------------|
| `mongodb-secret.yaml` | Secret holding the base64-encoded root username/password |
| `mongodb-deployment.yaml` | MongoDB Deployment; pulls credentials from the Secret via `secretKeyRef`; sets resource requests/limits |
| `mongodb-service.yaml` | Internal (ClusterIP) Service for pod-to-pod access |
| `mongodb-configmap.yaml` | ConfigMap holding the non-sensitive DB connection URL |

### `mongo-express/` — the UI tier

| File | What it does |
|------|--------------|
| `mongo-express-deployment.yaml` | Web UI Deployment; reads Mongo credentials from the Secret and the DB URL from the ConfigMap |
| `mongo-express-service.yaml` | External (LoadBalancer) Service to reach the UI from the browser |

**Concepts:** Secrets vs ConfigMaps (sensitive vs non-sensitive config) ·
injecting config into containers via env vars · internal ClusterIP vs external
LoadBalancer Services · service discovery by Service name · resource
requests/limits.

> ⚠️ `mongodb-secret.yaml` contains base64-encoded demo credentials. Base64 is
> **encoding, not encryption** — never reuse these values anywhere real.

---

## `test-dashboard/` — Ingress

Exposing the Kubernetes Dashboard through an Ingress instead of a raw Service.

| File | What it does |
|------|--------------|
| `dashboard-ingress.yaml` | Ingress routing `dashboard.com` → the `kubernetes-dashboard` Service via the nginx ingress controller |

**Concepts:** Ingress as L7 (host/path) routing into the cluster ·
`ingressClassName` selecting the controller · working inside a non-default
namespace (`kubernetes-dashboard`).

To reach it locally, map the host to your cluster IP in `/etc/hosts`:

```
<minikube-ip>  dashboard.com
```

---

## Commit history as a study log

Each commit documents the concepts its manifests introduce — browse with
`git log` to read them in order.
