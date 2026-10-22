# Points to Think About

A review of the whole hafifa (all 16 modules). These are suggestions for the team to discuss, not a list of things that must be done.

Priority: 🔴 high - affects what the trainee learns, 🟡 medium - affects how well they learn it, 🟢 low - polish.

---

## 🔴 1. Align the hafifa to the team's stack: Terraform, ArgoCD, Helm, GKE

The team's daily work is mostly **Terraform**, then ArgoCD, Helm and GKE. The hafifa now reflects that, and OpenShift is gone (see "What was changed" below). What's still open:

- **Terraform** (`15.IAC (Terraform)`) is on GCP, split into lessons, covers Fabric and FAST, and has a broad exercise. Still to decide:
  - **Align it with how the team actually works:** the exercise uses a folder per environment, Fabric modules from Git, and GitLab CI as a bonus. If the team uses Terragrunt, its own module registry, or a different CI, change the exercise to match.
  - **Does the team run FAST?** If you do, add a session where the trainee walks through your real stage repos (read-only) and finds where things live. That's worth more than any reading.
  - **Consider moving Terraform earlier**, e.g. right after `14.Google` but before `11.ArgoCD`, so the trainee spends more of the hafifa on the team's main tool.
  - **Sandbox projects and cost:** the exercise needs a sandbox project per trainee, with billing. Decide in advance who creates them, with which permissions, and the budget limit.
  - **Make the sandbox match the real environment.** The IaC exercise and the final project assume no Cloud NAT, no public IPs and no external load balancers, with VMs over IAP Desktop as the way in (a Windows VM over RDP for the browser, a Linux VM over SSH for the command line). Enforce this with the same organization policies as production, so trainees hit the real errors.
  - **Prepare what trainees can't build themselves**, before the first trainee starts:
    - Which **Git server** is reachable from the sandbox VPC (for ArgoCD and git-sync)
    - Whether Artifact Registry **remote repositories** are allowed for Docker Hub, `quay.io`, `ghcr.io` and `registry.k8s.io`
    - How tools get onto the **VMs** without internet (e.g. a shared GCS bucket of installers, an Artifact Registry remote repository for OS packages, or a prepared image)
    - Whether trainees' laptops run **IAP Desktop** (it's a Windows app, and connects to both Windows and Linux VMs), or `gcloud` + an RDP client on macOS / Linux
    - **Who maintains the chart mirror** for the Airflow chart in the final project, and how often it's refreshed
- **GKE** is covered hands-on by the GKE courses in `14.Google` (Part 2), and in practice by the `15.IAC` exercise and the final project. `09.Kubernetes` is still generic Kubernetes, which is fine as a base. Consider whether the GKE part should come earlier, right after `09.Kubernetes`, so trainees see GKE before Helm and ArgoCD.
- **The ArgoCD and Helm exercises run on minikube.** That's fine for learning. The Terraform exercise creates a GKE cluster, and its bonus installs ArgoCD on it. `16.FinalProject` brings them together: the trainee's Terraform provisions GKE, and ArgoCD deploys their charts on it.
- **Lots of VM-era content:** RHEL/`yum` and Vagrant in `01.BasicLinux`; deploying ntpd/chrony in `04.Time`; Linux bridges, VXLAN and OVN in `07.Virtualization`; and building Apache, HAProxy and Keepalived clusters on VMs in `06.ITTools`. The concepts still matter (reverse proxies, TLS and certificate chains, load balancing), and they map directly to our environment: internal load balancers, and a private CA for TLS, since Google-managed public certificates need public DNS and external load balancers. The PKI part of `06.ITTools` is used again in the final project. But a multi-day VM build exercise may not be the best use of time for a GKE/Terraform team. Consider keeping the concepts and shrinking the VM building.
- **Local VM images (open question):** trainees may want a local VirtualBox VM for `01`, `05` and `07`. The IaC exercise and final project don't need them, because their VMs are Compute Engine VMs created by Terraform. Decide whether local VMs are needed at all. If they are, check the image sources and licensing (a Vagrant box for Linux, Microsoft's evaluation VM for Windows, which expires) before you link them. Nothing has been added to the repo.

## 🔴 2. Add a root README

There is no entry point. A new trainee opening the repo doesn't know:
- The order of the modules, and which ones are optional
- How long each module should take. Only 6 of 16 modules state a duration (`05`, `10`, `11` exercise, `14`, `15`, `16`). See "Durations" below.
- How answers are submitted (a personal repo? a doc? a conversation with the mentor?)
- When there is a checkpoint or test (only `05.NetworkingAndDNS` and `15.IAC` mention a test)
- What access they need and how to get it: the dev GKE cluster (`09.Kubernetes`), the "tactic environment" (`10.Helm`), a sandbox GCP project (`15.IAC`), a GitLab account (`02.Git`), a Google Skills account (`14.Google`), and a Git server reachable from the VPC (`16.FinalProject`)

