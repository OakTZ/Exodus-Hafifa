# 🚀 Terraform Exercise - A Small GCP Environment

🕒 **Estimated Time**: 4-5 days
☁️ **Cloud**: Google Cloud, in a sandbox project you get from your mentor

In this exercise you will build a small but complete environment on GCP with Terraform: a network, IAM, storage, a database, DNS, a VM, a GKE cluster and a Cloud Run service.

**Breadth over depth:** keep every resource as simple as possible. The goal is to touch many services and connect them correctly, not to configure every option.

> **Important notice!**
> Everything is created with Terraform. Creating or changing resources in the console or with `gcloud` is not allowed, except where the exercise asks you to do it on purpose.
> Use the smallest tiers, and label every resource (e.g. `owner`, `env`, `managed-by: terraform`). Ask your mentor whether to destroy expensive resources (GKE, Cloud SQL) at the end of each day. It's all code, so you can bring them back with one `apply`.

## Rules
- Use **Cloud Foundation Fabric modules** for all resources, except where the exercise tells you to write your own. Pin them to a release with `?ref=`.
- No service account keys. Ever. Use impersonation.
- No public IPs, except the Cloud Run service.
- Every change goes through a commit. Keep the `plan` output of important changes in your merge requests.

## Prerequisites
- A sandbox project with billing, and permissions to manage it (ask your mentor)
- `gcloud`, `terraform`, `tflint` and `pre-commit` installed
- A Git repository for your code (GitLab, like in 02.Git)

Use the following layout (you may change it, but be ready to explain why):
```
hafifa-infra/
├── bootstrap/             # state bucket + Terraform service account (local state)
├── modules/
│   └── bucket/            # your own module (Part 2)
└── envs/
    └── dev/
        ├── backend.tf
        ├── main.tf
        ├── variables.tf
        ├── terraform.tfvars
        └── config/
            ├── subnets/   # subnet factory YAMLs
            └── firewall/  # firewall rule factory YAMLs
```

---

## 0. Design first
Before you write any code:
1. Draw the architecture in draw.io, with the official GCP icons. Include every service from parts 3-9 and how they connect.
2. Write an **IP plan**: the subnet range, the GKE pod and service ranges, and the Private Service Access range. Explain why the ranges don't overlap, and how many pods and nodes they allow.
3. Write down which service account each workload uses, and which roles it gets.

> [!IMPORTANT]
> Submit the drawing and the IP plan, and get approval before continuing.

## 1. Bootstrap
1. In `bootstrap/` (with **local** state), create:
   - A GCS bucket for Terraform state, with versioning enabled
   - A `terraform` service account with the roles it needs to manage the project
   - Permission for **your user** to impersonate it (and nothing more)
2. In `envs/dev/`, configure the `gcs` backend and the Google provider to **impersonate** the `terraform` service account.
3. **Question:** the bootstrap state is still on your laptop. What are the risks? Move it into the bucket as well (`terraform init -migrate-state`).
> **Check:** `terraform plan` in `envs/dev` works, and the audit logs show the `terraform` service account (not your user) doing the work.

## 2. Write your own module
Before using Fabric, write one module yourself, in `modules/bucket/`, using plain `google_*` resources:
- Inputs: name, location, labels, versioning (on/off), and a lifecycle rule that deletes objects older than N days
- Validate the inputs (e.g. the name must start with your prefix)
- Outputs: the bucket name and URL
- Write a `terraform test` for it
- Use it in `envs/dev` to create an `assets` bucket
> **Question:** Now read the Fabric `gcs` module. What does it do that yours doesn't? When would you write your own module and when would you use Fabric?

## 3. Network
Using `net-vpc`, `net-vpc-firewall` and `net-cloudnat`:
1. A custom-mode VPC (no auto-created subnets)
2. One subnet with **secondary ranges** for GKE pods and services, defined as a YAML file with the **subnet factory**
3. Firewall rules, defined as YAML files with the **firewall factory**:
   - Allow SSH only from the IAP range (`35.235.240.0/20`)
   - Allow internal traffic inside the subnet
   - Make sure everything else is denied
4. Cloud Router and **Cloud NAT**, so private machines can reach the internet
5. A **Private Service Access** range for Cloud SQL
> **Question:** Why does the private VM in Part 7 need Cloud NAT to run `apt install`? What does the NAT translation look like? (Connect this to NAT in 05.NetworkingAndDNS)

## 4. IAM and service accounts
Using `iam-service-account`, create a service account for each workload, with the least privilege it needs:
- `vm-sa` for the VM
- `gke-app-sa` for the app in GKE (it reads from the `assets` bucket and the DB password secret)
- `run-sa` for the Cloud Run service

Grant roles on the resources themselves (the bucket, the secret) and not on the whole project, where possible.
> **Question:** For each grant, did you use `iam`, `iam_bindings` or `iam_bindings_additive`? Why? What would happen if someone else in the team also managed IAM on the same resource?

