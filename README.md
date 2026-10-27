# Exodus Hafifa

Welcome! This repository is the hafifa (onboarding program) for the platform team. It covers Linux, networking, containers, Kubernetes, Helm, GitOps with ArgoCD, Terraform on Google Cloud, and a final project that brings it all together.

Go through the modules **in order**. Each module has its own README or lesson files, with `Goals` and `Tasks` lists.

## How to use this repo
1. Read the **access checklist** below and get everything you need before you start.
2. Work through the modules in the order of the table. Each row says how long the module takes and how it is checked.
3. Study each topic in depth, and write your own summary where the module asks for it.
4. Ask your mentor when something is unclear. Don't skip a checkpoint.

## Module order

Durations marked *stated* are written in the module. Durations marked *est.* are estimates, and may change after the first trainees finish.

| # | Module | Duration | Checkpoint |
|---|---|---|---|
| 01 | [Basic Linux](01.BasicLinux/README.md) | 7–10 days (est.) | Mentor review of answers |
| 02 | [Git](02.Git/README.md) | 1–2 days (est.) | Mentor review of answers |
| 03 | [Python](03.Python/00-PythonIntroduction.md) | 5–7 days (est.) | Mentor review of answers |
| 04 | [Time](04.Time/README.md) | 1 day (est.) | Mentor review of answers |
| 05 | [Networking & DNS](05.NetworkingAndDNS/README.md) | 3–4 days (stated 3, plus subnetting) | **Oral closed-book test** |
| 06 | [IT Tools](06.ITTools/README.md) | 5–7 days (est.) | Mentor review, and the exercise demo |
| 07 | [Virtualization](07.Virtualization/README.md) | 2–3 days (est.) | Mentor review of answers |
| 08 | [Docker](08.Docker/README.md) | 4–6 days (est., includes Docker-in-Docker reading) | Mentor review of answers |
| 09 | [Kubernetes](09.Kubernetes/README.md) | 9–11 days (est., includes the minikube checklist) | Minikube checklist, mentor review of answers, and the exercises |
| 10 | [Helm](10.Helm/README.md) | 2–3 days (est.; the exercise is 1 day, stated) | Mentor review, and the exercise demo |
| 11 | [ArgoCD](11.ArgoCD/README.md) | 3–4 days (stated, including a 2-day exercise) | Demo of the exercise |
| 12 | [Database & Cache](12.Database%20%26%20Cache/README.md) | 3–5 days (est.) | Mentor review. The PostgreSQL exercise is **optional** |
| 13 | [Identity Management](13.IdentityManagement/README.md) | 1–2 days (est.) | Mentor review of answers |
| 14 | [Google Cloud](14.Google/01-Google.md) | at least 12 days (stated) | Mentor review, and a lecture on a topic of your choice |
| 15 | [IAC (Terraform)](15.IAC%20%28Terraform%29/README.md) | 7–8 days (stated) | **Test** after lessons 1–3, then the exercise demo |
| 16 | [Final Project](16.FinalProject/README.md) | 2 weeks (stated) | Milestones with reviews, then the final presentation |

**Total:** about 75–95 working days, roughly 4 to 4.5 months.

## Access checklist
Get these before you start. Ask your mentor for anything you don't have.

- [ ] A **Linux machine** for the Linux modules: your own machine, or a VM you create yourself (VirtualBox, VMware, or a cloud VM)
- [ ] A **GitLab** account, for module 02 and for your code in the later modules
- [ ] A **Google Skills** account ([skills.google](https://www.skills.google/)), for module 14
- [ ] Access to the **dev GKE cluster**, for module 09
- [ ] Access to the **"tactic environment"** (the team's Helm exercises), for module 10. Ask your mentor for the details.
- [ ] A **sandbox GCP project** with billing, for modules 15 and 16. Your mentor creates it and gives you the permissions.
- [ ] A **Git server reachable from the sandbox VPC**, for ArgoCD and git-sync in module 16. Ask your mentor which one.
- [ ] On your laptop: `minikube` and `kubectl` for module 09, and `helm`, `gcloud`, `terraform`, `tflint`, `pre-commit`, and **IAP Desktop** (Windows) or an RDP client (macOS / Linux) for modules 10, 15 and 16

> **Note on the environment:** the sandbox GCP environment has no Cloud NAT, no public IPs and no external load balancers. You reach your VMs through IAP. The modules explain how this works.

## Submitting your answers

> **TODO for the team:** decide how answers are submitted (a personal repository, a shared document, or an oral review with the mentor). Until this is decided, ask your mentor.

## Who to ask
- Module questions: your mentor
- Access and accounts: your mentor
- Problems with this repository: tell your mentor

Good luck 🚀
