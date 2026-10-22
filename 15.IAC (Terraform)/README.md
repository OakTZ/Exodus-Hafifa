# IAC (Terraform)

> **Prerequisites:** 14.Google. The exercise also uses what you learned in 05.NetworkingAndDNS, 09.Kubernetes, 12.Database & Cache and 13.IdentityManagement.

🕒 **Estimated Time**: 7-8 days (about 3 days of learning, 4-5 days of exercise)
☁️ **Cloud**: Google Cloud

Terraform is the main tool our team works with. Take your time here, even if you finish early.

> 🔒 **Our environment is private:** no Cloud NAT, no public IPs and no external load balancers. You will expose services with **internal** load balancers, and test and deploy from VMs inside the VPC that you connect to with **IAP Desktop**: a **Windows VM** over RDP (for the browser) and a **Linux VM** over SSH (for the command line). The lessons and the exercise are built around this.

Go over the files in order:

1. [01-IAC.md](./01-IAC.md) - IaC and Terraform fundamentals
2. [02-TerraformOnGCP.md](./02-TerraformOnGCP.md) - Terraform with the Google provider, and the team workflow
3. [03-FabricFAST.md](./03-FabricFAST.md) - Cloud Foundation Fabric modules and FAST
4. [04-Exercise.md](./04-Exercise.md) - Build a small but complete GCP environment with Terraform

> [!IMPORTANT]
> After you finish files 1-3, schedule a test before starting the exercise.

Next: the [Final Project](../16.FinalProject/README.md). Keep your code, since you will build on it there.