## 5. Storage, registry and secrets
1. An **Artifact Registry** Docker repository (`artifact-registry`)
2. A **Secret Manager** secret for the DB password (`secret-manager`). Generate the password with `random_password`.
3. Push an image you built in an earlier module (e.g. your FastAPI app from 03.Python) to the repository
> **Question:** The generated password is in your Terraform state. Is that a problem? Who can read the state? How would you avoid it?

## 6. Database
Using `cloudsql-instance`:
- A PostgreSQL instance with **private IP only**, on the smallest tier
- A database and a user, with the password from Part 5
- Try `terraform destroy` on it. What stops you? Why is that a good thing? How do you get past it on purpose?

## 7. DNS and a VM
1. A **private** Cloud DNS zone (`dns`), e.g. `hafifa.internal.`, attached to your VPC
2. A record `db.hafifa.internal` that points to the Cloud SQL private IP. Take the IP from the module's output, not by copy-paste.
3. A small VM (`compute-vm`) **without a public IP**, running as `vm-sa`
4. SSH to it through IAP (`gcloud compute ssh --tunnel-through-iap`), then:
   - `dig db.hafifa.internal` - it should resolve to the private IP
   - Connect to the database with `psql` using the DNS name
   - `curl` a public website - it should work through Cloud NAT
> **Check:** All 3 work, and the VM has no external IP.

## 8. GKE
Using `gke-cluster-autopilot`:
1. A **private** Autopilot cluster in your VPC, using the secondary ranges from Part 3
2. Configure **Workload Identity Federation for GKE**, so a Kubernetes service account in the cluster acts as `gke-app-sa`
3. Run a pod with your image from Artifact Registry. Without any key files, it should:
   - Read a file from the `assets` bucket
   - Read the DB password from Secret Manager
> **Question:** How did the pod get credentials? Follow the path from the pod to the Google API.

## 9. Cloud Run
Using `cloud-run-v2`:
- A Cloud Run service running `us-docker.pkg.dev/cloudrun/container/hello`, as `run-sa`
- Make it publicly reachable, and `curl` its URL
- **Bonus:** replace the image with your FastAPI app from Artifact Registry, and connect it to Cloud SQL through **Direct VPC egress**
> **Question:** Compare running this service on Cloud Run vs on your GKE cluster. When would you choose each one?

## 10. Environments
1. Your `envs/dev/main.tf` is getting long. Move all the resources into a local module, `modules/environment/`, and call it from `envs/dev`.
   - Use **`moved` blocks** so that nothing is destroyed or recreated
   > **Check:** After the refactor, `terraform plan` shows **no changes**.
2. Create `envs/prod`, which calls the same module with different values (a different prefix, different IP ranges, a larger DB tier).
3. Run `terraform plan` for prod. Only `apply` it if your mentor approves.
> **Question:** What is different between dev and prod, and where does that difference live in the code?

## 11. Day-2 operations
Do each of these, and write down what happened:
1. **Drift:** change a firewall rule in the console. Run `plan`. What does Terraform want to do? Fix the drift.
2. **Import:** create a bucket by hand with `gcloud`, then bring it under Terraform with an `import` block, without recreating it.
3. **Remove:** stop managing that bucket with a `removed` block, without deleting it.
4. **Upgrade:** change the Fabric `?ref=` to a newer release. Read the plan carefully. Is it safe to apply?
5. **Break it:** make a change that forces the replacement of a resource (e.g. change the subnet's range). Read the plan **before** you confirm, and explain what would happen. Don't apply it.

## 12. Workflow
1. Add a `pre-commit` config that runs `terraform fmt`, `terraform validate` and `tflint`
2. Add `checkov` or `trivy`, and fix (or explicitly justify) every finding
3. **Bonus:** a GitLab CI pipeline that runs `terraform plan` on every merge request, and authenticates to GCP with **Workload Identity Federation** (no keys)

## 13. Destroy and recreate
1. `terraform destroy` everything in `envs/dev` (you will need to deal with deletion protection)
2. Recreate it all with a single `terraform apply`. How long did it take?
3. Did you need any manual step? If so, fix the code so you don't.

---

## 🏁 Bonus
- Install ArgoCD on your GKE cluster from Terraform (with the `helm` provider), and point it at your GitOps repo from 11.ArgoCD. Where exactly is the line between what Terraform manages and what ArgoCD manages?
- Add a **Pub/Sub** topic with a push subscription that triggers your Cloud Run service
- Put an external HTTPS load balancer (`net-lb-app-ext`) in front of Cloud Run
- Add a log-based alert or an uptime check on the Cloud Run service

## 📦 Deliverables
- A link to your repository
- The architecture drawing and the IP plan (updated to what you actually built)
- A live demo to your mentor:
  - The 3 checks from the VM (DNS, database, internet)
  - The GKE pod reading the bucket and the secret without keys
  - A drift being detected and fixed
  - Destroy and recreate of the dev environment

## ❓ Be ready to answer
- Someone deleted the state bucket. What do you do? How could you have prevented it?
- A teammate ran `apply` from their laptop with an older version of the code. What could happen?
- How would this environment look in an organization that uses FAST? Which parts would come from which stage?
- Why did we use Fabric modules instead of the Terraform Registry modules?
