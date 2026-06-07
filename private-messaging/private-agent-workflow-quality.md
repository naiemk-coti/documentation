# Private Agent Workflow Quality

## Purpose

Use this guide when COTI private messaging is part of a real multi-agent workflow, not just a one-off encrypted send.

The goal is to make private agent-to-agent coordination reliable enough that a coordinator agent can delegate work, receive a private result, and produce a public answer without leaking intermediate context.

## Quality Bar

A private agent workflow is ready when it has:

- a known sender identity
- a known recipient identity
- a clear private task payload
- a reply path
- a receipt or audit signal
- a failure path
- a rule for what can be shown publicly

If one of these is missing, the workflow is still a demo, not a dependable agent coordination layer.

## Canonical Workflow

Use this pattern for coordinator-to-specialist work.

1. Coordinator decides that a specialist needs private context.
2. Coordinator verifies the specialist wallet address and role.
3. Coordinator sends a structured private request with `send_message`.
4. Coordinator records the `transactionHash` and `messageId`.
5. Specialist checks `list_inbox`.
6. Specialist reads the request with `read_message`.
7. Specialist replies privately with `send_message`.
8. Coordinator checks `get_account_stats`, then `list_inbox`.
9. Coordinator reads the reply with `read_message`.
10. Coordinator writes the public answer without exposing private intermediate notes.

## Recipient Identity

Do not send sensitive context to an address just because it is syntactically valid.

Before sending, the agent should know:

- recipient role, such as `SecurityAgent`, `LegalAgent`, or `ReviewerAgent`
- recipient wallet address
- why that recipient is allowed to see the content
- whether the recipient can read inbox messages
- where the reply should be sent

Recommended recipient record:

```text
Agent: SecurityAgent
Role: Reviews authentication, secrets, dependency, and unsafe-code risks
Wallet: 0x...
Allowed content: security-sensitive implementation context
Reply path: send private reply to CoordinatorAgent wallet 0x...
```

If identity is uncertain, stop and ask for the recipient mapping. Guessing here is trash; it turns private messaging into private leakage.

## Message Templates

### Delegation Request

```text
Type: delegation_request
Task: <specific task>
Context: <private context needed to do the task>
Constraints: <what not to expose, modify, or assume>
Expected output: <format and level of detail>
Reply path: Reply privately to <wallet or agent name>
Public handling: <what may be included in the final public answer>
Deadline or priority: <optional>
```

### Expert Review Request

```text
Type: expert_review_request
Review target: <design, patch, claim, document, or decision>
Review lens: <security, legal, factuality, code quality, compliance>
Private evidence: <notes, sources, customer context, or draft>
Expected output: <findings, severity, fixes, confidence>
Reply path: Reply privately to <wallet or agent name>
Public handling: Do not reveal private notes directly; summarize only approved conclusions.
```

### Approval Request

```text
Type: approval_request
Decision needed: <approve, reject, escalate, or request changes>
Private context: <sensitive deal, customer, vendor, or incident details>
Approval criteria: <rules the reviewer should apply>
Expected output: <approval status plus reason>
Reply path: Reply privately to <wallet or agent name>
Audit note: Include the returned message ID in the coordination log.
```

### Research Handoff

```text
Type: research_handoff
Research question: <question or hypothesis>
Private findings: <intermediate notes, sources, contradictions>
Open questions: <what still needs checking>
Expected output: <summary, citations, recommendation, confidence>
Reply path: Reply privately to <wallet or agent name>
Public handling: Use only verified findings in the final answer.
```

### Incident Or Security Review

```text
Type: incident_security_review
Issue: <suspected secret, risky dependency, unsafe code, or incident>
Private evidence: <logs, filenames, customer data, or exploit notes>
Risk model: <who is exposed and how>
Expected output: <severity, exploitability, fix, validation step>
Reply path: Reply privately to <wallet or agent name>
Public handling: Do not expose secrets, exploit details, or customer identifiers.
```

## Receipts And Audit Signals

After each send, record:

- `transactionHash`
- `messageId` when available
- sender wallet
- recipient wallet
- purpose
- expected reply path

Use `list_sent` when the coordinator needs to prove that a request was sent or avoid duplicate delegation.

Use `get_message_metadata` when the agent needs public routing metadata without reading the message body.

Use `get_account_stats` before inbox polling when the agent only needs to know whether inbox state changed.

## Failure Handling

### Missing Wallet Or AES Key

Symptom:

- the agent cannot send or decrypt messages

Action:

- run the init path from `quickstart.md`
- confirm `PRIVATE_KEY` and `AES_KEY` exist in the configured runtime
- do not ask a recipient to handle private work until it can read its inbox

### No Gas

Symptom:

- sends fail before transaction submission or during broadcast

Action:

- let `--init` request starter gas
- if the starter grant is already claimed or unavailable, fund the wallet manually

### Unknown Recipient

Symptom:

- the task names a role but no wallet address is available

Action:

- ask for the role-to-wallet mapping
- do not substitute a random address or sink address for sensitive work

### Invalid Recipient

Symptom:

- the address is zero, malformed, or the sender itself

Action:

- reject the send and ask for a valid recipient wallet

### Oversized Message

Symptom:

- plaintext exceeds the contract chunk limit

Action:

- summarize the private context
- split into multiple purposeful messages
- use a shared file only when all intended readers are allowed to see it

### No Reply

Symptom:

- the coordinator sent a request but no response is visible

Action:

- check `list_sent` to verify the request
- check `get_account_stats` before listing inbox
- poll `list_inbox` only when there is evidence inbox state changed
- escalate publicly only if the user requested a status update

### Unauthorized Read

Symptom:

- `read_message` cannot decrypt a message

Action:

- confirm the configured wallet is the sender or recipient
- switch to the authorized agent runtime
- do not ask another agent to paste decrypted content into public chat

## Public Answer Boundary

Private messaging protects the message body, not the final answer.

Before answering the user, the coordinator should decide:

- what was private evidence
- what was private reasoning
- what conclusion can be safely summarized
- which identifiers, secrets, deal details, customer details, or exploit notes must stay hidden

Good public answer:

```text
The reviewer found two high-risk issues: token reuse and missing privilege checks. I recommend rotating the affected tokens and adding an authorization guard before release.
```

Bad public answer:

```text
SecurityAgent said the leaked customer token is sk_live_... and the exploit path is...
```

## When A Different Surface Is Better

Use a shared file when the artifact is large, durable, and safe for every intended reader.

Use local memory when the note is only for the same agent later.

Use public chat when the user should see the content directly.

Use a task manager when the important object is status, ownership, and deadline rather than private content.

Use email or Slack when the recipient is a human or existing team workflow, not an agent wallet.

## Minimum Test Before Claiming Workflow Quality

Run one end-to-end flow:

1. Initialize two wallets.
2. Send a delegation request from coordinator to specialist.
3. Read it from the specialist inbox.
4. Send a private specialist reply.
5. Read the reply from the coordinator inbox.
6. Confirm the final public answer excludes private-only content.

If this flow is not tested, do not claim production-quality multi-agent coordination.
