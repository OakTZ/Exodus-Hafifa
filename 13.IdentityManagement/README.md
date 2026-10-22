# Identity management


### Goals 
- The trainee will understand what is SSO and uses.
- The trainee will understand the difference between common IdPs.
- The trainee will understand how OIDC works and how we can configure it.
- The trainee will understand how authentication and authorization work in GKE.
- The trainee will understand how workloads in GKE authenticate to Google Cloud APIs.


### Tasks

- What is SSO?
- What are the benefits and use cases of SSO?
- How does SSO work (tokens, sessions, identity federation)?
- What is the difference between authentication and authorization?
- What is an Identity Provider (IdP)?
- What are common types of IdPs and the difference between them (LDAP, OAuth2, OIDC, SAML)?
- What is the flow of OIDC? 
- How does `kubectl` authenticate to a GKE cluster? What is `gke-gcloud-auth-plugin`?
- What is the difference between Google Cloud IAM and Kubernetes RBAC in GKE? How do they work together?
- What is Google Groups for RBAC? Why bind RBAC to groups and not to individual users?
- Why are static credentials (client certificates, service account key files) a risk? How does GKE avoid them?
- What is Workload Identity Federation for GKE? Why is it better than mounting a service account key in a pod?
- Deploy a pod on GKE that reads from a GCS bucket using Workload Identity Federation, without any key files
- What is Identity-Aware Proxy (IAP)? How does IAP TCP forwarding let you RDP or SSH to a VM that has no public IP? Which identity checks happen on the way?
- How does ArgoCD authenticate users? Read about ArgoCD SSO with OIDC (directly, or through Dex), and how to map groups to ArgoCD RBAC roles

### References
- [GKE - Authenticating to the Kubernetes API server](https://cloud.google.com/kubernetes-engine/docs/how-to/api-server-authentication)
- [GKE - Role-based access control](https://cloud.google.com/kubernetes-engine/docs/how-to/role-based-access-control)
- [GKE - Workload Identity Federation](https://cloud.google.com/kubernetes-engine/docs/concepts/workload-identity)
- [ArgoCD - User Management / SSO](https://argo-cd.readthedocs.io/en/stable/operator-manual/user-management/)
