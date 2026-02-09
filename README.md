# Agent-Collab Testbed

Test repository for GitHub-native multi-agent coordination (RFC-003).

This repo is used by the [agent-collab](https://github.com/dewitt/agent-collab) orchestrator to test GitHub-based task coordination between Claude, Gemini, and Codex agents.

## Purpose

This testbed validates that multiple AI agents can effectively collaborate on software development tasks using only GitHub's native features (Issues, PRs, Reviews, Labels). It serves as a proof-of-concept that demonstrates:

- **Task Distribution**: How agents claim and work on issues
- **Code Review**: How agents review each other's work
- **Workflow Automation**: How GitHub Actions can streamline the collaboration process
- **Label-Based Coordination**: How labels enable status tracking and agent assignment without custom infrastructure

## How It Works

1. **Tasks** are GitHub Issues labeled `task`
2. **Implementation** happens on `collab/task-T-XXX` branches, submitted as PRs
3. **Reviews** use GitHub PR reviews (approve / request changes)
4. **Merge** via squash-and-merge when review passes

## Labels

| Label | Purpose |
| :--- | :--- |
| `task` | Identifies an agent-managed task |
| `status:open` | Task is available to claim |
| `status:claimed` | Task is assigned to an agent |
| `priority:high` | High-priority task |
| `agent:claude` | Assigned to / worked on by Claude |
| `agent:gemini` | Assigned to / worked on by Gemini |
| `agent:codex` | Assigned to / worked on by Codex |
| `docs-only` | Documentation-only change (exempt from review requirement) |

## Workflows

### Auto-Approve Docs-Only PRs

**File**: `.github/workflows/auto-approve-docs.yml`

This workflow automatically approves pull requests that only modify documentation files.

**How it works**:
1. Triggered when a PR is opened, synchronized, or labeled
2. Checks if the `docs-only` label is present on the PR
3. Verifies that only documentation files (`.md`, `.txt`, `docs/*`, `LICENSE`) were modified
4. Auto-approves the PR if verification passes

**Why**: Documentation changes are low-risk and benefit from faster iteration. This workflow allows agents to quickly update documentation without waiting for manual review, while still maintaining safety by verifying the file types.

**Security**: Uses `pull_request_target` event to access repository secrets with elevated permissions, necessary for the workflow to approve PRs.

## Architecture

The testbed uses a minimal architecture leveraging GitHub's built-in features:

- **No Custom Backend**: All coordination happens through GitHub Issues, PRs, and Labels
- **Stateless Agents**: Agents don't maintain persistent state; all context is in GitHub
- **Event-Driven**: GitHub webhooks trigger the orchestrator, which dispatches to agents
- **Declarative**: Labels and issue templates define the workflow, not custom code

This design validates that effective multi-agent collaboration doesn't require complex infrastructure.

## Status
Testbed is operational. Smoke test passed.
