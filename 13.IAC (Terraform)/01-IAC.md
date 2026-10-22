# IAC

This document will guide you through the fundamentals of Infrastructure as Code (IaC) and Terraform, the tool we use to manage and provision infrastructure in the cloud.

## 📘 Goals

By the end of this onboarding, you should:

* Understand what IaC is and why it matters.

* Be comfortable writing, planning, and applying Terraform configurations.

* Know how to structure Terraform projects using best practices.

* Be able to deploy a small architecture using Terraform.

## 🧠 Part 1: Understanding the Concepts

Start by researching and writing short summaries for each of the following topics.
(You can find answers in the official Terraform and GCP docs, Medium articles, or YouTube tutorials.)

### 🏗️ What is Infrastructure as Code (IaC)?

* What problem does IaC solve?

* What are the benefits of using IaC?

* What are the risks or downsides of IaC?

### 🌍 Terraform Basics

* What is Terraform and how does it work?

* What is a Terraform provider?

* What are resources and data sources?

* What is the difference between terraform plan, apply, and destroy?

* What is the Terraform state file (terraform.tfstate) and why is it important?

* What is remote state and why should it be used?

### ⚙️ Core Terraform Concepts

* What is a module?

* What are variables and outputs?

* What are locals and data sources used for?

* How do you manage dependencies between resources?

### 🧩 Best Practices

* How should Terraform code be structured (folders, naming, etc.)?

* What is version locking for providers and modules?

* What is the purpose of a .terraform.lock.hcl file?

* How do you handle secrets in Terraform safely?
  
> [!IMPORTANT]  
> After you finish, schedule a test before moving on

## ☁️ Part 2: Building Architecture

Build an architecture in AWS that includes all the resources needed to deploy a RAG project. 

Use draw.io to draw the architecture with the proper resources symbols.

#### Requirements

For the vector DB use S3 Vectors.
For the RAG process use Lambdas and not ECS.


> [!IMPORTANT]  
> After you finish, submit the architecture and get an approval before Continuing.

## 💪 Part 2: Deploying the architecture

Ask for the relevant AWS account, write the terraform Infrastructure and deploy it :)

Good luck 😉
