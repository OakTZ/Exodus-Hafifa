# Points to Think About

A review of the whole hafifa (all 16 modules). These are suggestions for the team to discuss, not a list of things that must be done.

Priority: 🔴 high - affects what the trainee learns, 🟡 medium - affects how well they learn it, 🟢 low - polish.

---

## 🔴 1. Align the hafifa to the team's stack: Terraform, ArgoCD, Helm, GKE

The team's daily work is mostly **Terraform**, then ArgoCD, Helm and GKE. The hafifa doesn't reflect that yet. OpenShift has already been removed (see "What was changed" below). What's still out of line:

- **Terraform** (`15.IAC (Terraform)`) is now on GCP, split into lessons, covers Fabric and FAST, and has a broad exercise (see "What was changed"). Still to think about:
  - **Align it with how the team actually works:** the exercise uses a folder per environment, Fabric modules from Git, and GitLab CI as a bonus. If the team uses Terragrunt, its own module registry, or a different CI, change the exercise to match.
  - **Does the team run FAST?** If you do, add a session where the trainee walks through your real stage repos (read-only) and finds where things live. That's worth more than any reading.
  - **Consider moving Terraform earlier**, e.g. right after `14.Google` but before `11.ArgoCD`, so the trainee spends more of the hafifa on the team's main tool.
  - **Sandbox projects and cost:** the exercise needs a sandbox project per trainee, with billing. Decide in advance who creates them, with which permissions, and the budget limit.
- **GKE** is now covered hands-on by the GKE courses in `14.Google` (Part 2), and in practice by the `15.IAC` exercise. `09.Kubernetes` itself is still generic Kubernetes, which is fine as a base. Consider whether the GKE part should come earlier, right after `09.Kubernetes`, so trainees see GKE before Helm and ArgoCD.
- **The ArgoCD and Helm exercises run on minikube.** That's fine for learning. The Terraform exercise creates a GKE cluster, and its bonus installs ArgoCD on it. `16.FinalProject` brings them together: the trainee's Terraform provisions GKE, and ArgoCD deploys their charts on it.
- **Lots of VM-era content:** RHEL/`yum` and Vagrant in `01.BasicLinux`; deploying ntpd/chrony in `04.Time`; Linux bridges, VXLAN and OVN in `07.Virtualization`; and building Apache, HAProxy and Keepalived clusters on VMs in `06.ITTools`. The concepts still matter (reverse proxies, TLS and certificate chains, load balancing), and they map to GCP load balancers and Google-managed certificates. But a multi-day VM build exercise may not be the best use of time for a GKE/Terraform team. Consider keeping the concepts and shrinking the VM building.

## 🔴 2. Add a root README

There is no entry point. A new trainee opening the repo doesn't know:
- The order of the modules, and which ones are optional
- How long each module should take (only `14.Google`, the Helm exercise and now `11.ArgoCD` give estimates)
- How answers are submitted (a personal repo? a doc? a conversation with the mentor?)
- When there is a checkpoint or test (only `05.NetworkingAndDNS` and `15.IAC` mention a test)
- What access they need and how to get it: the dev GKE cluster (`09.Kubernetes`), the "tactic environment" (`10.Helm`), a sandbox GCP project (`15.IAC`), a GitLab account (`02.Git`)

A one-page README with a table (module, estimated days, deliverable, checkpoint) and an access checklist would fix most of this.

## 🔴 3. Missing topics

Topics that a platform team uses daily, but that the hafifa doesn't teach:

- **Networking**: now covered by `05.NetworkingAndDNS`. Two things to consider adding there:
  - **Subnetting / CIDR and routing** aren't listed, and they come up all the time in GCP work (VPC subnets, GKE pod and service ranges, firewall rules)
  - The module is study + an oral test, with no hands-on. A short optional lab (`dig` / `nslookup`, `tcpdump` of a TCP handshake, `traceroute`) would make the TCP, DNS and ICMP flows concrete
