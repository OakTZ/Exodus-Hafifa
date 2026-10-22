# Google Cloud

🕒 **Duration**: at least 12 days (about 10 days for the Cloud Architect learning path, 2 days for GKE)

You will be asked many questions about how Google Cloud works, from the point of view of a platform team that spends most of its time on architectures and their implementation. Take the time to study the material properly, even if you finish it earlier. The topics below are broad and relevant to everything we do in GCP, so it is recommended to research as much as you can, including topics that are not covered in detail here.

All the courses are on **Google Skills**: https://www.skills.google/ (sign in with the account your mentor tells you to use).

### Part 1: Cloud Architect learning path (~10 days)

On Google Skills, search the catalog for the **Professional Cloud Architect** learning path. It is very relevant to the topics below. The certification itself is described here: https://cloud.google.com/learn/certification/cloud-architect/

- Go over the learning path. It contains most of the material you will be asked about. If something is unclear, you are expected to research it on your own.
- It includes explanation videos and many labs. You don't have to perform the labs, but it is recommended to read their content, as they contain many practical examples, screenshots and explanations.
- Go over the key points below. Skip topics such as migration and concepts that are less specific to Google Cloud. If something is unclear, ask your mentor whether it is relevant.

### Part 2: GKE (~2 days)

GKE is where our workloads run, so here you **do** the labs.

- **Required:** [Getting Started with Google Kubernetes Engine](https://www.skills.google/course_templates/2) (about 6 hours)
- **Recommended:** [Architecting with Google Kubernetes Engine: Production](https://www.skills.google/course_templates/33) (about 4 hours) - GKE security, logging and monitoring, and using managed storage and databases from GKE
- **Recommended:** [Google Kubernetes Engine Best Practices: Security](https://www.skills.google/course_templates/732) (about 2 hours) - RBAC, hardening, VPC networking and Binary Authorization

While doing the GKE courses, make sure you can answer:
- What is the difference between GKE Autopilot and Standard? When would you choose each one?
- What are node pools? Why would you have more than one?
- What is a VPC-native cluster? Where do the pod and service IPs come from?
- What is a private cluster? How do you reach its control plane?
- How do workloads in GKE get access to Google Cloud APIs? (Workload Identity Federation for GKE)
- How do you expose a service in GKE: a LoadBalancer Service, Ingress, or Gateway? What does GKE create in Google Cloud for each one?
- Our environment doesn't allow external load balancers or Cloud NAT. How do you expose a service **internally** only (internal LoadBalancer Service, internal Gateway)? How do private nodes pull images without internet access?
- How do GKE upgrades work? What are release channels and maintenance windows?

### Lecture

After you finish both parts, you will give a lecture on a more specific topic and expand on it.

---

### 1.3 Designing network, storage, and compute resources. Considerations include:
- Integration with on-premises/multicloud environments
- Vertex AI solutions (e.g., Gemini LLMs, Agent Builder, Model Garden, Gemini models)
- Cloud-native networking (e.g., VPC, peering, firewalls, container networking, shared VPC, PSC)
- Choosing data processing solutions
- Choosing appropriate storage types (e.g., object, file, databases)
- Mapping compute needs to platform products (e.g., Google Kubernetes Engine [GKE], Cloud Run, Cloud Run functions)
- Choosing compute resources (e.g., spot VMs, custom machine types, specialized workload)

## Section 2: Managing and provisioning a solution infrastructure

### 2.1 Configuring network topologies. Considerations include:
- Extending to on-premises environments (hybrid networking)
- Extending to a multicloud environment that may include Google Cloud to Google Cloud communication
- Security protection (e.g. intrusion protection, access control, firewalls)
- VPC design and load balancing (e.g., access to cloud, internet, and cloud adjacent services)

### 2.3 Configuring compute systems. Considerations include:
- Compute resource provisioning
- Compute volatility configuration (spot vs. standard)
- Cloud-native network configuration for compute resources (e.g., Google Compute Engine, GKE, serverless networking, VMware Engine)
- Infrastructure orchestration, resource configuration, and patch management
- Container orchestration
- Serverless computing

### 2.4 Monitoring, logging, profiling, and alerting solutions. Considerations include:
- Monitoring and logging
- Profiling and benchmarking
- Alerting strategies

## Section 3: Designing for security and compliance

### 3.1 Designing for security. Considerations include:
- Identity and access management (IAM)
- Resource hierarchy (organizations, folders, projects)
- Data security (key management, encryption, secret management)
- Security controls (e.g., auditing, VPC Service Controls, context aware access, organization policy, hierarchical firewall policy)
- Managing customer-managed encryption keys with Cloud Key Management Service
- Remote access (e.g., IAP, service account impersonation, beyondcorp, RDP, oslogin (SSH), Workload identity federation)
- Securing software supply chain
- Gemini in Security

## Section 4: Managing implementation

### 4.1 Advising development and operations teams to ensure successful deployment of the solution. Considerations include:
- Application and infrastructure deployment
- API management best practices (e.g., Apigee)
- Testing frameworks (load/unit/integration)
- Data and system migration and management tooling
- Gemini Cloud Assist

### 4.2 Interacting with Google Cloud programmatically. Considerations include:
- Cloud Shell Editor, Cloud Code, and Cloud Shell Terminal
- Google Cloud SDK (e.g., gcloud, gsutil and bq)
- Gemini Code Assist
- Cloud Emulators (e.g. Bigtable, Spanner, Pub/Sub, Firestore)
- Infrastructure as code (e.g., IaC)
- Accessing Google API best practices
- Google API client libraries
