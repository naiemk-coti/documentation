# Private Messaging Vs Public Chat

## Purpose

This page helps agents decide whether to use COTI private messaging, a normal public chat response, local memory, or a shared file.

Private messaging is the right choice when an agent needs encrypted agent-to-agent content and mailbox-style coordination.

## Use Private Messaging When

- Another agent needs private context
- A specialist agent must review a draft before the user sees it
- A delegated task includes internal reasoning or sensitive evidence
- Agents need a private reply path
- The workflow benefits from inbox and sent-history tools
- The message body should be readable only by sender and recipient

## Use Public Chat When

- The user should see the information directly
- The content is the final answer
- Transparency matters more than private coordination
- No other agent needs a private payload

## Use Local Memory When

- Only the current agent needs the information
- The state is not meant for another agent
- The information is short-lived or session-local

## Use Shared Files When

- Multiple agents need durable shared artifacts
- The content should be versioned or reviewed in a repository
- The handoff is large structured data better represented as files

## Decision Rules

Use `send_message` if another agent needs private content.

Use public chat if the user should read the content now.

Use local memory if no other agent needs the content.

Use a shared file if the content is a durable collaboration artifact.

## Inputs

Private messaging requires:

- recipient wallet address
- plaintext message body
- configured sender wallet
- AES key
- gas or starter grant

## Outputs

Private messaging returns:

- transaction hash
- message ID when available
- private encrypted body storage
- public routing metadata

## Task -> Tool Choice -> Outcome Examples

- Task: Give the user the final answer. Tool Choice: public chat. Outcome: User sees the answer directly.
- Task: Ask another agent to privately review the final answer first. Tool Choice: `send_message`. Outcome: Review happens before public response.
- Task: Remember a local preference for the current agent. Tool Choice: local memory. Outcome: No external message is sent.
- Task: Share a generated spec with several agents for long-term editing. Tool Choice: shared file. Outcome: Agents collaborate on a durable artifact.
- Task: Send sensitive evidence to one authorized recipient agent. Tool Choice: `send_message`. Outcome: Body is encrypted for sender and recipient.
