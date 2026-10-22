# 🚀 Terraform Exercise - A Small GCP Environment

🕒 **Estimated Time**: 4-5 days
☁️ **Cloud**: Google Cloud, in a sandbox project you get from your mentor

In this exercise you will build a small but complete environment on GCP with Terraform: a private network, IAM, storage, a database, DNS, Windows and Linux VMs, a GKE cluster and a Cloud Run service.

**Breadth over depth:** keep every resource as simple as possible. The goal is to touch many services and connect them correctly, not to configure every option.

> **Important notice!**
> Everything is created with Terraform. Creating or changing resources in the console or with `gcloud` is not allowed, except where the exercise asks you to do it on purpose.
> Use the smallest tiers, and label every resource (e.g. `owner`, `env`, `managed-by: terraform`). Ask your mentor whether to destroy expensive resources (GKE, Cloud SQL) at the end of each day. It's all code, so you can bring them back with one `apply`.

## 🔒 Our environment's constraints
Our environment is private, like the real one you will work on:
- **No Cloud NAT and no public IPs.** Nothing inside the VPC can reach the internet. Google APIs are reached with **Private Google Access**.
- **No external load balancers.** Everything is exposed with **internal** load balancers, and reached from inside the VPC only.
- **VMs inside the VPC are your way in.** You connect to them with **[IAP Desktop](https://github.com/GoogleCloudPlatform/iap-desktop)**, which tunnels through IAP to **Windows VMs over RDP** and to **Linux VMs over SSH**. You test and deploy from them:
  - A **Windows VM**, for the browser: opening internal UIs
  - A **Linux VM**, for the command line: `kubectl`, `helm`, `terraform`, `dig`, `psql`
  - IAP Desktop itself runs on Windows. On macOS or Linux, use `gcloud compute ssh --tunnel-through-iap` for the Linux VM, and `gcloud compute start-iap-tunnel <vm> 3389 --local-host-port=localhost:3389` with an RDP client for the Windows VM.

Every part below is built around these constraints. When something doesn't work, the first question to ask is "does this need the internet?"

## Rules
- Use **Cloud Foundation Fabric modules** for all resources, except where the exercise tells you to write your own. Pin them to a release with `?ref=`.
- No service account keys. Ever. Use impersonation.
- No public IPs, no Cloud NAT, no external load balancers.
- Every change goes through a commit. Keep the `plan` output of important changes in your merge requests.

## Prerequisites
- A sandbox project with billing, and permissions to manage it (ask your mentor)
- `gcloud`, `terraform`, `tflint` and `pre-commit` installed on your machine
- IAP Desktop (on Windows), or `gcloud` + an RDP client (on macOS / Linux)
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
1. Draw the architecture in draw.io, with the official GCP icons. Include every service from parts 3-9, how they connect, and how **you** reach them (laptop → IAP → your VMs → internal LB).
2. Write an **IP plan**: the main subnet, the GKE pod and service ranges, the **proxy-only subnet** for internal load balancers, the Private Service Access range, and the internal IPs you reserve. Explain why the ranges don't overlap, and how many pods and nodes they allow.
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

## 3. Private network
Using `net-vpc` and `net-vpc-firewall`:
1. A custom-mode VPC (no auto-created subnets)
2. One subnet with **secondary ranges** for GKE pods and services, and **Private Google Access** enabled, defined as a YAML file with the **subnet factory**
3. A **proxy-only subnet** (needed by internal Application Load Balancers), also from the factory
4. Firewall rules, defined as YAML files with the **firewall factory**:
   - Allow RDP (3389) to the Windows VM and SSH (22) to the Linux VM, only from the IAP range (`35.235.240.0/20`). Use network tags or service accounts as targets, not "all instances".
   - Allow Google's health check ranges (`35.191.0.0/16`, `130.211.0.0/22`) to reach your backends
   - Allow internal traffic inside the subnet, and from the proxy-only subnet to your backends
   - Make sure everything else is denied
5. A **Private Service Access** range for Cloud SQL
> **Question:** Without Cloud NAT, what can a VM in this VPC reach, and what can't it reach? How does Private Google Access let it reach GCS and Artifact Registry without a public IP?

## 4. IAM and service accounts
Using `iam-service-account`, create a service account for each workload, with the least privilege it needs:
- `vm-sa` for the Windows and Linux VMs
- `gke-app-sa` for the app in GKE (it reads from the `assets` bucket and the DB password secret)
- `run-sa` for the Cloud Run service

Also give **your user** the IAP permission to tunnel to the VMs (`roles/iap.tunnelResourceAccessor`), and what you need to log in to the Linux VM (read about **OS Login**).

Grant roles on the resources themselves (the bucket, the secret, the VMs) and not on the whole project, where possible.
> **Question:** For each grant, did you use `iam`, `iam_bindings` or `iam_bindings_additive`? Why? What would happen if someone else in the team also managed IAM on the same resource?

## 5. Storage, registry and secrets
1. An **Artifact Registry** Docker repository (`artifact-registry`) for your own images
2. An Artifact Registry **remote repository** for Docker Hub (and other registries you need, like `quay.io` or `ghcr.io`). Your GKE nodes have no internet, so this is the only way they can pull public images.
3. A **Secret Manager** secret for the DB password (`secret-manager`). Generate the password with `random_password`.
4. Push an image you built in an earlier module (e.g. your FastAPI app from 03.Python) to your repository
> **Question:** The generated password is in your Terraform state. Is that a problem? Who can read the state? How would you avoid it?
>
> **Question:** How does a remote repository get images from Docker Hub, when nothing in your VPC can reach the internet?

## 6. Database
Using `cloudsql-instance`:
- A PostgreSQL instance with **private IP only**, on the smallest tier
- A database and a user, with the password from Part 5
- Try `terraform destroy` on it. What stops you? Why is that a good thing? How do you get past it on purpose?

## 7. DNS and the VMs
1. A **private** Cloud DNS zone (`dns`), e.g. `hafifa.internal.`, attached to your VPC
2. A record `db.hafifa.internal` that points to the Cloud SQL private IP. Take the IP from the module's output, not by copy-paste.
3. Two small VMs (`compute-vm`), both **without a public IP**, running as `vm-sa`:
   - A **Windows Server** VM
   - A **Linux** VM (e.g. Debian), with **OS Login** enabled
4. Connect to both with **IAP Desktop**: RDP to the Windows VM, SSH to the Linux VM. How do you get the Windows password? How does SSH to the Linux VM authenticate you, without you copying any key?
5. The VMs have no internet either. Get the tools you need onto them (e.g. a PostgreSQL client):
   - Windows: check what is already installed on Google's Windows images, and think about how to download files from a GCS bucket over Private Google Access
   - Linux: `apt install` can't reach the Debian mirrors. Hint: Artifact Registry remote repositories can proxy OS package repositories too.
6. From the Linux VM:
   - `dig db.hafifa.internal` - it should resolve to the private IP
   - Connect to the database with `psql`, using the DNS name
   - List the files in the `assets` bucket - it should work, over Private Google Access
   - `curl` a public website - it should **fail**. Explain why.
7. From the Windows VM: `nslookup db.hafifa.internal`, and keep it for the browser tests in Parts 8 and 9.
> **Check:** All the above behave as expected, and neither VM has an external IP.

## 8. GKE
Using `gke-cluster-autopilot`:
1. A **private** Autopilot cluster in your VPC, using the secondary ranges from Part 3, with a **private control plane endpoint**
2. Configure `kubectl` on the **Linux VM**, and reach the cluster from there
3. Configure **Workload Identity Federation for GKE**, so a Kubernetes service account in the cluster acts as `gke-app-sa`
4. Run a pod with your image from Artifact Registry. Without any key files, it should:
   - Read a file from the `assets` bucket
   - Read the DB password from Secret Manager
5. Run a public image (e.g. `nginx`) through your **remote repository**. What happens if you use `docker.io/nginx` directly? Why?
6. Expose a simple web app with an **internal load balancer**: an internal `LoadBalancer` Service, or a Gateway with an internal GatewayClass. Give it a DNS name in your private zone, and open it in a browser on the Windows VM.
> **Question:** How did the pod get credentials? Follow the path from the pod to the Google API.
>
> **Question:** What is the difference between an internal passthrough Network Load Balancer and an internal Application Load Balancer? Which one did you get, and why does one of them need the proxy-only subnet?

## 9. Cloud Run
Using `cloud-run-v2`:
- A Cloud Run service running `us-docker.pkg.dev/cloudrun/container/hello`, as `run-sa`
- Set its **ingress to internal**, and require authentication
- Call it from the Linux VM, with an identity token. Then try to call it from your laptop. What happens?
- **Bonus:** put an **internal** Application Load Balancer (`net-lb-app-int`) with a serverless NEG in front of it, with a name in your private DNS zone
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
6. **Try the forbidden:** add a Cloud NAT or an external IP to your code, and run `apply`. What stops you? (Read about organization policy constraints, e.g. `constraints/compute.vmExternalIpAccess`.) Remove it afterwards.

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
- Install ArgoCD on your GKE cluster from Terraform (with the `helm` provider), and point it at your GitOps repo from 11.ArgoCD. The `helm` provider must reach the private control plane, and the ArgoCD images and chart must come from somewhere reachable. Where do you run it from? Where exactly is the line between what Terraform manages and what ArgoCD manages?
- Add a **Pub/Sub** topic with a push subscription that triggers your Cloud Run service
- Add a log-based alert on the Cloud Run service

## 📦 Deliverables
- A link to your repository
- The architecture drawing and the IP plan (updated to what you actually built)
- A live demo to your mentor, from your VMs over IAP Desktop:
  - The checks from Part 7 (DNS, database, bucket, no internet)
  - The GKE pod reading the bucket and the secret without keys
  - The web app behind the internal load balancer, opened by its DNS name
  - A drift being detected and fixed
  - Destroy and recreate of the dev environment

## ❓ Be ready to answer
- Someone deleted the state bucket. What do you do? How could you have prevented it?
- A teammate ran `apply` from their laptop with an older version of the code. What could happen?
- Your GKE pod is stuck in `ImagePullBackOff`. In our environment, what are the first 3 things you check?
- How does your traffic get from your laptop to each of your VMs? Which protocols, ports and identities are involved? What is different between the Windows VM and the Linux VM?
- How would this environment look in an organization that uses FAST? Which parts would come from which stage?
- Why did we use Fabric modules instead of the Terraform Registry modules?