- **CI/CD**: nothing on pipelines (GitLab CI / GitHub Actions / Cloud Build). The trainee builds images, charts and GitOps repos, but never automates building and pushing an image.
- **Observability**: no Prometheus, Grafana, logging stack or alerting, except one bullet in `14.Google`.
- **Secrets management**: Vault / External Secrets / GCP Secret Manager. This matters even more now that `11.ArgoCD` puts everything in Git.
- **Bash scripting**: `01.BasicLinux` covers commands, but never asks the trainee to write a real script (variables, conditions, loops, exit codes).
- **Ansible**: `02.Git` says "use this repository to store all your Ansible scripts going forward", but there is no Ansible module. Add one, or remove the line.
- **Git workflow**: `02.Git` covers local Git well, but not merge requests, code review or branch protection. GitOps (`11.ArgoCD`) depends on that workflow.

You don't need all of them. Decide which ones are hafifa material and which ones are learned on the job, and write that down.

## 🔴 4. Outdated content

| File | Issue | Suggestion |
|---|---|---|
| `09.Kubernetes/README.md` | "What is a default-token Secret?" - not auto-created since 1.24 | Ask about projected ServiceAccount tokens |
| `09.Kubernetes/README.md` | *Kubernetes in Action* 1st edition (2017), with chapter skips based on it | Check that the skipped chapters still make sense, or move to a newer source |
| `10.Helm/README.md` | Task 3 uses the NGINX Ingress Controller. The Kubernetes project announced the retirement of ingress-nginx in Nov 2025 (best-effort maintenance ended March 2026) | Consider teaching Gateway API (and the GKE Gateway controller, if you're on GKE) |
| `10.Helm/README.md` | "Create a helm chart which uses some bitnami helm chart as a dependency" - in 2025 Bitnami cut back its free public catalog, and many images moved to `bitnamilegacy` | Check that it still works, or pick another public chart |
| `04.Time/01.NTP.md` | Deploying **ntpd** - removed from RHEL 8+, chrony is the default | Keep ntpd as reading only, or drop it |
| `01.BasicLinux/13.Logging.md` | Links to RHEL **6** rsyslog docs | Update to current docs |
| `01.BasicLinux` (several files) | Assumes CentOS/RHEL with `yum` (CentOS Linux is EOL) | Say which distro to use (Rocky / Alma / Fedora / Ubuntu) |
| `08.Docker/README.md` | "Read a bit about CRI-O and rkt" - rkt was archived in 2020 | Replace rkt with containerd |
| `03.Python/06-LangchainAssignment.md`, `07-FastAPI.md` | Hard-coded pins like `langchain==0.3.25` (LangChain 1.x is out) | Ask to "pin the versions you use" instead of hard-coding versions that go stale |

Consider a "last reviewed: <date>" line at the top of each module, so it's clear when content was last checked.

## 🟡 5. Make learning verifiable

Most modules are "Read about X / Explain X". That's a good base, but:

- **No definition of done.** When is a module finished? Who checks it? Add a short checkpoint per module (a conversation, a demo, or a small practical test).
- **No mentor guide.** Each mentor (hofef) checks answers based on their own knowledge. A private answer guide or rubric per module would make reviews consistent and faster.
- **A lot of passive reading.** `08.Docker` and `09.Kubernetes` are whole books, with long question lists and only one small exercise in Kubernetes ("deploy a simple web application"). Trainees tend to read for days without touching a cluster. Add hands-on tasks per chapter group: break a pod, debug a failing service, exhaust a resource quota, and so on.
- **Prefer "break and fix" over "explain".** Troubleshooting is most of the real job, and it's barely practiced. For example: "here is a broken deployment, find out why".
- **Have trainees teach.** `14.Google` ends with "give a lecture on a specific topic". That works well and could be repeated in other big modules (K8s, ArgoCD, Terraform).

## 🟡 6. Connect the exercises into one project

`16.FinalProject` now ties everything together at the end: Terraform provisions GKE, ArgoCD deploys **Apache Airflow** from its official chart (with Cloud SQL, Redis, GCS logs and the trainee's DAGs) plus the platform tools, and Grafana shows it all. To get the most out of it:

- **Tell trainees about it on day 1.** If they know it ends with configuring a big chart on GKE with Terraform and ArgoCD, they will take `10.Helm`, `11.ArgoCD` and `15.IAC` more seriously.
- **Someone in the team should run the Airflow setup once**, so mentors know the common traps (the Argo CD migration settings, which components need the logging service account, Pod Security with git-sync) and can review values files quickly.
- **Prepare the "break and fix" scenarios** for the final presentation in advance, so every trainee gets a similar challenge (e.g. a wrong NetworkPolicy between the scheduler and PgBouncer, a revoked IAM role on the logs bucket, a bad git-sync branch, a broken secret).
- **Airflow could be swapped** for another big chart the team actually runs, if there is one. The brief is built around "configure a large, well-documented chart", not around Airflow itself.
- **Grafana and Prometheus are taught only in the final project.** If observability matters to the team day to day, consider a short module before it (see section 3).
- **2 weeks is tight** for everything in it. After the first trainee, adjust the must / bonus split based on how long it really took.

## 🟡 7. Module order

- `03.Python` comes before networking, Docker and K8s. That's fine, but `09`/`10`/`12` don't use the Python apps, so the connection is lost (see section 6).
- `12.Database & Cache/05.Exersice.md` deploys with CloudNativePG, CronJobs and PVCs, so it requires K8s. It does come after K8s, but it is marked "Optional - Ask Your Hofef". Decide whether it is optional.
- `15.IAC (Terraform)` is last, even though Terraform is the team's main work (see section 1).
- `04.Time` is very small, and partly duplicated (`03.Timezone.md` has the DST goals, copied from `02.DST.md`). It could be a section of `01.BasicLinux`.

## 🟢 8. Repo hygiene

- **Large binaries in Git**: `08.Docker/Docker-Deep-Dive-2024.pdf` is 22MB, and the uncommitted version is 40MB. Every version stays in the Git history forever. Use Git LFS, or link to the file instead.
- **Copyrighted material**: Google Drive links to full book PDFs and a Udemy course folder (`09.Kubernetes`). Check that this is OK, or point to official purchase / company-license links.
- **Folder names with spaces and parentheses** (`12.Database & Cache`, `15.IAC (Terraform)`) are annoying in a shell. Consider `12.DatabaseAndCache`, `15.Terraform`.
- **Inconsistent structure**: some modules are split into `01.X.md` files with Goals/Tasks, others are one big README; `03.Python` uses `00-X.md` naming; `01.BasicLinux` has no `05` (keyboard shortcuts is in the README with no file); `12.Database & Cache` has an empty "Concepts that aren't relevant - ask Services" section; `14.Google` starts at section 1.3. Pick one template.
- **Typos** in almost every module ("consules", "exaplain", "Exersice", "attantion", "mafinests", "Identiy", "psudo", ...). Running `codespell` once, and maybe in CI, would catch most of them.
- **Inside jokes and names** (Ori and Matan, Yoav's hobbies, "Written by Danko", ":gun:"): they give character, but new people won't get the references, and some may not land well. You've already started removing the credits block in `06.ITTools/06.Exercise.md`. Decide on a consistent approach.

## 🟢 9. Ownership and feedback

- Add a `CODEOWNERS` file (or an owner line per module), so someone is responsible for keeping each module up to date. `12.Database & Cache/README.md` literally says "ask Services, they created this chapter".
- Collect feedback from each trainee at the end of each module (what was unclear, what took too long, what was missing) and update the material from it. Commit messages like "babla" make it hard to know why something changed.

---

## What was changed in this round

- **New module `11.ArgoCD`**: GitOps, architecture, repository types, Projects, Applications, App of Apps, ApplicationSet, sync phases/hooks/waves, and a hands-on exercise that deploys the charts from the Helm exercise.
- **New module `05.NetworkingAndDNS`** (3 days, oral closed-book test): MAC and IP addresses, the IP address problem, OSI vs TCP/IP, protocols (DNS, SSH/RDP, TCP, UDP, IP, ICMP, Ethernet, NAT), and concepts (firewall types, LB, VPN vs Proxy, IPAM). It comes right before `06.ITTools`, whose exercise needs DNS.
- **New module `16.FinalProject`** (2 weeks): Terraform with Fabric builds a private GKE Standard cluster, Cloud SQL, Artifact Registry, Secret Manager, DNS and TLS, and bootstraps ArgoCD. ArgoCD (App of Apps, AppProjects, ApplicationSets with multiple sources for dev/prod, sync waves) deploys the **official Apache Airflow chart** (thousands of lines of values), configured for production from its docs: Cloud SQL + PgBouncer, Redis, secrets from Secret Manager, git-sync DAGs, GCS remote logging with Workload Identity, a custom image, and every set value documented with a reason. Trainees also write a small `airflow-extras` chart (ExternalSecrets, NetworkPolicies, ServiceMonitor, alerts, dashboard) and 3 DAGs with CI. It also covers kube-prometheus-stack, External Secrets, Pod Security, Grafana dashboards and alerts as code, operations drills (load, change and rollback, a chart upgrade, disaster recovery), daily milestones with reviews, and a final presentation with a live "break and fix". It includes a table that maps every hafifa module to where the project uses it.
- **`14.Google/01-Google.md`**: the courses now point to Google Skills (https://www.skills.google/). Added Part 2, GKE (~2 days, labs required): the required "Getting Started with Google Kubernetes Engine" course, plus 2 recommended courses (GKE Production, GKE Security) and GKE questions to answer. Duration updated from 10 to at least 12 days.
- **Rewrote `15.IAC (Terraform)` for GCP** (the AWS RAG project is gone), split into:
  - `01-IAC.md` - the original concept questions, plus state locking, `for_each`, lifecycle rules, drift, `import` / `moved` / `removed`, environments and `terraform test`
  - `02-TerraformOnGCP.md` - authentication and impersonation, GCS state, APIs, authoritative vs additive IAM, deletion protection, and the team workflow (MR + plan review, CI, `tflint`, `checkov`)
  - `03-FabricFAST.md` - Cloud Foundation Fabric modules and their IAM interface, resource factories, and the FAST design (stages, contracts, `0-org-setup` → `2-*` stages, delegated role grants). Checked against Fabric v59.2.0
  - `04-Exercise.md` - a design review first, then a sandbox environment built with Fabric modules: bootstrap with impersonation, a hand-written module with tests, VPC + factories + Cloud NAT, service accounts, Artifact Registry, Secret Manager, private Cloud SQL, private Cloud DNS, a private VM over IAP, GKE Autopilot with Workload Identity, Cloud Run, a dev/prod refactor with `moved` blocks, day-2 drills, pre-commit/CI, and destroy + recreate
- **Renumbered** the modules to make room for the two new modules: `05.ITTools` → `06`, `06.Virtualization` → `07`, `07.Docker` → `08`, `08.Kubernetes` → `09`, `09.Helm` → `10`, `10.Database & Cache` → `12`, `11.IdentityManagement` → `13`, `12.Google` → `14`, `13.IAC (Terraform)` → `15`.
- **`10.Helm/README.md`**: removed the short ArgoCD questions, the external ArgoCD course link and the ArgoCD bonus task (all covered in `11.ArgoCD` now), added a pointer to the next module, and fixed the "likedds" typo from the last commit.
- **Removed OpenShift completely:**
  - `09.Kubernetes/README.md`: the exercise now deploys Google's `hello-app` sample on the dev GKE cluster; the "openshift SCC" and PodSecurityPolicy questions were replaced with Pod Security Admission / Pod Security Standards (PSP was removed in Kubernetes 1.25)
  - `13.IdentityManagement/README.md`: the OpenShift authentication questions were replaced with GKE: how `kubectl` authenticates, IAM vs RBAC, Google Groups for RBAC, Workload Identity Federation (with a hands-on task), and ArgoCD SSO
