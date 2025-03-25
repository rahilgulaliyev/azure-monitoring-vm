# Terraform Deployment: VM Provisioning in Azure

## Were all necessary resources created to ensure the VM is accessible?
Yes, the configuration includes all the required resources for accessibility. It provisions a virtual network, a subnet, and a public IP, and associates them with the VM. Additionally, a network security group (NSG) is attached to allow SSH access and monitoring ports (22, 9090, and 9100). This ensures that both remote administration and monitoring tools work as expected.

## Is the Terraform code parameterized to allow reuse across different environments?
Yes, the code is designed to be reusable. Variables like environment, prefix, location, and vm_size make it easy to deploy the same setup across different environments (dev, test, prod) without changing the core configuration. By simply adjusting variable values, teams can create consistent infrastructure while keeping things flexible.

## How can it be ensured that the VM is managed exclusively through Terraform?
To maintain full control via Terraform, manual changes in Azure should be avoided. Enabling the remote backend (the commented-out backend "azurerm" block) helps with state management in a team setup, preventing configuration drift. Using terraform plan before terraform apply ensures visibility into changes before applying them. Additionally, implementing terraform state commands can help track and manage resources strictly within Terraform.

## What modifications are needed to make the code suitable for a team setup?
For a team environment, a few improvements can help:

Remote backend: Storing the state file in Azure Blob Storage ensures state consistency across team members.

Role-based access control (RBAC): Implementing IAM roles in Azure prevents unauthorized modifications.

Modules: Splitting the code into reusable modules (e.g., networking, compute, security) makes it easier to maintain.

Version control: Using Git with pull requests and approvals prevents accidental changes.

## How can the correct order of creating interdependent resources be ensured?
Terraform handles dependencies automatically using resource references. For example, the VM depends on the network interface (network_interface_ids = [azurerm_network_interface.nic.id]), which in turn depends on the subnet and virtual network. Explicit dependencies (depends_on) can be added in edge cases, but Terraform’s built-in dependency resolution usually does the job well.

## How can this code be executed automatically? Which Terraform commands make sense in which scenarios?
To automate execution:

CI/CD Pipelines: Use GitHub Actions, GitLab CI/CD, or Azure DevOps to run Terraform commands automatically.

Terraform Cloud/Enterprise: Provides remote execution and state management.

Cron Jobs/Scheduled Tasks: Can be used for periodic drift detection with terraform plan.

Relevant Terraform commands:

terraform init – Initializes the working directory and downloads providers.

terraform plan – Shows what changes will be made before applying.

terraform apply – Creates/updates resources.

terraform destroy – Deletes all resources.

terraform state list – Displays tracked resources in the state file.

terraform output – Shows defined output values.

## What are the advantages and disadvantages of using Terraform?
### Advantages:
✔ Infrastructure as Code (IaC) – Ensures consistency and repeatability.
✔ Multi-Cloud Support – Works across AWS, Azure, GCP, and others.
✔ Declarative Approach – You define what you want, and Terraform figures out how to do it.
✔ State Management – Keeps track of resources to avoid duplication.
✔ Version Control Friendly – Changes can be reviewed before deployment.

### Disadvantages:
✖ State Management Complexity – Requires careful handling, especially in a team.
✖ Learning Curve – Syntax and best practices take time to master.
✖ Limited Azure Feature Support – Some new Azure features take time to be supported in Terraform.
✖ No Built-in Secrets Management – Requires external tools like Azure Key Vault or HashiCorp Vault.