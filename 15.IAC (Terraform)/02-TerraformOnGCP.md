# Terraform on Google Cloud

In this part you will learn how Terraform works with Google Cloud specifically, and how a team works with Terraform day to day.

## 📘 Goals

* Understand how Terraform authenticates to Google Cloud
* Understand how to store state in GCS
* Know the GCP-specific pitfalls (APIs, IAM, deletion protection)
* Understand the workflow around Terraform: reviews, CI and linting

## 🔐 Authentication

* What are Application Default Credentials (ADC)? What does `gcloud auth application-default login` do?
* What is **service account impersonation**? How do you configure the Google provider to impersonate a service account (`impersonate_service_account`)?
* Why is impersonation better than downloading a service account key file?
* How does a CI pipeline (e.g. GitLab CI) authenticate to GCP without a key file? Read about **Workload Identity Federation**

## 🗄️ State in GCS

* How do you configure the `gcs` backend? Does it support state locking?
* Why should the state bucket have object versioning enabled?
* The state bucket must exist before `terraform init`. How do you solve this chicken-and-egg problem?
* Who should have access to the state bucket? Why is the state sensitive?

## ☁️ The Google Provider

* What is the difference between the `google` and `google-beta` providers?
* Why do you need to enable APIs (`google_project_service`) before creating resources? What happens if you don't? What does `disable_on_destroy` do?
* **IAM in Terraform** - explain the difference between these resources, and why mixing them is dangerous:
  * `google_project_iam_policy`
  * `google_project_iam_binding`
  * `google_project_iam_member`
  * Which ones are **authoritative** and which are **additive**? What could happen if you apply `google_project_iam_binding` for `roles/owner` with only your service account in it?
* What is `deletion_protection` on resources like Cloud SQL and GKE? How do you destroy a resource that has it?
* What are **labels**? Why should every resource have them? (Think cost reports)

## 🔄 The Team Workflow

* How should a change go from your laptop to production? (branch → merge request with the `plan` output → review → `apply`)
* Why should `apply` run from CI and not from laptops?
* What do the following tools do? `terraform fmt`, `terraform validate`, `tflint`, `checkov` / `trivy`
* What is a **pre-commit hook**? How can it run these tools automatically?
* What should a reviewer look for in a `plan` output? Which words in a plan should make you stop and look twice? (hint: `destroy`, `forces replacement`)

### References
* [Google provider docs](https://registry.terraform.io/providers/hashicorp/google/latest/docs)
* [GCS backend](https://developer.hashicorp.com/terraform/language/backend/gcs)
* [Google Cloud - Terraform on Google Cloud](https://cloud.google.com/docs/terraform)
* [IAM policy for projects](https://registry.terraform.io/providers/hashicorp/google/latest/docs/resources/google_project_iam)
* [Workload Identity Federation](https://cloud.google.com/iam/docs/workload-identity-federation)
