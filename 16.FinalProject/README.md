# 🏁 Final Project

🕒 **Estimated Time**: 2 weeks (10 working days)
☁️ **Cloud**: Google Cloud, in a sandbox project you get from your mentor

This is the last step of the hafifa. You will build a production-like platform from scratch, and run **Apache Airflow** on it with its official Helm chart:

- **Terraform** creates the infrastructure on GCP, including a GKE cluster
- **ArgoCD** deploys everything that runs inside the cluster, from Git
- **Helm** - you will configure a large, real-world chart for production
- **Prometheus and Grafana** show you what is happening

### Why Airflow?
In real life, you rarely write the charts of the big tools you run. You take an official chart and configure it, and that is a skill of its own.

The [official Airflow chart](https://airflow.apache.org/docs/helm-chart/stable/index.html) has a `values.yaml` of thousands of lines, with dozens of components. You **will** get lost in it at first. The chart also has very good documentation. Your job is to read it, understand what each value does, and configure only what you need, correctly.

You will use almost everything you learned in the hafifa. At the end, you will present the project to the team and answer questions on all of it.

> **Important notice!**
> Same rules as in the 11.ArgoCD and 15.IAC exercises: everything is code, and every change goes through Git.
> No manual `kubectl apply`, no console changes, no service account keys, and no secrets in Git.
> The only manual steps allowed are the bootstrap steps you document in your README.

---

## 📚 Read before you configure
Keep these open the whole time. Most of the answers you need are in them:
- [Airflow Helm chart docs](https://airflow.apache.org/docs/helm-chart/stable/index.html), including the section about installing with **Argo CD**
- [Production Guide](https://airflow.apache.org/docs/helm-chart/stable/production-guide.html) - read it **before** you start
- [Parameters reference](https://airflow.apache.org/docs/helm-chart/stable/parameters-ref.html) - every value in the chart, explained
- [Manage DAG files](https://airflow.apache.org/docs/helm-chart/stable/manage-dag-files.html) and [Manage logs](https://airflow.apache.org/docs/helm-chart/stable/manage-logs.html)
- The chart's own [`values.yaml`](https://github.com/apache/airflow/blob/main/chart/values.yaml). The comments are documentation too. Read the version that matches the chart release you pin.
- [Airflow concepts](https://airflow.apache.org/docs/apache-airflow/stable/core-concepts/overview.html) - what the scheduler, DAG processor, triggerer, workers and API server are

> 💡 **How to approach a big chart:** don't start from the full `values.yaml`. Start from an empty values file, add one change at a time, and run `helm template` to see what it changes. Every value you set should have a reason.

---

## 🎯 What you are building

```
                    ┌───────────────────────────── GCP project (Terraform) ──────────────────────────────┐
                    │                                                                                       │
 user ── DNS ── HTTPS LB ──► GKE cluster (private)                                                           │
  (Cloud DNS + managed cert) │  ├── argocd        ◄── watches the GitOps repo                                 │
                    │        │  ├── monitoring    Prometheus, Grafana, Alertmanager                           │
                    │        │  ├── platform      External Secrets Operator                                   │
                    │        │  ├── airflow-dev ┐ API server (UI), scheduler, DAG processor, triggerer,       │
                    │        │  └── airflow-prod┘ Celery workers, Redis, PgBouncer, StatsD                    │
                    │        │        │   ├──► Cloud SQL (metadata DB, private IP)                            │
                    │        │        │   ├──► GCS bucket (task logs, via Workload Identity)                  │
                    │        │        │   └──► Secret Manager (via External Secrets)                          │
                    │        │        └── git-sync ◄── DAGs repo                                              │
                    │        VPC + Cloud NAT, Artifact Registry (custom Airflow image), IAM                    │
                    └───────────────────────────────────────────────────────────────────────────────────────┘
```

### Repositories
Use 3 Git repositories (or explain why you chose differently):

| Repo | Contains |
|---|---|
| `infra` | Terraform: bootstrap, Fabric modules, environments |
| `dags` | Your DAGs, their tests, the custom Airflow image (Dockerfile), and its CI pipeline |
| `gitops` | ArgoCD resources (projects, apps, ApplicationSets), the Airflow values per environment, and your `airflow-extras` chart |

---

## 📋 Requirements

**Must** items are required. **Bonus** items are for when the must items are done.

### 1. Infrastructure - Terraform (15.IAC, 14.Google, 05.NetworkingAndDNS)
**Must:**
- Terraform with **Fabric modules**, state in GCS, impersonation of a Terraform service account
- VPC, subnet with secondary ranges for GKE, firewall rules from the factory, Cloud Router + Cloud NAT
- A **private GKE Standard** cluster (in 15.IAC you used Autopilot, so be ready to compare), with a **spot** node pool sized from the resources you give Airflow, Workload Identity, and network policy enabled
- **Cloud SQL** for PostgreSQL (private IP only), with a database and a user for each environment
- A **GCS bucket** for Airflow task logs, per environment
- **Artifact Registry** for your custom Airflow image
- **Secret Manager** secrets for everything sensitive: the DB connection, the Fernet key, the API secret key, the JWT secret, the Redis password and the admin password
- A reserved static IP, Cloud DNS records for Airflow and Grafana, and a TLS certificate (Google-managed with Certificate Manager, or cert-manager with Let's Encrypt). Ask your mentor for a subdomain.
- Google service accounts for the workloads that need GCP access (Airflow logs, External Secrets), bound with Workload Identity
- **Terraform bootstraps ArgoCD**: it installs ArgoCD with the `helm` provider and creates the root Application. After that, Terraform doesn't touch anything inside the cluster.

**Bonus:**
- A Terraform CI pipeline: `plan` on every merge request, with Workload Identity Federation

### 2. Airflow - the big chart (10.Helm)
Deploy the official chart from the Airflow Helm repository (`https://airflow.apache.org`), **pinned to a released version**.

**Must:**
- **Values files, split by purpose:** `values-common.yaml` for everything shared, plus `values-dev.yaml` and `values-prod.yaml` for the differences. Prod is "bigger" (more replicas, more resources), not "different".
- **Document every value you set.** Each value in your files has a comment that explains **why** you set it, with a link to the docs when it isn't obvious. A reviewer should understand your config without opening the chart.
- **Executor:** use the `CeleryExecutor`. Be ready to explain why, and to compare it with the `KubernetesExecutor`.
- **Metadata database:** Cloud SQL instead of the chart's built-in PostgreSQL, with **PgBouncer** in front of it. The connection string comes from a Kubernetes Secret (see section 5), not from the values.
- **Redis** (the Celery broker): the chart's built-in Redis is fine, but with a static password from a Secret. (Read the docs to find out why a static password matters.)
- **Secrets:** the Fernet key, the API secret key, the JWT secret, the DB connection and the broker URL are all read from **existing Kubernetes Secrets**. None of them are generated by the chart or written in values.
- **Migrations with ArgoCD:** the chart docs have a section about installing with Argo CD. Find it, set what's required, and explain what would happen if you didn't.
- **DAGs:** loaded from your `dags` repo with **git-sync**
- **Remote logging:** task logs go to your GCS bucket, using **Workload Identity**. Find out which Airflow components write or read logs, and make sure each of them runs with the right Kubernetes service account.
- **Custom image:** build your own image `FROM` the official Airflow image, with the extra Python packages your DAGs need. Push it to Artifact Registry, and point the chart at it.
- **Resources:** set requests and limits for **every** component. Explain how you chose the numbers. (Hint: run it, then look at Grafana.)
- **Security:** components run as non-root. Change nothing in the chart's security defaults without a reason you can explain.
- **Exposure:** the Airflow UI over HTTPS on its own hostname
- **Admin user:** created with a password from Secret Manager, not a default password
- **Never-used components are disabled.** For every component you turn off (or leave on), know why.

**Bonus:**
- **KEDA** autoscaling for the Celery workers (the chart supports it), scaling on queued tasks
- Log in to Airflow with Google (OIDC)
- `helm template` your values in CI, and fail the pipeline if rendering fails

### 3. Your own chart - `airflow-extras` (10.Helm)
The big chart doesn't create everything you need. Write a **small** chart of your own, deployed next to Airflow in each environment, with:
- The `ExternalSecret` resources that create Airflow's Secrets from Secret Manager
- The `HTTPRoute` (Gateway API) or Ingress for the UI, if you don't use the chart's ingress (explain your choice)
- `NetworkPolicy` resources (see section 5)
- A `ServiceMonitor` for Airflow's StatsD exporter
- `PrometheusRule` alerts and the Grafana dashboard ConfigMap (see section 6)
- Values per environment, and it passes `helm lint`

### 4. DAGs (03.Python, 08.Docker)
**Must:** write at least 3 small DAGs that exercise the platform:
- A DAG that runs every few minutes, and generates and stores some data (e.g. calls a public API and writes the result to a GCS bucket with the Google provider, using Workload Identity)
- A DAG with parallel tasks, to keep the Celery workers busy
- A DAG that fails sometimes on purpose, with retries, so you have failures to see in Grafana and alerts

Plus:
- A test that loads all the DAGs and fails on import errors
- A CI pipeline in the `dags` repo that runs the tests, and builds and pushes the custom image to Artifact Registry, tagged with the commit SHA. It authenticates with **Workload Identity Federation**, not keys.

### 5. GitOps - ArgoCD (11.ArgoCD)
**Must:**
- **App of Apps**: a single root Application (created by Terraform) that manages everything else
- **AppProjects**: at least `platform` and `airflow`, each restricted to its own repos, namespaces and resource kinds
- **ApplicationSet**: deploy Airflow and `airflow-extras` to `airflow-dev` and `airflow-prod` from ApplicationSets. Use **multiple sources**: the chart from the Airflow Helm repo, and the values files from your `gitops` repo.
- **Platform Applications**, from their public Helm charts: `kube-prometheus-stack` and External Secrets Operator
- **Sync waves**, so the order is correct: CRDs and operators → platform tools → `airflow-extras` (the Secrets must exist before Airflow starts) → Airflow. Add the `Application` health check so the waves between apps actually wait.
- Automated sync with `prune` and `selfHeal` on dev, and **manual** sync on prod
- Repository credentials defined declaratively, without committing secrets

**Bonus:**
- ArgoCD SSO with Google (OIDC), mapped to ArgoCD RBAC roles
- A sync window that blocks syncs to prod on weekends
- ArgoCD notifications on sync failures

### 6. Security and identity (13.IdentityManagement, 06.ITTools)
**Must:**
- No service account keys anywhere. Every GCP access is through Workload Identity.
- **External Secrets Operator** reads every secret from Secret Manager
- HTTPS only, with a valid certificate (no `curl -k`, same rule as in 06.ITTools)
- The Airflow namespaces enforce the `baseline` Pod Security Standard at least. Try `restricted` and document what (if anything) breaks.
- NetworkPolicies: deny everything by default in the Airflow namespaces, then allow only what's needed. Draw the allowed flows between the components first.
- Least privilege everywhere: GCP IAM roles, Kubernetes RBAC and AppProjects. Be ready to justify every permission.

**Bonus:**
- Scan the custom image in CI (`trivy`) and fail the build on critical vulnerabilities

### 7. Observability - Prometheus and Grafana
**Must:**
- Grafana exposed over HTTPS on its own hostname, with an admin password from Secret Manager
- Airflow metrics in Prometheus, through the chart's StatsD exporter and your `ServiceMonitor`
- A Grafana dashboard **as code** (a ConfigMap picked up by the Grafana sidecar), per environment, showing:
  - Scheduler health (heartbeat), and DAG processing time
  - DAG run durations and task successes / failures
  - Queued vs running tasks, and the open slots in the default pool
  - CPU and memory of each component vs its requests and limits
- At least 2 alerts as `PrometheusRule` resources, e.g.:
  - The scheduler heartbeat stopped
  - The task failure rate is too high
- Show an alert firing in Alertmanager (cause it on purpose)

**Bonus:**
- Logs in Grafana (Loki), or explain how you would use Cloud Logging instead
- Alertmanager sends alerts to a Slack channel or email
- **Question:** compare self-managed `kube-prometheus-stack` with Google Cloud Managed Service for Prometheus. What would you choose for the team, and why?

### 8. Operations
**Must:**
- **Load:** trigger many DAG runs at once (with the Airflow CLI or REST API), and show in Grafana the queue growing and the workers draining it
- **Change and rollback:** change a value on dev (e.g. the worker concurrency), promote it to prod, then roll prod back the GitOps way (`git revert`)
- **Chart upgrade:** upgrade the chart to a newer release on dev. Read the release notes first, and review the diff in ArgoCD before you sync. Write down what changed.
- **Self-heal:** delete the scheduler Deployment by hand, and show ArgoCD bring it back
- **Disaster recovery:** destroy everything (`terraform destroy`), then rebuild it from zero with Terraform + ArgoCD. Measure how long it takes, and document every manual step (there should be almost none).

---

## 🗓️ Milestones

Each milestone ends with a short review with your mentor. Don't move on without it.

| Day | Milestone | Review |
|---|---|---|
| 1-2 | **Design**: read the Production Guide; architecture diagram (draw.io, GCP icons), IP plan, repo layout, and the list of Airflow components you will run and why | Approval of the design |
| 3-4 | **Infrastructure**: Terraform up to a running GKE cluster, with ArgoCD bootstrapped | `terraform plan` shows no changes; ArgoCD UI is reachable |
| 5-6 | **Airflow on dev**: secrets, Cloud SQL, git-sync, remote logs, custom image | A DAG runs, and its logs are in GCS |
| 7 | **Prod and security**: prod environment, Pod Security, NetworkPolicies | Walk through every value you set, and every permission |
| 8 | **Observability**: dashboards and alerts | Dashboard live under load |
| 9 | **Operations drills** and documentation | Chart upgrade + disaster recovery drill |
| 10 | **Final presentation** | 🎓 |

---

## 📦 Deliverables
- Links to the 3 repositories, each with a README:
  - What it contains, and how to use it
  - The architecture diagram and the IP plan (as built)
  - **Bootstrap instructions**: every manual step, in order
  - A short **runbook**: what to check when DAGs don't run, when tasks are stuck in `queued`, when a sync fails, or when an alert fires
  - Your main **design decisions**, and why (e.g. Celery vs Kubernetes executor, built-in Redis vs Memorystore, Standard vs Autopilot)
- Documented Airflow values files, where every value has a reason
- A Grafana dashboard and alerts, in Git

## 🎤 Final presentation
About 1 hour, in front of the team:
1. **Architecture** (15 min): walk through the diagram, and every Airflow component and what it does
2. **Live demo** (20 min):
   - A DAG change goes from a commit to running in prod
   - The dashboard under load, with the queue draining
   - An alert firing
   - A rollback
3. **Break and fix** (10 min): your mentor will break something in your environment before the presentation (without telling you what). Find and fix it live, using Grafana, ArgoCD, `kubectl` and logs.
4. **Questions** (15 min): from any module of the hafifa, and on any value in your values files

## ❓ Be ready to answer
- Pick any value in your values files. What does it do, what is the default, and why did you change it?
- Which values did you **not** change, even though you were tempted to? Why?
- A task is stuck in `queued`. Which components are involved, and where do you look first?
- Follow a task from the moment the scheduler decides to run it, to the moment its log appears in the UI. Which components, network connections and identities are involved?
- Follow a request from the browser to the Airflow UI: DNS, TLS, the load balancer, the Gateway, the Service, the pod. What happens at each step, and at which OSI layer?
- What happens to Airflow if Cloud SQL is down? If Redis is down? If the `dags` repo is down? If ArgoCD is down?
- After a chart upgrade ran a DB migration, can you roll back with `git revert`? Why not? What would you do instead?
- Where exactly is the line between Terraform and ArgoCD in your project? What would break if you crossed it?
- How would this project look in an organization that uses FAST? Which stage would own each part?
- What would you change before running this in real production?

---

## 🗺️ Hafifa coverage

| Module | Where you use it in the project |
|---|---|
| 01.BasicLinux | Debugging containers, processes and permissions (uid/gid) in the Airflow pods |
| 02.Git | 3 repos, merge requests, git-sync, `git revert` for rollbacks |
| 03.Python | Your DAGs and their tests |
| 04.Time | DAG schedules, time zones and `catchup` in Airflow, timestamps in logs and metrics |
| 05.NetworkingAndDNS | VPC and IP plan, Cloud NAT, Cloud DNS, NetworkPolicies, the request path |
| 06.ITTools | Load balancing, reverse proxy (Gateway), TLS certificates |
| 07.Virtualization | Nodes are VMs, containers are namespaces and cgroups |
| 08.Docker | The custom Airflow image |
| 09.Kubernetes | Deployments, StatefulSets, Jobs, NetworkPolicies, RBAC, resources |
| 10.Helm | Configuring the Airflow chart, and writing `airflow-extras` |
| 11.ArgoCD | App of Apps, AppProjects, ApplicationSets, multiple sources, sync waves |
| 12.Database & Cache | Cloud SQL as the metadata DB, PgBouncer, Redis as the Celery broker |
| 13.IdentityManagement | Workload Identity, IAM vs RBAC, SSO |
| 14.Google | GKE, Cloud SQL, GCS, Artifact Registry, Secret Manager, Cloud DNS |
| 15.IAC | All the infrastructure, with Fabric modules |

> 💰 **Cost:** use spot nodes and the smallest tiers. Ask your mentor whether to destroy the environment at night. It's all code, and rebuilding it is part of the project anyway.

Good luck 🚀
