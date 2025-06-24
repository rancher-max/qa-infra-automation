# Architecture

This repository is designed to maintain a clear separation of concerns, allowing for independent management of infrastructure, via Terraform, and product deployment, via Ansible. Both tools' enable consistent and reliable deployments, reducing configuration drift and errors among the various teams working on the SUSE Rancher Prime suite of products.

## Explanation of Components

1. Terraform (`terraform/`):
   - Purpose: This component is responsible for Infrastructure as Code (IaC). It defines and provisions your underlying cloud or virtualized infrastructure (like virtual machines, networks, and storage) in a repeatable and version-controlled manner.

   - Folder Structure:
      - `terraform/provider/`: Contains the core configurations for specific cloud providers (e.g., AWS, Harvester).
      - `terraform/provider/modules/`: Houses reusable, self-contained Terraform modules for common infrastructure patterns.
      - `terraform/provider/modules-with-context/`: Contains modules that might include additional context or specific configurations tailored for different environments or use cases, such as airgap.

   - Flow: Terraform directly interacts with the APIs of the Cloud Provider(s) to create, update, or destroy resources.

2. Ansible (`ansible/`):
   - Purpose: This component handles Configuration Management and Product Deployment. Once the infrastructure is ready (whether provisioned by Terraform or manually), Ansible takes over to install, configure, and manage your product on those servers.

   - Key Feature: Modularity: Ansible is designed to be highly modular. Its playbooks and roles can be executed independently of how the underlying infrastructure was brought up. This means it can configure servers provided by Terraform or servers that were provisioned manually.
     - Folder Structure:
       - `ansible/product/feature/`: Contains specific playbooks and scripts for deploying and configuring different products or aspects of them.
       - `ansible/roles/`: Houses reusable, modular units of Ansible content. Each role encapsulates specific configuration tasks (e.g., installing a package, setting up a service, deploying an application component). These roles are designed for reusability and adaptability.

   - Flow: Ansible connects to the target servers (typically via SSH) and executes playbooks to install software (like Rancher or RKE2), apply configurations, manage services, and ensure your product is correctly set up.

## High-Level Workflow

1. Infrastructure Provisioning: You either use Terraform to declaratively provision the necessary servers and network infrastructure in your chosen Cloud Provider(s), or you utilize manually provisioned / existing infrastructure.

2. Product Deployment & Configuration: Once the infrastructure is available and accessible, Ansible takes over. It connects to the provisioned (or existing) servers and applies its modular playbooks and roles to install, configure, and deploy your product.
