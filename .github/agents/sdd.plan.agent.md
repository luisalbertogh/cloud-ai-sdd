---
description: Execute the implementation planning workflow using the plan template to generate design artifacts.
tools: ['execute/getTerminalOutput', 'execute/runInTerminal', 'read/readFile', 'edit/createDirectory', 'edit/createFile', 'edit/editFiles', 'search/fileSearch', 'search/listDirectory', 'search/searchResults', 'search/textSearch', 'web', 'aws-knowledge-mcp-server/*', 'awslabs.aws-documentation-mcp-server/*', 'context7/*', 'agent']
handoffs: 
  - label: Create Tasks
    agent: speckit.tasks
    prompt: Break the plan into tasks.
  - label: Create Checklist
    agent: speckit.checklist
    prompt: Create a checklist for the provided specification.
---

## User Input

```text
$ARGUMENTS
```

You **MUST** consider the user input before proceeding (if not empty).

## Outline

1. **Setup**: Load the manifest file in `.specify/memory/manifest.md` to get the feature name and other context. Use the implementation plan template from `.specify/templates/plan.template.md` to create a new plan document under `[FEATURE_DIR]/plan.md`. If the file is already created, load it instead. The `IMPL_PLAN` variable should point to the path of the plan document.

2. **Load context**: Read FEATURE_SPEC and all `.specify/memory/*.constitution.md` files. Load IMPL_PLAN template (already copied).

3. **Execute plan workflow**: Follow the structure in IMPL_PLAN template to:
   - Fill Technical Context (mark unknowns as "NEEDS CLARIFICATION")
   - Fill Constitution Check section from constitution
   - Evaluate gates (ERROR if violations unjustified)
   - Phase 0: Generate `research.md` (resolve all NEEDS CLARIFICATION)
   - Phase 1: Generate `data-model.md`, `contracts/`, `quickstart.md`
   - Phase 2: Generate architecture plan, *iac* plan (if needed) and diagrams. Use available tools, MCP servers and skills.
   - Re-evaluate Constitution Check post-design

4. **Stop and report**: Command ends after Phase 2 planning. Report branch, IMPL_PLAN path, and generated artifacts.

## Phases

### Phase 0: Outline & Research

1. **Extract unknowns from Technical Context** above:
   - For each NEEDS CLARIFICATION → research task
   - For each dependency → best practices task
   - For each integration → patterns task

2. **Generate and dispatch research agents**:

   ```text
   For each unknown in Technical Context:
     Task: "Research {unknown} for {feature context}"
   For each technology choice:
     Task: "Find best practices for {tech} in {domain}"
   ```

3. **Consolidate findings** in `research.md` using format:
   - Decision: [what was chosen]
   - Rationale: [why chosen]
   - Alternatives considered: [what else evaluated]
   - Additional links/resources: [any relevant links]

**Output**: `research.md` with all NEEDS CLARIFICATION resolved

### Phase 1: Design & Contracts

**Prerequisites:** `research.md` complete

1. **Extract entities from feature spec** → `data-model.md`:
   - Entity name, fields, relationships
   - Validation rules from requirements
   - State transitions if applicable

2. **Define interface contracts** (if project has external interfaces) → `/contracts/`:
   - Identify what interfaces the project exposes to users or other systems
   - Document the contract format appropriate for the project type
   - Examples: public APIs for libraries, command schemas for CLI tools, endpoints for web services, grammars for parsers, UI contracts for applications
   - Skip if project is purely internal (build scripts, one-off tools, etc.)

**Output**: data-model.md, /contracts/*, quickstart.md, agent-specific file

### Phase 2: Architecture Planning

**Prerequisites:** `research.md` complete

> [!IMPORTANT]
> Use the available tools, MCP servers and skills.

1. Use the template in `.specify/templates/architecture-plan.template.md` to create a new document under `FEATURE_DIR` named `architecture.md`. If the file already exists, load it instead. `ARCH_PLAN` variable should point to the path of the architecture plan document.

2. Create all the **required D2 diagrams and pictures** and link them to the architecture plan within each corresponding section.

3. **Complete the architecture plan** with the requested explanations per section as described in the template.

4. **Infraestructure as Code**: If the specifications in `spec.md` requests the usage of *infrastructure as code* (e.g. Terraform/Opentofu/Terragrunt) create a separate document. Create a `iac.md` with sections for:
   - Module structure
   - State management
   - Provider requirements
   - Naming conventions
   - Cross-module dependency graph. Use D2 to create the diagram and corresponding SVG image and link it in the document. **ONLY if some problem arises with the use of D2**, include it as an ASCII graph within a markdown code block in the `iac.md` file.
   - Mandatory tags
   - Variable handling strategy

Fill in the sections with the provided information in the specifications and the guidelines described in the constitution files. **DO NOT INCLUDE SOURCE CODE** except for sample code snippets used for illustrative purposes.

## Key rules

- Use absolute paths
- ERROR on gate failures or unresolved clarifications

## Update the manifest file

Update the manifest file in `.specify/memory/manifest.md`: 
- Load the file from `.specify/memory/manifest.md`. If the manifest file does not exists, notify the user and **stop any further action**.
- Update only the placeholders, for example in `PROPERTY: [PROPERTY_NAME]`, update the `[PROPERTY_NAME]` with the actual value. Use the information you have for this purpose. **Replace ONLY the placeholders where you have actual values. Do not update other placeholders if you don't have actual values**. Do not use `TBD` or leave the placeholder empty like in `[]`. Keep it as it is.

  ```
  # If I only know FEATURE_NAME, before:
  FEATURE: [FEATURE_NAME]
  OTHER_PROPERTY: [OTHER_PROPERTY]

  # After my update:
  FEATURE: s3-file-processor
  OTHER_PROPERTY: [OTHER_PROPERTY]
  ```

- **Keep the rest of the template as it is**.
