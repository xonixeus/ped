# Product & Engineering Department

A department of AI agents that designs, builds and documents software products, run on [Multica](https://github.com/multica-ai/multica). It has a project manager and five specialists, each with clear responsibilities and explicit boundaries.

This repository is universal: it holds no product's code or data, and anyone may deploy it for their own products.

## Meet the Agents

| Agent      | Role                      | Focus                                                                                                                                                                               |
| ---------- | ------------------------- | ----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| **Woz**    | Project Manager           | Intake, planning, assignments, coordination, acceptance and integration of the department's work, version tags, and reports to the owner                                            |
| **Uma**    | UX/UI Designer            | User journeys, information architecture, wireframes, prototypes, interface copy, design-system guidance, and usability reviews                                                      |
| **Beck**   | Back-end Developer        | APIs, business logic, database schemas and migrations, service integrations, server-side authorization, and developer tests                                                         |
| **Faye**   | Front-end Developer       | User interfaces, responsive layouts, accessibility implementation, client-side state, API integration, and developer tests                                                          |
| **Sophie** | Internal Technical Writer | Internal system references, employee onboarding, standard operating procedures (SOPs), operational and troubleshooting guides for an internal knowledge platform such as Confluence |
| **Paige**  | Product Technical Writer  | Help-center and knowledge-base articles, user manuals, tutorials, release notes, reseller and partner playbooks, and developer-portal documentation                                 |

## Repository Layout

| Path                     | Contents                                                                                                       |
| ------------------------ | -------------------------------------------------------------------------------------------------------------- |
| `TEAM.md`                | The shared operating charter: roles, decision rights, workflow and handoffs, Git rules, and rules for every agent |
| `agents/<name>/AGENTS.md` | Each agent's role-specific instructions                                                                         |
| `docs/SETUP.md`          | The operator's guide to deploying the department on Multica                                                    |

Every rule that applies to more than one agent is in `TEAM.md`, stated once. Each `AGENTS.md` holds only what is specific to that role.

## Deploying on Multica

Follow [`docs/SETUP.md`](docs/SETUP.md): Part A sets up the runtime, agents and credentials once; Part B adds a product. Each step says how to check it worked.

When `TEAM.md` or an `AGENTS.md` changes, paste the new version into the matching agents' Instructions fields.

## Example Assignments

### Uma — UX/UI Design

> Design the project-members management flow using our existing design system. Define relevant interaction states, identify unresolved product decisions, and provide an implementation handoff for Faye and Beck.

### Beck — Back-end Development

> Implement the agreed project-members API using the existing service and database patterns. Enforce server-side authorization, test relevant success and failure cases, and document the contract for Faye.

### Faye — Front-end Development

> Implement the project-members screen from Uma’s approved design using the documented API. Include loading, empty, error, and success states, verify keyboard behavior, and report the checks performed.

### Sophie — Internal Technical Writing

> Write an SOP for rotating the project-members service credentials, based on Beck’s implementation notes. Identify who performs each step, the prerequisites, expected results, and escalation path, and flag any steps the system owner still needs to verify.

### Paige — Product Technical Writing

> Write a help-center article and release note for the new project-members feature. Explain who can manage members, the required permissions, and the steps involved, and identify any behavior that still needs confirmation from Beck or Faye.

For a feature that needs several roles, give the request to **Woz**, who plans it, assigns each part and coordinates the handoffs (`TEAM.md` §7).

## Profile Structure

Each specialist profile follows a shared structure:

| Section                     | Purpose                                        |
| --------------------------- | ---------------------------------------------- |
| Frontmatter                 | Agent name, description, version and author    |
| Who Are You                 | The role’s purpose                             |
| What You Do                 | Responsibilities the agent owns                |
| Rules                       | Role-specific boundaries and actions to avoid  |
| What Good Output Looks Like | Expectations for deliverables and verification |

## Scope and Quality

The department covers design, development, documentation and project management. It has no dedicated QA, security-review, operations or release-management role; until it does, the owner holds those approvals. Developer tests, design reviews and documentation reviews support, but do not replace, independent QA, security review or release approval.

Role instructions guide behavior. Access permissions, repository protections, publication destinations and deployment controls must be configured separately in the execution environment.

## Contributing

Changes to this repository are rare: they record a change to the team or to the flow of work or information. Keep them general; never add anything specific to one product or owner.

- Preserve the shared profile structure and keep responsibilities clear.
- State a rule once: in `TEAM.md` if it applies to more than one agent, otherwise in that agent's `AGENTS.md`.
- Define explicit ownership and practical handoff boundaries, and avoid unnecessary overlap between roles.
- Describe observable expectations for quality.
- Keep instructions independent of specific application frameworks.
- Base new rules on demonstrated needs and practical experience.
