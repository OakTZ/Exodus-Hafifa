# IAC

This document will guide you through the fundamentals of Infrastructure as Code (IaC) and Terraform, the tool we use to manage and provision infrastructure in the cloud.

## 📘 Goals

By the end of this onboarding, you should:

* Understand what IaC is and why it matters.

* Be comfortable writing, planning, and applying Terraform configurations.

* Know how to structure Terraform projects using best practices.

* Be able to deploy a small architecture on GCP using Terraform.

## 🧠 Understanding the Concepts

Start by researching and writing short summaries for each of the following topics.
(You can find answers in the official Terraform and Google Cloud docs, Medium articles, or YouTube tutorials.)

### 🏗️ What is Infrastructure as Code (IaC)?

* What problem does IaC solve?

* What are the benefits of using IaC?

* What are the risks or downsides of IaC?

* What is the difference between declarative and imperative IaC? Which one is Terraform?

### 🌍 Terraform Basics

* What is Terraform and how does it work?

* What is a Terraform provider?

* What are resources and data sources?

* What is the difference between terraform init, plan, apply, and destroy?

* What is the Terraform state file (terraform.tfstate) and why is it important?

* What is remote state and why should it be used?

* What is state locking? What happens if two people run `apply` at the same time without it?

* Why should you never edit the state file by hand? What do `terraform state list`, `terraform state mv` and `terraform state rm` do?

### ⚙️ Core Terraform Concepts

* What is a module? What is the difference between a root module and a child module?

* What are variables and outputs?

* What are locals and data sources used for?

* How do you manage dependencies between resources? What is the difference between implicit dependencies and `depends_on`?

* What are `count` and `for_each`? Why is `for_each` usually preferred?

* What are `dynamic` blocks?

* What are lifecycle rules (`prevent_destroy`, `create_before_destroy`, `ignore_changes`)?

### 🔁 Changing Existing Infrastructure

* What is **drift**? How does Terraform detect it?

* How do you bring a resource that was created manually under Terraform management? (`import` blocks vs `terraform import`)

* How do you rename a resource or move it into a module without destroying it? (`moved` blocks)

* How do you stop managing a resource without destroying it? (`removed` blocks)

* What does `-replace` do? When would you use it?

### 🧩 Best Practices

* How should Terraform code be structured (folders, naming, etc.)?

* What is version locking for providers and modules?

* What is the purpose of a .terraform.lock.hcl file?

* How do you handle secrets in Terraform safely? Are values marked `sensitive` encrypted in the state?

* How do you separate environments (dev / prod)? Compare a folder per environment, workspaces, and Terragrunt

* What is `terraform test`? What can you test with it?

### References
* [Terraform docs](https://developer.hashicorp.com/terraform/docs)
* [Terraform language](https://developer.hashicorp.com/terraform/language)
* [Google Cloud - Best practices for using Terraform](https://cloud.google.com/docs/terraform/best-practices/general-style-structure)
