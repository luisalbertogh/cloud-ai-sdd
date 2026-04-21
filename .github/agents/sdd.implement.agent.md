---
description: Execute the implementation plan by processing and executing all tasks defined in tasks.md
tools: ['execute/getTerminalOutput', 'execute/runInTerminal', 'read/readFile', 'edit/createDirectory', 'edit/createFile', 'edit/editFiles', 'search/fileSearch', 'search/listDirectory', 'search/searchResults', 'search/textSearch', 'web', 'aws-knowledge-mcp-server/*', 'awslabs.aws-documentation-mcp-server/*', 'context7/*', 'agent']
---

## User Input

```text
$ARGUMENTS
```

You **MUST** consider the user input before proceeding (if not empty).

## Outline

> [!IMPORTANT]
> Do not run more than 5 parallel tasks at once. If there are more than 5 parallel tasks, execute them in batches of 5 to avoid overwhelming the system.
>
> **Human-in-the-loop checkpoints** are required before proceeding to non-MVP tasks and before executing any tests. Always wait for user confirmation at these checkpoints.

1. **Set up context**: Load the `.specify/memory/manifest.md` file to get the current state of the development process, including completed tasks and any relevant context. Load the `.specify/memory/*.constitution.md` to understand the rules and guidelines for implementation.

2. **Check checklists status** (if `FEATURE_DIR/checklists/` exists):
   - Scan all checklist files in the `checklists/` directory. Skip `analysis_report.md` if present.
   - For each checklist, count:
     - Total items: All lines matching `- [ ]` or `- [X]` or `- [x]`
     - Completed items: Lines matching `- [X]` or `- [x]`
     - Incomplete items: Lines matching `- [ ]`
   - Create a status table:

     ```text
     | Checklist | Total | Completed | Incomplete | Status |
     |-----------|-------|-----------|------------|--------|
     | ux.md     | 12    | 12        | 0          | ✓ PASS |
     | test.md   | 8     | 5         | 3          | ✗ FAIL |
     | security.md | 6   | 6         | 0          | ✓ PASS |
     ```

   - Calculate overall status:
     - **PASS**: All checklists have 0 incomplete items
     - **FAIL**: One or more checklists have incomplete items

   - **If any checklist is incomplete**:
     - Display the table with incomplete item counts
     - **STOP** and ask: "Some checklists are incomplete. Do you want to proceed with implementation anyway? (yes/no)"
     - Wait for user response before continuing
     - If user says "no" or "wait" or "stop", halt execution
     - If user says "yes" or "proceed" or "continue", proceed to step 3

   - **If all checklists are complete**:
     - Display the table showing all checklists passed
     - Automatically proceed to step 3

3. Load and analyze the implementation context:
   - **REQUIRED**: Read `tasks.md` for the complete task list and execution plan
   - **REQUIRED**: Read `plan.md` and `architecture.md` for tech stack, architecture, and file structure
   - **IF EXISTS**: Read `iac.md` for infrastructure as code details (i.e. Terraform code)
   - **IF EXISTS**: Read `data-model.md` for entities and relationships
   - **IF EXISTS**: Read `contracts/` for API specifications and test requirements
   - **IF EXISTS**: Read `research.md` for technical decisions and constraints
   - **IF EXISTS**: Read `quickstart.md` for integration scenarios

4. **Project Setup Verification**:
   - **REQUIRED**: Create/verify `.gitignore` file based on actual project setup:

      **If ignore file already exists**: Verify it contains essential patterns, append missing critical patterns only.
      **If ignore file missing**: Create with full pattern set for detected technology.

5. Parse `tasks.md` structure and extract:
   - **Task phases**: Setup, Tests, Core, Integration, Polish
   - **Task dependencies**: Sequential vs parallel execution rules
   - **Task details**: ID, description, file paths, parallel markers [P]
   - **Execution flow**: Order and dependency requirements

6. Execute implementation following the task plan:
   - **Phase-by-phase execution**: Complete each phase before moving to the next. 
   - **MVP First**: Complete MVP tasks first. Then **STOP and ASK** the user to proceed with further implementation of non-MVP tasks. **Do not continue until the user confirms**. Proceed in the same way with the rest of the pending phases, that is, **IMPLEMENT PHASE (MVP or other)** --> **STOP** --> **ASK** --> **PROCEED** or **STOP**.
   - **Respect dependencies**: Run sequential tasks in order, parallel tasks [P] can run together  
   - **Follow TDD approach**: Suggest the execution of the test tasks before their corresponding implementation tasks. Ask for confirmation. **DO NOT EXECUTE ANY TEST unless the user explicitly confirms so**. Then, proceed with the tests or skip them based on user input.  
   - **File-based coordination**: Tasks affecting the same files must run sequentially
   - **Validation checkpoints**: Verify each phase completion before proceeding

7. Implementation execution rules:
   - **Setup first**: Initialize project structure, dependencies, configuration
   - **Tests before code**: If you need to write tests for contracts, entities, and integration scenarios
   - **Core development**: Implement models, services, CLI commands, endpoints
   - **Integration work**: Database connections, middleware, logging, external services
   - **Polish and validation**: Unit tests, performance optimization, documentation

8. Progress tracking and error handling:
   - Report progress after each completed task
   - Halt execution if any non-parallel task fails
   - For parallel tasks [P], continue with successful tasks, report failed ones
   - Provide clear error messages with context for debugging
   - Suggest next steps if implementation cannot proceed
   - **IMPORTANT** For completed tasks, make sure to mark the task off as [X] in the tasks file.

9. Completion validation:
   - Verify all required tasks are completed
   - Check that implemented features match the original specification
   - Validate that tests pass and coverage meets requirements
   - Confirm the implementation follows the technical plan
   - Report final status with summary of completed work

Note: This command assumes a complete task breakdown exists in `tasks.md`. If tasks are incomplete or missing, suggest running `/speckit.tasks` first to regenerate the task list.