A one-page README with a table (module, estimated days, deliverable, checkpoint) and an access checklist would fix most of this.

**Durations:** the estimate for the whole hafifa was about 75–95 working days (roughly 4–4.5 months). It was worked out in conversation and isn't recorded in the repo. Put the per-module estimates in the root README, so they're in one place and easy to change.

## 🔴 3. Missing topics

Topics that a platform team uses daily, but that the hafifa doesn't teach as modules:

- **Networking**: covered by `05.NetworkingAndDNS`, with an oral test. Two things to consider adding:
  - **Subnetting / CIDR and routing** aren't listed in the module, but the IaC exercise needs them (VPC subnets, GKE pod and service ranges, the proxy-only subnet, firewall rules). Add them to the module, or the exercise asks for something nobody taught.
  - The module is study + oral test, with no hands-on. A short optional lab (`dig` / `nslookup`, `tcpdump` of a TCP handshake, `traceroute`) would make the TCP, DNS and ICMP flows concrete.
- **CI/CD**: no module teaches pipelines. The final project requires a CI pipeline per repo that authenticates with Workload Identity Federation, and the IaC exercise has a GitLab CI bonus. Trainees are expected to do this without having been taught it. Consider a short module before the final project, or at least a hands-on lab.
- **Observability**: Prometheus, Grafana, dashboards and alerts are taught only in `16.FinalProject`, with a live break-and-fix at the end. If observability matters to the team day to day, consider a short module before it. This is the most important gap for the final project.
- **Secrets management**: now used in the IaC exercise and the final project (Secret Manager with External Secrets Operator), but no module explains the options (Vault, External Secrets, Sealed Secrets, SOPS). Decide whether that's needed as a lesson or whether the final project is enough.
- **Bash scripting**: `01.BasicLinux` covers commands, but never asks the trainee to write a real script (variables, conditions, loops, exit codes). The final project does ask for shell in CI and Jobs, so this matters more now.
- **Ansible**: `02.Git` says "use this repository to store all your Ansible scripts going forward", but there is no Ansible module. Add one, or remove the line.
- **Git workflow**: `02.Git` covers local Git well, but not merge requests, code review or branch protection. GitOps (`11.ArgoCD`), the IaC exercise and the final project all depend on that workflow.

You don't need all of them. Decide which ones are hafifa material and which ones are learned on the job, and write that down.

## 🔴 4. Outdated content

Items 1–3 below were checked against the repo. Items 4–6 are from my own knowledge of the projects and **haven't been verified** in this review. Check them before changing any material.

| File | Issue | Status | Suggestion |
|---|---|---|---|
| `09.Kubernetes/README.md` | "What is a default-token Secret?" | Still there. Automatic token Secrets were removed in Kubernetes 1.24 | Ask about projected ServiceAccount tokens |
| `09.Kubernetes/README.md` | *Kubernetes in Action* 1st edition (2017), with chapter skips based on it | Still there | Check that the skipped chapters still make sense, or move to a newer source |
| `04.Time/01.NTP.md` | Deploying **ntpd** (the module names ntpd and chronyd) | Still there | Keep ntpd as reading only, or drop it, since chrony is the default on RHEL 8+ |
| `08.Docker/README.md` | "Read a bit about CRI-O and rkt" | Still there | Replace rkt with containerd |
| `03.Python/06-LangchainAssignment.md` | Hard-coded pin `langchain==0.3.25` | Still there | Ask to "pin the versions you use" instead of hard-coded versions that go stale |
| `01.BasicLinux/13.Logging.md` | Links to RHEL **6** rsyslog docs | Not re-checked | Update to current docs |
| `01.BasicLinux` (several files) | Assumes CentOS/RHEL with `yum` | Not re-checked. CentOS Linux is EOL | Say which distro to use |
| `10.Helm/README.md` | Task 3 uses the NGINX Ingress Controller, which I believe is being retired by the Kubernetes project | **Unverified** | Check the current status, then consider teaching Gateway API |
| `10.Helm/README.md` | Uses a Bitnami chart as a dependency. I believe Bitnami changed its free catalog in 2025 | **Unverified** | Check that the chart still works, or pick another public chart |

Consider a "last reviewed: <date>" line at the top of each module, so it's clear when content was last checked.

## 🟡 5. Make learning verifiable

Most modules are "Read about X / Explain X". That's a good base, but:

