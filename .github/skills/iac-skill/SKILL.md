---
name: iac-skill
description: Use when working with Terraform, OpenTofu or Terragrunt - creating modules, writing tests (native test framework, Terratest), setting up CI/CD pipelines, reviewing configurations, choosing between testing approaches, debugging state issues, implementing security scanning (trivy, checkov), or making infrastructure-as-code architecture decisions
compatibility: Requires the below specified tools
---

# Infrastructure as Code (IaC) best practices

Comprehensive Terraform, OpenTofu and Terragrunt guidance covering testing, modules, CI/CD, and production patterns. Use the below instructions to answer any query regarding infrastructure as code.

> **WHAT TO EXPECT FROM THIS SKILL**
>
> - The skill is focused in generating Terraform/Opentofu/Terragrunt code. **Do not attempt to run any Terraform/Terragrunt command** unless explicitily requested.
> - Include a `manifest.md` file to expose the used versions of the languages and tech stacks applied (Terraform, Opentofu, Terragrunt, etc).

## Tools

Use the below tools if available to get **up-to-date documentation and recommended practices** when developing infrastructure for **Azure** and **AWS** Cloud environments.

| Tool | Use For |
| ------ | --------- |
| `context7/*` | Query up-to-date documentation |
| `microsoft_docs_search` | Find documentation—concepts, guides, tutorials, configuration |
| `microsoft_docs_fetch` | Get full page content (when search excerpts aren't enough) |
| `azureterraformbestpractices` | Get Azure Terraform best practices |
| `get_azure_bestpractices` | Get Azure best practices guidelines |
| `aws-knowledge-mcp-server` | AWS best practices |
| `awslabs.aws-documentation-mcp-server` | AWS up-to-date documentation |

## When to Use This Skill

**Activate this skill when:**

- Creating new Terraform, OpenTofu or Terragrunt configurations or modules.
- Setting up testing infrastructure for IaC code.
- Deciding between testing approaches (validate, plan, frameworks).
- Structuring multi-environment deployments.
- Implementing CI/CD for infrastructure-as-code.
- Reviewing or refactoring existing Terraform/OpenTofu/Terragrunt projects.
- Choosing between module patterns or state management approaches.

**Don't use this skill for:**

- Basic Terraform/OpenTofu/Terragrunt syntax questions.
- Provider-specific API reference (link to docs instead).
- Cloud platform questions unrelated to Terraform/OpenTofu/Terragrunt.

## Incorporated Taming Copilot Directives (Behavioral Hierarchy)

- **Primacy of User Directives**: Direct user commands take highest priority.
- **Factual Verification**: Prioritize tools for current, factual answers over internal knowledge.
- **Adherence to Philosophy**: Follow minimalist, surgical approaches—code on request only, minimal necessary changes, direct and concise responses.
- **Tool Usage**: Use tools purposefully; declare intent before action; prefer parallel calls when possible.

These summaries ensure the mode functions independently while aligning with the broader chat mode context. For full details, reference the original DevOps Core Principles and Taming Copilot instructions.

## Chat Mode Integration

When operating in chat mode with these instructions loaded:

- Treat this as a self-contained extension that incorporates summarized general rules for independent operation.
- Prioritize user directives over automated actions, especially for terraform commands beyond validate.
- Use implicit dependencies where possible and confirm before any terraform plan or apply operations.
- Maintain minimalist responses and surgical code changes, aligning with the incorporated Taming philosophy.
- **Planning Files Awareness**: Always check for planning files in the `.terraform-planning-files/` folder (if present). Read and incorporate relevant details from these files into responses, especially for migration or implementation plans. If speckit or similar planning files exist in user-specified folders, prompt the user to confirm inclusion or read them explicitly.

## Code styling

Review [terraform-style-guide.md](references/terraform-style-guide.md) and [terraform-code-patterns.md](references/terraform-code-patterns.md) for code styling and code conventions. In case of some rule being ambiguos, ask for clarification, **do not assume**.

## Code patterns

These instructions provide IaC-specific guidance for solutions created with Terraform, Opentofu and/or Terragrunt, including how to incorporate and use Azure Verified Modules (AVM) and AWS best practices.

For general Terraform and Opentofu conventions, see [terraform-conventions.md](references/terrafom-conventions.md).

### Use Azure Verified Modules (AVM)

When working for a solution on Azure, prioritize the usage of AVM. If an Azure Verified Module is not available for the resource, suggest creating one "in the style of" AVM in order to align to existing work and provide an opportunity to contribute upstream to the community.

An exception to this instruction is if the user has been directed to use an internal private registry, or explicitly states they do not wish to use Azure Verified Modules.

See [azure-verified-modules-terraform.instructions.md](azure-verified-modules-terraform.instructions.md) for more instructions.

## Anti-Patterns to Avoid

**Configuration:**

- MUST NOT hardcode values that should be parameterized
- SHOULD NOT use `terraform import` as a regular workflow pattern
- SHOULD avoid complex conditional logic that makes code hard to understand
- MUST NOT use `local-exec` provisioners unless absolutely necessary

**Security:**

- MUST NEVER store secrets in Terraform files or state
- MUST avoid overly permissive IAM/RBAC roles or network rules
- MUST NOT disable security features for convenience
- MUST NOT use default passwords or keys

**Operational:**

- MUST NOT run any Terraform/Terragrunt command, only generate Terraform/Opentofu code.

These build on the incorporated Taming Copilot directives for secure, operational practices.
