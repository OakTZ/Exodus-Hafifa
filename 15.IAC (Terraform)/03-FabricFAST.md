# Cloud Foundation Fabric and FAST

[Cloud Foundation Fabric](https://github.com/GoogleCloudPlatform/cloud-foundation-fabric) is a collection of Terraform modules and reference designs for Google Cloud, written and maintained by Google Cloud's Professional Services team. It has two main parts:

* **Modules** (`modules/`) - building blocks: one module per GCP resource type (project, VPC, GKE cluster, Cloud SQL, ...)
* **FAST** (`fast/`) - a complete design for a GCP organization, implemented as Terraform **stages** built from these modules

## 📘 Goals

* Understand how Fabric modules are designed and how to use them
* Understand resource factories (YAML-driven Terraform)
* Understand the FAST design: stages, contracts and the security model
* Be able to find where a given resource would live in a FAST-based organization

## 🧱 Fabric Modules

Read the [modules README](https://github.com/GoogleCloudPlatform/cloud-foundation-fabric/tree/master/modules), then go over the READMEs of these modules: `project`, `net-vpc`, `net-vpc-firewall`, `iam-service-account`, `gcs`, `gke-cluster-autopilot`.

* How do you use a Fabric module in your code? Why must you pin it with `?ref=<version>`? What happens if you don't?
* Fabric modules don't publish to the Terraform Registry. What are the pros and cons of sourcing modules from Git?
* Fabric modules share a common **IAM interface**. Explain the difference between `iam`, `iam_bindings`, `iam_bindings_additive` and `iam_by_principals`. Which ones are authoritative? (Connect this to what you learned in 02-TerraformOnGCP.md)
* Compare a Fabric module (e.g. `net-vpc`) with writing the same resources yourself. What do you gain? What do you give up?
* Read a module's code (e.g. `modules/gcs`). How is it structured? What does it do with the variables you pass?
* Fabric modules are meant to be read, not just used. Why is it important to read the code of a module before you use it?

## 🏭 Resource Factories

* Read [Resource Factories: A descriptive approach to Terraform](https://medium.com/google-cloud/resource-factories-a-descriptive-approach-to-terraform-581b3ebb59c)
* What is a **resource factory**? What problem does it solve?
* Look at the factories in `net-vpc` (subnets) and `net-vpc-firewall` (rules). What does a YAML file for a subnet look like?
* Who can change infrastructure when it's defined in YAML? Why is that useful for a platform team?
* What are the downsides of factories? (Think about validation and debugging)
* Read the beginning of the `project-factory` module README. What can it create from YAML?

## 🚀 FAST

Read the [FAST README](https://github.com/GoogleCloudPlatform/cloud-foundation-fabric/tree/master/fast) and the [stages README](https://github.com/GoogleCloudPlatform/cloud-foundation-fabric/tree/master/fast/stages).

* What problem does FAST solve?
* What is a **stage**? Why is FAST split into stages instead of being one big Terraform root module?
* Why are stages modeled around **security boundaries** and **team ownership**?
* What does it mean that stages are **contracts**? How do outputs of one stage become inputs of the next one? Why does data only flow forward?
* Explain the purpose of each stage:
  * `0-org-setup`
  * `1-vpcsc`
  * `2-security`
  * `2-networking`
  * `2-project-factory`
  * Stage 3 (application-level stages)
* Each stage runs with its own service account. Why? How does that limit the damage of a mistake?
* What are **delegated role grants**? What problem do they solve?
* How does FAST run in CI/CD without service account keys?
* Read about the networking designs in `2-networking` (hub-and-spoke with peering, VPN, NCC). Draw one of them.
* You need a new project for a new team, with access to a Shared VPC and a KMS key. Which stages are involved, and in what order?
* Why won't we deploy FAST itself in the exercise? (hint: which permissions does stage 0 need?)

### References
* [Cloud Foundation Fabric](https://github.com/GoogleCloudPlatform/cloud-foundation-fabric)
* [Fabric modules](https://github.com/GoogleCloudPlatform/cloud-foundation-fabric/tree/master/modules)
* [FAST](https://github.com/GoogleCloudPlatform/cloud-foundation-fabric/tree/master/fast)
* [Google Cloud - Enterprise foundations blueprint](https://cloud.google.com/architecture/security-foundations)
