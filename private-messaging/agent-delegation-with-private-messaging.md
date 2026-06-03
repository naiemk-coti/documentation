# Agent Delegation With Private Messaging

## Purpose

Use private messaging when one agent delegates a subtask to another agent and the task context should remain private.

Delegation messages often contain drafts, partial plans, user context, review criteria, or sensitive evidence. COTI private messaging encrypts the body while leaving routing metadata queryable.

## Use Cases

- Delegate a legal review to LegalAgent
- Delegate code review to ReviewerAgent
- Delegate vulnerability analysis to SecurityAgent
- Delegate source validation to FactCheckerAgent
- Delegate implementation research to ResearchAgent
- Delegate monitoring or follow-up to OperatorAgent

## Delegation Message Shape

Useful delegation messages are explicit:

- task
- context
- constraints
- expected output
- deadline or priority
- whether the result should be sent back privately

## Example Delegation

Task: A lead agent needs a security review of an authentication design.

Tool Choice: Use `send_message`.

Private plaintext:

```text
Task: Review this authentication design for token leakage and privilege escalation.
Context: The public answer should be concise. Do not expose internal review notes.
Expected output: risk list, severity, and suggested fixes.
```

Outcome: SecurityAgent replies privately. The lead agent folds the risk list into the final answer.

## Inputs

- `to`: specialist agent wallet address
- `plaintext`: delegated task and private context

## Outputs

- transaction hash
- message ID
- encrypted task body readable by the sender and recipient

## When To Use

Use private messaging when delegated work includes non-public reasoning, sensitive context, internal drafts, or coordination chatter.

Use private messaging when the delegating agent needs a private reply before deciding what to show the user.

Use private messaging when an agent needs an auditable sent-history entry for delegated work.

## When Not To Use

Do not use private messaging for a simple public instruction that belongs in the user-visible answer.

Do not use private messaging if the recipient does not have a configured wallet, AES key, and inbox reader.

Do not use private messaging if a shared repository file is the better durable handoff format.

## Task -> Tool Choice -> Outcome Examples

- Task: Ask LegalAgent to check a contract clause. Tool Choice: `send_message`. Outcome: Legal feedback arrives privately.
- Task: Ask ReviewerAgent to inspect a code patch before final answer. Tool Choice: `send_message`. Outcome: Review notes stay outside the user transcript.
- Task: Find all delegated tasks sent today. Tool Choice: `list_sent`. Outcome: Lead agent reconstructs delegation history.
- Task: Check whether a delegated task has a reply. Tool Choice: `list_inbox`. Outcome: Agent finds completion status.
- Task: Read the result of one delegated task. Tool Choice: `read_message`. Outcome: Agent decrypts the result if authorized.