- **No definition of done in most modules.** `15.IAC` has a test after its lessons, and `16.FinalProject` has milestones with reviews. Everything else has no checkpoint. Add a short checkpoint per module (a conversation, a demo, or a small practical test).
- **No mentor guide.** Each mentor (hofef) checks answers based on their own knowledge. A private answer guide or rubric per module would make reviews consistent and faster.
- **A lot of passive reading.** `08.Docker` and `09.Kubernetes` are whole books, with long question lists and only one small exercise in Kubernetes ("deploy a simple web application"). Trainees tend to read for days without touching a cluster. Add hands-on tasks per chapter group: break a pod, debug a failing service, exhaust a resource quota, and so on.
- **Prefer "break and fix" over "explain".** Troubleshooting is most of the real job, and it's barely practiced before the final project. A small break-and-fix task in the IaC exercise and in the ArgoCD exercise would prepare trainees for it.
- **Have trainees teach.** `14.Google` ends with "give a lecture on a specific topic". That works well and could be repeated in other big modules (K8s, ArgoCD, Terraform).

## 🟡 6. Connect the exercises into one project

`16.FinalProject` ties everything together at the end: Terraform provisions a private GKE cluster, ArgoCD deploys **Apache Airflow** from its official chart (with Cloud SQL, Redis, GCS logs and the trainee's DAGs) plus the platform tools, and Grafana shows it all. To get the most out of it:

- **Tell trainees about it on day 1.** If they know it ends with configuring a big chart on GKE with Terraform and ArgoCD, they will take `10.Helm`, `11.ArgoCD` and `15.IAC` more seriously.
- **Someone in the team should run the Airflow setup once**, so mentors know the common traps (the Argo CD migration settings, which components need the logging service account, Pod Security with git-sync) and can review values files quickly. This also checks the private-environment steps: the chart and image mirror, and the Git server.
- **Prepare the "break and fix" scenarios** for the final presentation in advance, so every trainee gets a similar challenge (e.g. a wrong NetworkPolicy between the scheduler and PgBouncer, a revoked IAM role on the logs bucket, a bad git-sync branch, a broken secret).
- **Airflow could be swapped** for another big chart the team actually runs, if there is one. The brief is built around "configure a large, well-documented chart", not around Airflow itself.
- **Observability is taught only here** (see section 3).
- **2 weeks is tight** for everything in it. After the first trainee, adjust the must / bonus split based on how long it really took.

## 🟡 7. Module order

- `03.Python` comes before networking, Docker and K8s. That's fine, but `09`/`10`/`12` don't use the Python apps, so the connection is lost until the final project (see section 6).
- `12.Database & Cache/05.Exersice.md` deploys with CloudNativePG, CronJobs and PVCs, so it requires K8s. It does come after K8s, but it is marked "Optional - Ask Your Hofef". Decide whether it is optional.
- `15.IAC (Terraform)` is last, even though Terraform is the team's main work (see section 1).
- `04.Time` is very small, and partly duplicated (`03.Timezone.md` has the DST goals, copied from `02.DST.md`). It could be a section of `01.BasicLinux`.
- `05.NetworkingAndDNS` is now before `06.ITTools`, which fits, since the IT Tools exercise needs DNS. The IaC exercise needs networking again, so the two are linked.

## 🟢 8. Repo hygiene

- **Large binaries in Git**: `08.Docker/Docker-Deep-Dive-2024.pdf` was 22MB in Git, and the working copy is now 40MB, so the history grows with every version. Use Git LFS, or link to the file instead. Don't add VM images to the repo either.
- **Copyrighted material**: Google Drive links to full book PDFs and a Udemy course folder (`09.Kubernetes`). Check that this is OK, or point to official purchase / company-license links.
- **Folder names with spaces and parentheses** (`12.Database & Cache`, `15.IAC (Terraform)`) are annoying in a shell. Consider `12.DatabaseAndCache`, `15.Terraform`.
- **Inconsistent structure**: some modules are split into `01.X.md` files with Goals/Tasks, others are one big README; `03.Python` uses `00-X.md` naming; `01.BasicLinux` has no `05` (keyboard shortcuts is in the README with no file); `12.Database & Cache` has an empty "Concepts that aren't relevant - ask Services" section; `14.Google` starts at section 1.3. The newest modules (`11`, `15`, `16`) use the Goals/Tasks style, so they can serve as the template.
- **Typos** in several modules ("consules", "exaplain", "Exersice", "attantion", "mafinests", "Identiy", "psudo", ...). Running `codespell` once, and maybe in CI, would catch most of them.
- **Inside jokes and names** (Ori and Matan, Yoav's hobbies, "Written by Danko", ":gun:"): they give character, but new people won't get the references, and some may not land well. The credits block in `06.ITTools/06.Exercise.md` is already removed. Decide on a consistent approach for the rest.

## 🟢 9. Ownership and feedback

- Add a `CODEOWNERS` file (or an owner line per module), so someone is responsible for keeping each module up to date. `12.Database & Cache/README.md` literally says "ask Services, they created this chapter".
- Collect feedback from each trainee at the end of each module (what was unclear, what took too long, what was missing) and update the material from it. Commit messages like "babla" make it hard to know why something changed.

---

## What was changed

- **New module `11.ArgoCD`**: GitOps, architecture, repository types, Projects, Applications, App of Apps, ApplicationSet, sync phases/hooks/waves, and a hands-on exercise that deploys the charts from the Helm exercise.
- **New module `05.NetworkingAndDNS`** (3 days, oral closed-book test): MAC and IP addresses, the IP address problem, OSI vs TCP/IP, protocols (DNS, SSH/RDP, TCP, UDP, IP, ICMP, Ethernet, NAT), and concepts (firewall types, LB, VPN vs Proxy, IPAM). It comes right before `06.ITTools`, whose exercise needs DNS.
- **New module `16.FinalProject`** (2 weeks): Terraform with Fabric builds a private GKE Standard cluster, Cloud SQL, Artifact Registry, Secret Manager, a private DNS zone and TLS, and bootstraps ArgoCD. ArgoCD (App of Apps, AppProjects, ApplicationSets with multiple sources for dev/prod, sync waves) deploys the **official Apache Airflow chart**, configured for production from its docs: Cloud SQL + PgBouncer, Redis, secrets from Secret Manager, git-sync DAGs, GCS remote logging with Workload Identity, a custom image, and every set value documented with a reason. Trainees also write a small `airflow-extras` chart and 3 DAGs with CI. It covers kube-prometheus-stack, External Secrets, Pod Security, Grafana dashboards and alerts as code, operations drills, daily milestones with reviews, and a final presentation with a live "break and fix". It maps every hafifa module to where the project uses it. It is built for the **private environment**: no Cloud NAT or external load balancers, so images go through Artifact Registry remote repositories, charts are mirrored, the UIs are on internal load balancers with TLS from the trainee's own CA, and all work is done from VMs inside the VPC over IAP Desktop.
- **`14.Google/01-Google.md`**: the courses point to Google Skills (https://www.skills.google/). Added Part 2, GKE (~2 days, labs required): the required "Getting Started with Google Kubernetes Engine" course, plus 2 recommended courses (GKE Production, GKE Security), and GKE questions, including internal exposure without NAT. Duration is at least 12 days.
- **Rewrote `15.IAC (Terraform)` for GCP** (the AWS RAG project is gone), split into:
  - `01-IAC.md` - the original concept questions, plus state locking, `for_each`, lifecycle rules, drift, `import` / `moved` / `removed`, environments and `terraform test`
  - `02-TerraformOnGCP.md` - authentication and impersonation, GCS state, APIs, authoritative vs additive IAM, deletion protection, private environments (org policy constraints, Private Google Access, remote repositories, IAP and IAP Desktop, OS Login, internal load balancers), and the team workflow
  - `03-FabricFAST.md` - Cloud Foundation Fabric modules and their IAM interface, resource factories, and the FAST design. Checked against Fabric v59.2.0
  - `04-Exercise.md` - a design review first, then a sandbox environment built with Fabric modules: bootstrap with impersonation, a hand-written module with tests, a private VPC from factories (Private Google Access, a proxy-only subnet, no Cloud NAT), service accounts, Artifact Registry with remote repositories, Secret Manager, private Cloud SQL, private Cloud DNS, Windows and Linux VMs reached with IAP Desktop, a private GKE Autopilot cluster with Workload Identity and an internal load balancer, internal Cloud Run, a dev/prod refactor with `moved` blocks, day-2 drills, pre-commit/CI, and destroy + recreate
- **Renumbered** the modules to make room for the two new modules: `05.ITTools` → `06`, `06.Virtualization` → `07`, `07.Docker` → `08`, `08.Kubernetes` → `09`, `09.Helm` → `10`, `10.Database & Cache` → `12`, `11.IdentityManagement` → `13`, `12.Google` → `14`, `13.IAC (Terraform)` → `15`.
- **`10.Helm/README.md`**: removed the short ArgoCD questions, the external ArgoCD course link and the ArgoCD bonus task (all covered in `11.ArgoCD` now), added a pointer to the next module, and fixed the "likedds" typo from the last commit.
- **Removed OpenShift completely:**
  - `09.Kubernetes/README.md`: the exercise deploys Google's `hello-app` sample on the dev GKE cluster; the "openshift SCC" and PodSecurityPolicy questions were replaced with Pod Security Admission / Pod Security Standards (PSP was removed in Kubernetes 1.25)
  - `13.IdentityManagement/README.md`: the OpenShift authentication questions were replaced with GKE: how `kubectl` authenticates, IAM vs RBAC, Google Groups for RBAC, Workload Identity Federation (with a hands-on task), ArgoCD SSO, and IAP
