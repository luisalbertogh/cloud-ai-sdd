# Terraform/Opentofu Conventions

## General Instructions

- Use Terraform to provision and manage infrastructure in the Cloud (AWS, Azure).
- Create only `.tf` files (Terraform). Skip other files like `.gitignore` or `.tfvars`.
- Add a `README.md` file at each module root level with each created Terraform module including:
  - A brief description of the purpose of the module.
  - Requirements and versions.
  - List of used individual resources.
  - No more than three examples of its usage with different combinations of the optional inputs.
  - An arguments reference (required inputs and optional inputs).
  - An attributes reference (outputs).
  - Do not attempt to use `terraform-docs` or any other tool to automatically generate any documentation.
- Do not create any test or try to perform any validation.

## Security

> Use the Terraform security patterns found in [terraform-security-patterns.md](terraform-security-patterns.md) as a reference.

- Always use the latest stable version of Terraform and its providers, unless speficic versions are provided as part of the prompt.
- Never include sensitive information such as Cloud credentials, API keys, passwords or certificates.
  - Use `.gitignore` to exclude files containing sensitive information from version control.
- Always mark sensitive variables as `sensitive = true` in your Terraform configurations.
  - This prevents sensitive values from being displayed in the Terraform plan or apply output.
- Use `ephemeral` for randomly created secrets.

## Modularity

- Use modules to avoid duplication of configurations.
  - Use modules to encapsulate related resources and configurations (i.e. VPC + subnets, Security group + rules)
  - Use modules to simplify complex configurations and improve readability.
  - Avoid circular dependencies between modules.
  - Avoid unnecessary layers of abstraction; use modules only when they add value.
    - Avoid using modules for single resources; only use them for groups of related resources.
    - Avoid excessive nesting of modules; keep the module hierarchy shallow.
- Use `output` blocks to expose important information about your infrastructure.
  - Use outputs to provide information that is useful for other modules or for users of the configuration.
  - Avoid exposing sensitive information in outputs; mark outputs as `sensitive = true` if they contain sensitive data.

## Maintainability

- Prioritize readability, clarity, and maintainability.
- Use comments to explain complex configurations and why certain design decisions were made.
- Write concise, efficient, and idiomatic configs that are easy to understand.
- Avoid using hard-coded values; use variables for configuration instead.
  - Set default values for variables, where appropriate.
- Use data sources to retrieve information about existing resources instead of requiring manual configuration.
  - This reduces the risk of errors, ensures that configurations are always up-to-date, and allows configurations to adapt to different environments.
  - Avoid using data sources for resources that are created within the same configuration; use outputs instead.
  - Avoid, or remove, unnecessary data sources; they slow down `plan` and `apply` operations.
- Use `locals` for values that are used multiple times to ensure consistency.

## Style and Formatting

Use the Terraform code patterns found in [terraform-code-patterns.md](terraform-code-patterns.md) as a reference.

## Documentation

- Always include `description` and `type` attributes for variables and outputs.
  - Use clear and concise descriptions to explain the purpose of each variable and output.
  - Use appropriate types for variables (e.g., `string`, `number`, `bool`, `list`, `map`).
- Document your Terraform configurations using comments, where appropriate.
  - Use comments to explain the purpose of resources and variables.
  - Use comments to explain complex configurations or decisions.
  - Avoid redundant comments; comments should add value and clarity.

## Azure-Specific Best Practices

If available, use the `#azureterraformbestpractices` tool to download up-to-date recommendations to work with Terraform for Azure. In any case, consider the below best practices when defining infrastructure for the Cloud:

### Resource Naming and Tagging

- Use the `microsoft-learn` tools to retrieve updated naming conventions.
- Use consistent region naming and variables for multi-region deployments.
- Implement consistent tagging.

### Resource Group Strategy for Azure

- Use existing resource groups when specified.
- Create new resource groups only when necessary.
- Use descriptive names indicating purpose and environment.

### Networking Considerations

- Use NSGs and ASGs appropriately.
- Implement private endpoints for PaaS services when required, use resource firewall restrictions to restrict public access otherwise. Comment exceptions where public endpoints are required.

### Security and Compliance

- Use Managed Identities instead of service principals.
- Implement Key Vault with appropriate RBAC.
- Enable diagnostic settings for audit trails.
- Follow principle of least privilege.

## Cost Management

- Consider any option that could reduce cost without impacting significantly in performance, realibility or availability (lifecycle policies, housekeeping, etc).
- Use environment-appropriate sizing (dev vs prod).
- Assume average cost constraints if not specified.

## State Management

- Use remote backend (Azure Storage) with state locking.
- Enforce OIDC for backend authentication.
- Never commit state files to source control.
- Enable encryption at rest and in transit.

## AWS-Specific Best Practices

If available, use the `#aws__search_documentation` tool to download up-to-date recommendations to work with AWS. In any case, consider the below best practices when defining infrastructure for the Cloud:

### Resource Naming and Tagging

- Use consistent region naming and variables for multi-region deployments.
- Implement consistent tagging.

### Networking Considerations

- Use security groups appropriately.
- Implement private endpoints for PaaS services when required, use resource firewall restrictions to restrict public access otherwise. Comment exceptions where public endpoints are required.

### Security and Compliance

- Use IAM roles as a preference.
- Enable diagnostic settings for audit trails.
- Follow principle of least privilege.

## Cost Management

- Consider any option that could reduce cost without impacting significantly in performance, realibility or availability (lifecycle policies, housekeeping, etc).
- Use environment-appropriate sizing (dev vs prod).
- Assume average cost constraints if not specified.

## State Management

- Use remote backend (S3) with state locking. Prefer S3 state locking over DynamoDB if possible.
- Enforce OIDC for backend authentication.
- Never commit state files to source control.
- Enable encryption at rest and in transit.
