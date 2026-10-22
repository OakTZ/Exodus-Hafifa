# ArgoCD

> **Prerequisites:** 09.Kubernetes and 10.Helm, including the DevOps exercise at the end of 10.Helm.
> The final exercise of this module deploys the Helm charts you wrote there.

🕒 **Estimated Time**: 3-4 days (about half reading, half hands-on)
🧪 **Kubernetes Cluster Type**: `minikube` (or `kind`)

Go over the files in order. Each one builds on the previous one.

### GitOps
- What GitOps is and why we use it
- Push vs Pull based deployments

### ArgoCD architecture
- ArgoCD components
- The reconciliation loop
- Sync status and health status

### Repositories
- Git, Helm and OCI repositories
- Repository credentials and credential templates
- App repo vs config (GitOps) repo

### Projects
- AppProject
- The `default` project
- Source, destination and resource restrictions

### Applications
- The Application resource
- Sources: plain YAML, Helm, Kustomize
- Sync policies: manual, automated, prune, self-heal

### App of Apps
- Bootstrapping a cluster from a single Application

### ApplicationSet
- Generators (List, Cluster, Git, Matrix, Merge, SCM Provider, Pull Request)
- Templates

### Sync phases, hooks and waves
- PreSync, Sync, PostSync, SyncFail
- Sync waves
- Ordering between Applications

### Exercise
- [09.Exercise.md](./09.Exercise.md)
