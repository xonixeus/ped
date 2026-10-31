# Production & Engineering Agents

Reusable AI agent profiles for the Production & Engineering Department, with clear responsibilities, explicit boundaries, and project-specific memory.

The collection currently includes five specialists: **Beck** for back-end development, **Faye** for front-end development, **Uma** for UX/UI design, **Sophie** for internal technical writing, and **Paige** for product technical writing. Together, they support the department’s design, development, and documentation work.

## Meet the Agents

| Agent      | Role                      | Focus                                                                                                                                                                               |
| ---------- | ------------------------- | ----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| **Beck**   | Back-end Developer        | APIs, business logic, database schemas and migrations, service integrations, server-side authorization, and developer tests                                                         |
| **Faye**   | Front-end Developer       | User interfaces, responsive layouts, accessibility implementation, client-side state, API integration, and developer tests                                                          |
| **Uma**    | UX/UI Agent               | User journeys, information architecture, wireframes, prototypes, interface copy, design-system guidance, and usability reviews                                                      |
| **Sophie** | Internal Technical Writer | Internal system references, employee onboarding, standard operating procedures (SOPs), operational and troubleshooting guides for an internal knowledge platform such as Confluence |
| **Paige**  | Product Technical Writer  | Help-center and knowledge-base articles, user manuals, tutorials, release notes, reseller and partner playbooks, and developer-portal documentation                                 |

Each agent follows the project’s existing conventions, asks when a consequential decision is unresolved, and hands off work outside its responsibilities.

## Getting Started

1. Select the agent whose responsibilities match your task.
2. Load its Markdown profile into your AI environment using the environment’s supported agent configuration or instruction-loading mechanism.
3. Provide project context, relevant files, requirements, and acceptance criteria.
4. Configure the tools and permissions needed for the assignment.
5. Review the deliverables, verification evidence, and unresolved dependencies before integrating the work.

Support for configuration fields varies by environment. Verify how your runner handles the frontmatter, particularly `allowed_handoffs`. Automatic delegation requires support from the environment running the agents.

## Example Assignments

### Beck — Back-end Development

> Implement the agreed project-members API using the existing service and database patterns. Enforce server-side authorization, test relevant success and failure cases, and document the contract for Faye.

### Faye — Front-end Development

> Implement the project-members screen from Uma’s approved design using the documented API. Include loading, empty, error, and success states, verify keyboard behavior, and report the checks performed.

### Uma — UX/UI Design

> Design the project-members management flow using our existing design system. Define relevant interaction states, identify unresolved product decisions, and provide an implementation handoff for Faye and Beck.

### Sophie — Internal Technical Writing

> Write an SOP for rotating the project-members service credentials, based on Beck’s implementation notes. Identify who performs each step, the prerequisites, expected results, and escalation path, and flag any steps the system owner still needs to verify.

### Paige — Product Technical Writing

> Write a help-center article and release note for the new project-members feature. Explain who can manage members, the required permissions, and the steps involved, and identify any behavior that still needs confirmation from Beck or Faye.

## Working Together

For a feature involving all five roles:

1. **Uma** defines the experience and communicates the intended behavior.
2. **Beck and Faye** agree on the API contract and implementation dependencies.
3. **Beck** implements the supporting services and data behavior.
4. **Faye** implements the interface and integrates it with those services.
5. **Uma** reviews the implemented experience, while Beck and Faye verify their changes and address findings within their scope.
6. **Sophie** documents internal operation of the feature—system references, SOPs, and onboarding notes—verified with Beck and Faye.
7. **Paige** documents the released behavior for customers, partners, and developers, verified against the implementation and Uma’s design.

Work can proceed in parallel when ownership and shared contracts are clear. If delegation is unavailable, the agent asks the user or coordinator how to proceed.

Each profile lists its intended handoff partners in `allowed_handoffs`:

| Agent  | Hands off to             |
| ------ | ------------------------ |
| Beck   | Faye, Uma, Sophie, Paige |
| Faye   | Beck, Uma, Sophie, Paige |
| Uma    | Faye, Beck, Paige        |
| Sophie | Faye, Beck, Paige        |
| Paige  | Faye, Beck, Uma, Sophie  |

A useful handoff includes:

- The action or decision needed.
- The reason it is needed.
- Relevant requirements, evidence, and constraints.
- Unresolved questions and dependencies.
- What has already been completed or verified.

## Profile Structure

Each profile follows a shared structure:

| Section                     | Purpose                                                 |
| --------------------------- | ------------------------------------------------------- |
| Frontmatter                 | Agent identity, description, and configuration metadata |
| Who Are You                 | The role’s purpose                                      |
| What You Do                 | Responsibilities the agent owns                         |
| Rules                       | Scope boundaries and actions to avoid                   |
| What Good Output Looks Like | Expectations for deliverables and verification          |

## Scope and Quality

The current collection covers design, development, and documentation. Dedicated QA, security-review, operations, and release-management profiles are not yet included.

Each agent is expected to:

- Stay within its assigned responsibilities.
- Preserve existing project conventions and other agents’ work.
- Identify mocks, unresolved dependencies, and verification limitations.
- Report checks as passed only when they were actually run and passed.
- Provide evidence and context for the next person or agent.

Developer tests, design reviews, and documentation reviews support—but do not replace—independent QA, security review, or release approval. The writers document verified behavior; they do not claim technical, security, legal, or release approval on behalf of other roles.

Role instructions guide behavior. Access permissions, repository protections, publication destinations, and deployment controls must be configured separately in the execution environment.

## Contributing

Contributions should preserve the shared profile structure and keep responsibilities clear.

- Define explicit ownership and practical handoff boundaries.
- Describe observable expectations for quality.
- Avoid unnecessary overlap between roles.
- Keep general instructions independent of specific application frameworks.
- Distinguish runtime-supported settings from descriptive metadata.
- Base new rules on demonstrated needs and practical experience.
