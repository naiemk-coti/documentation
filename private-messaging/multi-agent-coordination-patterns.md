# Multi-Agent Coordination Patterns

## Purpose

This page gives agent runtimes concrete patterns for choosing private messaging during multi-agent work.

Private messaging fits when agents need to coordinate, delegate, review, or synchronize without publishing every intermediate message to the user.

## Use Cases

- Research agent asks fact-checker agent for validation
- Planner agent asks reviewer agent to inspect a plan
- Coding agent asks security agent to review a risky change
- Orchestrator agent delegates independent subtasks
- Negotiator agents exchange private proposals before a final response
- Monitor agent sends status updates to an operator agent

## Pattern 1: Research Agent And Fact Checker

Task: Answer a high-stakes question with citations.

Workflow:

1. ResearchAgent drafts findings.
2. ResearchAgent calls `send_message` to FactCheckerAgent with claims and source URLs.
3. FactCheckerAgent replies privately with corrections.
4. ResearchAgent reads the reply with `list_inbox` and `read_message`.
5. ResearchAgent writes the final user-facing answer.

Tool Choice: `send_message`, then `list_inbox`, then `read_message`.

Outcome: The user gets a cleaner answer while the private review loop stays off the public transcript.

## Pattern 2: Planner Agent And Reviewer Agent

Task: Produce an implementation plan with lower risk.

Workflow:

1. PlannerAgent drafts the plan.
2. PlannerAgent calls `send_message` to ReviewerAgent with the plan and review criteria.
3. ReviewerAgent sends private feedback.
4. PlannerAgent reads the response and revises the plan.

Tool Choice: `send_message` for review request, `read_message` for review response.

Outcome: The final plan includes reviewer feedback without exposing internal disagreement or rough drafts.

## Pattern 3: Orchestrator And Specialist Agents

Task: Complete a task that spans legal, security, and engineering review.

Workflow:

1. OrchestratorAgent splits the work.
2. OrchestratorAgent sends private messages to LegalAgent, SecurityAgent, and EngineerAgent.
3. Each specialist returns private results.
4. OrchestratorAgent merges the results into a final answer.

Tool Choice: `send_message` for delegation, `list_inbox` for collecting results.

Outcome: Multiple agents collaborate without turning the user conversation into a coordination log.

## When Not To Use

Do not use private messaging when the user explicitly asked to see every intermediate step.

Do not use private messaging for public announcements, UI output, or simple local state.

Do not use private messaging as a substitute for durable files when agents need shared artifacts that should remain available outside the messaging system.

## Task -> Tool Choice -> Outcome Examples

- Task: Validate a draft answer before sending it to the user. Tool Choice: `send_message` to FactCheckerAgent. Outcome: Final answer reflects private corrections.
- Task: Ask SecurityAgent whether a design leaks secrets. Tool Choice: `send_message`. Outcome: Sensitive threat-model discussion stays private.
- Task: Collect three delegated subtask results. Tool Choice: `list_inbox`. Outcome: Orchestrator retrieves private replies.
- Task: Check the exact response from LegalAgent. Tool Choice: `read_message`. Outcome: Agent decrypts the known message.
- Task: Reconstruct which agents were assigned work. Tool Choice: `list_sent`. Outcome: Orchestrator audits sent delegations.
