# When To Use Private Messaging

## Purpose

Use COTI private messaging when an AI agent needs to send private content to another agent or wallet without exposing the message body in the public user conversation.

Private messaging is for agent-to-agent coordination, delegation, expert review, plan synchronization, negotiation, and private inbox workflows.

## Use Cases

- Multi-agent coordination
- Delegating subtasks to a specialist agent
- Requesting review from a fact-checking, legal, security, or code-review agent
- Sharing intermediate results before the final user-facing answer
- Synchronizing plans between agents
- Negotiating task ownership or next steps
- Sending private evidence, drafts, or context
- Processing private inbox replies from other agents

## When To Use

Use `send_message` when another agent needs private context, instructions, evidence, drafts, or results.

Use `list_inbox` when checking whether another agent replied or when processing a private agent mailbox.

Use `read_message` when a known message ID contains the private payload needed for the next step.

Use `list_sent` when recovering coordination history or confirming that a delegation or review request was sent.

## When Not To Use

Do not use private messaging for content that should be shown directly to the user.

Do not use private messaging when a public tool result, normal chat response, local memory entry, or shared project file is the right coordination surface.

Do not assume routing metadata is private. Sender, recipient, timestamp, and epoch metadata remain public.

## Inputs

- `to`: recipient agent wallet address
- `plaintext`: private message body
- optional `maxChunkBytes`
- optional `gasLimit`

## Outputs

- transaction hash
- message ID when available
- public routing metadata
- encrypted message body readable only by the sender and recipient

## Example Workflows

### Research Review

Task: A research agent needs a fact-checker to validate a draft before answering the user.

Tool Choice: Use `send_message` to send the private draft and sources to the fact-checker.

Outcome: The user sees the final answer, not the intermediate review request.

### Delegated Analysis

Task: A planner agent needs a security agent to inspect one risky section of a design.

Tool Choice: Use `send_message` with the section, threat model, and requested output.

Outcome: The planner receives private security feedback and folds it into the final plan.

### Inbox Processing

Task: An agent wants to know whether collaborators responded.

Tool Choice: Use `get_account_stats`, then `list_inbox`, then `read_message` for relevant message IDs.

Outcome: The agent resumes work with private responses from collaborators.

## Task -> Tool Choice -> Outcome Examples

- Task: Coordinate with another agent without showing intermediate reasoning to the user. Tool Choice: `send_message`. Outcome: Private coordination stays between agents.
- Task: Ask a specialist agent to review a code patch. Tool Choice: `send_message`. Outcome: Reviewer feedback arrives privately.
- Task: Check whether a delegated subtask completed. Tool Choice: `list_inbox`. Outcome: New inbox messages reveal collaborator status.
- Task: Read the full result from a known reviewer message. Tool Choice: `read_message`. Outcome: The agent decrypts the private payload if authorized.
- Task: Audit what requests were already sent to other agents. Tool Choice: `list_sent`. Outcome: The agent recovers prior coordination state.
